# item 09 : try-finally보다는 tiy-with-resources를 사용하라

자바 라이브러리에는 직접 `close` 메서드를 호출하여 닫아주어야 하는 자원이 많다. `InputStream`, `OutputStream`, `java.sql.Connection` 등이 그 예입니다. 이러한 자원은 클라이언트가 닫는 것을 놓치기 쉬워 예측할 수 없는 성능 문제로 이어질 수 있습니다.&#x20;

전통적으로 자원을 제대로 닫기 위해 `try-finally` 구조를 사용했다. 예외가 발생하거나 메서드에서 반환되는 경우를 포함하여 자원을 닫아준다.

### 코드 9-1: `try-finally`를 사용한 자원 회수 (더 이상 최선의 방법이 아님)

```java
static String firstLineOfFile(String path) throws IOException {
    // 파일 경로를 받아 BufferedReader 생성
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        // 파일의 첫 번째 줄을 읽어 반환
        return br.readLine();
    } finally {
        // BufferedReader를 닫아 자원 해제
        br.close();
    }
}
```

하지만 자원을 하나 더 사용해야 한다면 코드가 복잡해진다.

### 코드 9-2: 자원이 둘 이상일 때의 `try-finally` (너무 지저분함)

```java
static void copy(String src, String dst) throws IOException {
    // 입력 스트림 생성
    InputStream in = new FileInputStream(src);
    try {
        // 출력 스트림 생성
        OutputStream out = new FileOutputStream(dst);
        try {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            // 데이터를 읽어서 복사
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        } finally {
            // 출력 스트림 닫기
            out.close();
        }
    } finally {
        // 입력 스트림 닫기
        in.close();
    }
}

```

심지어 자바 라이브러리에서도 `close` 메서드를 제대로 구현하지 않은 경우가 많다.

또한, `try` 블록과 `finally` 블록 모두에서 예외가 발생하면, `finally` 블록에서 발생한 <mark style="color:red;">예외가 처음 예외를 덮어써서 실제로 발생한 문제를 파악하기 어렵게 만든다.</mark>

> 이러한 문제들은 자바 7에서 도입된 **`try-with-resources`** 구조로 모두 해결되었다.&#x20;

이 구조를 사용하려면 해당 자원이 `AutoCloseable` 인터페이스를 구현해야 한다. 이 인터페이스는 단순히 `void close()` 메서드 하나만을 정의한다.

`AutoCloseable`을 구현한 자원은 `try-with-resources`를 사용하여 자동으로 자원을 닫을 수 있다.

### 코드 9-3: `try-with-resources`를 사용한 자원 회수 (최선의 방법)

```java
static String firstLineOfFile(String path) throws IOException {
    // try-with-resources를 사용하여 BufferedReader 생성
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        // 파일의 첫 번째 줄을 읽어 반환
        return br.readLine();
    } // br.close()가 자동으로 호출되어 자원 해제
}

```

복수의 자원을 처리할 때도 코드가 훨씬 간결

### 코드 9-4: 복수의 자원을 처리하는 `try-with-resources` (짧고 매혹적임)

```java
static void copy(String src, String dst) throws IOException {
    // 여러 자원을 try-with-resources에 선언
    try (InputStream in = new FileInputStream(src);
         OutputStream out = new FileOutputStream(dst)) {
        byte[] buf = new byte[BUFFER_SIZE];
        int n;
        // 데이터를 읽어서 복사
        while ((n = in.read(buf)) >= 0)
            out.write(buf, 0, n);
    } // in.close()와 out.close()가 자동으로 호출되어 자원 해제
}

```

> `try-with-resources`를 사용하면 코드가 짧고 읽기 쉬울 뿐만 아니라 예외 처리도 개선된된다.&#x20;

`readLine()`과 (코드에는 나타나지 않는) `close()` 모두에서 예외가 발생할 경우, `close()`에서 발생한 예외는 숨겨지고 `readLine()`에서 발생한 예외가 기록된다. 이렇게 하면 실제 문제를 진단하기가 더 쉬워진진다. 숨겨진 예외들은 스택 추적 내역에 **suppressed**로 표시되며, `Throwable.getSuppressed()` 메서드를 통해 프로그램에서 접근할 수 있다.

또한, `try-with-resources`에서도 `catch` 절을 사용할 수 있어 예외 처리를 유연하게 할 수 있다.

* **`try` 블록**: 전체 자원 관리와 예외 처리를 담당하는 블록
* **`with` 부분**: `try` 뒤의 괄호 안에서 자원을 선언하고 초기화하는 부분
* **`resources`**: `with` 부분에서 선언된 자원 객체들로, `AutoCloseable`을 구현해야 한다
* **with 블록 (자원 선언 부분)**:
  * `InputStream in = new FileInputStream("input.txt");`
  * `OutputStream out = new FileOutputStream("output.txt");`
  * 여러 자원을 세미콜론 `;`으로 구분하여 선언할 수 있다.
  * **resource (자원들)**:
    * `in`과 `out`이 각각의 자원이다.

#### 코드 9-5: `try-with-resources`에 `catch` 절을 함께 사용

```java
static String firstLineOfFile(String path, String defaultVal) {
    // try-with-resources를 사용하여 BufferedReader 생성
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        // 파일의 첫 번째 줄을 읽어 반환
        return br.readLine();
    } catch (IOException e) {
        // 예외 발생 시 기본값 반환
        return defaultVal;
    }
}

```

* **`AutoCloseable` 인터페이스**: 자바 7부터 추가된 인터페이스로, `close()` 메서드 하나만을 가지고 있다. 이 인터페이스를 구현하면 `try-with-resources`에서 사용할 수 있다.
* **`try-with-resources`의 장점**:
  * **코드의 간결성**: 중첩된 `try-finally` 블록을 피할 수 있어 코드가 깔끔해진다.
  * **예외 처리 개선**: 여러 예외가 발생해도 처음 예외를 보존하고, 나머지 예외는 suppressed로 처리되어 디버깅이 용이하다.
  * **자동 자원 관리**: 자원을 자동으로 닫아주어 자원 누수를 방지한다.

**참고 자료:**

* **Oracle 공식 문서**: [The try-with-resources Statement](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html)
* **Java Language Specification (JLS) 14.20.3**: [try-with-resources 문](https://docs.oracle.com/javase/specs/jls/se7/html/jls-14.html#jls-14.20.3)

> **✨ 정리**
>
> **회수해야 하는 자원을 다룰 때는 try-finally 말고, `try-with-resources`를 사용하자.** 자바에서 자원을 안전하고 효율적으로 관리하기 위해서는 `try-with-resources`를 사용하는 것이 최선의 방법이다. 이를 통해 코드의 가독성을 높이고, 예외 처리도 개선할 수 있다. 자원을 사용하는 클래스를 작성할 때는 반드시 `AutoCloseable` 인터페이스를 구현하여 `try-with-resources`에서 활용할 수 있도록 해야 한다. 코드는 더 짧고 분명해지고, 만들어지는 예외 정보도 훨씬 유용하다.&#x20;
