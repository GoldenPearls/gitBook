# AutoCloseable 인터페이스

**AutoCloseable 인터페이스란?**

`AutoCloseable` 인터페이스는 **자바 7에서 도입된 인터페이스**로, 자원을 자동으로 닫을 수 있도록 해주는 역할을 합니다. 이 인터페이스를 구현하면 해당 객체는 `try-with-resources` 문에서 사용될 수 있으며, 블록이 끝날 때 자동으로 `close()` 메서드가 호출되어 자원을 해제합니다.

```java
public interface AutoCloseable {
    void close() throws Exception;
}
```

**주요 특징:**

* **자원 관리 자동화**: `AutoCloseable`을 구현한 객체는 `try-with-resources` 문에서 사용될 때 블록이 끝나면 자동으로 `close()` 메서드가 호출됩니다.
* **예외 처리 통합**: `close()` 메서드에서 발생하는 예외는 `try-with-resources` 문에서 관리되어, 예외가 발생하더라도 자원이 확실히 해제됩니다.
* **범용성**: `AutoCloseable`은 다양한 종류의 자원에 적용될 수 있어, 파일, 네트워크 연결, 데이터베이스 연결 등 여러 자원 관리에 사용됩니다.

**사용 방법:**

`AutoCloseable`을 구현한 클래스는 `try-with-resources` 문에서 다음과 같이 사용할 수 있습니다.

```java
try (MyResource resource = new MyResource()) {
    // 자원 사용
}
// 여기서 resource.close()가 자동으로 호출되어 자원 해제
```

**예시 클래스:**

```java
public class MyResource implements AutoCloseable {
    public MyResource() {
        // 자원 초기화
        System.out.println("자원이 초기화되었습니다.");
    }

    @Override
    public void close() throws Exception {
        // 자원 해제 로직
        System.out.println("자원이 해제되었습니다.");
    }

    public void doSomething() {
        // 자원 사용 로직
        System.out.println("자원을 사용하고 있습니다.");
    }
}
```

**예시 사용법:**

```java
public class Main {
    public static void main(String[] args) {
        try (MyResource resource = new MyResource()) {
            resource.doSomething();
        } catch (Exception e) {
            e.printStackTrace();
        }
        // 여기서 resource.close()가 자동으로 호출됩니다.
    }
}
```

**실행 결과:**

```
자원이 초기화되었습니다.
자원을 사용하고 있습니다.
자원이 해제되었습니다.
```

**`AutoCloseable`과 `Closeable`의 차이점:**

* **`AutoCloseable`**:
  * 자바 7에서 도입된 인터페이스.
  * `close()` 메서드가 `Exception`을 던질 수 있습니다.
  * 모든 종류의 자원에 대해 일반적으로 사용됩니다.
* **`Closeable`**:
  * `AutoCloseable`의 하위 인터페이스로, `java.io` 패키지에 위치.
  * `close()` 메서드가 `IOException`을 던집니다.
  * 입출력(I/O) 자원에 특화되어 있습니다.

**왜 `AutoCloseable`을 사용해야 하나요?**

* **안전한 자원 해제**: 예외 발생 여부와 관계없이 자원이 확실히 해제됩니다.
* **코드 간결성**: 중첩된 `try-finally` 블록을 피할 수 있어 코드가 더 깔끔해집니다.
* **예외 처리 개선**: 여러 예외가 발생해도 첫 번째 예외를 보존하고, 추가적인 예외는 suppressed로 처리되어 디버깅이 용이합니다.
* **에러 방지**: 개발자가 자원을 수동으로 닫는 것을 잊어버릴 위험이 줄어듭니다.

**실제 적용 예시:**

파일을 읽을 때 `BufferedReader`를 사용하는 경우를 생각해보겠습니다.

```java
public static void readFile(String path) {
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        String line;
        while ((line = br.readLine()) != null) {
            System.out.println(line);
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
    // br.close()가 자동으로 호출되어 자원이 해제됩니다.
}
```

**추가 설명:**

