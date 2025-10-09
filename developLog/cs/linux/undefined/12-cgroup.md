---
icon: clock-twelve
---

# 12장 cgroup

## 1. cgroup v1: 리눅스 자원 제어의 실체

### 1) cgroup이란?

{% hint style="danger" %}
cgroup(Control Group)은 리눅스에서 **CPU, 메모리, 디스크 I/O, 네트워크 등 자원 사용을 제어하기 위한 기능**입니다.
{% endhint %}

컨테이너, 가상 머신, 멀티 사용자 환경 등에서 **자원 남용을 방지하고**, 시스템을 안정적으로 유지하기 위한 핵심 기술입니다.

* **프로세스를 그룹으로 묶고**, 그룹 단위로 자원 제어
* **cgroup v1**과 **cgroup v2**가 있음 (이번 내용은 v1 중심)

### 2) 왜 필요한가?

#### 가상화, 클라우드, 공유 환경에서의 문제

<figure><img src="../../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

* 하나의 머신에 여러 사용자가 접근 → 자원 과도 사용
* 특정 사용자가 **메모리 12GB**를 써버리면, 나머지 사용자에게 **단 4GB**만 남음\
  → **메모리 고갈, 서비스 품질 저하**

> 그림 예시: 사용자 A가 먼저 가상 머신을 띄우고 12GB를 사용해버리면,\
> 사용자 B는 4GB밖에 사용할 수 없게 됨.

또한, **백업 등 비업무 처리**가 I/O 자원을 과점해버리면,\
**업무 처리 성능이 급격히 저하**될 수 있음.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

이런 문제는 cgroup을 사용하면 해결할 수 있다.

### 3) 제어 가능한 자원과 컨트롤러

| 컨트롤러명              | 제어 자원       | 설명                     |
| ------------------ | ----------- | ---------------------- |
| `cpu`              | CPU 사용 시간   | 일정 시간 동안 사용 시간 제한      |
| `memory`           | 메모리 사용량     | OOM Killer 영향 범위 제한 가능 |
| `blkio`            | 디스크 I/O 대역폭 | 읽기/쓰기 속도 제한            |
| `net_cls/net_prio` | 네트워크 대역폭    | 패킷 우선순위, 네트워크 제한 등     |

### 4) cgroup 파일 시스템 구조

* cgroup은 **메모리 기반 특수 파일 시스템**을 통해 관리
* 우분투 기준 위치: `/sys/fs/cgroup/`
* 예: `/sys/fs/cgroup/cpu/`, `/sys/fs/cgroup/memory/`

```bash
$ ls /sys/fs/cgroup/
blkio  cpu  memory  net_cls  ...
```

## ⚙️ 사용 예: CPU 사용 시간 제어

### 1) test 그룹 생성 및 설정

```bash
# mkdir /sys/fs/cgroup/cpu/test
```

→ 디렉토리를 생성하면, 커널이 자동으로 관련 파일을 생성함

```bash
# ls /sys/fs/cgroup/cpu/test/
cpu.cfs_period_us  cpu.cfs_quota_us  tasks
```

* `cpu.cfs_period_us`: 제어 주기(기본 100ms)
* `cpu.cfs_quota_us`: 사용할 수 있는 시간(μs 단위)

#### 기본값 확인

```bash
# cat cpu.cfs_period_us
100000   # 100ms

# cat cpu.cfs_quota_us
-1        # 무제한 사용 가능
```

### 2) 프로세스를 그룹에 소속시키고 제한하기

```bash
# ./inf-loop.py &        # CPU를 100% 쓰는 무한루프 프로세스
# echo <PID> > tasks     # 해당 PID를 그룹에 등록
```

→ `top` 명령어로 확인하면 CPU 100% 사용

#### CPU 사용률을 50%로 제한

```bash
# echo 50000 > cpu.cfs_quota_us
```

→ 100ms 동안 50ms만 실행 → CPU 50% 사용

### 3) CPU 대역폭 컨트롤러 개념 요약

* **주기마다 quota 만큼만 실행**하고 나머지는 idle로 대기
* 사용량 제어의 핵심

```
|<-- 100ms -->|
[사용(50ms)] [대기(50ms)]
```

## 2. 응용 예시

### 1) 직접 쓰는 경우는 드뭄

cgroup은 보통 **시스템 서비스나 컨테이너 런타임이 자동으로 사용**합니다:

* `systemd`: 서비스마다 자동으로 그룹 생성 (예: `user.slice`)
* `docker`, `kubernetes`: 내부적으로 cgroup으로 자원 제어
* `virt-manager`, `libvirt`: 가상 머신 자원 제한 시 사용

> 👉 사용자는 몰라도 내부에선 항상 쓰이고 있음

## 3. cgroup이 커널에 포함된 이유

### 기존 문제

* 커널에 이식하기 어려움 (코드 복잡, 성능 저하 우려)
* 서버 환경에선 관심 부족

### 전환 계기

* **클라우드(IaaS)** 등장
* **사용자 분리·자원 제어** 필요 급증
* 메이저 서버 벤더 + 클라우드 사업자들이 커널 기능 도입에 참여

→ 결국 **리눅스 커널 공식 기능으로 포함됨**

## 🆚 cgroup v1 vs v2

| 항목        | cgroup v1             | cgroup v2         |
| --------- | --------------------- | ----------------- |
| 구조        | 컨트롤러별 개별 계층           | 모든 컨트롤러 통합 계층     |
| 유연성       | 높음                    | 중간 수준             |
| 연계 제어     | 어려움                   | 가능 (일괄 제어)        |
| 입출력 제어 문제 | direct I/O만 제어됨 (한계)  | 해결됨               |
| 사용 범위     | 널리 사용됨 (2024년 기준 여전히) | 도입 중 (이전보다 증가 추세) |

> “자원을 나눠 쓰는 세상에서는 자원을 다루는 기술이 진짜 실력이다.”\
> – 클라우드 시대의 커널 이야기 중
