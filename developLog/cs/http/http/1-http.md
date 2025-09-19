# 1부 Http의 웹의 기초

## 1장 HTTP 개관

> 전 세계의 웹브라우저, 서버, 웹 애플리케이션은 모두 HTTP를 통해 서로 대화한다. HTTP는 현대 인터넷의 공용어다.

### Http : 인터넷의 멀티미디어 배달부&#x20;

Http는 전 세계의 **웹 서버로부터** 이 대량의 정보를 빠르고, 간편하고, 정확하게 사람들의 PC에 설치된 **웹브라우저로 옮겨준다.**&#x20;



HTTP는 <mark style="color:red;">신뢰성 있는 데이터 전송 프로토콜</mark>을 사용하기 때문에, 데이터가 지구 반대편에서 오더라도 전송 중 손상되거나 꼬이지 않음을 보장한다.



### 웹 클라이언트와 서버

> 웹 콘텐츠는 웹 서버에 존재한다.&#x20;

1. 웹   서버 (Web Server)

<figure><img src="../../../.gitbook/assets/image (363).png" alt=""><figcaption></figcaption></figure>

* 웹 서버는 **Http 프로토콜로 의사소통하기 때문에** 보통 HTTP 서버라고 한다.
* 웹 브라우저 : 크롬, 구글, 인터넷 익스플로러
  * 서버에게 HTTP 객체를 요청하고 사용자의 화면에 보여준다.

### 🌐 Web Resource

> Web Server는 Web Resource를 관리하고 제공한다.&#x20;

* **정의**: 웹 서버가 관리하고 제공하는 모든 콘텐츠 원천
* **종류**
  * **정적 파일(Static File)**:\
    HTML, 이미지(JPEG, GIF), 동영상(AVI) 등 서버에 그대로 저장된 파일
  * **동적 콘텐츠(Dynamic Content)**:\
    요청에 따라 프로그램이 실행되어 생성되는 결과물 (예: 재고 조회, 검색 결과 페이지 등)

{% hint style="danger" %}
👉 **결론**: 웹 리소스는 **웹 콘텐츠를 제공하는 모든 것**을 말하며, 반드시 정적 파일일 필요는 없다.\
\
리소스는 요청에 따라 **콘텐츠를 생산하는 프로그램**이 될 수도 있다.
{% endhint %}

<figure><img src="../../../.gitbook/assets/image (364).png" alt=""><figcaption></figcaption></figure>

**어떤 종류의 콘텐츠 소스도 리소스가 될 수 있다.**

#### <mark style="background-color:red;">미디어 타입</mark>&#x20;

