---
description: HTTP 메시지의 모든 것(어떻게 메시지를 만들고 이해하는지)에 대해 말해준다.
---

# 3장 HTTP 메세지

## 1. 메시지의 흐름

* **HTTP 메시지**: 클라이언트, 서버, 프락시 사이 즉, **애플리케이션  사이에서 주고받는 데이터 블록.**
  * 이 데이터 블록들은 메시지의 내용과 의미를 설명하는 **텍스트 메타 정보로 시작**하고 **그 다음에 선택적으로 데이터가 올 수 있다.**

<figure><img src="../../../../.gitbook/assets/image (7) (1).png" alt=""><figcaption><p><a href="https://velog.io/@hogu8159/3.HTTP-%EB%A9%94%EC%84%B8%EC%A7%80">https://velog.io/@hogu8159/3.HTTP-%EB%A9%94%EC%84%B8%EC%A7%80</a></p></figcaption></figure>

* 메시지는 항상 **원 서버 → 클라이언트 방향**으로 이동하며, 다음과 같은 용어로 방향성을 설명함:
  * **인바운드 (Inbound)**: 원 서버로 향하는 메시지 (예: 클라이언트 → 서버의 요청).
  * **아웃바운드 (Outbound)**: 사용자 에이전트(클라이언트)로 되돌아가는 메시지 (서버 → 클라이언트의 응답).
  * **업스트림 (Upstream)**: 메시지를 보내는 쪽.
  * **다운스트림 (Downstream)**: 메시지를 받는 쪽.

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="danger" %}
→ 요청/응답 구분 없이 **모든 메시지는 다운스트림으로 흐른다.**
{% endhint %}

&#x20;**📡 "모든 메시지는 다운스트림으로 흐른다"의 의미**

HTTP 메시지를 강물처럼 생각하면 이해하기 쉽다.

* 강물은 항상 한 방향으로 흐르듯,
* **HTTP 메시지도 “보내는 쪽 → 받는 쪽” 방향으로만 흐른다**는 것이다.

여기서 중요한 건, **요청이든 응답이든 상관없이** “수신자 입장에서는 다운스트림”이라는 사실이다.

**🔼 업스트림 / 🔽 다운스트림**

* **업스트림 (Upstream)**: 메시지를 **보내는 쪽** (발송자).
* **다운스트림 (Downstream)**: 메시지를 **받는 쪽** (수신자).

> 즉, “누가 누구한테 메시지를 보내냐”에 따라 달라지는 상대적인 개념

**예시: 프락시(Proxy) 체인**

```
클라이언트 → 프락시1 → 프락시2 → 프락시3 → 원 서버
```

1. 요청(Request) 메시지의 경우

* 클라이언트가 `/index.html` 요청을 보냄.
* 이 메시지는 계속 서버 쪽으로 흘러감
* 따라서 **프락시1은 프락시3의 업스트림** (보내는 쪽),\
  프락시3은 프락시1의 다운스트림 (받는 쪽).

2. 응답(Response) 메시지의 경우

* 원 서버가 `200 OK` 응답을 보냄.
* 이 메시지는 반대로 클라이언트 쪽으로 흘러가죠.
* 이때는 **프락시3이 응답을 받아서 프락시1 쪽으로 내려보내므로**,\
  응답에서는 **프락시3이 프락시1의 업스트림**이 되고,\
  프락시1은 다운스트림이 됨.



**핵심 정리**

* **모든 HTTP 메시지는 다운스트림으로 흐른다.**
  * 요청: 클라이언트 → 서버 방향으로 다운스트림.
  * 응답: 서버 → 클라이언트 방향으로 다운스트림.
* 업스트림/다운스트림은 절대적인 방향이 아니라 **발송자와 수신자의 관계**에 따라 정해진다.

> 👉 즉, "업스트림/다운스트림"은 **위에서 아래로 흐르는 데이터의 물줄기 같은 개념**이다.\
> 서버든 클라이언트든, 그 순간 메시지를 받는 쪽은 항상 **다운스트림**이 되는 것



***

## 2. HTTP 메시지 구조 (3가지 구성 요소)

HTTP 메시지는 크게 **시작줄(Start Line) + 헤더(Header) + 본문(Body)** 로 구성된다.

{% hint style="success" %}
**시작줄**은 <mark style="color:red;">**이것이 어떤 메시지인지 서술**</mark>하며 **헤더 블록**은 <mark style="color:red;">**속성**</mark>을 **본문**은 <mark style="color:red;">**데이터**</mark>를 담고\
있다. 본문은 아예 없을 수도 있다
{% endhint %}

### 1) 시작줄 (Start Line)

* **요청 메시지**: `메서드(Method) + 요청 URL + HTTP 버전`
  * 예) `GET /index.html HTTP/1.1`
* **응답 메시지**: `HTTP 버전 + 상태 코드(Status Code) + 사유 구절(Reason-Phrase)`
  * 예) `HTTP/1.1 200 OK`

### 2) 헤더 (Headers)

> 시작과 헤더는 그냥 줄 단위로 분리된 아스키 문자열

* 이름:값 형태의 메타데이터 집합.
* 종류:
  * **일반 헤더**: 요청/응답 모두 사용 (예: `Date`)
  * **요청 헤더**: 클라이언트가 서버에 전달하는 부가정보 (예: `Accept`, `Host`)
  * **응답 헤더**: 서버가 클라이언트에 제공하는 부가정보 (예: `Server`)
  * **엔터티 헤더**: 본문 데이터에 대한 정보 (예: `Content-Length`, `Content-Type`)
