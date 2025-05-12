# item 49 : 매개변수가 유효한지 검사하라

## 1. 오류 검사의 일반 원칙1: 오류를 즉시 잡아라

> 오류는 가능한 한 빨리 발생한 곳에서 잡아야 한다.

오류를 발생한 즉시 잡지 못하면 해당 오류를 감지하기도 어렵고, 정확히 어디서 발생했는지 알기도 어렵다.

### 1) 다양한 매개변수 검사 예시 (가장 간단한 원칙)

1. 인덱스 값은 음수이면 안 된다.
2. 객체 참조는 null 이 아니어야 한다.

### 2) 매개변수 검사를 제대로 하지 못했을 때 벌어지는 일

> 아래와 같이 실패 원자성(_아이템 74_)을 어기는 문제가 발생한다.

1. 메서드가 수행되다가 모호한 예외를 던지며 실패한다.
2. 메서드가 잘 수행되지만 잘못된 결과를 반환한다.
3. 메서드에서 사용한 다른 객체를 이상한 상태로 만들어 미래의 알 수 없는 시점에 문제가 발생한다.

> 아래 단계의 현상일수록 문제는 더욱 심각해진다.

**예외의 문서화**

자바독의 `@throws` 태그를 이용해서 가능하다.

IllegalArgumentException, IndexOutOFBoundsException, NullPointerException 중 하나가 될 것이다.

{% hint style="danger" %}
메서드와 생성자는 입력 매개변수의 값이 특정 조건을 만족해야 하는 경우가 많은데, 이러한 경우 오류는 가능한 한 빨리 검사해야 하는 것이 좋으므로 **메서드 몸체가 시작되기 전에 매개변수를 검사하는 것이 좋다.**
{% endhint %}

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## 2. 매개변수를 검사하는 방법 : 공개 메서드 <a href="#id-1" id="id-1"></a>

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 1) @throws <a href="#throws" id="throws"></a>

public과 protected 메서드는 매개변수 값이 잘못됐을 때 던지는 예외를 `@throws` 자바독 태그를 활용해 문서화해야 한다.

발생하는 예외는 보통 `IllegalArgumentException`, `IndexOutOfBoundsException`, `NullPointerException` 중 하나가 될 것이다(_아이템 72_)

```java
/**
(현재 값 mod m)을 반환한다. 
...
@param m 계수(양수여야 한다.)
@return 현재 값 Mod m
@thorws ArithmeticException m이 0보다 작거나 같으면 발생한다.
*/
public BigInteger mod(BigInteger m){
	if (m.signum() <= 0){
    	throw new ArithmeticException("계수(m)는 양수여야 합니다. " + m);
    ...
}
```

* 이 메서드의 @throws 태그는 0보다 작거나 같은 값의 나머지를 구하려 하면, ArithmeticException이 발생한다고 친절하게 알려주고 있다.

```
All methods and constructors in this class throw {@code NullPointerException}
when passed a null object reference for any input parameter
```

클래스 전체 설명에는 위와 같이 모든 메서드와 생성자가 `NullPointerException`을 던질 수 있다는 점을 내포하고 있다는 점도 볼만하다. 그래서 다른 메서드들에 따로 @throws NullPointerException을 적어주진 않고 있다.

### 2) null 검사시 requireNonNull <a href="#null-requirenonnull" id="null-requirenonnull"></a>

`@Nullable` 이나 비슷한 어노테이션을 사용하여 특정 매개변수는 null이 될 수 있다고 알려줄 수 있지만, 표준적인 방법은 아니다.

대신 자바 7에서 추가된  `java.util.Objects. requireNonNull` 메서드를 사용한다면 더 이상 null 검사를 수동으로 하지 않아도 된다.

원하는 예외 메시지도 지정할 수 있다. 또한 입력을 그대로 반환하므로 값을 사용하는 동시 에 null 검사를 수행할 수 있다

▶️ 생성자에서 Null 검사 기능 활용

```java
this.strategy = Objects.requireNonNull(strategy, "전략");  // 예외 메시지 지정
```

* 반환값은 그냥 무시하고 필요한 곳 어디서든 순수한 null 검사 목적으로 사용 해도 된다.

### 3) Objects 범위 검사 <a href="#objects" id="objects"></a>

자바 9부터는 `checkFromIndexSize()`, `checkFromToIndex()`, `checkIndex()` 와 같은 메서드들이 추가되면서 Objects에 범위 검사 기능도 더해졌다.

단 예외 메시지를 지정할 수 없으며 리스트와 배열 전용으로 설계되었다. 또한 닫힌 범위(양 끝단의 값을 포함하는)는 다루지 못한다는 단점이 존재한다.