> 참고하면 좋을 만 한 글 :[ HTTP multipart/form-data 이해하기](https://lena-chamna.netlify.app/post/http_multipart_form-data/)

1. MIME(Multipurpose Internet Mail Extensions)

{% hint style="success" %}
클라이언트에게 전송된 문서의 종류를 알린다. 파일 변환을 위한 포맷
{% endhint %}

* HTTP는 웹에서 전송되는 객체 각각에 신중하게 MIME 타입이라는 `데이터 포맷 라벨`을 붙인다.
* **역할** : 클라이언트(웹 브라우저)에게 **전송된 문서의 종류**를 알려주는 포맷 라벨
* **구조** : `주 타입(primary type) / 부 타입(subtype)` 형태
*   **예시**

    | MIME 타입                         | 설명            |
    | ------------------------------- | ------------- |
    | `text/html`                     | HTML 문서       |
    | `text/plain`                    | 일반 텍스트(ASCII) |
    | `image/jpeg`                    | JPEG 이미지      |
    | `image/gif`                     | GIF 이미지       |
    | `video/quicktime`               | 퀵타임 동영상       |
    | `application/vnd.ms-powerpoint` | MS 파워포인트 문서   |

👉 HTTP는 전송되는 객체마다 MIME 타입을 붙여서 브라우저가 알맞게 처리하도록 한다.

파일에는 여러가지 타입이 있다.&#x20;

{% hint style="success" %}
Text부터 Image, Vidio, Audio 등 웹 상에는 수 많은 문서와 파일이 돌아다닌다. `MIME 타입`은 이메일이 등장한 이후, **이메일에 파일을 첨부하여 보내기 위해 등장**했다. 첨부된 파일을 텍스트 문자 형태로 변환해서 이메일과 함께 전송하기 위한 포맷이다.&#x20;
{% endhint %}

따라서 텍스트 문자 형태로 변환하기 위해 각각의 타입을 표준화하여 관리한다.&#x20;

현재는 IANA(Internet Assigned Numbers Authority)라는 인터넷 할당 번호 관리기관에서 다양한 파일 타입을 표준화하여 관리하고 있다. MIME 타입은 매우 많은 종류를 표준화하고 있으며, 새로운 타입의 파일이 생길 때마다 계속해서 추가되고 있다.&#x20;

MIME으로 인코딩 한 파일은 **Content-type 정보를 파일의 앞부분에 담게 되며,** Content-type은 여러가지의 타입이 있는데, 특정 Content-type은 웹서버로부터 전달받아 웹브라우저에서 열어볼 수 있다. 웹브라우저에서 서버에 html문서를 요청하면서 html문서에 있는 이미지파일의 경로를 불러올 수 오는데, 이때 이미지 경로의 파일이 웹브라우저에서 지원되는 MIME-type 이라면 열어볼 수 있는 것이다.

* 텍스트가 아닌 정보 포함 가능: 그림, 음악, 비디오 등의 파일&#x20;
* 임의의 바이너리 파일 포함 가능: 실행파일, autoCAD파일, PDF 파일 등.. &#x20;
* ASCII가 아닌 다른 문자셋을 사용하는 텍스트 메시지 포함 가능

> 주로 사용되는 .gjf , .jsp, .mov 등의 파일도 웹브라우저에서 열 수가 있는데, 브라우저에서 지원하지 않은 유형은 별도 지정해주어야 한다.

**원리**

이미지, 사진, 동영상 등의 파일은 바이너리 데이터이다.

`바이너리(Binary)` 란, 2진법, 즉 0과 1로만 수를 표현하는 진법이다. 컴퓨터는 0과 1로 이루어져 있다.

> 즉, 바이너리는 컴퓨터가 직접 해석할 수 있는 자료이며, <mark style="color:red;">사람이 이해하는 Text를 제외한 모든것을 Binary로 분류할 수 있다.</mark>

당연히 이미지, 사진, 동영상 등은 **오직 바이너리 데이터**로, **2진법**으로 이루어져 있어 컴퓨터가 읽어들일 수 있다.

{% hint style="danger" %}
문제는 메일 메시지는 **기본적으로 ASCII 코드 기반의 텍스트만을 전송**한다.
{% endhint %}

하지만 <mark style="color:red;">7비트 형식</mark>의 ASCII 코드가 지원하지 않는 각국의 언어와 이진 데이터 형식의 실행 파일, 영상, 음성 데이터를 전송하려면 기능을 확장이 반드시 필요하다.

> 이런 기능을 포함한 것이 MIME 으로 Multipurpose Internet Mail Extension의 약자이다.

MIME는 멀티미디어 데이터를 수용하기 위한 기능을 확장한 메시지이다.

메일 송신 전에 비-ASCII 데이터를 ASCII 데이터로 변환하고 메일을 수신받기 전에 ASCII 데이터를 비-ASCII 데이터로 변환하는 과정을 수행한다.

출처: [https://heannim-world.tistory.com/106](https://heannim-world.tistory.com/106) \[히앤님의 개발 세상:티스토리]

***

**🌐 MIME 타입 정리**

> MIME은 `multipart/form-data` **하나만 있는 게 아니고**, 아주 많은 **타입 분류 체계** 중 하나예요.

1\. MIME 타입의 기본 구조

```
주 타입(primary type) / 부 타입(subtype)
```

예:

* `text/html` → HTML 문서
* `image/png` → PNG 이미지
* `audio/mpeg` → MP3 음악
* `application/json` → JSON 데이터



2\. 주요 MIME 타입 그룹

| 주 타입             | 예시                                                                                   | 설명                  |
| ---------------- | ------------------------------------------------------------------------------------ | ------------------- |
| **text/**        | `text/plain`, `text/html`, `text/css`, `text/csv`                                    | 사람이 읽을 수 있는 텍스트 데이터 |
| **image/**       | `image/jpeg`, `image/png`, `image/gif`, `image/svg+xml`                              | 이미지 파일              |
| **audio/**       | `audio/mpeg`, `audio/ogg`, `audio/wav`                                               | 오디오 파일              |
| **video/**       | `video/mp4`, `video/webm`, `video/quicktime`                                         | 비디오 파일              |
| **application/** | `application/json`, `application/pdf`, `application/octet-stream`, `application/zip` | 실행, 압축, 데이터 전송 등 범용 |
| **multipart/**   | `multipart/form-data`, `multipart/mixed`, `multipart/alternative`                    | 여러 데이터 조각을 묶어서 전송   |
| **message/**     | `message/rfc822`                                                                     | 이메일 메시지 형식          |



3\. `multipart/form-data`의 위치

* **multipart/** 타입의 하위에 속하는 **특정한 포맷**
* 주로 **파일 업로드**나 **폼 데이터 전송**에 사용됨
* 하지만 MIME 전체에서 보면, **수많은 타입 중 하나**일 뿐임

{% hint style="success" %}
MIME은 **웹에서 데이터를 주고받을 때 파일 형식을 설명하는 라벨 체계**이고,

그 안에서 `multipart/form-data`는 **여러 조각 데이터를 동시에 보내는 특수한 경우**임.
{% endhint %}

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption><p><a href="https://hanamon.kr/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EA%B8%B0%EB%B3%B8-url-uri-urn-%EC%B0%A8%EC%9D%B4%EC%A0%90/">https://hanamon.kr/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EA%B8%B0%EB%B3%B8-url-uri-urn-%EC%B0%A8%EC%9D%B4%EC%A0%90/</a></p></figcaption></figure>

#### <mark style="background-color:red;">URI (Uniform Resource Identifier)</mark>

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

* **정의**: 인터넷상의 자원을 **고유하게 식별하고 위치 지정**하는 문자열
* **역할 비유**: 인터넷 세계의 **우편 주소**
* **형태**:
  * `http://www.joes-hardware.com/specials/saw-blade.gif`

#### <mark style="background-color:red;">URL (Uniform Resource Locator)</mark>

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

* **URI의 한 종류** → 가장 흔히 사용됨
* **특징**: 특정 리소스의 **정확한 위치와 접근 방법**을 알려줌
* **구조 (3부분)**
  1. **스킴(Scheme)**: 접근 프로토콜 (예: `http://`, `ftp://`)
  2. **호스트(Host)**: 서버 주소 (예: `www.example.com`)
  3. **리소스 경로(Path)**: 서버 내 파일/리소스 위치 (예: `/images/logo.gif`)
* **예시**
  * `http://www.oreilly.com/index.html` → 오라일리 홈페이지
  * `ftp://user:password@ftp.server.com/file.gif` → FTP로 접근하는 이미지 파일



#### <mark style="background-color:red;">URN (Uniform Resource Name)</mark>

* **URI의 또 다른 종류**
* **특징**:
  * 리소스의 **위치와 무관**하게 **고유하게 지칭**
  * 리소스가 옮겨져도 **동일한 이름 유지**
* **예시**:
  * `urn:ietf:rfc:2141` → RFC 2141 문서를 어디에 있든 지칭 가능
* **상태**: 아직 실험적 단계, 인프라 부족으로 URL만큼 널리 쓰이지 않음

#### <mark style="background-color:$primary;">정리</mark>

* **웹 리소스**: 웹 서버가 제공하는 모든 콘텐츠 (정적/동적)
* **MIME 타입**: 문서의 종류를 알려주는 라벨 → `타입/서브타입`
* **URI**: 자원을 식별하는 표준 이름
  * **URL**: 위치 기반 (현재 가장 널리 사용)
  * **URN**: 이름 기반, 위치 독립적 (아직 실험 단계)
* URI는 **URL과 URL을 포함하는 상위개념이다.** 따라서, ‘URL은 URI다.’ 는 참이고, ‘URI는 URL이다.’ 는 거짓이다.

{% hint style="danger" %}
**웹에서 콘텐츠는 ‘웹 리소스’이고, 전송될 때 MIME 타입으로 형식이 구분되며, 클라이언트는 URI(특히 URL)를 통해 해당 리소스에 접근한다.**
{% endhint %}

### UR, URL, URN 정리

> 💡 간단 요약 : `URI`는 자원을 식별하는 방법으로, `URL`은 자원의 위치를, `URN`은 이름을 나타냐고 생각해. URL은 scheme, host, port, path, query, fragment 등으로 이뤄져 있어. **웹 브라우저가 서버에 요청할 때는** DNS 조회, HTTP 요청 생성, TCP/IP 연결 등이 순서대로 이루어져. 그리고 서버가 응답하면 데이터가 브라우저에 렌더링돼. URI는 자원을 유일하게 식별하는 데 쓰여.

> 🔗 사진과 강의 출처 : [김영한님의 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC)

#### 1. URI(Uniform Resource Identifier)

> URI는 로케이터(**l**ocator), 이름(**n**ame) 또는 둘 다 추가로 분류될 수 있다.

![](https://velog.velcdn.com/images/prettylee620/post/267e6e41-226e-4309-9221-de23897fa6e7/image.png)

URI 안에 URL과 URN으로 구성되어 있다.



**1) URL과 URN의 예시**

![](https://velog.velcdn.com/images/prettylee620/post/30baf40c-50cf-4e4b-b261-2a9658c8192d/image.png)



**2) URI의 단어의 뜻**

1. **Uniform** : 리소스 식별하는 통일된 방식
2. **Resource** : 자원, URI로 식별할 수 있는 모든 것(제한 없음)

* 실시간 교통 정보, 파일 등

3. **Identifier** : 다른 항목과 구분하는데 필요한 정보



**3) URL, URN 단어의 뜻**

1. URL - Locator: 리소스가 있는 `위치`를 지정
2. URN - Name: 리소스에 `이름`을 부여

* 위치는 변할 수 있지만, 이름은 변하지 않는다.

3. urn:isbn:8960777331 (어떤 책의 isbn URN)
4. URN 이름만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되지 않음

* 앞으로 URI를 URL과 같은 의미로 이야기하겠음



**4) URL 전체 문법**

1. scheme://\[userinfo@]host\[:port]\[/path]\[?query]\[#fragment]

* [https://www.google.com:443/search?q=hello\&hl=ko](https://www.google.com/search?q=hello\&hl=ko)

2. 구성

* 프로토콜(https)
* 호스트명([www.google.com](http://www.google.com/))
* 포트 번호(443)
* 패스(/search)
* 쿼리 파라미터(q=hello\&hl=ko)



**5) URL scheme**

1. **scheme:**//\[userinfo@]host\[:port]\[/path]\[?query]\[#fragment]

* **https:**//www.google.com:443/search?q=hello\&hl=ko
  * q : 검색 쿼리 검색어 hello

1. 주로 프로토콜 사용

* 프로토콜: 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙
* 예) http, https, ftp 등등
* http는 80 포트, https는 443 포트를 주로 사용, 포트는 생략 가능
* `https`는 http에 보안 추가 (HTTP Secure)



**6) USL(userinfo)**

1. scheme://\*\*\[userinfo@]\*\*host\[:port]\[/path]\[?query]\[#fragment]

* URL에 사용자 정보를 포함해서 인증
* 거의 사용하지 않음



**7) USL(host)**

