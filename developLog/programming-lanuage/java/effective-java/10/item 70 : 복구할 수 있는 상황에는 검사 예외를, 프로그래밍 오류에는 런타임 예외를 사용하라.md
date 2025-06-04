# item 70 : 복구할 수 있는 상황에는 검사 예외를, 프로그래밍 오류에는 런타임 예외를 사용하라

## 자바 예외 처리 가이드: 검사 예외, 런타임 예외, 에러의 사용법

자바는 문제 상황을 알리는 타입으로 **검사 예외(Checked Exception)**, **런타임 예외(Runtime Exception)**, **에러(Error)** 이렇게 세 가지를 제공한다. 언제 이 타입들을 사용해야 하는지 헷갈려 하는 프로그래머들이 종종 있다. 100% 명확한 규칙은 아니지만, 참고할 만한 지침들을 함께 살펴보자.

{% hint style="info" %}
검사 예외, 런타임 예외, 에러의 뜻을 알아보자
{% endhint %}

### 1) 예외의 의미와 사용 시점

검사 예외, 런타임 예외, 에러는 자바에서 오류를 처리하기 위한 예외의 세 가지 주요 유형이다. 각각의 의미는 다음과 같다:

1. **검사 예외 (Checked Exception)**
   * **정의**: 검사 예외는 컴파일러에 의해 체크되는 예외이다. 즉, 컴파일 단계에서 반드시 처리되거나 선언되어야 하는 예외를 말한다.
   * **사용 시점**: 일반적으로 **복구 가능한 문제**를 나타낸다. 예를 들어, 파일을 열 때 파일이 없거나 네트워크 연결이 실패할 수 있는 상황처럼 외부 환경에 의해 문제가 발생할 가능성이 있을 때 사용한다.
   * **특징**: 검사 예외가 발생할 가능성이 있는 메서드는 반드시 `throws` 키워드를 사용해 예외를 선언해야 하며, 호출자는 이 예외를 처리(`try-catch`)하거나 다시 던지도록 강제된다.
   * **예시**: `IOException`, `SQLException` 등이 있다.
2. **런타임 예외 (Runtime Exception)**
   * **정의**: 런타임 예외는 컴파일러가 강제하지 않는 예외로, 실행 중에 발생하는 예외를 말한다.
   * **사용 시점**: 주로 **프로그래밍 오류**로 인해 발생하는 예외이다. 예를 들어, 배열의 범위를 벗어난 인덱스에 접근하려고 할 때나, 잘못된 값으로 객체를 초기화할 때 사용된다. 이러한 오류는 대부분 개발자가 미리 수정해야 하는 문제이다.
   * **특징**: 런타임 예외는 코드에서 명시적으로 처리하지 않아도 된다. 이는 주로 프로그래머의 실수로 인해 발생하기 때문에, 코드의 논리적 결함을 수정하는 것이 더 적절하다고 여겨진다.
   * **예시**: `NullPointerException`, `ArrayIndexOutOfBoundsException`, `IllegalArgumentException` 등이 있다.
3. **에러 (Error)**
   * **정의**: 에러는 시스템 레벨의 문제로, 애플리케이션에서 직접적으로 처리할 수 없는 **심각한 오류**를 나타낸다.
   * **사용 시점**: 일반적으로 JVM(Java Virtual Machine)에서 발생하는 오류로, 프로그램의 실행을 지속할 수 없는 상황을 의미한다. 예를 들어, 메모리가 부족하거나 스택 오버플로우가 발생할 때 사용된다.
   * **특징**: 에러는 대부분 복구가 불가능하고, 애플리케이션 코드에서 이를 처리하지 않는 것이 일반적이다. 따라서 `Error`를 상속해서 사용자 정의 에러를 만드는 일은 피해야 한다.
   * **예시**: `OutOfMemoryError`, `StackOverflowError` 등이 있다.

이 세 가지 유형을 통해 자바는 다양한 오류 상황을 구분하고, 각 상황에 맞는 적절한 처리를 할 수 있도록 설계되었다. 검사 예외는 복구 가능한 상황을 위해, 런타임 예외는 프로그래밍 오류를 나타내기 위해, 에러는 시스템의 심각한 문제를 위해 사용된다.

### 2) 검사 예외(Checked Exception) 사용 시점

* **복구할 수 있는 상황**이라면 검사 예외를 사용하자. 검사 예외는 호출자가 그 예외를 `catch`로 잡아 처리하거나 더 바깥으로 전파하도록 강제한다.
* 메서드 선언에 포함된 검사 예외 각각은 해당 메서드를 호출했을 때 발생할 수 있는 유력한 결과임을 **API 사용자에게 알리는 역할**을 한다.
* API 설계자가 API 사용자에게 그 상황에서 **복구할 것을 요구**하는 것이다. 물론 사용자가 예외를 잡고 아무 조치도 취하지 않을 수도 있지만, 이는 보통 좋지 않은 생각이다.

#### 예제 코드: 검사 예외 사용

```java
public class BalanceChecker {
    public void checkBalance(double amount) throws InsufficientBalanceException {
        if (amount > getCurrentBalance()) {
            throw new InsufficientBalanceException("잔고가 부족합니다.");
        }
    }
}
```

* 위 예제에서 `checkBalance()` 메서드는 검사 예외인 `InsufficientBalanceException`을 던진다. 이 예외는 호출자가 반드시 처리해야 하므로, API 사용자는 이러한 상황에 대응하는 코드를 작성하게 된다.

### 3) 비검사 예외(Runtime Exception)와 에러(Error) 사용 시점