* 헤더는 반드시 **빈 줄(CRLF)** 로 끝나며, 이후 본문이 시작됨.

### 3) 엔터티 본문 (Body)

* 실제 데이터(payload)를 담음.
* HTML, JSON, 이미지, 비디오 등 다양한 형태 가능.
* 모든 메시지가 본문을 가지는 것은 아님 (예: `GET` 요청은 보통 없음).

***

## 3. HTTP 메시지 문법 정리

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p><a href="https://velog.io/@twomanyzero/01.-%EC%9B%B9-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%EC%9D%98-%EC%9D%B4%ED%95%B43">https://velog.io/@twomanyzero/01.-%EC%9B%B9-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%EC%9D%98-%EC%9D%B4%ED%95%B43</a></p></figcaption></figure>

### 1) HTTP 메시지의 종류

* **요청 메시지(Request)**: 클라이언트가 서버에게 어떤 동작을 요구.
* **응답 메시지(Response)**: 서버가 요청 결과를 클라이언트에게 반환.

### 2) 메시지 기본 구조

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> 이 그림은 **HTTP 트랜잭션(요청과 응답)**&#xC774; 실제로 어떻게 이루어지는지를 보여주는 예시

1️⃣ 왼쪽: 클라이언트(웹 브라우저)

* 사용자가 웹 브라우저에서 `www.joes-hardware.com` 서버에 있는 `/specials/saw-blade.gif` 파일을 요청했다고 가정.
*   브라우저가 서버로 보낸 **HTTP 요청 메시지**는 다음과 같음:

    ```
    GET /specials/saw-blade.gif HTTP/1.0
    Host: www.joes-hardware.com
    ```

    * **GET** → 서버에 리소스를 달라는 요청 메서드.
    * **/specials/saw-blade.gif** → 요청하는 파일의 경로.
    * **HTTP/1.0** → 사용하는 프로토콜 버전.
    * **Host** 헤더 → 어느 서버(호스트)인지 지정.



2️⃣ 가운데: 인터넷(네트워크 전송)

* 요청은 인터넷을 통해 원 서버(`www.joes-hardware.com`)로 전달됨.
* 서버가 요청을 처리한 뒤 응답 메시지를 다시 인터넷을 거쳐 클라이언트로 돌려보냄.



3️⃣ 오른쪽: 서버

* 서버는 요청받은 파일 `saw-blade.gif`를 찾음.
*   찾은 후 클라이언트에게 **HTTP 응답 메시지**를 보냄:

    ```
    HTTP/1.0 200 OK
    Content-Type: image/gif
    Content-Length: 8572
    ```

    * **HTTP/1.0** → 응답도 같은 프로토콜 버전 사용.
    * **200 OK** → 요청이 성공적으로 처리되었음을 나타내는 상태 코드.
    * **Content-Type: image/gif** → 응답 본문이 GIF 이미지라는 정보.
    * **Content-Length: 8572** → 응답 본문의 크기(바이트 단위).
    * 그리고 실제 데이터(바이너리 GIF 파일)가 본문에 포함됨.

4️⃣ 결과

* 클라이언트(브라우저)는 응답 본문을 받아서 화면에 `saw-blade.gif` 이미지를 표시할 수 있게 됨.
* 따라서 그림은 **요청(Request) → 서버 처리 → 응답(Response)** 의 전형적인 HTTP 메시지 흐름을 나타냄.



✅ **정리**

* **왼쪽(요청)**: 클라이언트가 서버에 “이 파일 주세요!”라고 요청.
* **오른쪽(응답)**: 서버가 “여기 있습니다. GIF 이미지고 크기는 8572바이트예요” 하고 응답.

> 이 한 사이클이 바로 **HTTP 트랜잭션**

**🍕 HTTP 트랜잭션 = 피자 주문 과정**

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

1. **클라이언트 → 주문 넣기 (HTTP 요청)**

* **클라이언트(웹 브라우저)** = 배고픈 손님
*   손님이 피자집(서버)에 전화해서 말해요:

    ```
    GET /specials/pepperoni-pizza HTTP/1.0
    Host: www.pizza-shop.com
    ```

    👉 "저 [www.pizza-shop.com에서](http://www.pizza-shop.xn--com-k94n91q/) 페퍼로니 피자 하나 주문이요!"

여기서:

* `GET` = "주문합니다!"
* `/specials/pepperoni-pizza` = "페퍼로니 피자 메뉴"
* `Host` = "이 주문은 피자샵 본점에 하는 거예요"
* `HTTP/1.0` = "제가 주문할 때 쓰는 프로토콜 버전(말투)"



2. &#x20;**인터넷 → 주문 전달**

* 전화망(= 인터넷)을 통해 피자집에 주문이 전달돼요.
* 이게 바로 **요청 메시지(Request Message)**.



3. **서버(피자집) → 주문 확인 & 피자 굽기**

* 피자집(서버)이 주문서를 확인하고 피자를 굽기 시작합니다.
* 다 구운 뒤 배달 상자에 넣고, 배달원에게 넘기죠.

이때 배달 상자 안에는 \*\*응답 메시지(Response Message)\*\*가 들어있어요:

```
HTTP/1.0 200 OK
Content-Type: pizza/pepperoni
Content-Length: 8572
```

👉 해석하면:

* `200 OK` = "주문 잘 받았고, 피자 다 구워서 문제 없이 준비됐습니다!"
* `Content-Type: pizza/pepperoni` = "안에 들어 있는 건 페퍼로니 피자예요"
* `Content-Length: 8572` = "피자의 크기는 이 정도예요 (8,572바이트)"



