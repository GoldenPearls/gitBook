# 현대의  웹 서버 연결 수락

{% hint style="success" %}
🔍 즉, 지금의 웹 서버들은 “연결 수락” 자체를 논블로킹(비동기 I/O) + 보안 계층(TLS) + 프록시 계층(LB) 으로 분리하여 처리한다. 를 자세히 풀기
{% endhint %}



> **연결 수락(accept)** 자체를 커널/이벤트 루프, **TLS(보안 계층)**, **프록시·로드밸런서(LB)** 로 **분리**해 다루면서, 각 계층이 **비동기 I/O** 기반으로 **병목 없이** 흐르도록 만드는 것입니다.

## 전체 흐름 한눈에 보기

<figure><img src="../../../../.gitbook/assets/image (392).png" alt=""><figcaption></figcaption></figure>

```
[클라이언트] 
   ⇅ TCP/UDP
[ NIC ] → [커널 TCP 스택 & 소켓 백로그]
   ⇅ (accept4, non-blocking, epoll/kqueue/io_uring)
[리스너/이벤트 루프] 
   ⇅ (ALPN, 핸드셰이크, 세션 재개)
[TLS 종료/종단(terminator)]
   ⇅ (HTTP 파싱, 라우팅/리라이트, 정책)
[L7 프록시/리버스 프록시] 
   ⇅ (연결 재사용, 헬스체크, 리트라이, 회로차단)
[백엔드 서버/애플리케이션]
```

***

### 1) “연결 수락”의 논블로킹(비동기 I/O)

#### 핵심 아이디어

* 리스닝 소켓을 **논블로킹**으로 열고, `accept4(O_NONBLOCK)` 이후 모든 I/O를 **이벤트 루프**(예: epoll/kqueue/io\_uring)에서 처리합니다.
* 덕분에 **스레드/프로세스 폭증 없이** 수만\~수십만 동시 연결을 다룸.

#### 실무 요소

* **백로그(backlog)**: 커널이 SYN/ESTABLISHED 대기열을 관리. `somaxconn`과 앱의 `listen()` 파라미터를 함께 고려.
* **SO\_REUSEPORT**: 여러 워커가 **각자** 같은 포트에서 수락(“리스너 샤딩”), 커널이 부하 분산.
* **multi\_accept / accept\_mutex**(NGINX 옛 옵션): 우발적 **가로채기(thundering herd)** 방지.
* **타임아웃으로 Slowloris 방어**: `client_header_timeout`, `keepalive_timeout`, `limit_conn` 등으로 **느린 요청/헤더 지연** 차단.
* **백프레셔**: upstream 응답이 느리면 **읽기/쓰기 이벤트**를 적절히 꺼서(interest 해제) 큐 폭주 방지.

***

### 2) TLS 계층(보안) 분리

#### 왜 분리하나?

* TLS 핸드셰이크는 **CPU·왕복 지연** 비용이 큽니다. 종종 **프록시 층**(NGINX/Envoy 등)에서 **앞단에서 종료**시켜 애플리케이션 부담을 낮춥니다.

#### 포인트

* **ALPN**: 핸드셰이크 중 **프로토콜 협상** → `h2`(HTTP/2), `http/1.1`, `h3`(QUIC) 선택.
* **세션 재개**: session ticket/cache로 재연결 비용 절약. 단, **0-RTT(early data)** 는 **재생 공격(replay)** 위험 → 민감 요청엔 비활성/거절.
* **OCSP Stapling**: 인증서 폐지 상태를 서버가 **미리 첨부**, 클라이언트 검증 지연 감소.
* **mTLS(상호 인증)**: 클라이언트 인증서 검사로 **내부 시스템/파트너 전용 API** 보호.
* **kTLS/하드웨어 오프로드**: 커널/니코프로드로 TLS 암복호화 비용을 줄여 **스루풋↑/CPU↓**.
* **HTTP/2, HTTP/3**:
  * `h2`: 단일 TCP에서 **멀티플렉싱**(HoL 차단은 스트림 레벨에서 완화)
  * `h3(QUIC/UDP)`: **전송계층부터** 멀티플렉싱·0-RTT, 패킷 손실에도 연결 유지/빠른 회복

***

### 3) 프록시·로드밸런서(LB) 계층 분리

#### L4 vs L7

* **L4 LB**(TCP/UDP 레벨): IP/포트 기준 분산(예: AWS NLB). **매우 빠르고 단순**, 애플리케이션 레벨 로직 없음.
* **L7 프록시**(HTTP 레벨): 경로/호스트/헤더/쿠키 기반 **정책적 라우팅**(A/B, 카나리, 버전별). **관측성/보안/리트라이** 등 **풍부한 제어**.

#### L7에서 주로 하는 일

* **정책 라우팅**: `Host`, `Path`, `Header`, `Method` 조건으로 서로 다른 백엔드로 보냄.
* **커넥션 풀링/재사용**: 프록시↔백엔드 사이 **keepalive** 로 **핸드셰이크 비용 절약**.
* **헬스체크 & 서킷브레이커**: 실패율↑ 시 **격리**, **리트라이 예산(retry budget)** 으로 폭주 방지.
* **레이트 리밋/폭주 차단**: IP·토큰 단위 한도.
* **보안 헤더/필터**: HSTS, CSP, CORS, WAF 룰 등.
* **압축/캐시**: 정적 자원 or API 응답 캐싱·압축으로 대역폭 절감/지연 단축.
* **Observability**: 구조화된 액세스 로그, 지표(요청률·지연·오류), 트레이싱(Trace-Id) 삽입.

