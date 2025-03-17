# 멀티캠퍼스에서 배운 cs 지식

## 1. 서버(Server)

### **1) 서버란..**

**언어적인 관점**에서 보자면

* 테니스, 탁구, 배구 따위에서 하는 서브하는 쪽, 또는 그 사람
* 음식, 음료를 내는데 쓰는 도구
* 주된 정보의 제공이나, 작업을 수행하는 컴퓨터 시스템

### **2) 컴퓨터의 서버는 클라이언트에 서비스를 제공한다.**

웹브라우저 : 클라이언트 컴퓨터 : 서버

### **3) 서버의 처리는 클라이언트의 요청으로 시작된다.**

서버는 그 자체로 작동하는 것이 아니라, 불특정 다수의 컴퓨터에 대해 일방적으로 서비스를 제공하지 않음 클라이언트로부터 `요청(request)`을 받아서 처음으로 처리를 시작하고, 서비스를 `제공(응답, response)`한다.

1. 클라이언트는 서버에 무언가의 서비스를 요청한다.
2. 서버는 요청에 따라 맞춰 처리를 수행한다.
3. 서버는 처리 결과를 클라이언트로 반환
4. 클라이언트는 처리 결과를 받는다.

### **4) 웹서비스에 대입해보자**

**클라이언트** : 구글 크롬, 사파리와 같은 웹브라우저 **서버** : 웹사이트(의 구성 파일)이 있는 컴퓨터

5. 웹브라우저는 웹서버에 ㅇㅇ 사이트의 데이터를 주십시오라고 요청한다.
6. 웹서버는 ㅇㅇ 사이트의 파일을 찾는다.
7. 웹서버는 ㅇㅇ 사이트의 파일을 웹브라우저에 반환한다.
8. 웹브라우저는 ㅇㅇ사이트의 파일을 받아서 화면에 표시한다. ⇒ 이러한 시스템을 `클라이언트/서버 시스템`이라고 함

출처 : 서버의 기초 책

***

## 2. 웹 서버(Web Server)

### 1) 웹서비스

**클라이언트** : 구글 크롬, 사파리와 같은 웹브라우저 **서버** : 웹사이트(의 구성 파일)이 있는 컴퓨터



### **2) 웹 서버**란(WEB) = 아파치

* 하드웨어와 소프트웨어 혹은 두 개가 같이 동작하는 것을 의미
* **말 그대로 작성된 html 페이지 등을 네트워크 망에 종속되지 않고, 웹서비스를 할 수 있도록 어플리케이션**
* 브라우저에서 웹 서버에서 불려진 파일을 필요로 할 때, 브라우저는 HTTP를 통해 파일을 요청
* 요청이 올바른 `웹 서버(하드웨어)`에 도달 시, `HTTP 서버(소프트웨어)`는 요청된 문서를 HTTP를 이용해 보내줌

#### 웹 서버 소프트웨어 종류

* 서비스별로 서버 소프트웨어가 있으며, 각각 특징이 있으나 웹 서버로의 기능은 공통
* APache
* IIS
* nginx

### 3) 하드웨어 측면

* `웹 서버`는 웹 서버의 소프트웨어와 website의 컴포넌트 파일을 저장하는 컴퓨터
* 소프트웨어 기능을 제공하는 컴퓨터 프로그램을 실행하는 컴퓨
  * **컴포넌트 파일**이란?
    * HTML문서, images, CSS stylesheets, JavaScript files
* 웹 서버는 인터넷에 연결된 다른 기기들이 웹 서버의 데이터(컴포넌트 파일들)을 주고받을 수 있도록 함

### 4) 소프트웨어 측면

* 웹 서버는 기본적으로 웹 사용자가 어떻게 호스트에 파일들에 접근하는지를 관리하는 컴퓨터 프로그램
* HTTP 서버는 URL과 HTTP(우리의 브라우저가 웹 페이지를 보여주기 위해 사용하는 프로토콜)의 소프트웨어
  * **URL의 구성요소**
    * 프로토콜, 서버주소, 포트번호, 파일경로
* `HTTPS 서버, HTTP 서버`를 웹 서버라고 부르기로 함

