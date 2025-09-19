# 1부 Http의 웹의 기초

## 1장 HTTP 개관

> 전 세계의 웹브라우저, 서버, 웹 애플리케이션은 모두 HTTP를 통해 서로 대화한다. HTTP는 현대 인터넷의 공용어다.

### 1) Http : 인터넷의 멀티미디어 배달부&#x20;

Http는 전 세계의 **웹 서버로부터** 이 대량의 정보를 빠르고, 간편하고, 정확하게 사람들의 PC에 설치된 **웹브라우저로 옮겨준다.**&#x20;



HTTP는 <mark style="color:red;">신뢰성 있는 데이터 전송 프로토콜</mark>을 사용하기 때문에, 데이터가 지구 반대편에서 오더라도 전송 중 손상되거나 꼬이지 않음을 보장한다.



### 2) 웹 클라이언트와 서버

> 웹 콘텐츠는 웹 서버에 존재한다.&#x20;

1. 웹   서버 (Web Server)

<figure><img src="../../../.gitbook/assets/image (363).png" alt=""><figcaption></figcaption></figure>

* 웹 서버는 **Http 프로토콜로 의사소통하기 때문에** 보통 HTTP 서버라고 한다.
* 웹 브라우저 : 크롬, 구글, 인터넷 익스플로러
  * 서버에게 HTTP 객체를 요청하고 사용자의 화면에 보여준다.

### 3) 🌐 Web Resource

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

#### <mark style="background-color:red;">미디어 타입</mark>

1. MIME(Multipurpose Internet Mail Extensions)

{% hint style="success" %}
클라이언트에게 전송된 문서의 종류를 알린다. 파일 변환을 위한 포맷
{% endhint %}

* HTTP는 웹에서 전송되는 객체 각각에 신중하게 MIME 타입이라는 `데이터 포맷 라벨`을 붙인다.
* **역할**:\
  클라이언트(웹 브라우저)에게 **전송된 문서의 종류**를 알려주는 포맷 라벨
* **구조**:\
  `주 타입(primary type) / 부 타입(subtype)` 형태
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

#### <mark style="background-color:red;">URI (Uniform Resource Identifier)</mark>

* **정의**: 인터넷상의 자원을 **고유하게 식별하고 위치 지정**하는 문자열
* **역할 비유**: 인터넷 세계의 **우편 주소**
* **형태**:
  * `http://www.joes-hardware.com/specials/saw-blade.gif`

#### <mark style="background-color:red;">URL (Uniform Resource Locator)</mark>

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

{% hint style="danger" %}
**웹에서 콘텐츠는 ‘웹 리소스’이고, 전송될 때 MIME 타입으로 형식이 구분되며, 클라이언트는 URI(특히 URL)를 통해 해당 리소스에 접근한다.**
{% endhint %}

### 4) UR, URL, URN 정리

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