4. &#x20;클라이언트(손님) → 응답 받기

* 손님(브라우저)은 배달 온 피자를 받아서 박스를 열고 맛있게 먹습니다.
* 웹 브라우저는 응답 본문에 들어 있던 **GIF 이미지(피자)**&#xB97C; 꺼내 화면에 띄우는 거죠.



핵심

* **요청(Request) = 손님이 피자 주문 넣기**
* **응답(Response) = 피자집이 피자를 배달하기**
* **HTTP 트랜잭션 = 주문 \~ 배달까지의 한 사이클**



#### 요청 메시지

```
<메서드> <요청 URL> <버전>
<헤더>
<엔터티 본문>
```

#### 응답 메시지

```
<버전> <상태 코드> <사유 구절>
<헤더>
<엔터티 본문>
```

***

### 3)  주요 구성 요소

#### (1) 메서드 (Method)

{% hint style="success" %}
클라이언트 측에서 **서버가 리소스에 대해 수행해주길 바라는 동작**이다.
{% endhint %}

* 서버에게 어떤 동작을 할지 지정.
*   대표적인 메서드:

    | 메서드     | 설명              | 본문 |
    | ------- | --------------- | -- |
    | GET     | 서버에서 문서 가져오기    | 없음 |
    | HEAD    | 문서의 헤더만 가져오기    | 없음 |
    | POST    | 서버가 처리할 데이터 전송  | 있음 |
    | PUT     | 서버에 데이터 저장      | 있음 |
    | DELETE  | 문서 삭제           | 없음 |
    | OPTIONS | 서버가 지원하는 메서드 확인 | 없음 |
    | TRACE   | 메시지 경로 추적       | 없음 |

※ 모든 서버가 모든 메서드를 구현하지는 않음. 필요에 따라 확장 메서드 존재.

***

#### (2) 요청 URL

* 요청 대상 리소스를 지칭하는 URL 또는 경로.
* 절대 경로 형태만 맞다면 서버는 자신의 호스트/포트로 간주.

***

#### (3) 버전 (Version)

* `HTTP/<메이저>.<마이너>` 형식.
* 예: `HTTP/1.0`, `HTTP/1.1`, `HTTP/2.0`.
* 숫자는 분리해서 비교해야 함 → `HTTP/2.22` > `HTTP/2.3`.

***

#### (4) 상태 코드 (Status Code)

* 응답의 결과를 나타내는 3자리 숫자.
*   범위별 의미:

    | 범위      | 의미       | 예시                        |
    | ------- | -------- | ------------------------- |
    | 100–199 | 정보       | 100 Continue              |
    | 200–299 | 성공       | 200 OK                    |
    | 300–399 | 리다이렉션    | 301 Moved Permanently     |
    | 400–499 | 클라이언트 오류 | 404 Not Found             |
    | 500–599 | 서버 오류    | 500 Internal Server Error |

***

#### (5) 사유 구절 (Reason Phrase)

* 상태 코드에 대한 사람이 읽기 쉬운 설명.
* 예: `HTTP/1.0 200 OK` → 상태코드=200, 사유구절=OK.
* 기계적 의미는 숫자가 담당, 문구는 참고용.

***

#### (6) 헤더 (Header)

* 이름:값 형식의 메타데이터.
*   종류:

    * **일반 헤더**: 요청/응답 모두 사용 가능.
    * **요청 헤더**: 요청 관련 부가정보 (예: `Accept`, `Host`).
      * Accept 관련 헤더, 조건부(Condition) 요청 해더, 요청 보안 헤더, 프록시 헤더가 대표적
        * Host
          * 요청 대상이 되는 서버의 호스트명과 포트 번호를 명시
        * Accept
          * 서버에거 클라이언트가 받을 수 있는 Content-Type을 명시
        * If-None-Match
          * 리소스의 ETag가 동일하지 않다면 신규 리소스를 전달 해달라는 요청, 동일하다면 304
        * Authorization
          * 클라이언트가 서버에 제공하는 인증 정보
        * Max-Forwards
          * 클라이언트로부터 시작하여 서버까지 넘어갈 수 있는 최대 홉(Hop) 수

    ```
    // 요청 헤더
    Host: sabatada.tistory.com
    Connection: keep-alive
    Cache-Control: max-age=0
    ...하략
    ```

    * **응답 헤더**: 응답 관련 부가정보 (예: `Server`)
    * ```
      // 응답 헤더
      Content-Type: text/html; charset=utf-8
      Content-Length: 14058
      Set-Cookie: TSSESSION_KEEP=1; ...
      Vary: Accept-Encoding
      ...하략
      ```
      * 협상(Negotiation) 헤더, 응답 보안 헤더, 캐시 관련 헤더가 대표적
        * Server
          * 서버의 어플리케이션의 버전과 이름
        * Vary
          * 서버가 확인해봐야할 클라이언트에 주는 메시지에 영향을 줄 수 있는 헤더의 목록
    * **엔터티 헤더**: 본문 데이터 설명 (`Content-Length`, `Content-Type`).
    * **확장 헤더**: 표준 외의 추가 헤더.
*   예시:

    ```
    Date: Tue, 3 Oct 1997 02:16:03 GMT
    Content-Length: 15040
    Content-Type: image/gif
    Accept: image/gif, image/jpeg, text/html
    ```

***

#### (7) 엔터티 본문 (Entity Body)