📌 참고로 ㅇㅇ 서버의 ㅇㅇ에는 **제공하는 서비스의 이름**을 넣는다. ex. 카카오톡 서비스를 제공하는 것은 카카오톡 서버 ex2. 웹 서버를 ‘HTTP 서버’ 메일 서버를 ‘SMTP 서버’라고 부르기도 함



* 출처: https://developer.mozilla.org/ko/docs/Learn/Common\_questions/Web\_mechanics/What\_is\_a\_web\_server

***

## 3. 웹 컨테이너(Web Container)

### 1) 웹 컨테이너란?

* `서블릿 컨테이너`라고도 함
* **JSP + 서블릿을 실행시킬 수 있는 소프트웨어**
* 웹 서버의 컴포넌트 중 하나로 자바 서블릿과 상호작용
* **서블릿의 생명주기를 관리**하고, URL과 특정 서블릿을 맵핑하며 URL 요청이 올바른 접근 권한을 갖도록 보장
* 서블릿, 자바 서버 페이지(JSP) 파일, 그리고 서버-사이드 코드가 포함된 다른 타입의 파일들에 대한 `요청`을 다룬다.
* 서블릿 객체 생성, 서블릿을 로드와 언로드하며, `요청과 응답` 객체를 생성하고 관리하고, 다른 서블릿 관리 작업을 수행
* 웹 컴포넌트 자바 EE 아키텍처 제약을 구현하고, 보안, 병행성, 생명주기 관리, 트랜잭션, 배포 등 다른 서비스를 포함하는 웹 컴포넌트의 실행환경 명세

### 2) 서블릿 컨테이너 목록

* 아파치 톰캣
* 아파치 제로니모
* 오라클의 클래스피시
* 제이보스
* 출처