* scheme://\[userinfo@]**host**\[:port]\[/path]\[?query]\[#fragment]
* https://**www.google.com**:443/search?q=hello\&hl=ko
* 호스트명
* 도메인명 또는 IP 주소를 직접 사용가능



**8) USL(PORT)**

* scheme://\[userinfo@]host\*\*\[:port]\*\*\[/path]\[?query]\[#fragment]
* https://www.google.com:**443**/search?q=hello\&hl=ko
* 포트(PORT)
* 접속 포트
* 일반적으로 생략, 생략시 http는 80, https는 443



**9) URL(PATH)**

* scheme://\[userinfo@]host\[:port]**\[/path]**\[?query]\[#fragment]
* https://www.google.com:443/**search**?q=hello\&hl=ko
* 리소스 경로(path), **계층적 구조**
* 예)
  * /home/file1.jpg
  * /members
  * /members/100, /items/iphone12



**10) URL(Query)**

* scheme://\[userinfo@]host\[:port]\[/path]**\[?query]**\[#fragment]
* https://www.google.com:443/search\*\*?q=hello\&hl=ko\*\*
* key=value 형태
* **?로 시작, &로 추가 가능** ?keyA=valueA\&keyB=valueB
* query parameter, query string 등으로 불림, 웹서버에 제공하는 파라미터, 문자 형태