* **`try-with-resources` 문**: 괄호 안에 선언된 자원들은 `AutoCloseable`을 구현해야 하며, 블록이 종료될 때 자동으로 `close()` 메서드가 호출됩니다.
* **예외 처리 흐름**: `try` 블록 내에서 예외가 발생하더라도 `close()` 메서드가 호출되며, `close()` 메서드에서 발생하는 예외는 suppressed되어 원래의 예외에 포함됩니다.

**참고 자료:**

* **Effective Java 3판**: 조슈아 블로크 저서로, 자바 프로그래밍 모범 사례를 다루며, 여기서도 `try-with-resources`의 중요성을 강조합니다.
* **Oracle 공식 문서**:
  * [The try-with-resources Statement](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html)
  * [AutoCloseable 인터페이스](https://docs.oracle.com/javase/8/docs/api/java/lang/AutoCloseable.html)
* **Java Language Specification (JLS) 14.20.3**:
  * [try-with-resources 문](https://docs.oracle.com/javase/specs/jls/se7/html/jls-14.html#jls-14.20.3)

**결론:**

`AutoCloseable` 인터페이스는 자바에서 자원 관리를 단순화하고 안전하게 만들어주는 중요한 요소입니다. 자원을 사용하는 클래스를 작성할 때 이 인터페이스를 구현하면 `try-with-resources` 문을 통해 자원을 자동으로 해제할 수 있어, 메모리 누수나 기타 자원 누수를 방지할 수 있습니다. 따라서 자원을 다루는 모든 클래스는 `AutoCloseable`을 구현하여 개발자의 실수를 줄이고 코드의 안정성을 높이는 것이 좋습니다.



> 🧐 AutoCloseable 인터페이스는 자원자동 닫기 말고는 안 쓸까?

**AutoCloseable 인터페이스는 자원 자동 닫기 외에도 다른 용도로 사용되나요?**

AutoCloseable 인터페이스는 주로 **자원을 자동으로 닫기 위해** 사용되지만, 그 외에도 몇 가지 추가적인 용도로 활용될 수 있습니다.

1. **수동 자원 관리**: AutoCloseable을 구현한 클래스는 `close()` 메서드를 제공하므로, 개발자는 필요에 따라 수동으로 자원을 닫을 수 있습니다. 이는 자동으로 자원을 닫지 않는 상황에서 유용합니다.
2. **자원 관리의 표준화**: AutoCloseable 인터페이스를 구현함으로써 해당 클래스는 자원을 해제하는 표준 방법을 제공하게 됩니다. 이는 라이브러리나 프레임워크에서 자원을 일관되게 관리할 수 있도록 도와줍니다.
3. **다른 API와의 통합**: 일부 프레임워크나 라이브러리는 AutoCloseable 인터페이스를 구현한 객체를 특별하게 취급하여 자원 관리를 최적화합니다. 예를 들어, 병렬 스트림 처리나 비동기 작업에서 AutoCloseable 자원을 안전하게 관리할 수 있습니다.

그러나 **주된 목적은 자원을 닫는 것**이며, AutoCloseable 인터페이스는 **자원 해제와 관련된 기능을 제공**하는 데 초점을 맞추고 있습니다. 따라서 자원 자동 닫기 외에 다른 용도로 사용되는 경우는 상대적으로 적습니다.

**추가 설명:**

* **자원 누수 방지**: AutoCloseable을 구현하면 예외 발생 시에도 자원이 확실하게 해제되어 메모리 누수나 다른 자원 누수를 방지할 수 있습니다.
* **API 설계 시의 이점**: 라이브러리나 API를 설계할 때 AutoCloseable을 구현하면 사용자가 해당 자원을 명시적으로 닫아야 함을 인지하게 되어, 올바른 사용을 유도할 수 있습니다.

**예시:**

```java
public class CustomResource implements AutoCloseable {
    public void useResource() {
        // 자원 사용 로직
    }

    @Override
    public void close() {
        // 자원 해제 로직
    }
}

// 사용 예시
CustomResource resource = new CustomResource();
try {
    resource.useResource();
} finally {
    resource.close(); // 수동으로 자원 해제
}
```

**결론:**

AutoCloseable 인터페이스는 **자원을 안전하고 효율적으로 해제하기 위한 목적으로 설계**되었으며, 자동 또는 수동으로 자원을 닫는 데 사용됩니다. 자원 관리의 표준화를 통해 코드의 안정성과 유지보수성을 높일 수 있지만, 자원을 닫는 것 외에 다른 목적으로 사용되는 경우는 일반적이지 않습니다.



> 🧐  AutoCloseable 인터페이스의 정의 된 내용을 뜯어봐줘

**AutoCloseable 인터페이스의 정의된 내용을 분석해드리겠습니다.**

`AutoCloseable` 인터페이스는 **자바 7**에서 도입된 인터페이스로, **자원을 자동으로 닫기 위한 표준 방법**을 제공합니다. <mark style="color:red;">이 인터페이스는 하나의 메서드만을 선언하고 있습니다.</mark>

#### 인터페이스 정의

```java
public interface AutoCloseable {
    void close() throws Exception;
}
```

**주요 특징:**

1. **단일 메서드 `close()`**: `AutoCloseable` 인터페이스는 `void close() throws Exception` 메서드 하나만을 정의합니다.
2. **예외 처리**: `close()` 메서드는 `Exception`을 던질 수 있습니다. 이는 **일반적인 예외 타입**인 `Exception`을 사용함으로써 다양한 종류의 예외를 처리할 수 있도록 합니다.
3. **`try-with-resources`와의 연동**: `AutoCloseable`을 구현한 객체는 `try-with-resources` 문에서 사용할 수 있으며, 블록이 종료되면 `close()` 메서드가 자동으로 호출됩니다.

#### 세부 분석

1.  **메서드 시그니처**

    ```java
    void close() throws Exception;
    ```

    * **`void` 반환형**: `close()` 메서드는 반환값이 없습니다. 자원을 닫는 작업을 수행하며, 결과를 반환하지 않습니다.
    * **`throws Exception`**: `Exception`은 모든 예외의 최상위 클래스입니다. 따라서 `close()` 메서드는 어떤 종류의 예외도 던질 수 있습니다.
      * **장점**: 예외 타입에 대한 제한이 없으므로 다양한 자원에서 발생하는 다양한 예외를 처리할 수 있습니다.
      * **단점**: 너무 일반적인 예외를 던지므로, 호출 측에서 구체적인 예외 처리가 어려울 수 있습니다.
2. **예외 처리 및 호환성**
   *   `AutoCloseable` 인터페이스를 구현하는 클래스는 `close()` 메서드에서 `Exception`의 하위 타입을 던질 수 있습니다.

       ```java
       public class MyResource implements AutoCloseable {
           @Override
           public void close() throws SpecificException {
               // 자원 해제 로직
           }
       }
       ```
   * 이 경우, `SpecificException`은 `Exception`의 하위 클래스여야 합니다.
3. **`try-with-resources` 문과의 관계**
   *   `try-with-resources` 문은 `AutoCloseable` 인터페이스를 구현한 자원을 자동으로 관리합니다.

       ```java
       try (AutoCloseableResource res = new AutoCloseableResource()) {
           // 자원 사용
       } catch (Exception e) {
           // 예외 처리
       }
       // 여기서 res.close()가 자동으로 호출됩니다.
       ```
   * 블록이 정상적으로 종료되거나 예외가 발생하더라도 `close()` 메서드가 호출됩니다.
4. **`Closeable` 인터페이스와의 비교**
   *   **`Closeable`**:

       ```java
       public interface Closeable extends AutoCloseable {
           void close() throws IOException;
       }
       ```

       * `Closeable`은 `AutoCloseable`의 하위 인터페이스입니다.
       * `close()` 메서드는 `IOException`을 던집니다.
       * 주로 입출력(I/O) 관련 자원에서 사용됩니다.
   * **차이점**:
     * `AutoCloseable`의 `close()`는 \*\*`Exception`\*\*을 던지는 반면, `Closeable`의 `close()`는 \*\*`IOException`\*\*을 던집니다.
     * 따라서 `Closeable`은 더 구체적인 예외를 던지며, 이는 I/O 작업에서 발생하는 예외를 처리하는 데 적합합니다.
5. **설계 이유**
   * **일반화된 자원 관리**: 자바는 다양한 종류의 자원을 관리해야 합니다. 파일, 네트워크 연결, 데이터베이스 연결 등 각기 다른 예외를 던질 수 있는 자원을 일관되게 관리하기 위해 `Exception`을 사용합니다.
   * **호환성 유지**: 기존의 `Closeable` 인터페이스는 `IOException`을 던지기 때문에, `AutoCloseable`은 더 일반적인 `Exception`을 던져서 다양한 자원을 포괄합니다.
6. **구현 시 고려 사항**
   * `AutoCloseable`을 구현하는 클래스는 `close()` 메서드에서 필요한 자원 해제 로직을 구현해야 합니다.
   * `close()` 메서드에서 예외가 발생하지 않도록 하는 것이 바람직합니다. 만약 예외가 발생하더라도, 예외 처리를 통해 자원이 완전히 해제되도록 해야 합니다.
7. **예외 발생 시 행동**
   * `try-with-resources` 문에서 `close()` 메서드가 예외를 던지면, 해당 예외는 **suppressed 예외**로 처리됩니다.
     * **주 예외**: `try` 블록에서 발생한 예외.
     * **suppressed 예외**: `close()` 메서드에서 발생한 예외.
   * 이 예외들은 `Throwable.getSuppressed()` 메서드를 통해 확인할 수 있습니다.
8.  **예제**

    ```java
    public class DatabaseConnection implements AutoCloseable {
        public void connect() {
            // 데이터베이스 연결 로직
        }

        @Override
        public void close() throws Exception {
            // 연결 해제 로직
        }
    }

    // 사용 예시
    try (DatabaseConnection dbConn = new DatabaseConnection()) {
        dbConn.connect();
        // 데이터베이스 작업 수행
    } catch (Exception e) {
        // 예외 처리
    }
    // dbConn.close()가 자동으로 호출되어 자원 해제
    ```
9. **자바 언어 사양과의 관계**
   * **Java Language Specification (JLS) 14.20.3**에서 `try-with-resources` 문에 대한 자세한 내용을 다루고 있습니다.
   * `try-with-resources` 문은 하나 이상의 리소스 변수를 선언하며, 각 변수는 `AutoCloseable`을 구현한 자원이어야 합니다.
10. **실제 사용 예시**

    * `java.sql.Connection`, `java.sql.Statement`, `java.sql.ResultSet` 등 데이터베이스 관련 클래스들은 `AutoCloseable`을 구현하고 있습니다.
    * 이를 통해 데이터베이스 자원을 효율적으로 관리할 수 있습니다.

    ```java
    try (Connection conn = DriverManager.getConnection(url);
         Statement stmt = conn.createStatement();
         ResultSet rs = stmt.executeQuery(query)) {
        while (rs.next()) {
            // 결과 처리
        }
    } catch (SQLException e) {
        // 예외 처리
    }
    // conn.close(), stmt.close(), rs.close()가 자동으로 호출됨
    ```

**결론**

`AutoCloseable` 인터페이스는 자바에서 **자원 관리를 표준화하고 자동화하기 위한 핵심 인터페이스**입니다. 하나의 메서드 `close()`만을 정의하며, 다양한 종류의 예외를 던질 수 있도록 `Exception`을 사용합니다. 이를 통해 다양한 자원을 일관되게 관리할 수 있으며, `try-with-resources` 문과 함께 사용하여 자원 누수를 방지하고 코드의 가독성을 높일 수 있습니다.

**참고 자료**

* **Java API 문서**: [AutoCloseable 인터페이스](https://docs.oracle.com/javase/8/docs/api/java/lang/AutoCloseable.html)
* **Java Language Specification (JLS) 14.20.3**: [Try-with-resources 문](https://docs.oracle.com/javase/specs/jls/se8/html/jls-14.html#jls-14.20.3)
* **Effective Java 3판**: 자바 프로그래밍 모범 사례를 다루며, 자원 관리를 위한 `AutoCloseable`과 `try-with-resources`의 중요성을 강조합니다.
