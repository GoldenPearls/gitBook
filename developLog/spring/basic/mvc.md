# MVC

## MVC 란?

> 스프링 MVC (Model-View-Controller) 패턴은 웹 애플리케이션을 구조화하는 데 사용되는 디자인 패턴으로, 애플리케이션을 세 가지 주요 구성 요소인 **모델(Model), 뷰(View), 컨트롤러(Controller)**로 나누어 관리한다. 스프링 MVC는 이러한 패턴을 구현하여 웹 애플리케이션의 개발을 용이하게 하고 유지 보수를 효율적으로 할 수 있도록 힌다.

### 1) 구성 요소

1. **Model (모델)**:

* 애플리케이션의 **데이터와 비즈니스 로직을 처리**한다.
* 데이터베이스와의 상호작용, 상태 관리 및 데이터의 유효성 검사 등을 담당한다.
* 모델은 데이터를 뷰와 컨트롤러에 전달하며, 그 자체로 사용자 인터페이스에 대한 정보를 포함하지 않는다.

2. **View (뷰)**:

* 사용자에게 **데이터를 표시하는 역할**을 한다.
* HTML, JSP, Thymeleaf와 같은 템플릿 엔진을 사용하여 데이터를 시각적으로 표현한다.
* 뷰는 모델로부터 데이터를 받아와서 사용자에게 렌더링하며, 비즈니스 로직은 포함되지 않는다.

3. **Controller (컨트롤러)**:

* 사용자의 입력을 처리하고, **모델과 뷰를 연결하는 역할**을 한다.
* 사용자의 요청을 받아서 해당 요청을 처리할 모델을 결정하고, 처리 결과를 뷰에 전달한다.
* HTTP 요청을 처리하고, 적절한 응답을 생성하는 메소드를 포함한다.



### 2) 동작원리

1. **사용자 요청 (Request)**:

* 사용자가 브라우저를 통해 `URL`을 요청합니다. 이 요청은 HTTP 요청으로 서버에 전달됩니다.

2. **디스패처 서블릿 (DispatcherServlet)**:

* 모든 요청은 먼저 `DispatcherServlet`에 도달합니다. 이는 스프링 <mark style="color:red;">MVC의 프론트 컨트롤러</mark>로, 중앙에서 모든 요청을 처리합니다.
* `DispatcherServlet`은 요청을 분석하여 적절한 컨트롤러로 전달합니다.

3. **핸들러 매핑 (Handler Mapping)**:

* `DispatcherServlet`은 요청 URL에 따라 어느 컨트롤러가 이 요청을 처리할지 결정하기 위해 `Handler Mapping`을 사용합니다.
* `Handler Mapping`은 요청에 매핑된 컨트롤러를 찾아서 `DispatcherServlet`에 반환합니다.

4. **컨트롤러 처리 (Controller Handling)**:

* `DispatcherServlet`은 요<mark style="color:red;">청을 처리할 컨트롤러를 호출</mark>합니다.
* 컨트롤러는 비즈니스 로직을 실행하고, 모델 데이터를 생성하거나 수정합니다.
* 그런 다음, 어떤 뷰를 사용할 것인지 결정하여 `DispatcherServlet`에 반환합니다.

5. **뷰 리졸버 (View Resolver)**:

* `DispatcherServlet`은 반환된 뷰 이름을 기반으로 `View Resolver`를 통해 실제 뷰(HTML, JSP 등)를 찾습니다.
* 이 과정에서 뷰의 위치나 파일 확장자 등을 결정하게 됩니다.

6. **뷰 렌더링 (View Rendering)**:

* `View Resolver`가 찾아낸 뷰를 사용하여 모델 데이터를 포함하는 최종 HTML이 생성됩니다.
* 이 최종 결과는 `DispatcherServlet`에 의해 클라이언트(브라우저)로 전달됩니다.

7. **응답 (Response)**:

* 브라우저는 서버로부터 받은 HTML을 렌더링하여 사용자에게 표시합니다.

### 3) 데이터 베이스와의 상호작용

> 그럼 웹 => 컨트롤러 => 서비스 => dao => sql 후에&#x20;
>
> sql => dao => 서비스 => 컨트롤러 => 뷰로 반환

#### 1. 웹 => 컨트롤러

* **웹 (Web)**: 사용자가 웹 애플리케이션에 접근하여 특정 URL을 요청합니다.
* **컨트롤러 (Controller)**: 사용자의 요청을 처리하는 첫 번째 레이어입니다. 이 레이어에서 요청을 받아서 적절한 서비스를 호출합니다.

#### 2. 컨트롤러 => 서비스

* **서비스 (Service)**: 컨트롤러에서 비즈니스 로직을 담당하는 서비스 클래스를 호출합니다.
  * 서비스 레이어는 비즈니스 로직을 구현하는 곳으로, 데이터 처리, 비즈니스 규칙 적용 등을 담당합니다.
  * 서비스는 DAO(Data Access Object) 레이어를 통해 데이터베이스와 상호작용할 수 있습니다.

#### 3. 서비스 => DAO (Data Access Object)

* **DAO**: 데이터베이스와의 직접적인 통신을 담당합니다.
  * DAO 클래스는 SQL 쿼리를 실행하여 데이터를 가져오거나 변경하는 역할을 합니다.
  * 서비스 레이어는 DAO를 통해 데이터베이스에 필요한 데이터를 요청하고 결과를 받습니다.

#### 4. DAO => SQL

* **SQL**: DAO 클래스에서 작성된 SQL 쿼리가 실행됩니다.
  * SQL 쿼리는 데이터베이스에서 데이터를 가져오거나, 업데이트, 삭제, 삽입 등의 작업을 수행합니다.

#### 5. SQL => DAO

* **DAO**: SQL 쿼리의 결과를 받아서 필요한 데이터 구조로 변환합니다. 이 데이터는 서비스 레이어로 반환됩니다.

#### 6. DAO => 서비스

* **서비스 (Service)**: DAO로부터 받은 데이터를 처리합니다. 예를 들어, 여러 DAO에서 데이터를 조합하거나 비즈니스 로직을 추가로 적용할 수 있습니다.
  * 처리된 데이터를 컨트롤러로 반환합니다.

#### 7. 서비스 => 컨트롤러

* **컨트롤러 (Controller)**: 서비스에서 처리된 데이터를 받아서, 최종적으로 뷰에 데이터를 전달합니다.
  * 컨트롤러는 어떤 뷰를 사용할지 결정하고, 그 뷰에 데이터를 넘깁니다.

#### 8. 컨트롤러 => 뷰

* **뷰 (View)**: 컨트롤러에서 전달받은 데이터를 기반으로 화면을 렌더링합니다.
  * 사용자는 최종적으로 뷰에서 렌더링된 웹 페이지를 보게 됩니다.



#### 요약

이 전체 과정은 다음과 같이 정리할 수 있습니다:

1. 사용자가 웹 애플리케이션에 요청을 보냅니다.
2. 요청은 컨트롤러에 의해 처리됩니다.
3. 컨트롤러는 서비스 레이어를 호출하여 비즈니스 로직을 처리합니다.
4. 서비스는 DAO를 통해 데이터베이스와 상호작용합니다.
5. 데이터베이스에서 쿼리가 실행되고, 결과가 DAO를 통해 서비스로 반환됩니다.
6. 서비스는 받은 데이터를 처리하여 컨트롤러로 전달합니다.
7. 컨트롤러는 최종적으로 뷰에 데이터를 전달하여 사용자에게 보여줍니다.