**11) URL(Fragment)**

* scheme://\[userinfo@]host\[:port]\[/path]\[?query]**\[#fragment]**
* https://docs.spring.io/spring-boot/docs/current/reference/html/gettingstarted.html\*\*#getting-started-introducing-spring-boot\*\*
* fragment
* html `내부 북마크` 등에 사용
* 서버에 전송하는 정보 아님



### HTTP 트랜잭션, 메시지, 연결 흐름 총정리

#### 0) 전체 그림 – 한 장 요약

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

1. **클라이언트**가 **요청(HTTP Request)** 을 보냄 → `메서드 + URI + 헤더 + (본문)`
2. **서버**가 **응답(HTTP Response)** 을 돌려줌 → `상태줄 + 헤더 + (본문)`
3. 한 “페이지”를 그리려면 **여러 리소스**(HTML, CSS, JS, 이미지 등) 때문에 **여러 번의 HTTP 트랜잭션**이 발생
4. 이 모든 메시지는 네트워크에서 **TCP 연결** 위로 전송됨 (순서 보장/무손실)



#### 1) HTTP 트랜잭션(거래) = 요청 + 응답

#### 요청(Request) 핵심

* **시작줄**: `GET /specials/saw-blade.gif HTTP/1.1`
* **헤더들**: `Host`, `Accept`, `User-Agent`, `Content-Type`, `Content-Length`, …
* **본문(body)**: 주로 `POST/PUT` 등에서 전송(폼/JSON/파일 등)