그래도 이런 제약이 걸림돌이 되지 않는 상황에서는 아주 유용하고 편하다.

**Java 7에서 추가된 `null` 검사 기능: `Objects.requireNonNull()`**

`Objects.requireNonNull()`은 Java 7에서 추가된 메서드로, 전달받은 매개변수가 `null`인지 검사하고 \*`null`이라면 `NullPointerException`을 던진다.&#x20;

> 주로 메서드 인수의 **유효성 검사**에 사용되며, `null`로 인해 발생할 수 있는 오류를 미리 방지한다.

아래는 `Objects.requireNonNull()`의 사용 예제

```java
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;

import java.util.Objects;

public class Item49Test {

    @Test
    public void requireNonNullTest() {
        // null 값을 전달하여 NullPointerException이 발생하는지 확인
        Assertions.assertThrows(NullPointerException.class, () -> {
            nullTest(null);
        });
    }

    public void nullTest(Object obj) {
        // 전달받은 객체가 null인지 확인하고, null일 경우 예외 던지기
        Objects.requireNonNull(obj, "널이 들어왔음");
    }
}
```

* `Objects.requireNonNull()`의 두 번째 인수로 전달된 문자열은 예외 메시지로 사용된다. 여기서는 `"널이 들어왔음"` 메시지가 포함되어 예외 발생 시 문제를 더 명확하게 알 수 있다.
* 이 메서드는 메서드 호출 시 `null`로 인해 발생할 수 있는 문제를 **사전에 방지**할 수 있도록 도와준다.

## 3. 매개변수를 검사하는 방법 : 비공개 메서드 <a href="#id-2" id="id-2"></a>

<figure><img src="../../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 1) **Java의 `assert` 구문을 사용한 `null` 검사** <a href="#assert" id="assert"></a>

공개되지 않은 메서드라면, 패키지 제작자가 직접 메서드가 호출되는 상황을 통제 가능하기 때문에 오직 유효한 값만이 메서드에 넘겨지리라는 것을 보증해야 한다.

**즉, public 이 아닌 메서드라면 단언문(assert)를 사용해 매개변수 유효성을 검사하자.** 핵심은 단언문들이 자신이 단언한 조건이 무조건 참이라고 선언한다는 것이다.&#x20;

Java의 **`assert`** 구문을 사용하여도 `null` 검사를 수행할 수 있다. `assert`는 특정 조건이 **참인지 확인**하며, 조건이 **거짓**인 경우 `AssertionError`를 던진다. 이는 주로 **디버깅** 목적으로 사용되며, **런타임** 시에 활성화할 수 있다.

```java
private static void sort(long a[], int offset, int length){
	assert a != null;
    assert offset >= 0 && offset <= a.length;
    assert length >= 0 && length <= a.length - offset;
    ...// 계산 수행
}
```

> 🔖 **단언문(assert) 특징**\
> 1\) 실패시 AssertionError를 던진다.\
> 2\) 런타임에 아무런 효과도, 아무런 성능 저하도 없다.

**메서드가 직접 사용하지 않으나 나중에 쓰려고 저장하는 매개변수의 유효성**은 더욱 신경 써서 검사해야 한다. 생성자 매개변수의 유효성 검사는, 클래스 불변식을 어기는 객체가 만들어지지 않게 하는데 꼭 필요하다.

```java
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;

public class Item49Test {

    @Test
    public void requireNonNullTest2() {
        // null 값을 전달하여 AssertionError가 발생하는지 확인
        Assertions.assertThrows(AssertionError.class, () -> {
            nullTest2(null);
        });
    }

    public void nullTest2(Object obj) {
        // 객체가 null인지 검사, null이면 AssertionError 발생
        assert obj != null;
    }
}
```

* **`assert obj != null;`**: `obj`가 `null`이 아닌지 확인하는 조건입니다. 조건이 만족되지 않으면 `AssertionError`가 발생
* `Assertions.assertThrows()`를 통해 **AssertionError**가 발생하는지 테스트한다.

`assert`는 주로 **개발 중**에 사용되며, 프로덕션 환경에서는 활성화되지 않을 수 있다. 이 때문에 중요한 유효성 검사에는 `Objects.requireNonNull()`과 같은 명시적인 검사를 사용하는 것이 좋다.

### **4. 오류 검사의 일반 원칙 2: 나중에 쓰려고 저장하는 매개변수의 유효성 검사**

<figure><img src="../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