<figure><img src="../../../../.gitbook/assets/image (378).png" alt=""><figcaption></figcaption></figure>

* 실제 전송되는 데이터(payload).
* HTML, JSON, 이미지, 비디오 등 다양.
* 모든 메시지가 본문을 갖는 것은 아님.
  * 때때로 메시지는 그냥 빈줄  (CRLF)으로 끝나게 된다

> 규칙을> &#x20;잘 지키지 않는 구현체와의 호환을 위해, 클라이언트와 서버는 **마지막 CRLF 없이**> \
> **끝나는 메시지도 받아들일 수 있어야 한다.**

***

### 4) HTTP/0.9 (초기 버전)

* 요청: `GET /file`
* 응답: 엔터티 본문만 반환.
* 버전 정보, 상태 코드, 헤더 없음.
* 너무 단순해 현대 웹 환경에 맞지 않음.

### 5) 핵심 요약

1. HTTP 메시지는 **요청**과 **응답**으로 나뉨.
2. 구조는 `시작줄 + 헤더 + 본문`.
3. 메서드는 `GET`, `POST`, `PUT` 등으로 동작 정의.
4. 상태 코드는 3자리 숫자로 결과 의미 전달.
5. 헤더는 메타데이터, 본문은 실제 데이터.
6. 초기 HTTP/0.9는 단순했으나 현재는 더 복잡한 구조로 발전.



## 4.  HTTP 메시지의 시작줄과 메서드 정리

<figure><img src="../../../../.gitbook/assets/image (379).png" alt=""><figcaption></figcaption></figure>

### 1) 시작줄(Start Line)

HTTP 메시지는 항상 **시작줄**로 시작하며, 요청과 응답에 따라 달라짐.

* **요청줄(Request Line)**
  * 클라이언트가 서버에게 "무엇을 해야 하는지" 알려줌
  * 구성: **메서드 + 요청 URL + HTTP 버전**
  *   예시:

      ```
      GET /test/hi-there.txt HTTP/1.1
      ```
* **응답줄(Status Line)**
  * 서버가 클라이언트에게 "무슨 일이 일어났는지" 알려줌
  * 구성: **HTTP 버전 + 상태 코드 + 사유 구절**
  *   예시:

      ```
      HTTP/1.0 200 OK
      ```

{% hint style="danger" %}
**공백으로 구분**된다는 점이 중요.
{% endhint %}

HTTP/1.0 이전 시절에는 응답에 응답줄이 들어있을 필요가 없었다.

### 2) 메서드(Method)

> **요청의 시작줄**은 메서드로 시작

서버에게 요청하는 **동작**을 정의.\
(모든 서버가 모든 메서드를 구현하지는 않음, 필요시 확장 가능 ⇒ `확장  메서드`)

{% hint style="warning" %}
HTTP 명세는 공**통 요청 메서드의 집합**을 정의
{% endhint %}

| 메서드         | 설명                                  | 메시지 본문 유무 |
| ----------- | ----------------------------------- | --------- |
| **GET**     | 서버에서 문서를 가져옴                        | 없음        |
| **HEAD**    | 문서의 헤더만 가져옴                         | 없음        |
| **POST**    | 서버가 처리할 데이터를 전송                     | 있음        |
| **PUT**     | 요청 본문을 리소스에 저장 (새로 생성 or 교체)        | 있음        |
| **DELETE**  | 리소스 삭제 요청                           | 없음        |
| **TRACE**   | 클라이언트 → 서버까지 요청이 어떻게 전달됐는지 확인 (루프백) | 없음        |
| **OPTIONS** | 서버가 지원하는 메서드 확인                     | 없음        |

👉 HTTP/1.1 호환성을 위해 최소 **GET과 HEAD**는 구현해야 함.

***

### 3) 상태 코드(Status Code)

> 상태 코드는 **응답의 시작줄**에 위치

응답줄에 포함되며, 요청 처리 결과를 숫자로 알려줌.

{% hint style="info" %}
HTTP/1.0 200 OK' 라는 줄에서 상태 코드는 200
{% endhint %}

* **100–199**: 정보 (예: 100 Continue)
* **200–299**: 성공 (예: 200 OK)
* **300–399**: 리다이렉션 (예: 302 Found)
* **400–499**: 클라이언트 오류 (예: 404 Not Found)
* **500–599**: 서버 오류 (예: 500 Internal Server Error)

➡️ 숫자는 기계가 처리, 사유 구절은 사람이 이해하기 쉽게 돕는 용도.

### 5) 사유구절

> 응답 시작줄의 마지막 구성 요소

```
HTTP/1.0 200 OK
```

에서 OK 부분이 사유구절로 상태 코드와 일대일로 대응되며, 어떤 엄격한 규칙도 제공되지 않는다.

{% hint style="info" %}
그저, 사람이 이해하기 쉽게 만들었다.
{% endhint %}

### 6) 버전(Version)

> 응답 메세지. 요청 양쪽 모두에 기술되며, 자신이 따르는 프로토콜 버전을 상대방에게 말해주기 위한 수단이다.

* 형식: `HTTP/<메이저>.<마이너>`
* 예: `HTTP/1.0`, `HTTP/1.1`, `HTTP/2.0`
* **주의**: 분수가 아니라 각 숫자를 따로 비교해야 함.\
  → `HTTP/2.22` > `HTTP/2.3` (22 > 3)

### 7) 헤더(Header)와 본문(Entity Body)