#### 응답(Response) 핵심

* **상태줄**: `HTTP/1.1 200 OK`
* **헤더들**: `Content-Type`, `Content-Length`, `Date`, `Server`, `Last-Modified`, …
* **본문(body)**: 실제 문서/이미지/JSON 등

> 둘 다 **문자 줄 단위 텍스트 구조**(헤더까지). 본문은 **텍스트/바이너리 모두 가능**.



#### 2) 메서드(Method) – 서버에 “무엇을 해달라”는 지시

| 메서드        | 의미(대표 용도)                              |
| ---------- | -------------------------------------- |
| **GET**    | 지정 리소스를 받아와라(조회)                       |
| **POST**   | 클라이언트 데이터를 서버(게이트웨이 앱)에 보내 처리해라(생성/액션) |
| **PUT**    | 보낸 데이터를 “지정 이름”의 리소스로 저장(치환)           |
| **DELETE** | 지정 리소스를 삭제                             |
| **HEAD**   | 지정 리소스의 **헤더만** 달라(본문 없이 메타만 확인)       |

> 이외에도 `PATCH, OPTIONS` 등 있지만, 여기서는 기초 5개만.



#### 3) 상태 코드(Status Code) – 요청 결과 요약 숫자

| 코드      | 뜻                   |
| ------- | ------------------- |
| **200** | OK: 성공              |
| **302** | Redirect: 다른 곳으로 가라 |
| **404** | Not Found: 리소스 없음   |