어떤 메서드에서 반환된 값을 **나중에 사용하기 위해 보관**할 때, 해당 값이 `null`인지 확인하지 않으면 **예상치 못한 오류**가 발생할 수 있다.&#x20;

> 나중에 이 값이 `null`임을 발견하게 되면 **오류의 원인을 추적하는 것이 매우 어려울 수 있으므로**, **저장하기 전에 유효성을 검사**하는 것이 중요하다.

#### &#x20;**유효성 검사의 중요성 예시**

만약 어떤 메서드에서 반환된 객체를 나중에 사용하기 위해 **저장**해 두고, `null`인지 확인하지 않은 경우 **예상치 못한 오류**가 발생할 수 있다. 이를 방지하기 위해 저장하기 전에 유효성을 검사하는 것이 매우 중요하다.

아래는 **유효성 검사를 통해 `null` 방지**하는 코드 예시

```java
import java.util.Objects;

public class UserManager {

    // 사용자 정보를 저장하는 메서드
    public void storeUser(User user) {
        // 저장하기 전에 유효성 검사 수행
        Objects.requireNonNull(user, "User 객체가 null입니다. 저장할 수 없습니다.");
        // 유효성 검사 후 안전하게 객체 저장
        // ...
    }
    
    // User 클래스는 단순한 데이터 객체로 가정
    static class User {
        String name;
        
        public User(String name) {
            this.name = name;
        }
    }
}
```

* **`Objects.requireNonNull(user, "User 객체가 null입니다. 저장할 수 없습니다.")`**: 사용자 객체가 `null`인지 검사한다. 이로 인해 **저장하기 전에 `null` 값을 방지**하여 예기치 않은 오류를 예방할 수 있다.
* 이를 통해 나중에 **null 포인터 문제**로 발생할 수 있는 오류를 미리 방지

### **일반 원칙 2의 예외 상황**

1. **유효성 검사 비용이 지나치게 높거나 실용적이지 않을 때**:
   * 유효성 검사에 큰 비용이 든다면, 실용적이지 않을 수 있다.
2. **계산 과정에서 암묵적으로 검사가 수행될 때**:
   * 예를 들어, `Collections.sort(List)`의 경우 **정렬 과정**에서 **타입의 유효성**이 검사된다. 만약 상호 비교될 수 없는 타입이 들어 있다면 `ClassCastException`을 던진다.
   * **암묵적 유효성 검사**에 의존하는 것은 실패 원자성(safety failure)을 해칠 수 있으므로 주의가 필요
     * **실패 원자성**이란, 유효성 검사 실패 이후에도 객체의 상태가 변하지 않도록 보장하는 것이다. 즉, 메서드가 여러 번 실패하더라도 **항상 같은 결과를 반환**해야 한다.  예를 들어, 데이터를 추가하는 도중 오류가 발생하더라도 데이터가 절반만 추가되는 상황을 방지하는 것이 실패 원자성이다.

```java
import java.util.ArrayList;
import java.util.List;

public class ProductManager {
    private final List<String> products = new ArrayList<>();

    // 제품 추가 메서드
    public void addProduct(String product) {
        if (product == null) {
            throw new IllegalArgumentException("제품 이름은 null이 될 수 없습니다.");
        }

        try {
            products.add(product);
        } catch (Exception e) {
            // 오류 발생 시 리스트를 변경하기 전 상태로 되돌리기
            products.remove(product);
            throw e; // 예외를 다시 던짐
        }
    }

    public List<String> getProducts() {
        return new ArrayList<>(products); // 방어적 복사
    }
}
```

* **`addProduct(String product)`** 메서드는 제품을 리스트에 추가하는 메서드
* **제품 이름이 `null`이면 예외를 던집니다**. 유효성 검사를 통해 **`null` 값으로 인한 문제**를 방지한다.

> 추가 도중 오류가 발생하면, **리스트에서 해당 제품을 제거**하여 **리스트 상태가 변경되지 않도록** 한다. 이렇게 함으로써 **실패 원자성**을 유지한다.

**계산 중 던져지는 예외와 API 예외의 차이**

* 계산 중에 던져지는 예외와 API에서 던지기로 한 예외가 **다를 수** 있다.
* 이 경우 **저수준 예외를 잡아 고수준의 사용자 정의 예외로 변환**하는 예외 번역(exception translation)이 필요하다.
  * 예를 들어, 내부적으로 발생하는 `ClassCastException`을 더 의미 있는 `InvalidDataException`과 같은 사용자 정의 예외로 변환하여 제공할 수 있다.

**계산 중에 던져지는 저수준 예외**와 API에서 던지기로 한 **고수준 예외**가 다를 수 있다. 이때는 저수준 예외를 잡아 **의미 있는 고수준 사용자 정의 예외**로 변환해주는 예외 번역(exception translation)이 필요