{% hint style="success" %}
6\) 까지는 요청과 응답의 첫 번째 줄(메서드, 상태 코드, 사유 구절, 버전 번호)에&#x20;초점을 맞췄다.
{% endhint %}

* 시작줄 뒤에는 **0개 이상의 헤더** → 항상 CRLF로 끝남.
* 헤더 종류:
  * 일반 헤더 (요청/응답 공통)
  * 요청 헤더 (Accept, Host 등)
  * 응답 헤더 (Server 등)
  * 엔터티 헤더 (Content-Length, Content-Type)
  * 확장 헤더 (사용자 정의)
* 엔터티 본문(Entity Body): 실제 데이터 (HTML, JSON, 이미지 등).\
  모든 요청/응답이 본문을 가지는 것은 아님.



### 8) 특별 케이스: HTTP/0.9

<figure><img src="../../../../.gitbook/assets/image (380).png" alt=""><figcaption></figcaption></figure>

* 초기 단순 버전
* 요청: **메서드 + URL** 만
* 응답: 본문만 (상태 코드, 헤더 없음)
* 지나치게 단순 → 다양한 기능 지원 불가 → 현재는 거의 사용 안함

## 5. HTTP 메서드 완벽 정리 – 웹이 대화하는 8가지 방법

웹 브라우저와 서버는 마치 서로 다른 언어를 쓰는 두 사람이 공통된 "제스처(메서드)"로 대화하는 것과 같다.&#x20;

그 제스처가 바로 **HTTP 메서드(Method)** 이다.

> "어떤 행동을 서버에게 요청할까?"에 따라 선택하는 것

### 1️) 안전한 메서드 (Safe Method)

* **GET / HEAD**
* 특징: 서버에 "작용"을 일으키지 않음 → 조회만 함
* 📌 예시:
  * GET → “책 좀 보여주세요”
  * HEAD → “책은 안 줘도 돼요, 표지랑 목차만 보여주세요”

> 👉 안전하다고 _반드시 무해한 건 아님_. 개발자가 구현을 이상하게 해놓으면 GET으로도 DB 바뀔 수 있음.

### 2️) 자주 쓰이는 핵심 메서드

<table><thead><tr><th>메서드</th><th width="147">하는 일</th><th>본문(Body)</th><th>비유</th><th data-type="image"></th></tr></thead><tbody><tr><td><strong>GET</strong></td><td>서버에서 리소스 가져오기</td><td>❌ 없음</td><td>도서관에서 책 빌리기</td><td><a href="../../../../.gitbook/assets/image (382).png">image (382).png</a></td></tr><tr><td><strong>HEAD</strong></td><td>리소스의 헤더만 요청<br>- 엔터티 본문 X</td><td>❌ 없음</td><td>책 제목/목차만 확인</td><td><a href="../../../../.gitbook/assets/image (383).png">image (383).png</a></td></tr><tr><td><strong>POST</strong></td><td>서버에 데이터 제출</td><td>⭕ 있음</td><td>신청서 작성해서 접수하기</td><td><a href="../../../../.gitbook/assets/image (385).png">image (385).png</a></td></tr><tr><td><strong>PUT</strong></td><td>특정 리소스 생성/교체<br>- GET은 서버로부터 문서를 읽는다면,  PUT은서버에 문서를 씀</td><td>⭕ 있음</td><td>책 전체를 새 원고로 교체</td><td><a href="../../../../.gitbook/assets/image (387).png">image (387).png</a></td></tr><tr><td><strong>DELETE</strong></td><td>리소스 삭제</td><td>❌ 없음</td><td>책 반납하고 서가에서 제거</td><td><a href="../../../../.gitbook/assets/image (389).png">image (389).png</a></td></tr><tr><td><strong>TRACE</strong></td><td>요청이 서버에     도달 시 어떻게 변했는지 확인</td><td>❌ 없음</td><td>우편물이 배달되는 경로 추적</td><td><a href="../../../../.gitbook/assets/image (384).png">image (384).png</a></td></tr><tr><td><strong>OPTIONS</strong></td><td>어떤 메서드 지원하는지 확인</td><td>❌ 없음</td><td>“이 책관에서는 어떤 서비스 되나요?” 물어보기</td><td><a href="../../../../.gitbook/assets/image (388).png">image (388).png</a></td></tr></tbody></table>

#### TRACE 메서드 – 요청의 거울을 보여준다

<figure><img src="../../../../.gitbook/assets/image (386).png" alt=""><figcaption></figcaption></figure>

1. &#x20;TRACE 메서드란?

* TRACE는 **진단용 메서드**
* 클라이언트가 서버에 **“내가 보낸 요청이 최종적으로 어떻게 보였는지 돌려줘”** 하고 묻는 방식이다.
* 서버는 받은 요청을 그대로 응답의 본문에 넣어 돌려줍니다. → **“루프백(loopback)”** 진단이라고 부름.



2. &#x20;왜 필요할까?

인터넷을 건너는 HTTP 요청은 방화벽, 프락시, 게이트웨이 등 여러 애플리케이션을 통과한다.\
이 과정에서 **요청이 변조되거나 수정될 수 있다.**

TRACE는 이런 상황을 확인하는 도구:

* 요청이 **의도한 경로를 제대로 거쳤는지** 확인
* 중간 서버(프락시 등)가 요청을 **수정했는지 여부** 진단
* 요청/응답 연쇄에서 **망가진 부분** 찾기



3. &#x20;특징