* 숫자 뒤의 **사유 구절(reason phrase)** 는 설명용 텍스트라 가변적이어도 무방.\
  예) `200 OK`, `200 Success` → **동일하게 200으로 처리**.



#### 4) “웹페이지 = 리소스 묶음” → 트랜잭션이 여러 번

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

* 브라우저는 먼저 **HTML(뼈대)** 를 GET
* 그 HTML 안의 **이미지, CSS, JS, 폰트** 등 **각각에 대해 추가 GET**
* 심지어 **다른 서버**(CDN 등)로도 요청 가능\
  → 한 페이지 로딩에 **다수의 HTTP 요청/응답**이 오간다.

{% hint style="warning" %}
이와 같이 '웹페이지'는 보통 하나의 리소스가&#x20;아닌 리소스의 모음이다.
{% endhint %}



#### 5) HTTP 메시지 형태 (사람이 읽기 쉬운 텍스트)

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

(a) 요청 예시

```
GET /test/hi-there.txt HTTP/1.0
Accept: text/*
Accept-Language: en, fr

```

* **빈 줄**이 헤더의 끝.
* 여기선 **본문 없음**.

(b) 응답 예시

```
HTTP/1.0 200 OK
Content-Type: text/plain
Content-Length: 19

Hi! I'm a message!
```

#### 6) MIME(Content-Type) – 브라우저에게 “이건 어떤 데이터야”

* 예: `text/html`, `image/jpeg`, `application/json`, `image/gif`
* **응답 헤더 `Content-Type`** 에 적힘 → 브라우저가 **어떻게 처리할지** 결정

<figure><img src="../../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

* **요청 본문**이 있을 땐 **요청 `Content-Type`** 도 중요\
  (예: `multipart/form-data` 파일 업로드, `application/json` API 호출)

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

#### 7) TCP 연결 – HTTP는 TCP 위에서 달린다

<figure><img src="../../../.gitbook/assets/image (14).png" alt=""><figcaption><p><a href="https://velog.io/@dnr6054/web-technologies-and-http">https://velog.io/@dnr6054/web-technologies-and-http</a></p></figcaption></figure>

* HTTP는 **애플리케이션 계층**, TCP는 **전송 계층**
* TCP가 제공: **무손실**, **순서 보장**, **스트림 전송**
* **URL → (DNS로) IP를 구함 → 포트(보통 80/443)로 TCP 연결**\
  그 위로 HTTP 메시지를 보냄/받음

#### URL에서 얻는 것

* **호스트**: `www.example.com` → DNS로 IP 변환
* **포트**: 명시 없으면 기본 `80(HTTP)`, `443(HTTPS)`
* **경로**: `/path/to/resource.html`

#### 8) 텔넷으로 “손맛” HTTP 해보기 (개념 이해용)

Telnet으로 서버 80포트 연결 후 직접 타이핑:

```
telnet www.joes-hardware.com 80
GET /tools.html HTTP/1.1
Host: www.joes-hardware.com

```

서버가 응답:

```
HTTP/1.1 200 OK
Date: ...
Content-Length: 433
Content-Type: text/html

<HTML> ... </HTML>
```

> 실제 자동화엔 telnet보다 `curl`, `nc(netcat)`, Postman, 브라우저 DevTools가 훨씬 편함.
>
>

#### 9) 실제 흐름을 단계로

<figure><img src="../../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

1. 브라우저가 URL 입력
2. **호스트명 → DNS로 IP 조회**
3. **포트 결정**(명시 없으면 80/443)
4. **TCP 연결 수립**
5. **HTTP 요청 전송**(메서드/URI/헤더/본문)
6. **HTTP 응답 수신**(상태/헤더/본문)
7. 필요 리소스에 대해 **추가 요청 반복**
8. 렌더링 후 사용자에게 페이지 표시

