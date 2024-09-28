# @PreDestroy 어노테이션

`@PreDestroy` 어노테이션은 Java의 **Java EE (Enterprise Edition)** 및 **Spring 프레임워크**에서 사용하는 어노테이션으로, 빈(Bean)이 소멸되기 전에 호출될 메서드를 지정하는 역할을 합니다. 주로 애플리케이션에서 자원을 정리하거나 종료 작업을 처리할 때 사용됩니다.

#### 주요 개념

* **`@PreDestroy` 어노테이션의 동작**:
  * 빈이 **컨테이너(Spring 또는 Java EE 컨테이너)에 의해 제거되기 직전**에 실행되는 메서드를 정의합니다.
  * 이 메서드는 빈의 라이프사이클이 끝날 때 호출되어, 해당 빈이 사용한 리소스나 연결된 자원(파일, 데이터베이스 연결 등)을 안전하게 정리할 수 있는 기회를 제공합니다.
* **사용 예시**:

```java
import javax.annotation.PreDestroy;

public class MyBean {

    // 빈이 소멸되기 전에 호출되는 메서드
    @PreDestroy
    public void cleanup() {
        System.out.println("Cleaning up resources before bean is destroyed");
        // 리소스 정리, 파일 닫기, DB 연결 종료 등
    }
}
```

#### Spring에서의 활용

* Spring에서 `@PreDestroy`는 보통 **싱글톤 빈**이나 **프로토타입 빈**에 대해 사용되며, 해당 빈이 **스프링 컨테이너에 의해 관리되고 소멸될 때** 호출됩니다.
* 빈이 애플리케이션에서 더 이상 필요하지 않을 때나, 컨테이너가 종료될 때, `@PreDestroy`가 붙은 메서드가 호출됩니다.

#### 자주 쓰이는 상황

* **데이터베이스 연결 해제**
* **파일이나 네트워크 자원 정리**
* **스레드 풀 종료**
* **캐시 정리**

`@PreDestroy`는 자원을 안전하게 정리하여 메모리 누수나 자원 잠금과 같은 문제를 방지하는 데 중요한 역할을 합니다.
