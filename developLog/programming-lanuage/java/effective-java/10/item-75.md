# item 75 : 예외의 상세 메시지에 실패 관련 정보를 담으라

## 📂 예외 메시지와 실패 포착의 중요성

예외를 제대로 처리하지 못하면 프로그램은 실패하며, 자바 시스템은 **스택 추적 정보**를 자동으로 출력한다. 이 정보는 **예외의 클래스 이름**, **상세 메시지**, 그리고 **스택 추적 경로**를 포함한다.

> `스택 추적`은 **예외 객체의 toString 메 서드를 호출해 얻는 문자열**로, 보통은 예외의 클래스 이름 뒤에 상세 메시지 가 붙는 형태다. 스택 추적은 문제를 분석하는 프로그래머와 사이트 신뢰성 엔지니어(Site Reliability Engineer, SRE)가 사용할 수 있는 유일한 정보인 경우가 많다.

특히, 실패를 재현하기 어려운 경우라면 스택 추적이 더욱 중요하다. 따라서 **실패 원인에 관한 정보를 가능한 한 많이 상세 메시지에 담아야 한다.**

## 1. 실패 원인 포착을 위한 상세 메시지 작성법

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 1) 상세 메시지 작성의 기본 원칙

1. **필요한 모든 정보 제공**: 발생한 예외와 관련된 모든 매개변수와 필드 값을 메시지에 담아야 한다.

* 예컨대 IndexOutOfBoundsException의 상세 메 시지는 범위의 최솟값과 최댓값, 그리고 그 범위를 벗어났다는 인덱스의 값 을 담아야 한다. 이 정보는 실패에 관한 많은 것을 알려준다.

2. **구체적이고 간결하게**: 장황하게 작성하기보다는 문제를 명확히 설명할 수 있는 핵심 정보를 담아야 한다.
3. **보안 정보 제외**: 암호, 키 등 민감한 정보는 메시지에 포함되지 않아야 한다.

> 관련 데이터를 모두 담아야 하지만 장황할 필요는 없다. 문제를 분석하는 사람 은 스택 추적뿐 아니라 관련 문서와 (필요하다면) 소스코드를 함께 살펴본다.

<mark style="color:red;">**예외의 상세 메시지와 최종 사용자에게 보여줄 오류 메시지를 혼동해서는 안 된다.**</mark> `최종 사용자`에게는 친절한 **안내 메시지**를 보여줘야 하는 반면, `예외 메시지`는 가독성보다는 **담긴 내용**이 훨씬 중요하다

#### **예시: IndexOutOfBoundsException 상세 메시지**

> **실패를 포착하는 상세 메시지**

* 예외를 던질 때, 실패와 관련된 정보를 최대한 상세히 기록하여 메시지를 포함해야 한다.
* 실패 정보를 명확히 드러내면, 디버깅 및 문제 해결이 쉬워진다.
* 예를 들어, `IndexOutOfBoundsException`의 생성자에서 아래와 같이 상세 정보를 추가하는 설계가 권장된다:

```java
public class CustomIndexOutOfBoundsException extends IndexOutOfBoundsException {
    private final int lowerBound;
    private final int upperBound;
    private final int index;

    public CustomIndexOutOfBoundsException(int lowerBound, int upperBound, int index) {
        super(String.format("최솟값: %d, 최댓값: %d, 인덱스: %d", lowerBound, upperBound, index));
        this.lowerBound = lowerBound;
        this.upperBound = upperBound;
        this.index = index;
    }

    public int getLowerBound() {
        return lowerBound;
    }

    public int getUpperBound() {
        return upperBound;
    }

    public int getIndex() {
        return index;
    }
}
```

#### 🔍 코드 설명

1. **상세 메시지 포함**: `String.format`을 사용하여 최솟값, 최댓값, 인덱스 값을 포함한 상세 메시지를 생성
2. **접근자 메서드 제공**: `getLowerBound`, `getUpperBound`, `getIndex` 메서드를 통해 각 정보를 안전하게 가져올 수 있다.
3.  **사용 예시**:

    ```java
    public static void main(String[] args) {
        try {
            throw new CustomIndexOutOfBoundsException(0, 10, 15);
        } catch (CustomIndexOutOfBoundsException e) {
            System.out.println(e.getMessage()); // 출력: 최솟값: 0, 최댓값: 10, 인덱스: 15
        }
    }
    ```



***

#### **추가 설명 및 부연**

**1. 예외 메시지의 중요성**