[https://ko.wikipedia.org/wiki/웹\_컨테이너](https://ko.wikipedia.org/wiki/%EC%9B%B9_%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)

[https://helloworld-88.tistory.com/71#:\~:text=■](https://helloworld-88.tistory.com/71) 웹 애플리케이션 서버 (WAS%2C Web Application Server)란%3F,컴퓨터나 장치에 애플리케이션을 수행해 주는 미들웨어 (소프트웨어 엔진)이다.

***

## 4. WAS(Web Application Server) = tomcat

### 1) WAS란?

* 웹 서버 + 웹 컨테이너(Servel 컨테이)
* 인터넷 상에서 HTTP를 통해 사용자 컴퓨터나 장치에 애플리케이션을 수행해 주는 미들웨어(소프트웨어 엔진)
* `동적 서버 콘텐츠를 수행하는 것`으로 일반적인 웹 서버와 구별되며, 주로 데이터베이스 서버와 같이 수행
* 웹 상에 사용하는 컴포넌트를 올려놓고 사용하게 되는 서버

#### 기능

* 프로그램 실행 환경과 데이터 베이스 접속 기능 제공
* 여러 개의 트랜잭션 관리
* 업무를 처리하는 비즈니스 로직 수행
* Web Service 플랫폼의 역활

#### WAS 종류

* tomcat
* tMax jeus
* Oracle

### 2) WEB 서버와 WAS 비교

* WEB 서버 : HTML 문서와 같은 `정적 컨텐츠`를 처리 하는 것(HTTP 프로토콜을 통해 읽힐 수 있는 문서)
* WAS 서버 : asp, php, jsp 등 개발 언어를 읽고 처리하여 `동적 컨텐츠`, 웹 응용 프로그램 서비스 처리하는 것

***

## 5. HTTP(HyperText Treansfer protocol)

### 1) HTTP란?

* 웹 서비스를 위해 이용되는 프로토콜로 보안성이 안 좋음
* 암호화되지 않는 상태로 웹 볼 때 사용되는 프로토콜
* 인터넷에서 하이퍼텍스트(hypertext) 문서를 교환하기 위하여 사용되는 통신규약
* 동작원리 : POST,GET

### 2) HTTPS(HTTP Secure)란

* 암호화된 상태로 웹을 볼 때 HTTP에 Secure을 뜻하는 s가 붙어 HTTPS라고 함

***

## 7. 프로토콜(Protocol)

### 1) 프로토콜이란?

* 통신 프로토콜 또는 통신 규약은 컴퓨터나 원거리 통신 장비 사이에서 메세지를 주고 받는 양식과 규칙의 체계, 통신 규약 및 약속
* 톰 마릴은 컴퓨터가 **메세지를 전달하고, 메세지가 제대로 도착했는지 확인하며, 도착하지 않았을 경우 메세지를 재전송하는 일련의 방법**을 기술적 은어로 프로토콜이라고 한다.
* 통신을 위해 프로토콜이 가져야 하는 일반적인 기능에는 데이터 처리 기능, 제어 기능, 관리적 기능

### 2) 프로토콜의 기본 요소

* `구문(Syntax)` : 전송하고자 하는 데이터의 형식(Format), 부호화(Coding), 신호 레벨(Singnal Level) 등을 규정
* `의미(Semantics)` : 두 기기간의 효율적이고 정확한 정보 전송을 위한 협조 사항과 오류 관리를 위한 **제어 정보를 규정**
* `시간(Timing)` : 두 기기 간의 통신 속도, 메세지의 순서 등을 규정

### 3) 프로토콜 종류

<figure><img src="../../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

***

## 8. 포트(Port)

* 네트워크 상에서 특정 PC를 나타내는 IP 주소와 그 주소에 진입할 수 있는 정해진 통로
  * IP 주소 : 네트워크에 연결된 특정 PC의 주소를 나타내는 체계
* IP 내에서 **애플리케이션 상호 구분(프로세스 구분)을 위해 사용하는 번호**
* 주로 포트를 사용하는 프로토콜은 전송 계층 프로토콜
* 이미 사용 중은 포트는 중복해서 사용할 수 없다.
* **포트 번호는 0\~ 65,535 까지 사용할 수 있다.**
* **포트 번호**
  * IP 주소가 가리키는 PC에 접속할 수 있는 통로
  * `잘 알려진 포트 번호`
    * 22 : SSH
    * 80 : HTTP
    * 443 : HTTPS

#### 웹 서비스에 이용되는 포트번호

* 80번 포트(실제 상용화된 서비스 제공 시 이용)
* 출처

[https://hanamon.kr/네트워크-기본-ip-주소와-포트-port/](https://hanamon.kr/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EA%B8%B0%EB%B3%B8-ip-%EC%A3%BC%EC%86%8C%EC%99%80-%ED%8F%AC%ED%8A%B8-port/)

***

## 9. 정적 웹 프로그래밍과 동적 웹 프로그래밍

### 1) 정적 웹 프로그래밍

* 주어진 HTML 파일을 보여주기만 함

### 2) 동적 웹 프로그래밍

* 사용자 입력에 따라 다른 페이지를 보여주거나, 필요에 따라 동적으로 페이지 생성해서 보여줌

#### CGI

* 사용자 입력을 받아들이기 위해 `<form>`과 `<input>` 태그를 도입한 가장 초창기의 동적 웹 프로그래밍 기술

#### JSP(Java server page)

* HTML 코드에 JAVA 코드를 넣어 동적 웹페이지를 생성하는 웹어플리케이션 도구
* JSP 가 실행되면 자바 서블릿(Servlet) 으로 변환되며 **웹 어플리케이션 서버에서 동작**되면서 필요한 기능을 수행하고 그렇게 생성된 데이터를 웹페이지와 함께 클라이언트로 응답
  * `자바 서블릿`이란?
    * 서블릿이란 웹페이지를 동적으로 생성하기 위해 서버측 프로그램
    * 자바 언어를 기반으로 만들지며 웹 어플리케이션 서버 ( Web Application Sever ) 위에서 컴파일 되고 동작
* 필요 소프트웨어
  * 웹브라우저(크롬 등)
  * JSP 컨테이너 기능을 포함한 웹 서버(톰캣 등)
  * JDK(java delvelopment kit, jsp 컴파일 및 실행을 위해 필요)
  * 통합 개발환경(이클립스 등)
  * 데이터베이스 서버(MySQL, Oracle)
  * 기타 라이브러리(JDBC, Apache Commons 등)
* 출처

[https://javacpro.tistory.com/43](https://javacpro.tistory.com/43)

#### ASP

* IIS는 마이크로소프트에서 ASP 개발을 위해 이용

#### 톰캣(tomcat) 서버

* HTTP 요청과 응답 처리 외에, JSP 기반의 동적 웹 프로그래밍 기술을 함께 지원하기 위한 웹 서버
* 개발 과정에서 톰캣 서버와 연결을 위해 일반적으로 이용되는 포트번호
  * **8080 포트**
* 이런 에러 발생 원인

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 외부에서 실행되는 톰캣 서버 존재
* 서버 연결 정보가 제대로 주어지지 않을 때
* 톰캣이 아닌 다른 웹 서버가 이미 실행 중일때

CF. 여러 개의 브라우저 서버 동시 접속 시도는 지원 되어야 하는 기능

***

## 10. JDK와 JRE

### 1) JRE(JAVA Runtime Enviroment)

* java 실행에 필요한 프로그램만을 가지고 있음

### 2) JDK(Java Development Kit)

* JRE 외에 개발에 필요한 컴파일러나 디버거 등을 함께 포함한다는 점

***

## 3) 웹 환경 구축

* 톰캣(웹 서버 + Servlet 컨테이너) : WAS
* IDE : 통합 개발 환경 - 이클립스 : J2EE
* 웹 브라우저(HTTP 서비스)
* DBMS(ORACLE, MySQl 선택)
* DBMS(ORACLE, MYSQL 선택)
* Junit, lombok

***

## 그 외

1. 다음 HTML 입력 태그 중 \<input type=“checkbox”>와 동일한 역할을 수행하는 것은? **\<select multiple>**
2. 여러 줄의 텍스트를 입력받기 위해 이용될 수 있는 HTML 입력 태그는 무엇인가? **\<textarea> 태그**
3. 여러 개의 \<input type=“radio”> 태그를 공통 그룹으로 묶으려면 어떤 속성의 값을 동일하게 지정해야 하나요? **name 속성**
4. \<input type=“submit”>과 모양이 같은 HTML 태그는 어떤 것이 있나요? **\<button> 태그 or \<input type=”reset”>**
5. `텍스트 입력형` **사용자 인터페이스에서만 이용**되는 \<input> 태그의 공통 속성은 어떤 것이 있나요? **placeholder와 required**
6. `옵션 선택형 사용자 인터페이스`에서만 이용되는 \<input> 태그의 공통 속성은 어떤 것이 있나요? **checked**
7. **다음과 동일한 역할을 수행하는 \<input> 태그를 작성해 주세요.**

```java
<select name="sport">
	<option value="baseball">Baseball</option>
	<option  value="football">Football</option>
	<option  value="volleyball"  selected>Volleyball</option>
</select>
```

답

```java
<input type="checkbox" name="sport" value="baseball">Baseball<br>
<input  type="checkbox"  name="sport"  value="football">Football<br>
<input  type="checkbox"  name="sport"  value="volleyball"  checked>Volleyball<br>
```

8. **다음의 화면을 구현하기 위한 HTML 코드를 작성해 주세요**

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```java
<html>
<body>
<lable> ID </lable>
<input type="text" name="id" value="kim" size=10> </br>
<lable> Password </lable>
<input type="text" name="password" value="111" size=10> </br>
<input type="button" name="login" value="Login">
</body>
</html>
```

9. **다음 color.html에서 입력창에 'Green'을 입력하고 OK 버튼을 눌렀을 때 생성되는 URL은 무엇인가요? (color.html은 Hello 프로젝트에 포함되어 있습니다)**

```java
<body>
<form  action="color.jsp">
What  is  your  favorite  color?  <br>
<input  type="text"  name="select"  placeholder="Red,  green,  or  blue?"><br><br>
<input  type="submit"  value="OK">
</form>
</body
```

[http://localhost:8080/Hello/color.jsp?select=Green](http://localhost:8080/Hello/color.jsp?select=Green) 또는Hello/color.jsp?select=Green

name=받은값

10. **회원 가입 화면은 로그인 화면과 유사한 방법으로 구현할 수 있습니다. 다음의 회원가입 화면을 구현하기 위한 signup.html 코드를 작성해 주세요**

```java
<html>
<body>
<table  align=center>
<tr><td  colspan=2  align=center  height=40><b>회원가입</b><td></tr>
<tr>
        <td  align=right>아이디&nbsp;</td> 
        <td><input  type="text"  name="id"  placeholder="Email  address"  required></td>
</tr>
<tr>
        <td  align=right>패스워드&nbsp;</td> 
   
        <td><input  type="password"  name="ps"  required></td>
</tr>
<tr>
        <td  align=right>패스워드  확인&nbsp;</td> 
        <td><input  type="password"  name="ps2"  required></td>
</tr>
<tr>
        <td  align=right>이름&nbsp;</td> 
        <td><input  type="text"  name="name"  required></td>
</tr>
<tr>
        <td  colspan=2  align=center  height=50>
                <input  type="button"  value="회원가입하기">
        </td>
</tr>
</table>
</body>
</html>
```

11. **Form 태그의 action 속성에 지정되는 값은 무엇인가요?**

\<input>, \<textarea>, \<select> 등의 양식을 통해 입력된 데이터가 **전송될 서버 측 URL**을 지정합니다.

12. **다음 중 Form 태그의 속성과 거리가 먼 것은 무엇인가요?**

action, method, name, value

13. **다음 중 Form 태그의 method 속성에 지정될 수 있는 값과 거리가 먼 것은 무엇인가요?**

GET, PUT, PUSH, POST

14. **다음 중 URL에 기술된 서비스 파일의 확장자에 따른 톰캣의 처리 방법 중 틀린 내용은?**

* 확장자가 html일 경우, 요청된 html 파일을 찾아 클라이언트로 전송
* 확장자가 jsp일 경우, 요청된 jsp 파일을 실행
* 확장자가 없을 경우, 요청된 이름에 해당하는 Java 클래스를 실행

⇒ 자바클래스가 아닌 서블릿 형태의 클래스 실행

* 요청된 파일이 없을 경우, 404 에러 출력

15. **다음 중 HTTP 요청 메시지 `헤더의 시작 라인`에 포함되는 내용이 아닌 것은 무엇인가요?**

HTTP 전송 방식, 컨텐츠 길이, URL, HTTP 버전

16. **HTTP 전송 방식 중 `파일을 전송`하고자 할 때 이용되는 방식은?**

POST ⇒ HTTP 요청 메세지의 바디에 파일 내용 추가 전송 가능 그렇지 못할 경우, URL 형태로 전송되어야 하며, 이 경우 전송 파일의 크기 제한 문제 발생

17. **GET 방식의 문제점과 해결점**

사용자가 입력한 값이 URL에 그대로 노출되어 보안 문제 발생 해결하기 위해서는 \<form> 태그 method 속성 값을 post로 수정

18. **동적 웹 프로그래밍에서 URL은 어떤 형식으로 구성**

URL은 포트번호, 프로토콜, 서버 주소, 파일 경로의 네 부분으로 구성되며, 동적 웹 프로그래밍의 경우 **파일 경로 부분이 다시 세분화**되어 프로젝트 이름, 서비스 파일(파일명), 그리고 사용자가 입력한 파라미터를 리스트 형태로 표현한 쿼리 스트링 등으로 구성된다.

19. **다음 city.html에서 'Seoul'과 'Busan'을 입력하고 OK 버튼을 눌렀을 때 생성되는 URL은 무엇인가요? (city.html은 Hello 프로젝트에 포함되어 있습니다.)**

```java
<body>
<form  action="city.jsp">
<input  type="checkbox"  name="city"  value="seoul"  checked>Seoul<br>
<input  type="checkbox"  name="city"  value="busan"  checked>Busan<br>
<input  type="checkbox"  name="city"  value="incheon">Incheon<br>
<input  type="submit"  value="OK">
</form>
</body>
```

[http://localhost:8080/Hello/city.jsp?city=seoul\&city=busan](http://localhost:8080/Hello/city.jsp?city=seoul\&city=busan) 또는

Hello/city.jsp?city=seoul\&city=busan

20. **09번에서 주어진 코드를 실행했을 때, 전송되는 HTTP 요청 메시지 헤더는 어떤 형태인가요? 크롬 브라우저의 디버깅 모드를 통해 요청 메시지 내용을 확인할 수 있습니다**

아래는 크롬의 디버깅 모드를 통해 얻어진 메시지 형태입니다. 브라우저에 따라 내용이 조금씩 달라질 수 있습니다.
