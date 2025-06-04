# item 12 : toString을 항상 재정의하라

## 1. 모든 하위 클래스에서 toString을 재정의하라

Object의 기본 메서드 중 `toString`은 재정의하지 않으면 `phone@adbbd`처럼 단순히 **클래스명@해시코드**를 표현한다. toString의 규약은 모든 하위 클래스에서 이 메서드를 재정의하라이다.

&#x20;

`toString` 메서드는 객체를 `println`, `printf`, `+`, `assert`, `디버거` 등 을 출력할 때 자동으로 호출된다. 즉, 직접 호출하지 않아도 사용되는 경우가 많다. <mark style="color:red;">따라서 재정의는 필수적이며, 재정의 시에 객체가 가진 주요 정보를 모두 반환하는 것이 올바르다.</mark>

> toString을 제대로 재정의하지 않는다면 쓸모없는 메시지만 로그에 남을 것이다.

```
/**  
* Returns a string representation of this collection.  The string
* representation consists of a list of the collection's elements in the
* order they are returned by its iterator, enclosed in square brackets
* ({@code "[]"}).  Adjacent elements are separated by the characters
* {@code ", "} (comma and space).  Elements are converted to strings as
* by {@link String#valueOf(Object)}.
*
* @return a string representation of this collection
*/
public String toString() {
    Iterator<E> it = iterator();
    if (! it.hasNext())
        return "[]";

    StringBuilder sb = new StringBuilder();
    sb.append('[');
    for (;;) {
        E e = it.next();
        sb.append(e == this ? "(this Collection)" : e);
        if (! it.hasNext())
            return sb.append(']').toString();
        sb.append(',').append(' ');
    }
}
```

* 컬렉션의 `toString`은 상위 타입인 `AbstractCollection<E>` 추상 클래스에서 선언된 메서드대로 출력 된다.&#x20;
* 우리가 단순히 <mark style="color:red;">println(list) 등을 했을 때 값이 출력되는 부분이 해당 메서드 정의 덕분</mark>이다.

## 2.  toString을 구현 시 반환값의 포맷을 문서화할지 결정하자

> `toString`을 구현할 때 반환 값의 포맷을 문서화할 지 여부를 결정해야 한다. 전화번호나 행렬 같은 값 클래스라면 문서화를 권한다.&#x20;

포맷을 명시하기로 했다면, 명시한 포맷에 맞는 문자열과 객체를 상호 전환할 수 있는 정적 팩터리나 생성자를 함께 제공해주면 좋다.

자바 플랫폼의 많은 값 클래스가 따르는 방식이기도 하&#xB2E4;**. Biginteger, BigDecimal**과 대부분의 `기본 타입 클래스`가 여기 해당한다.

> 포맷을 명시하든 아니든 의도는 명확히 밝혀야 한다.

### 포맷을 명시한 예