* **목적**: 발생한 문제를 최대한 빠르고 명확하게 파악할 수 있어야 한다.
*   **문제 상황**: 아래와 같은 단순 메시지는 유용성이 떨어진다.

    ```java
    throw new IndexOutOfBoundsException("인덱스 초과");
    ```

    이런 경우, "무엇이", "어디서", "왜" 문제가 발생했는지를 알기 어렵다.
* **권장 방식**: 상세한 정보를 제공하여 디버깅 시간을 줄이고 문제의 원인을 명확히 전달한다.

**2. 실패 정보를 저장하고 접근 가능하게 설계**

* 예외 객체 내부에 실패 정보를 저장하면 프로그램에서 이를 활용할 수 있다.\
  예를 들어, `lowerBound`, `upperBound`, `index` 값을 확인하여 잘못된 입력 값을 사용자에게 안내할 수 있다.
* **비교 예시**:
  *   **실패 정보 저장 없음**

      ```java
      throw new IndexOutOfBoundsException("Index out of bounds");
      ```
  *   **실패 정보 저장 및 활용 가능**

      ```java
      throw new IndexOutOfBoundsException(0, 10, 15);
      ```

**3. API 설계와 일관성**

* Java 라이브러리의 일부 예외 클래스는 상세 정보를 전달하지 않는 단순한 설계를 채택했지만, 상세 정보를 포함한 설계는 **유지보수성과 디버깅 편의성** 측면에서 장점이 크다.
* 따라서, 애플리케이션 수준에서 예외를 설계할 때는 추가적인 정보와 접근자 메서드를 제공하는 것이 유리하다.

**4. 검사 예외와 비검사 예외의 차이**

* **검사 예외(Checked Exception)**:
  * 메서드 시그니처에 선언되며, 호출하는 쪽에서 반드시 처리해야 한다.
  * 실패 정보를 저장하고, 복구 작업을 돕는 접근자 메서드가 특히 유용하다.
* **비검사 예외(Unchecked Exception)**:
  * 런타임에 발생하며, 반드시 처리하지 않아도 된다.
  * 프로그램적 접근이 드물지만, `toString`을 통해 디버깅 정보를 제공하는 것이 좋다.

***

#### **요약된 조언**

* 실패 정보를 포착하는 상세 메시지를 예외 객체 생성 시 포함하라.
* 예외 객체에 실패 정보를 저장하고, 이를 접근할 수 있는 메서드를 제공하라.
* `toString`이나 로그 메시지에 유용한 정보를 포함시켜 디버깅 시간을 줄여라.
* 검사 예외와 비검사 예외의 특성을 고려하여 설계하라.

## 2. 예외 메시지 작성 시 주의사항

### 1) 보안 관련 주의점

스택 추적 정보를 많은 사람이 볼 수 있으므로, **비밀번호나 암호 키 같은 정보는 포함해서는 안된다.**

### 2) 예외 메시지 vs 사용자 메시지

* **예외 메시지**: 디버깅을 위한 정보로 작성됩니다. 가독성보다는 **내용의 정확성**이 중요하다.
* **사용자 메시지**: 사용자에게 보여줄 메시지는 친절하게 작성하고, 현지어로 번역될 수 있어야 한다.

## 3. 실패 포착을 위한 예외 생성자 설계

*

### 1) 실패 정보를 담는 예외 생성자

실패 상황에 필요한 정보를 예외 생성자에서 모두 받아 상세 메시지로 포함하면, 문제를 분석하는 데 큰 도움이 된다.

**개선된 IndexOutOfBoundsException 생성자 예시**

```java
public class EnhancedIndexOutOfBoundsException extends IndexOutOfBoundsException {
    public EnhancedIndexOutOfBoundsException(int lowerBound, int upperBound, int index) {
        super(String.format("인덱스 범위 초과: [%d, %d), 실제: %d", lowerBound, upperBound, index));
    }
}
```

* **실패 정보를 객체 내부에 저장**
  * 예외 객체 내부에 실패 정보를 저장해두면, 프로그램에서 추가적인 분석이나 복구 작업에 활용할 수 있다.
  * 예를 들어, 실패 정보를 저장하기 위해 `lowerBound`, `upperBound`, `index`와 같은 필드를 예외 객체에 포함할 수 있다.
* **접근자 메서드 제공**
  * 저장한 실패 정보에 접근할 수 있도록 접근자 메서드(getter)를 제공하자.
  *   예:

      ```java
      public int getLowerBound() { return lowerBound; }
      public int getUpperBound() { return upperBound; }
      public int getIndex() { return index; }
      ```
  * 특히, 검사 예외(Checked Exception)의 경우, 실패 정보를 복구하는 데 유용하다.