* **본문 없음** → TRACE 요청에는 엔터티 본문을 담을 수 없음.
* **응답 본문 = 요청 원문** → 서버가 받은 그대로 돌려줌.
* 주로 **디버깅·테스트용**으로만 사용.
* 실제 서비스 환경에서는 보안상 위험(민감한 정보 노출 가능) 때문에 **차단하는 경우 많음**.



4. &#x20;예시 흐름

**요청 메시지**

```http
TRACE /product-list.txt HTTP/1.1
Host: www.joes-hardware.com
Accept: *
```

**응답 메시지**

```http
HTTP/1.1 200 OK
Content-Type: text/plain
Content-Length: 96

TRACE /product-list.txt HTTP/1.1
Host: www.joes-hardware.com
Accept: *
Via: 1.1 proxy3.company.com
```

> 📌 여기서 중요한 점: 응답 본문에 클라이언트가 보낸 요청이 그대로 담겨 있다.\
> 즉, “내 요청이 중간에서 **proxy3**라는 프락시를 거쳤구나”를 알 수 있죠.



5. 요약

| 특징    | 설명                       |
| ----- | ------------------------ |
| 목적    | 진단/디버깅 (요청이 어떻게 보이는지 확인) |
| 요청 본문 | 없음                       |
| 응답 본문 | 서버가 받은 요청 그대로 반환         |
| 장점    | 요청 변조 여부, 프락시 경로 추적 가능   |
| 주의    | 보안상 위험 → 실서비스에선 차단하기도 함  |

{% hint style="success" %}
👉 TRACE는 실제 개발에서 자주 쓰이진 않지만,\
&#xNAN;**“내가 보낸 메시지가 서버에 도착했을 때 어떤 모습일까?”**\
궁금할 때 거울처럼 보여주는 유용한 도구예요.
{% endhint %}



### 3️) 확장 메서드 (WebDAV 등)

HTTP는 확장 가능한 구조라 필요하면 새 제스처를 추가할 수 있어요.\
대표 예시(WebDAV 확장):

| 확장 메서드    | 설명                    |
| --------- | --------------------- |
| **LOCK**  | 문서를 잠가서 동시에 수정 못하게 막기 |
| **MKCOL** | 새 컬렉션(폴더 같은 개념) 만들기   |
| **COPY**  | 리소스 복사하기              |
| **MOVE**  | 리소스 옮기기               |

> 📌 규칙: “**엄격하게 보내고, 관대하게 받아들여라**” → 내가 모르는 확장 메서드가 와도 무작정 거부하지 말고, 가능한 한 전달하기.

### 4️) 핵심 정리

* 모든 서버가 모든 메서드를 구현하지는 않음. (특히 DELETE, PUT은 제한적)
* **HTTP/1.1 호환을 위해서는 GET과 HEAD만 있어도 충분.**
* 메서드는 **“행동을 약속하는 언어”** → GET은 가져오기, POST는 제출하기처럼 직관적.

{% hint style="danger" %}
요약하자면:\
HTTP 메서드는 **서버에게 부탁하는 방식**이고, 안전한 조회부터 리소스 생성·삭제, 진단용 메서드까지 다양하다다.\
웹 개발자는 상황에 맞는 메서드를 쓰고, 브라우저는 이를 이용해 올바르게 동작한다.
{% endhint %}

## 6. 상태 코드

<figure><img src="../../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

### 1) 상태 코드 다섯 분류의 “의도”와 “실무상 쓰임새”

#### 1. 1xx 정보(Informational): “계속해도 됩니다”

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **도입 배경**: 큰 본문(파일 업로드 등)을 **괜히** 보냈다가 서버가 거부하면 네트워크 낭비.
* **대표 코드**
  * **100 Continue**
    * 목적: 클라이언트가 `Expect: 100-continue`를 보냈을 때, **본문을 보내도 된다**는 신호.
    * 실패 시: 서버가 바로 4xx/5xx로 응답하면, 클라이언트는 큰 본문 전송을 **건너뛰어** 낭비를 줄인다.
  * **101 Switching Protocols**
    * `Upgrade` 헤더와 함께, 예: HTTP/1.1 → WebSocket 등 **프로토콜 업그레이드** 승인.

> **실무 주의**
>
> * 100은 모든 서버/프락시에서 완벽히 구현되지 않은 사례가 있어 **타임아웃 후 본문 전송** 로직을 넣는 게 안전.
> * 1.0/레거시 클라이언트와 혼용 시 프락시가 `Expect`/`100`을 **중계하지 말아야** 하는 경우가 있음.

#### 2. 2xx 성공(Success): “요청 OK”

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **200 OK**: 가장 일반적. 본문에 요청한 표현이 담김.
* **201 Created**: `PUT`/`POST` 등으로 **리소스가 새로 생성**되었을 때. 응답의 `Location`에 새 리소스 URL 제공.
* **202 Accepted**: **비동기 처리** 수락. 결과는 아직 모름(큐 처리/배치 작업). 상태 폴링 엔드포인트를 본문/링크로 함께 제공하기도.
* **203 Non-Authoritative Information**: 원서버가 아닌 **중개본**에서 온 메타정보. 메타 검증이 불완전할 수 있음을 암시.
* **204 No Content**: 성공했지만 **본문 없음**(예: 폼 검증 OK 후 화면 갱신 필요 없음).
* **206 Partial Content**: `Range` 요청(바이트 범위) 응답. 대용량 미디어 **구간 재생/이어받기**에 핵심.

> **실무 팁**
>
> * 파일 업로드 후에는 **201 + Location**을 일관되게 제공 → 클라가 후속 조회/갱신 경로를 확실히 알 수 있음.
> * 스트리밍/다운로드는 **206** 지원 여부가 사용자 경험을 크게 좌우(되감기/이어받기).
>
>