***

### 4) 쿠버네티스에서의 배치 패턴

* **Service (ClusterIP/NodePort/LoadBalancer)**: Pod 앞의 가상 IP.
* **Ingress + Ingress Controller (NGINX/Envoy/HAProxy)**: **L7 엔드포인트**.
* **Service Mesh (Envoy/Istio/Linkerd)**: 사이드카로 **mTLS, 리트라이, 라우팅, 트레이싱**을 코드 수정 없이 제공.
* **수평 오토스케일(HPA)** + **프록시 워커 수 조정**: **이벤트 루프 워커** × **Pod 개수**로 용량 유연 확장.

***

### 5) NGINX 기준 “핵심 튜닝 체크리스트”

```nginx
# (1) 워커/이벤트
worker_processes  auto;
events {
  worker_connections  65535;   # 1워커당 동접 수 상한
  multi_accept on;             # 한 번에 여러 accept
  # use epoll;                 # Linux면 암시적으로 epoll
}

# (2) TLS + HTTP/2/3(별도 QUIC 빌드 필요)
server {
  listen 443 ssl http2;        # http/1.1 + h2
  # listen 443 http3 reuseport;  # h3(QUIC) 환경에서

  ssl_protocols TLSv1.2 TLSv1.3;
  ssl_session_cache shared:SSL:50m;
  ssl_session_timeout 1d;
  ssl_stapling on; ssl_stapling_verify on;
  # ssl_early_data off;        # 0-RTT 위험 시 차단

  keepalive_timeout  65s;
  keepalive_requests 1000;     # 연결 재사용 한도
  client_header_timeout 10s;   # Slowloris 방지
  client_body_timeout   10s;

  # (3) 라우팅/프록시
  location /api/ {
    proxy_http_version 1.1;
    proxy_set_header Connection "";
    proxy_set_header Host $host;
    proxy_read_timeout 30s;
    proxy_pass http://backend_pool;
  }
}

# (4) 백엔드 풀(커넥션 재사용)
upstream backend_pool {
  keepalive 256;               # 프록시→백엔드 keepalive
  server 10.0.1.10:8080 max_fails=3 fail_timeout=10s;
  server 10.0.1.11:8080 max_fails=3 fail_timeout=10s;
}
```

**설명 요점**

* `worker_processes auto` + `worker_connections`로 **이벤트 루프 동접 캐파** 설정
* 헤더/바디 **타임아웃**으로 느린 클라이언트 방어
* TLS **세션 캐시/스테이플링**으로 핸드셰이크 비용↓
* `keepalive_requests` 로 **커넥션 장수 제한**(리소스 누수 방지)
* upstream `keepalive` 로 **프록시→백엔드 연결 재사용**
* `max_fails`/`fail_timeout` 으로 **헬스체크 대용**(간단 실패 격리)

***

### 6) 성능·안정성 관점에서의 분리 이점

* **격리**: TLS 문제(인증서, 핸드셰이크 폭주)와 앱 문제(슬로쿼리, GC)를 **레이어별로 분리**해 진단·스케일링.
* **효율**: 프록시가 **핫 커넥션 풀**을 유지해 백엔드 **핸드셰이크/accept 부하 감소**.
* **복원력**: L7에서 **리트라이/회로차단/서킷브레이커**로 장애 국소화.
* **보안**: 전면에서 **mTLS/WAF/레이트 리밋**을 적용해 애플리케이션을 보호.
* **업그레이드 유연성**: 앱을 건드리지 않고 **TLS 정책·암호군·프로토콜(h2/h3)** 을 갱신.

***

### 7) 흔한 병목과 대처

| 병목/리스크           | 증상        | 대응                                  |
| ---------------- | --------- | ----------------------------------- |
| 헤더 지연(Slowloris) | 워커 고갈     | 헤더/바디 타임아웃, `limit_conn`            |
| TLS 핸드셰이크 폭주     | CPU 급증    | 세션 재개, OCSP 스테이플링, kTLS/오프로드        |
| 백엔드 느림           | 큐 적체      | read/write 이벤트 토글, 큐 한도, 서킷브레이커, 캐시 |
| 단일 리스너 병목        | accept 지연 | `SO_REUSEPORT`, 워커 수 조절             |
| 로그 I/O 병목        | 지연 스파이크   | 비동기 로거, 배치/버퍼, 샘플링                  |
| 대역폭 제한           | 전송 지연     | 압축·캐시·HTTP/2/3 멀티플렉싱                |

***

#### 한 줄 요약

> **연결 수락을 이벤트 루프에서 비동기로 처리**하고, **TLS는 앞단에서 종료**, **L7 프록시에서 정책/관측/회복탄력성**을 담당하게 분리하면, **고동시성·고신뢰** 웹 인프라를 만들 수 있습니다.

필요하시면 위 내용을 **아키텍처 다이어그램(PNG)** 이나 **체크리스트 표(엑셀/CSV)** 로도 만들어 드릴게요. 어느 형식이 편하세요?