* 장점 : 그 객체는 표준적이고, 명확하고, 사람이 읽을 수 있게 된다. 따라서 그 값 그대로 입출력에 사용하거나 CSV 파일처럼 사람이 읽을 수 있는 데이터 객체로 저장할 수도 있다.
* 단점 : 포맷을 한번 명시하면  (그 클래스가 많이 쓰인다면）평생 그 포 맷에 얽매이게 된다.

```java

/**
 * 이 전화번호의 문자열 표현을 반환한다.
 * 이 문자열은 "XXX-YYY-ZZZZ" 형태의 12글자로 구성된다.
 * XXX는 지역 코드, YYY는 프리픽스, ZZZZ는 가입자 번호다.
 * 각각의 대문자는 10진수 숫자 하나를 나타낸다.
 *
 * 전화번호의 각 부분의 값이 너무 작아서 자릿수를 채울 수 없다면,
 * 앞에서부터 0으로 채워나간다. 예를 들어 가입자 번호가 123이라면
 * 전화번호의 마지막 네 문자는 "0123"이다.
 */
 @Override
 public String toString() {
     return String.format("%03d-%03d-%04d",areaCode, prefix, lineNum);
 }
```

### 포맷을 명시하지 않은 예

* 장점 : 향후 릴리스에서 정보를 더 넣거나 포맷을 개선할 수 있는 유연성을 얻게 된다.

```java
 /**
 * 이 약물에 관한 대락적인 설명을 반환한다.
 * 다음은 이 설명의 일반적인 형태이나,
 * 상세 형식은 정해지지 않았으며 향후 변경될 수 있다.
 *
 * "[약물 #9: 유형=사랑, 냄새=테레빈유, 겉모습=먹물]"
 */
 @Override
 public String toString() { ... }
```

## **3.** String이 반환한 값에 포함된 정보를 얻어올 수 있는 API를 제공하자.

1. **`toString()`의 반환값에 포함된 정보를 얻을 수 있는 API를 제공해야 한다.**

* 예를 들어, `PhoneNumber` 클래스는 지역 코드, 프리픽스, 가입자 번호에 대한 접근자 메서드를 제공해야 한다.
* 그렇지 않으면 프로그래머는 필요한 정보를 얻기 위해 `toString()`의 반환값을 파싱해야  한다.
* 이는 성능 저하와 불필요한 작업을 초래하며, 향후 포맷 변경 시 시스템 오류를 발생시킬 수 있다.
* 접근자를 제공하지 않으면 포맷이 사실상 변경될 수 없는 준표준 API가 되어버린다.

2. **특정 클래스에서는 `toString()`을 재정의할 필요가 없다.**

* **정적 유틸리티 클래스**(아이템 4)는 `toString()`을 제공할 이유가 없다.
* **대부분의 열거 타입**(아이템 34)은 자바가 이미 완벽한 `toString()`을 제공하므로 재정의할 필요가 없다.
* 하지만 **하위 클래스들이 공유해야 할 문자열 표현이 있는 추상 클래스**라면 `toString()`을 재정의해야 한다.
  * 예를 들어, 대다수의 컬렉션 구현체는 추상 컬렉션 클래스의 `toString()` 메서드를 상속하여 사용한다.

2. **자동 생성된 `toString()`의 한계**

* AutoValue는 각 필드의 내용을 깔끔하게 표시하지만, 클래스의 `의미`까지 파악하지는 못합니다.
  * 예를 들어, `PhoneNumber` 클래스의 `toString()`은 자동 생성에 적합하지 않는다(전화번호는 표준 형식을 따라야 함).
  * 반면에 `Potion` 클래스는 자동 생성된 `toString()`이 적합한다.
* 자동 생성된 `toString()`이 적합하지 않더라도, 객체의 값에 대해 아무것도 알려주지 않는 `Object`의 `toString()`보다는 훨씬 유용

## 4. 부가 설명

### **1) `toString()`을 재정의하는 이유**

* **가독성 향상**: 객체를 사람이 이해하기 쉬운 문자열로 표현하여 디버깅, 로깅, 콘솔 출력 등에 유용하게 활용할 수 있다.
* **기본 구현의 한계**: `Object`의 기본 `toString()`은 클래스 이름과 해시 코드로 이루어져 있어 객체의 상태를 파악하는 데 도움이 되지 않는다.
* **API 설계 시 고려사항**
  * **정보 은닉**: `toString()`의 반환값에 의존하여 객체의 내부 상태를 파싱하지 않도록 해야 한다.
    * 필요한 정보는 **적절한 접근자 메서드**를 통해 제공하여 `toString()`의 구현과 무관하게 안정적인 API를 유지해야 한다.
    * 그렇지 않으면 `toString()`의 포맷 변경 시 의도치 않은 오류가 발생할 수 있다.
  * **포맷 변경의 위험성**: 프로그래머들이 `toString()`의 반환값을 파싱하게 되면, 포맷이 변경될 경우 시스템 전체에 영향을 미칠 수 있다.

### **2) 자동 생성된 `toString()`의 활용**

* **장점**: 클래스의 모든 필드를 나열하여 객체의 상태를 한눈에 파악할 수 있어 디버깅 시 유용하다.
* **한계**: 클래스의 **의미나 표준 형식**을 반영하지 못하므로, 특정 요구 사항이 있는 경우 수동으로 재정의해야 한다.
  * 예를 들어, 전화번호나 날짜처럼 **표준화된 형식**이 필요한 경우

### **3) 추상 클래스와 `toString()`**

* **공통 표현 제공**: 하위 클래스들이 공유해야 할 문자열 표현이 있다면, 추상 클래스에서 `toString()`을 재정의하여 일관된 표현을 제공할 수 있다.
* **상속 활용**: 구체 클래스들은 추상 클래스의 `toString()`을 상속받아 별도의 구현 없이도 적절한 문자열 표현을 가질 수 있다.
* **`toString()` 구현 시 주의사항**
  * **중요 정보 포함**: 객체의 핵심 정보를 포함하되, **민감한 정보나 보안에 취약한 데이터는 포함하지 않아야 한**다.
  * **포맷 명시 여부**:
    * **포맷을 명시하는 경우**: 반환되는 문자열의 형식을 API 문서에 상세히 기술하여 사용자들이 그 형식에 의존할 수 있게 한다.
    * **포맷을 명시하지 않는 경우**: 포맷이 변경될 수 있음을 명시하여 사용자들이 `toString()`의 반환값에 의존하지 않도록 한다.
  * **일관성 유지**: 동일한 객체 상태에 대해 항상 동일한 문자열을 반환하도록 한다.

### **4) Best Practices**

* **간결하고 이해하기 쉽게**: 복잡한 내부 구현보다는 객체의 중요한 상태를 간결하게 표현한다.
* **예외 처리**: `null` 값이나 예외 상황에서도 `toString()`이 예외를 던지지 않고 의미 있는 정보를 반환하도록 한다.
* **표준 형식 준수**: 날짜, 시간, 수치 등의 표현에서는 지역화나 국제 표준을 고려
*   **예시**

    ```java
    public class PhoneNumber {
        private final int areaCode;
        private final int prefix;
        private final int lineNumber;

        // 접근자 메서드 제공
        public int getAreaCode() { return areaCode; }
        public int getPrefix() { return prefix; }
        public int getLineNumber() { return lineNumber; }

        @Override
        public String toString() {
            return String.format("(%03d) %03d-%04d", areaCode, prefix, lineNumber);
        }
    }
    ```

    * **설명**:
      * `toString()`은 표준 전화번호 형식을 따르도록 수동으로 재정의되었다.
      * 필요한 정보를 얻기 위해 `getAreaCode()`, `getPrefix()`, `getLineNumber()` 접근자 메서드를 제공한다.
      * 이렇게 함으로써 `toString()`의 포맷 변경이 있더라도 외부에 미치는 영향을 최소화할 수 있다.

## **✨** 결론

* **모든 구체 클래스에서 `Object`의 `toString()`을 재정의하자.**
  * 상위 클래스에서 이미 알맞게 재정의한 경우는 예외
* `toString()`을 재정의한 클래스는 **사용하기도 즐겁고**, 그 클래스를 사용한 시스템을 **디버깅하기 쉽게** 해준다.
* `toString()`은 해당 객체에 관한 **명확하고 유용한 정보**를 **읽기 좋은 형태**로 반환해야 한다.

#### 스터디

* 민감한 정보 마스킹작업..? toString 어떻게 처리하는 지 찾아보자

<figure><img src="../../../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>



> 참고글 : [https://sasca37.tistory.com/251](https://sasca37.tistory.com/251)