#### 3. 3xx 리다이렉션(Redirection): “이동/조건부”

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **301 Moved Permanently**: **영구 이동**. 검색엔진은 링크 권한(SEO)을 **새 URL로 이전**. 캐시 강함.
* **302 Found**: 임시 이동. 전통적으로 브라우저가 **POST→GET**으로 바꿔버리는 혼선이 있었음.
* **303 See Other**: 명확히 <mark style="color:red;">“리다이렉트는 GET으로 가라”는 의도.</mark> 폼 제출 후 결과 페이지로 보내는 패턴에 적합.
* **304 Not Modified**: 조건부 요청(예: `If-Modified-Since`, `If-None-Match`)에 대해 **변경 없음** → 본문 없이 캐시 재사용.
* **307 Temporary Redirect**: **메서드/본문 유지** 보장. POST는 POST 그대로.
* **308 Permanent Redirect**: 301의 **영구성 + 메서드/본문 유지** 보장.

> **실무 팁**
>
> * **POST 후 중복 제출 방지**: 서버는 200 대신 **303**으로 결과 리소스 URL을 제시 → 새로고침해도 재전송 방지.
> * CDN/리버스프락시 앞단에서 **304**를 적극 활용(ETag/Last-Modified). 대역폭·TTFB 절감에 즉효.

#### 4. 4xx 클라이언트 오류(Client Error): “요청이 잘못됐어요”

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **400 Bad Request**: 문법 오류/파싱 실패/스키마 위반. 본문에 **에러 상세**(필드 검증 실패 등) 넣어주면 UX 향상.
* **401 Unauthorized**: 인증 필요(혹은 실패). **WWW-Authenticate** 헤더로 인증 스킴 제시(Basic/Bearer 등).
* **403 Forbidden**: 인증은 되었지만 **권한 없음**. 401과 구분.
* **404 Not Found**: 리소스 없음(혹은 의도적 은폐). 보안상 존재 유무를 감추려 404로 통일 응답하기도.
* **405 Method Not Allowed**: URL은 존재하지만 **해당 메서드는 금지**. **Allow** 헤더로 허용 목록 제공.
* **406 Not Acceptable**: `Accept-*` 협상 결과 **보내줄 표현이 없음**.
* **408 Request Timeout**: 클라이언트가 너무 느림(커넥션 유지 정책). 재시도 힌트를 본문/헤더로 제공 가능.
* **409 Conflict**: 요청이 **현 리소스 상태와 충돌**. 예: 버전 충돌(Optimistic Locking: `If-Match` 불일치).
* **410 Gone**: **영구 삭제**를 명시(SEO/클라 캐시의 확실한 폐기 유도).
* **411 Length Required**: `Content-Length` 필수인데 없음.
* **412 Precondition Failed**: `If-*` 조건 불충족(Etag/LM 기반 동시성 제어 실패).
* **413 Payload Too Large**, **414 URI Too Long**, **415 Unsupported Media Type**, **416 Range Not Satisfiable**, **417 Expectation Failed** 등: 용량/형식/범위/Expect 정책 위반.

> **실무 팁**
>
> * 리소스 업데이트에는 **조건부 헤더(Etag + If-Match)** 를 강제 → **경합 업데이트 방지**.
> * 404와 410을 의도에 맞게 구분해 쓰면 SEO/클라이언트 캐시 정리가 깔끔.

#### 5. 5xx 서버 오류(Server Error): “서버 쪽 문제”

<figure><img src="../../../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **500 Internal Server Error**: 가장 포괄적. 내부 예외/미처 처리 못한 상황.
* **501 Not Implemented**: 메서드/기능 미구현(확장 메서드에 흔함).
* **502 Bad Gateway**: 게이트웨이/프락시가 **업스트림**으로부터 잘못된 응답을 받음.
* **503 Service Unavailable**: 과부하/점검. 가능한 **Retry-After** 제공.
* **504 Gateway Timeout**: 업스트림 **타임아웃**.
* **505 HTTP Version Not Supported**: 미지원 버전.

> **실무 팁**
>
> * 503에는 **Retry-After**(초/날짜) 넣어 **백오프**를 유도.
> * 에러 응답에는 **추적 ID**(예: `X-Request-Id`)를 넣어 고객문의 시 로그 상호참조가 쉽다.

### 2) 100 Continue – 전체 시퀀스(클/서/프 흐름도 느낌)

<figure><img src="../../../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

1.  **클라이언트**:

    ```
    POST /upload HTTP/1.1
    Host: api.example.com
    Expect: 100-continue
    Content-Length: 104857600
    Content-Type: application/octet-stream
    ```

    → “100 받으면 100MB 본문 보낼게요.”
2. **서버**(정책/권한/형식 빨리 점검)
   * 가능 → `HTTP/1.1 100 Continue` → 클라는 본문 전송 시작
   * 불가 → 즉시 `417/4xx/5xx` → 클라는 **본문 전송 생략**
3. **프락시**
   * 다음 홉이 1.1인지 **모르면** `Expect`를 **보존**해서 전달
   * 1.1 **아님을 알면** `417`로 빠르게 단락시키는 게 낫다
   * 1.0 클라 대신 `Expect`를 덧붙였다면 **100을 직접 전달하지 말 것**
4. **최종 응답**: 본문 수신 후 `201/200/4xx/5xx` 중 하나를 **반드시** 보냄.