```java
import java.util.List;

public class DataService {

    // 데이터를 검색하는 메서드
    public String findData(List<String> data, int index) {
        try {
            // 인덱스를 통해 데이터를 검색
            return data.get(index);
        } catch (IndexOutOfBoundsException e) {
            // 저수준 예외인 IndexOutOfBoundsException을 사용자 정의 예외로 변환
            throw new DataNotFoundException("데이터 인덱스가 잘못되었습니다: " + index, e);
        }
    }

    // 사용자 정의 예외
    public static class DataNotFoundException extends RuntimeException {
        public DataNotFoundException(String message, Throwable cause) {
            super(message, cause);
        }
    }

    public static void main(String[] args) {
        DataService service = new DataService();
        List<String> data = List.of("apple", "banana", "cherry");

        try {
            // 잘못된 인덱스를 전달하여 예외 발생
            service.findData(data, 5);
        } catch (DataNotFoundException e) {
            System.err.println("오류 발생: " + e.getMessage());
        }
    }
}
```

* **`findData(List<String> data, int index)`** 메서드는 주어진 인덱스에서 데이터를 검색한다.
* 만약 인덱스가 잘못되면 `IndexOutOfBoundsException`이 발생한다. 이 예외를 그대로 던지는 대신, `DataNotFoundException`이라는 **사용자 정의 예외**로 변환
* 이렇게 **저수준 예외를 고수준 예외로 번역**하여 더 의미 있는 정보를 제공할 수 있다.

> `DataNotFoundException`은 **고수준 예외**로, 예외 메시지를 통해 문제가 무엇인지 더 명확하게 알 수 있다.

## 5. 예외 <a href="#id-3" id="id-3"></a>

메서드 몸체 실행 전에 매개변수 유효성을 검사해야 한다는 규칙에도 예외는 존재한다. 바로, 유효성 검사 비용이 지나치게 높거나 실용적이지 않을 때, 혹은 계산 과정에서 암묵적으로 검사가 수행될 때이다.

예를 들어 후자의 경우, `Collections.sort(List)` 처럼 객체 리스트를 정렬하는 메서드를 생각해보면 리스트 안의 객체들은 모두 상호 비교되어 있어야 하며 비교될 수 없는 타입의 객체가 들어있다면 ClassCastException을 던진다. 하지만 암묵적 유효성 검사에 너무 의존한다면 실패 원자성(_아이템 76_)을 해칠 수 있으니 주의하자.

<figure><img src="../../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## 📚 핵심 정리

> 메서드나 생성자를 작성할 때는 매개변수들에 어떠한 제약이 있을 지 생각해야 한다.&#x20;

그 제약들을 문서화하고 메서드 코드 시작 부분에서 명시적으로 검사하면, 유효성 검사가 실제 오류를 처음 걸러낼 때 효과를 볼 것이다.&#x20;

* 에러가 날만한 부분을 미리 걸러내서 나중에 복잡한 에러를 만들지 말자.

> 단, 무작정 매개변수에 많은 제약을 걸진 말자. 메서드는 최대한 범용적으로 작성되는 것이 맞다.

* 메서드 작성 시, 매개변수의 **유효성 검사는 반드시** 필요합니다. 특히 `null` 검사를 통해 예상치 못한 **NullPointerException**을 방지하고, 더 **견고한 코드**를 작성할 수 있다.
* Java의 `assert`는 **개발 중 디버깅 목적으로는 유용**하지만, 프로덕션 환경에서의 **유효성 검사에는 적합하지 않다**. 따라서 중요한 검사에는 `Objects.requireNonNull()`과 같은 명시적인 방식을 사용하는 것이 좋다.
* 매개변수 유효성 검사 시 **비용과 실용성**을 함께 고려하고, 무조건 모든 경우를 검사하기보다는 **필요한 경우에만** 적용하여 효율적인 코드를 작성하는 것이 좋다.

> 출처 및  참고&#x20;
>
> * [https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-49-%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98%EA%B0%80-%EC%9C%A0%ED%9A%A8%ED%95%9C%EC%A7%80-%EA%B2%80%EC%82%AC%ED%95%98%EB%9D%BC](https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-49-%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98%EA%B0%80-%EC%9C%A0%ED%9A%A8%ED%95%9C%EC%A7%80-%EA%B2%80%EC%82%AC%ED%95%98%EB%9D%BC)
> * [https://jake-seo-dev.tistory.com/515](https://jake-seo-dev.tistory.com/515)