비검사 throwable은 두 가지로 나뉜다: 런타임 예외(Runtime Exception)와 **에러(Error)**. 이 둘은 동작 측면에서는 다르지 않으며, 프로그램에서 **잡을 필요가 없거나 통상적으로는 잡지 말아야** 한다.

* 프로그램에서 비검사 예외나 에러를 던졌다는 것은 복구가 불가능하거나 **더 이상 실행해봐야 득보다는 실이 많다는 뜻**이다.
* 이러한 throwable을 잡지 않은 스레드는 **적절한 오류 메시지**를 내뱉으며 중단된다.

#### 런타임 예외 사용 시점

* <mark style="color:red;">**프로그래밍 오류**</mark><mark style="color:red;">를 나타낼 때는 런타임 예외를 사용하자.</mark> 런타임 예외는 전제조건이 만족되지 않았을 때 발생하는 경우가 많다.
* 예를 들어, 배열의 인덱스가 잘못된 경우(`ArrayIndexOutOfBoundsException`)가 있다. 이는 클라이언트가 API 명세의 제약 조건을 지키지 않았다는 뜻이다.

#### 예제 코드: 런타임 예외 사용

```java
public class ArrayHandler {
    public int getElement(int[] array, int index) {
        if (index < 0 || index >= array.length) {
            throw new ArrayIndexOutOfBoundsException("잘못된 인덱스: " + index);
        }
        return array[index];
    }
    public static void main(String[] args) {
        ArrayHandler handler = new ArrayHandler();
        int[] myArray = {1, 2, 3};
        handler.getElement(myArray, 5);  // 여기서 런타임 예외 발생
    }
}
```

* 위 예제에서 `getElement()` 메서드는 인덱스가 잘못된 경우 `ArrayIndexOutOfBoundsException`을 던진다. 이는 **클라이언트가 잘못된 인덱스를 사용**했음을 알리는 것이다.

#### 에러(Error) 사용 시점

* **JVM이 자원 부족, 불변식 깨짐 등 더 이상 수행을 계속할 수 없는 상황**을 나타낼 때 사용한다.
* 에러는 보통 JVM에서 발생하며, 사용자가 직접 처리하지 않는 것이 일반적이다.
* **Error 클래스를 상속해 하위 클래스를 만드는 일은 자제**해야 하며, 직접 `throw`로 던지는 것도 지양해야 한다.

```java
public class ErrorExample {
    public static void main(String[] args) {
        try {
            recursiveMethod();
        } catch (StackOverflowError e) {
            System.err.println("스택 오버플로우 오류 발생: " + e.getMessage());
        }
    }

    public static void recursiveMethod() {
        recursiveMethod();  // 무한 재귀 호출로 StackOverflowError 발생
    }
}
```

* 위 예제에서 `getElement()` 메서드는 인덱스가 잘못된 경우 `ArrayIndexOutOfBoundsException`을 던진다. 이는 **클라이언트가 잘못된 인덱스를 사용**했음을 알리는 것이다.

### 4) 상태 검사 메서드와 대체 방안

상태 검사 메서드 대신 사용할 수 있는 선택지도 있다. 올바르지 않은 상태일 때 **빈 옵셔널(아이템 55)** 혹은 `null` 같은 특수한 값을 반환하는 방법이다. 상태 검사 메서드, 옵셔널, 특정 값 중 하나를 선택하는 지침을 몇 개 소개하겠다.

1. **외부 동기화 없이 여러 스레드가 동시에 접근할 수 있거나 외부 요인으로 상태가 변할 수 있다면** 옵셔널이나 특정 값을 사용한다. 상태 검사 메서드와 상태 의존적 메서드 호출 사이에 객체의 상태가 변할 수 있기 때문이다.
2. **성능이 중요한 상황에서 상태 검사 메서드가 상태 의존적 메서드의 작업 일부를 중복 수행한다면** 옵셔널이나 특정 값을 선택한다.
3. **다른 모든 경우에는 상태 검사 메서드 방식이 조금 더 낫다**. 가독성이 살짝 더 좋고, 잘못 사용했을 때 발견하기가 쉽다. 상태 검사 메서드 호출을 깜빡 잊었다면 상태 의존적 메서드가 예외를 던져 버그를 확실히 드러낼 것이다. 반면 특정 값은 검사하지 않고 지나쳐도 발견하기가 어렵다(옵셔널에는 해당하지 않는 문제다).

### 5) 잘못된 throwable 사용 피하기

* **Exception, RuntimeException, Error를 상속하지 않는 throwable을 만들지 말자**. 이는 API 사용자를 헷갈리게 하며, 정상적인 검사 예외보다 나을 게 없다.
* 예외에는 **상황에 관한 정보를 코드 형태로 전달하는 메서드**를 제공해야 한다. 오류 메시지를 파싱해 정보를 빼내는 방식은 대단히 나쁜 습관이다.

## 핵심 정리

* **복구할 수 있는 상황**이면 검사 예외를 던지고, **프로그래밍 오류**라면 비검사 예외를 던지자.
* 확실하지 않다면 비검사 예외를 던지는 것이 더 나을 수 있다.
* 검사 예외도 아니고 런타임 예외도 아닌 **특수한 throwable은 정의하지 말자**.
* 검사 예외라면 **복구에 필요한 정보를 알려주는 메서드**도 제공하자.

> 이러한 원칙들을 따르면 예외 처리에 대한 혼란을 줄이고, 코드의 가독성과 유지보수성을 높일 수 있다.

> 참고하면 좋은 글
>
> * [Checked Exception VS Unchecked Exception 비교](https://velog.io/@woosim34/Checked-Exception-VS-Unchecked-Exception-%EB%B9%84%EA%B5%90)
> * [예외 처리 방법과 종류](https://cocobi.tistory.com/146)