> **안정성 체크리스트**
>
> * 100 응답 **무한대기 금지**(타임아웃·재전송 전략).
> * 일부 서버는 100을 **생략**하고 바로 최종 응답만 보낼 수도 있게 처리.



## 7. 헤더 — “분류별 사고법”과 대표 헤더 세트

### 1) 일반 헤더(요청·응답 공통 메타)

* `Date`: 메시지 생성시각
* `Connection`: `keep-alive`/`close`/hop-by-hop 옵션
* `Transfer-Encoding`: `chunked` 등 전송 코딩
* `Upgrade`: 프로토콜 업그레이드 희망(WebSocket 등)
* `Via`: 거쳐온 **중개자**들 표시(디버깅/루프 방지)

> **팁**: `Connection`/`Proxy-Connection`은 **홉-바이-홉**이므로 프락시가 소비/관리(엔드투엔드 아님).



### 2) 요청 헤더

* **식별/출처**: `Host`(필수), `Referer`, `User-Agent`
* **협상(클라 선호도)**:
  * `Accept`(미디어 타입), `Accept-Language`(언어), `Accept-Encoding`(gzip 등), `Accept-Charset`, `TE`
* **조건부/범위/동시성**:
  * `If-Modified-Since` ↔ `Last-Modified`
  * `If-None-Match`/`If-Match` ↔ `ETag`
  * `Range`/`If-Range`(부분 전송/재개)
* **보안/세션**: `Authorization`(Basic/Bearer…), `Cookie`
* **프락시용**: `Max-Forwards`(TRACE hop 수 제한), `Proxy-Authorization`

> **콘텐츠 협상**: 서버는 요청의 `Accept-*`를 읽어 **가장 적합한 표현**을 선택하고, 응답에 `Vary`로 “어떤 헤더가 선택에 영향을 줬는지”를 알린다(캐시 일관성).



### 3) 응답 헤더

* **보안/인증유도**: `WWW-Authenticate`, `Proxy-Authenticate`
* **세션**: `Set-Cookie`, `Set-Cookie2(구식)`
* **협상정보**: `Accept-Ranges`(바이트 범위 지원), `Vary`(선택에 영향 준 요청 헤더 목록)



### 4) 엔터티(콘텐츠) 헤더

* **엔터티 정보**:
  * `Allow`(허용 메서드 목록—405와 함께), `Location`(새/실제 위치: 201/3xx에서 핵심)
* **콘텐츠 기술**:
  * `Content-Type`(MIME + charset), `Content-Length`, `Content-Encoding`(gzip 등), `Content-Language`, `Content-Location`, `Content-Range`, `Content-MD5(구현차)`
* **캐싱/검증**:
  * `ETag`(버전 식별자—강/약), `Last-Modified`, `Expires`
  * (캐시 제어는 보통 `Cache-Control`도 함께—max-age, no-cache, must-revalidate 등)

> **캐시 전략 요령**
>
> * 정적 파일: `ETag` + `Last-Modified` + `Cache-Control: public, max-age=…`
> * API JSON: 잦은 변경이라면 `ETag` 기반 조건부 GET으로 304 적극 활용
> * 다국어/압축: `Vary: Accept-Language, Accept-Encoding`로 캐시 키 분리



## 8. 미묘하지만 중요한 짝꿍들

* **201 + Location**: 생성 위치를 알려 재조회 가능케 함.
* **301 vs 308 / 302 vs 307**: **메서드/본문 유지 여부**가 관건(308/307은 유지).
* **304 + ETag/LM**: “변경 없음”을 통한 대역폭 절감.
* **405 + Allow**: 허용 메서드 안내로 클라가 **올바른 재시도** 가능.
* **401 + WWW-Authenticate**: 인증 스킴 명시(없으면 클라가 방법을 모름).
* **409/412 + If-Match**: 낙관적 락(버전 충돌) 처리의 표준 패턴.



## 9. 실전 시나리오 3가지

1. **파일 업로드(대용량)**

* 요청: `Expect: 100-continue` → `100` 받으면 본문 전송
* 응답: 성공 시 `201 Created + Location`
* 실패: 형식 오류는 **415**, 크기 초과는 **413**, 인증 실패는 **401**



2. **조건부 GET(캐시 재검증)**

```
GET /article/123
If-None-Match: "v7k3"
If-Modified-Since: Tue, 25 Mar 2025 10:10:10 GMT
```

* 변경 없으면 **304**(본문 없음)
* 변경 있으면 **200 + 새 ETag/Last-Modified**



3. **POST/리다이렉트 패턴**

* 폼 제출 성공 → **303 See Other**로 결과 페이지 GET 유도(중복 제출 방지)
* 임시 이동이지만 메서드 유지 필요 → **307 Temporary Redirect**



#### 체크리스트(구현/운영)

* [ ] 2xx/3xx/4xx/5xx **균형 있게** 사용(무조건 200에 오류메시지 넣지 않기)
* [ ] `Location`, `Allow`, `WWW-Authenticate`, `Retry-After` 등 **꼭 필요한 짝꿍 헤더** 채우기
* [ ] 캐시 가능 리소스에 **ETag/Last-Modified/Cache-Control** 구성
* [ ] 업로드/대용량에 **100-continue** 고려 + 클라이언트 타임아웃 전략
* [ ] 게이트웨이/프락시 앞단의 502/504/503 로깅 및 **X-Request-Id** 추적
* [ ] 국제화/압축 시 **Vary** 정확히 표기(캐시 오염 방지)

