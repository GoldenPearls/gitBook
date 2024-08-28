# URI와 웹 브라우저의 요청 흐름

![](https://velog.velcdn.com/images/prettylee620/post/e1fcad90-9d05-433c-a9c8-040d46db17a3/image.png)

> 💡 간단 요약 : `URI`는 자원을 식별하는 방법으로, `URL`은 자원의 위치를, `URN`은 이름을 나타냐고 생각해. URL은 scheme, host, port, path, query, fragment 등으로 이뤄져 있어. **웹 브라우저가 서버에 요청할 때는** DNS 조회, HTTP 요청 생성, TCP/IP 연결 등이 순서대로 이루어져. 그리고 서버가 응답하면 데이터가 브라우저에 렌더링돼. URI는 자원을 유일하게 식별하는 데 쓰여.

> 🔗 사진과 강의 출처 : [김영한님의 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC)

## 1. URI(Uniform Resource Identifier)

> URI는 로케이터(**l**ocator), 이름(**n**ame) 또는 둘 다 추가로 분류될 수 있다.

![](https://velog.velcdn.com/images/prettylee620/post/267e6e41-226e-4309-9221-de23897fa6e7/image.png)

URI 안에 URL과 URN으로 구성되어 있다.

### 1) URL과 URN의 예시

![](https://velog.velcdn.com/images/prettylee620/post/30baf40c-50cf-4e4b-b261-2a9658c8192d/image.png)

### 2) URI의 단어의 뜻

1. **Uniform** : 리소스 식별하는 통일된 방식
2. **Resource** : 자원, URI로 식별할 수 있는 모든 것(제한 없음)

* 실시간 교통 정보, 파일 등

3. **Identifier** : 다른 항목과 구분하는데 필요한 정보

### 3) URL, URN 단어의 뜻

1. URL - Locator: 리소스가 있는 `위치`를 지정
2. URN - Name: 리소스에 `이름`을 부여

* 위치는 변할 수 있지만, 이름은 변하지 않는다.

3. urn:isbn:8960777331 (어떤 책의 isbn URN)
4. URN 이름만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되지 않음

* 앞으로 URI를 URL과 같은 의미로 이야기하겠음

### 4) URL 전체 문법

1. scheme://\[userinfo@]host\[:port]\[/path]\[?query]\[#fragment]

* [https://www.google.com:443/search?q=hello\&hl=ko](https://www.google.com/search?q=hello\&hl=ko)

2. 구성

* 프로토콜(https)
* 호스트명([www.google.com](http://www.google.com/))
* 포트 번호(443)
* 패스(/search)
* 쿼리 파라미터(q=hello\&hl=ko)

### 5) URL scheme

1. **scheme:**//\[userinfo@]host\[:port]\[/path]\[?query]\[#fragment]

* **https:**//www.google.com:443/search?q=hello\&hl=ko
  * q : 검색 쿼리 검색어 hello

1. 주로 프로토콜 사용

* 프로토콜: 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙
* 예) http, https, ftp 등등
* http는 80 포트, https는 443 포트를 주로 사용, 포트는 생략 가능
* `https`는 http에 보안 추가 (HTTP Secure)

### 6) USL(userinfo)

1. scheme://\*\*\[userinfo@]\*\*host\[:port]\[/path]\[?query]\[#fragment]

* URL에 사용자 정보를 포함해서 인증
* 거의 사용하지 않음

### 7) USL(host)

* scheme://\[userinfo@]**host**\[:port]\[/path]\[?query]\[#fragment]
* https://**www.google.com**:443/search?q=hello\&hl=ko
* 호스트명
* 도메인명 또는 IP 주소를 직접 사용가능

### 8) USL(PORT)

* scheme://\[userinfo@]host\*\*\[:port]\*\*\[/path]\[?query]\[#fragment]
* https://www.google.com:**443**/search?q=hello\&hl=ko
* 포트(PORT)
* 접속 포트
* 일반적으로 생략, 생략시 http는 80, https는 443

### 9) URL(PATH)

* scheme://\[userinfo@]host\[:port]**\[/path]**\[?query]\[#fragment]
* https://www.google.com:443/**search**?q=hello\&hl=ko
* 리소스 경로(path), **계층적 구조**
* 예)
  * /home/file1.jpg
  * /members
  * /members/100, /items/iphone12

### 10) URL(Query)

* scheme://\[userinfo@]host\[:port]\[/path]**\[?query]**\[#fragment]
* https://www.google.com:443/search\*\*?q=hello\&hl=ko\*\*
* key=value 형태
* **?로 시작, &로 추가 가능** ?keyA=valueA\&keyB=valueB
* query parameter, query string 등으로 불림, 웹서버에 제공하는 파라미터, 문자 형태

### 11) URL(Fragment)

* scheme://\[userinfo@]host\[:port]\[/path]\[?query]**\[#fragment]**
* https://docs.spring.io/spring-boot/docs/current/reference/html/gettingstarted.html\*\*#getting-started-introducing-spring-boot\*\*
* fragment
* html `내부 북마크` 등에 사용
* 서버에 전송하는 정보 아님

## 2. 웹 브라우저 요청 흐름

### HTTP 메세지 정보

![](https://velog.velcdn.com/images/prettylee620/post/e5c23f20-a6a3-4552-b6ea-f777d5835ce9/image.png)

![](https://velog.velcdn.com/images/prettylee620/post/b6f2afb0-1222-4ed8-8b0c-b9072106aa1b/image.png)

1. DNS 조회(IP 매칭)
2. HTTPS PORT 생략
3. HTTP 요청 메세지 생성

* HTTP 버전 정보
* Host 정보

![](https://velog.velcdn.com/images/prettylee620/post/52e175f3-fc80-42af-bcdd-db5c508dcbc2/image.png)

4. SOCKET 라이브러리를 통해 전달

* A : TCP/IP 연결(IP, PORT)
* B : 데이터 전달

5. TCP/IP 패킷 생성, HTTP 메세지 포함

* 웹브라우저가 만든 전송 데이터

![](https://velog.velcdn.com/images/prettylee620/post/8450d75b-df1d-477f-80f6-507738cf37c9/image.png)

6. 웹 브라우저가 구글 서버에게 `요청` 패킷 전달
7. 요청 패킷 도착
8. 구글 서버가 웹 브라우저에게 `응답` 패킷 전달

![](https://velog.velcdn.com/images/prettylee620/post/6ae7f1b5-3d17-4308-a41e-d7027d0c1f9e/image.png)

* 응답하는 데이터 타입은 HTML에 UTF-8

9. 응답 패킷 도착
10. 웹 브라우저 HTML 렌더링

![](https://velog.velcdn.com/images/prettylee620/post/3619a388-7dc3-458a-8e64-adf3ad169995/image.png)