#### 10) 핵심 포인트만 콕!

* **요청/응답**은 **텍스트 기반 메시지 구조**(헤더는 줄 단위, 본문은 자유 형식)
* **메서드**는 “무엇을 할지”, **상태코드**는 “결과가 어땠는지”
* **MIME(Content-Type)** 은 “이 데이터의 형식은 무엇인지”
* 한 페이지 = **많은 리소스** = **여러 HTTP 트랜잭션**
* **TCP 위**에서 신뢰성 있게 전달, **URL**이 연결에 필요한 정보(IP/포트/경로)를 제공



### 🌐 웹 브라우저의 모험: HTTP 메시지가 TCP 위를 달린다

#### 1)  출발 – 브라우저의 첫 질문

사용자가 주소창에 `http://www.example.com/index.html`을 입력한다.\
브라우저는 묻는다.

> “이 리소스를 가져오려면 어디로 가야 하지?”

#### 2) 길을 찾는다 – DNS와 포트

브라우저는 URL을 분석한다.

* 호스트명: `www.example.com`
* 경로: `/index.html`
* 스킴: `http` → 기본 포트는 80

호스트명을 IP 주소로 바꾸기 위해 DNS를 찾는다.

DNS는 `93.184.216.34`라는 숫자 주소를 알려준다. 이제 목적지는 `93.184.216.34:80`.



#### 3) 문을 두드린다 – TCP 연결

브라우저는 TCP 연결을 연다.\
전화기의 기능은 단순하지만 확실하다.

* 오류 없이 데이터를 전송한다.
* 순서대로 도착하게 한다.
* 데이터가 중간에 찢어지지 않게 한다.

TCP의 3-way handshake가 끝나면 통화가 열린다.



#### 4) 첫 대화 – HTTP 요청

브라우저는 이렇게 요청한다.

```
GET /index.html HTTP/1.1
Host: www.example.com
Accept: text/html, image/png, */*
```

말로 풀면,

> “저는 HTML과 이미지 파일을 이해할 수 있습니다. `index.html`을 보내주세요.”



#### 5) 서버의 답 – HTTP 응답

서버는 대답한다.

```
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 1024
```

본문에는 HTML 문서가 담겨 있다.

말로 풀면,

> “네, 여기 있습니다. 형식은 HTML이고 길이는 1024바이트입니다.”



#### 6) 여정은 한 번으로 끝나지 않는다

HTML 안에는 이미지, CSS, JS가 포함되어 있다.\
브라우저는 각각에 대해 추가 요청을 보낸다.\
페이지 하나를 보여주기 위해 여러 번의 HTTP 거래가 일어난다.



#### 7) 도중의 안내자들

* **프락시**: 요청을 받아 대신 서버에 전송한다. 보안·필터링 담당.
* **캐시**: 자주 찾는 문서를 저장한다. 가까운 곳에서 빠르게 꺼내준다.
* **게이트웨이**: 다른 프로토콜 세계와 연결한다. HTTP 요청을 받아 FTP 문서를 가져오는 식이다.
* **터널**: 내용을 들여다보지 않고 그대로 전달한다. HTTPS 트래픽이 대표적인 예다.



#### 8) HTTP 버전의 진화

<figure><img src="../../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>



* HTTP/0.9: GET만, 단순 HTML 전송
* HTTP/1.0: 헤더·MIME 추가, 본격적 웹 시작
* HTTP/1.1: keep-alive, 캐시, 가상 호스팅 지원 → 현재 주류
* HTTP/2: 멀티플렉싱, 헤더 압축
* HTTP/3: TCP 대신 QUIC(UDP 기반) 사용



#### 9) 완성 – 브라우저의 보고

브라우저는 모든 리소스를 모은다. 화면에는 사용자가 기대한 웹페이지가 나타난다. 여정은 끝났다.

