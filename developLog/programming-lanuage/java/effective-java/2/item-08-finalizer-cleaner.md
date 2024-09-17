# item 08. finalizer와 cleaner 사용을 피하라

## 객체 소멸자

자바에는 아래의두 가지 객체 소멸자가 존재한다.  다른 블로그글을 참고하니 보통은 try-with-resources[^1]구문과 [@PreDestory 어노테이션](#user-content-fn-2)[^2]을 사용해서 자바에서 객체가 더이상 필요하지 않을 때 리소스를 정리한다고 함... 그래서 생소하다는데 나도 처음 보는 생소한 느낌?

### finalizer

* 과거에 자바에서 객체가 더 이상 필요하지 않을 때 리소스 정기하기 위해 사용
* 실행 시점이 불확실함

실행 시점이 정확히 알아도 사용할까 말까인데.. 예측할 수 없기 때문에 GC가 언제 객체 수거할 지 명확하 지않기 때문에 리소스가 제대로 해제 되 지않는 문제가 발생할 수 있다고 함

* 심각한 성능 저하

일반적인 객체 정리보다 최대 50배 느려 **대규모 애플리케이션**에서 성능 저하로 인해 심각한 문제를 초래 할 수 있다.

* 예외 무시 현상 + 보안 문제

예외가 발생할 경우, 예외가 무시되며 객체의 정리가 불완전하게 끝날  수 있다고 함.. 이 는 객체 를불완전 상태로 남겨 공격 벡터로 활용될  수 있음

**😱 실제 책에 있는 썰**

필자의 동료가 원인을 알 수 없는 `OutOfMemoryError`를 내며 죽는 GUI 애플리케이션을 디버깅한 이야기를 들려주겠다. 그 친구가 상황을 분석해보니 애플리케이션이 죽는 시점에 그래픽스 객체 수천 개가 finalizer 대기 열에서 회수되기만을 기다 리고 있었다. 불행히도 finalizer 스레드는 다른 애플리케이션 스레드보다 우선 순위가 낮아서 실행될 기회를 제대로 얻지 못한 것이다

### cleaner

* finalizer의 대안으로 나온 것&#x20;
* 여전히 성능 저하 문제와 예측 불가능성 남아 있음
* 백그라운드에서 수행되며, 가비지 컬렉터의 통제하에 있으니 즉각 수행 보장이 없음

> finalizer와 cleaner로는 제때 실행되어야 하는 작업을 절대 할 수 없으며.. 시스템의 중대한 오류를 일으킬 수 있기에 그냥 안쓰는 게 맞음

### AutoCloseable

> 위 두개의 대안으로 나온 것

* `AutoCloseable` 인터페이스를 구현하는 클래스는 리소스를 명시적으로 해제할 때 `close()` 메서드를 사용
* 특히, **try-with-resources** 구문을 통해 자동으로 `close()` 메서드가 호출되어 리소스가 제대로 해제될 수 있도록 한다.(예외가 발생하더라도 안전하게 리소드 관리 가능)
* 또한, **객체가 이미 닫혔는지** 추적하는 것이 중요한데, 이는 재사용되지 않도록 방지하고 예외 처리로 안전성을 확보하기 위함

> 만약 **닫힌 객체의 메서드가 호출되면** `IllegalStateException`을 던지도록 코드를 작성할 수 있다.

```java
public class Resource implements AutoCloseable {
    private boolean closed = false; // 객체가 닫혔는지 추적하는 필드

    // 리소스를 사용하는 메서드
    public void doSomething() {
        if (closed) {
            throw new IllegalStateException("Resource is already closed");
        }
        System.out.println("Using the resource");
    }

    @Override
    public void close() {
        if (closed) {
            throw new IllegalStateException("Resource is already closed");
        }
        closed = true; // 객체가 닫혔음을 표시
        System.out.println("Resource has been closed");
    }

    public static void main(String[] args) {
        // try-with-resources를 사용한 예외 처리 및 리소스 자동 종료
        try (Resource resource = new Resource()) {
            resource.doSomething(); // 리소스 사용
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

#### **✔️** 스프링에서 관리되지 않는 객체나 리소스 사용 시, try-with-resources 사용하는 것으로 예시로 파일을 읽거나 쓰는 경우, 아래와 같이 작성 가능하다고 함

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class TryWithResourcesExample {
    public static void main(String[] args) {
        // try-with-resources를 사용한 파일 읽기
        try (BufferedReader br = new BufferedReader(new FileReader("test.txt"))) {
            String line;
            while ((line = br.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

이 코드는 파일을 읽고, 파일 처리가 끝나면 `BufferedReader`와 `FileReader`가 **자동으로 닫힌다**. `try` 블록이 종료되거나 예외가 발생하더라도 자원이 자동으로 해제되기 때문에 메모리 누수나 파일 잠금 문제를 방지할 수 있다.



스프링 부트에서도 자주 사용하는 패턴으로 물론, 스프링 부트를 사용 시 일반적인 리소스 관리는 스프링 컨텍스트가 `객체 생명주기를 관리`함으로써 해결 되긴 함

EX. 데이터베이스 연결, 파일 스트림, 혹은 소켓 같은 리소스 사용  시 `@Bean`으로 등록된 객체는 컨텍스트 종료 시 자동 정리 됨

```java
@Bean
public DataSource dataSource(){
    return new HikariDataSource(); //Connection pool을 사용하는 예시
}
```



물론 특정 상황에서는 cleaner를 안정망으로 사용하거나 네이트 리소스를 해제하는 용도로도 사용 가능하다고 함 **불확실성과 성능 저하**가 있으므로, 네이티브 자원 관리에 주의가 필요하며, 가능하다면 자원을 즉시 해제하는 방식이 더 바람직하기에.. 스프링 부트의 어노테이션이나 try-with-resources를 쓰는 게 더 적절하다고 함

> **✔️** 핵심정리&#x20;
>
> cleaner(자바 8까지는 finalizer)는 안전망 역할이나 중요하지 않은 네이티브 자원 회수 용으로만 사용하자. 물론 이런 경우라도 불확실성과 성능 저하에 주의해야 한다.

참고 글 : [https://comdolidol-i.tistory.com/435](https://comdolidol-i.tistory.com/435)

[^1]: `try-with-resources`는 **자바 7**에서 도입된 기능으로, **자원을 자동으로 해제**하는 구조를 제공합니다. **AutoCloseable** 또는 **Closeable** 인터페이스를 구현한 객체를 사용할 때, `try-with-resources` 구문을 사용하면 **try 블록이 끝나거나 예외가 발생**하더라도 **자동으로 `close()` 메서드가 호출**되어 리소스가 안전하게 해제됩니다.

    이 구문은 자바에서 **파일, 데이터베이스 연결, 네트워크 소켓** 등의 자원을 사용할 때 주로 사용됩니다.

    #### 특징

    1. **자동 자원 해제**: `try` 블록이 종료될 때 자원이 자동으로 해제됩니다.
    2. **명시적인 `close()` 호출이 필요 없음**: 자원을 해제하기 위해 별도로 `close()`를 호출할 필요가 없습니다.
    3. **깨끗한 코드**: 자원을 사용하는 코드가 간결하고 예외 처리도 자동으로 지원됩니다

[^2]: `@PreDestroy` 어노테이션은 Java의 **Java EE (Enterprise Edition)** 및 **Spring 프레임워크**에서 사용하는 어노테이션으로, \*\*빈(Bean)\*\*이 소멸되기 전에 호출될 메서드를 지정하는 역할을 합니다. 주로 애플리케이션에서 자원을 정리하거나 종료 작업을 처리할 때 사용됩니다.