* **toString 메서드 활용**
  * `toString` 메서드가 반환하는 값에 상세 정보를 포함시키면 디버깅 시 유용하다.
  * 비검사 예외(Unchecked Exception)의 경우에도 접근자 메서드를 제공하는 것이 좋다.

### 2) 생성자 설계의 장점

1. **포괄적 정보 제공**: 실패 원인을 정확히 기술하여 문제를 빠르게 파악할 수 있다.
2. **반복 코드 방지**: 예외 클래스로 메시지 생성 로직을 캡슐화하여 중복 작업을 줄인다.

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## 4. 예외 연쇄(Exception Chaining)

### 1) 예외 연쇄란?

**예외 연쇄**는 **저수준 예외**(근본 원인)를 **고수준 예외**에 포함시키는 방식이다. 이를 통해 문제의 근본 원인을 분석할 수 있다.

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### **2) 예외 연쇄 구현 예시**

```java
public class HigherLevelException extends Exception {
    public HigherLevelException(String message, Throwable cause) {
        super(message, cause);
    }
}

public class Example {
    public static void main(String[] args) {
        try {
            throw new IllegalArgumentException("잘못된 인수");
        } catch (IllegalArgumentException e) {
            throw new HigherLevelException("고수준 예외 발생", e);
        }
    }
}
```

### 3) 예외 연쇄의 장점

1. **원인 추적 가능**: `getCause()` 메서드를 통해 근본 원인을 확인할 수 있다.
2. **스택 추적 통합**: 상위 예외와 하위 예외의 정보를 모두 스택 추적에 포함한다.

## 5. **추가 설명 및 부연**

### **1) 예외 메시지의 중요성**

* **목적**: 발생한 문제를 최대한 빠르고 명확하게 파악할 수 있어야 한다.
*   **문제 상황**: 아래와 같은 단순 메시지는 유용성이 떨어진다.

    ```java
    throw new IndexOutOfBoundsException("인덱스 초과");
    ```

    이런 경우, "무엇이", "어디서", "왜" 문제가 발생했는지를 알기 어렵다.
* **권장 방식**: 상세한 정보를 제공하여 디버깅 시간을 줄이고 문제의 원인을 명확히 전달한다.

### **2) 실패 정보를 저장하고 접근 가능하게 설계**

* 예외 객체 내부에 실패 정보를 저장하면 프로그램에서 이를 활용할 수 있다.\
  예를 들어, `lowerBound`, `upperBound`, `index` 값을 확인하여 잘못된 입력 값을 사용자에게 안내할 수 있다.
* **비교 예시**:
  *   **실패 정보 저장 없음**

      ```java
      throw new IndexOutOfBoundsException("Index out of bounds");
      ```
  *   **실패 정보 저장 및 활용 가능**

      ```java
      throw new IndexOutOfBoundsException(0, 10, 15);
      ```

### **3) API 설계와 일관성**

* Java 라이브러리의 일부 예외 클래스는 상세 정보를 전달하지 않는 단순한 설계를 채택했지만, 상세 정보를 포함한 설계는 **유지보수성과 디버깅 편의성** 측면에서 장점이 크다.
* 따라서, 애플리케이션 수준에서 예외를 설계할 때는 추가적인 정보와 접근자 메서드를 제공하는 것이 유리하다.

### **4) 검사 예외와 비검사 예외의 차이**

* **검사 예외(Checked Exception)**:
  * 메서드 시그니처에 선언되며, 호출하는 쪽에서 반드시 처리해야 한다.
  * 실패 정보를 저장하고, 복구 작업을 돕는 접근자 메서드가 특히 유용하다.
* **비검사 예외(Unchecked Exception)**:
  * 런타임에 발생하며, 반드시 처리하지 않아도 된다.
  * 프로그램적 접근이 드물지만, `toString`을 통해 디버깅 정보를 제공하는 것이 좋다.

## 📚 정리: 실패 포착의 중요성

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **예외 메시지**는 실패 원인을 분석하는 데 중요한 정보를 제공하자
* 모든 매개변수와 상태 정보를 담아 예외 생성자를 설계하자
* 필요시 **예외 연쇄**를 활용해 문제의 근본 원인을 제공하자
* 사용자 메시지와 예외 메시지를 명확히 구분하여 설계하지

> 적절한 예외 메시지와 실패 포착은 디버깅과 시스템 신뢰성을 높이는 데 핵심
