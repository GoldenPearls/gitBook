# item 14 : Comparable 을 구현할지 고려하라

## 1. compareTo

### ConpateTo란?

{% hint style="info" %}
Comparable 인터페이스의 유일무이한 메서드로 단순 동치성 비교에 더해 순서까지 비교할 수 있으며, 제네릭하다.[^1]
{% endhint %}

* 3장의  다른 메서드들과 달리 compareTo는 Object의 메서드가 아니다.
* 성격은 2가지를 제외하면, Object equals와 같음
* compareTo 를 구현했다는 것은, <mark style="color:red;">순서가 존재하다는 것이고, Arrays.sort 를 활용한 정렬이 가능</mark>하다는 것

```java
public class WordList {
    public static void main(String[] args) {
        Set<String> s = new TreeSet ();
        Collections.addAll(s, args); 
        System.out.println(s);
    } 
}
```

* [String이 CompareTo 를 구현한 덕분](#user-content-fn-2)[^2]에 자동 정렬되는 TreeSet 자료구조 상 출력하면 알파벳순으로 정렬되어 출력

> 사실상 자바 플랫폼 라이브러리의 모든 값 클래스와 열거 타입(아이템 34)이 Comparable을 구현했다. **알파벳, 숫자, 연대 같이 순서가 명확한 값 클래스를 작성한다면 반드시 Comparable 인터페이스를 구현**하자.

## 2. CompareTo 일반규약

```java
public interface Comparable<T> { 
    int compareTo(T t);
}
```

### **한 눈의 정리** 🔥

{% hint style="info" %}
`compareTo`가 `equals`와 일관성을 유지하는 것이 권장되지만, 이는 필수는 아니다. 대칭성과 추이성은 필수이다. 하지만만약 `compareTo`와 `equals`가 일관되지 않으면 `TreeSet` 같은 정렬된 컬렉션에서 예상치 못한 동작이 발생할 수 있다.
{% endhint %}

1. **대칭성**: `x.compareTo(y)`가 음수면, `y.compareTo(x)`는 양수여야 하고, `x.compareTo(y)`가 0이면 `y.compareTo(x)`도 0이어야 한다.
2. **추이성**: `x.compareTo(y)`가 양수이고, `y.compareTo(z)`가 양수라면, `x.compareTo(z)`도 양수여야 한다.
3. **일관성**: `x.compareTo(y)`가 0이면, `x.compareTo(z)`와 `y.compareTo(z)`는 같은 값을 가져야 한다.

### 부가적인 내용 ⚡

모든 객체에 대해 전역 동치관계를 부여하는 equals 메서드와 달리, `compareTo`는 **타입이 다른 객체를 신경 쓰지 않아도 된다.**

타입이 다른 객체가 주어지면 간단히 `ClassCastException`을 던져도 된다. compareTo 규약을 지키지 못하면 비교를 활용하는 클래스와 어울리지 못한다.

### `compareTo` 메서드의 구현과 `equals`와의 일관성 유지의 중요성

{% hint style="info" %}
`compareTo` 메서드의 마지막 규약은 필수 사항은 아니지만, **반드시 지키는 것이 좋다**. 이 규약은 간단히 말해, **`compareTo` 메서드를 통해 수행한 동치성 테스트의 결과가 `equals` 메서드의 결과와 일치해야 한다**는 것이다. 이 규약을 잘 지키면 `compareTo`에 따른 정렬 순서와 `equals`의 결과가 일관되게 된다.
{% endhint %}

그러나 `compareTo`의 순서와 `equals`의 결과가 일치하지 않는 <mark style="color:red;">클래스도 여전히 동작은 한다.</mark> 다만, 이러한 클래스를 **정렬된 컬렉션**(예: `TreeSet`, `TreeMap`)에 넣으면 해당 컬렉션이 구현한 인터페이스(`Collection`, `Set`, `Map`)의 동작과 **엇박자가 발생할 수 있다**. 이는 이 인터페이스들이 `equals` 메서드의 규약을 따르도록 정의되어 있지만, **정렬된 컬렉션은 동치성 비교 시 `equals` 대신 `compareTo`를 사용하기 때문이**다.



**권장 사항:**

* 가능하다면 `x.equals(y)`가 `true`일 때 `x.compareTo(y)`는 반드시 `0`을 반환하도록 구현해야 한다.
* 반대로 `x.compareTo(y)`가 `0`일 때 `x.equals(y)`가 `true`가 되도록 구현하면 더욱 좋다.

***

#### `BigDecimal` 클래스의 예시

`compareTo`와 `equals`가 <mark style="color:red;">일관되지 않는 클래스</mark>로 `BigDecimal`을 예로 들 수 있다.&#x20;

{% hint style="info" %}
`BigDecimal` 클래스에서 `equals`는 정밀도(precision)까지 고려하여 비교한다.

* 예를 들어, `new BigDecimal("1.0")`과 `new BigDecimal("1.00")`은 `equals`로 비교하면 **다른 객체**로 간주
* 하지만 `compareTo`는 **수학적인 값**만을 비교하므로, 두 객체를 **동일한 값**으로 간주
* 이로 인해 `HashSet`과 `TreeSet`에서 원소의 개수가 달라지는 문제가 발생한다.
{% endhint %}

```java
import java.math.BigDecimal;
import java.util.HashSet;
import java.util.TreeSet;

public class BigDecimalExample {
    public static void main(String[] args) {
        // HashSet 예제
        HashSet<BigDecimal> hashSet = new HashSet<>();
        hashSet.add(new BigDecimal("1.0"));
        hashSet.add(new BigDecimal("1.00"));
        System.out.println("HashSet 크기: " + hashSet.size()); // 출력: 2

        // TreeSet 예제
        TreeSet<BigDecimal> treeSet = new TreeSet<>();
        treeSet.add(new BigDecimal("1.0"));
        treeSet.add(new BigDecimal("1.00"));
        System.out.println("TreeSet 크기: " + treeSet.size()); // 출력: 1
    }
}
```

**설명:**

* `HashSet`에 `new BigDecimal("1.0")`과 `new BigDecimal("1.00")`을 추가하면, `equals` 메서드로 비교하여 두 객체는 **다르다고 판단**하므로 `HashSet`의 원소는 2개가 된다.
* 반면에 `TreeSet`은 `compareTo` 메서드로 비교하여 두 객체를 **같다고 판단**하므로 `TreeSet`의 원소는 1개가 된다.
* 이로 인해 `HashSet`과 `TreeSet`에서 같은 `BigDecimal` 객체를 사용하더라도 **원소의 개수가 달라지는 문제**가 발생한다.

> 🤔 음.. 이거는 HashSet과 TreeSet 의 자료구조의 문제 아냐?

답변 : 이러한 현상은 **`BigDecimal` 클래스의 설계와 컬렉션 프레임워크의 동작 방식의 차이**로 인해 발생하는 것입니다. 일반적으로 **`equals()`와 `compareTo()` 메서드는 일관되게 동작하도록 구현하는 것이 권장**됩니다. 즉, `equals()`로 같다고 판단되는 객체는 `compareTo()`로 비교했을 때 `0`을 반환해야 합니다.

하지만 `BigDecimal` 클래스는 정밀도(precision)를 중요시하기 때문에 `equals()` 메서드에서 스케일까지 고려하도록 설계되었습니다. 반면에 수치적인 비교를 위한 `compareTo()`는 스케일을 무시하고 값만 비교하도록 되어 있습니다.

따라서 이는 **클래스의 설계 의도에 따른 결과**이며, 컬렉션 프레임워크의 동작과 충돌할 수 있는 부분입니다. 이러한 차이로 인해 발생하는 문제는 개발자가 인지하고 조심해야 할 부분이지, Java 언어나 컬렉션 프레임워크의 구조적인 결함이라고 보기는 어렵습니다.

**따라서, 구조적인 문제라기보다는 해당 클래스와 컬렉션 사용 시 주의해야 할 사항**으로 보는 것이 맞습니다



> 🤔  하지만 위의 코드는 compareTo메서드가 아니라 size라는 메서드를 쓴거 아닌가?

* 예제 코드에서 `size()` 메서드를 사용한 것은 **집합에 실제로 몇 개의 원소가 저장되었는지 확인하기 위한 것**입니다.
* **원소의 개수가 달라지는 이유는 `HashSet`과 `TreeSet`이 원소의 동일성을 판단하는 기준이 다르기 때문**입니다.
  * `HashSet`은 `equals()`와 `hashCode()`를 사용하고,
  * `TreeSet`은 `compareTo()`를 사용합니다.
  * **`BigDecimal` 클래스의 `equals()`와 `compareTo()` 메서드가 일관되지 않게 동작하기 때문에** 이런 차이가 발생합니다.

{% hint style="info" %}
즉, **`size()` 메서드를 호출하기 전에 원소를 추가하는 과정에서 `compareTo()`와 `equals()` 메서드가 어떻게 동작하는지가 집합의 원소 개수에 영향을 미치게 됩니다**.
{% endhint %}

1. **`HashSet`의 동작 원리**

* `HashSet`에 원소를 추가할 때, **원소의 `hashCode()` 값을 사용하여 버킷을 결정**합니다.
* **이미 같은 `hashCode()`를 가진 원소가 있는 경우**, **`equals()` 메서드를 사용하여 두 원소가 동일한지 비교**합니다.
* **`equals()` 메서드가 `false`를 반환하면**, 해당 원소는 집합에 **새로운 원소로 추가**됩니다.

**2. `TreeSet`의 동작 원리**

* `TreeSet`은 **이진 탐색 트리**를 기반으로 구현되어 있으며, **원소를 정렬된 순서로 유지**합니다.
* 원소를 추가할 때, **`compareTo()` 메서드나 제공된 `Comparator`를 사용하여 원소의 순서와 동일성을 판단**합니다.
* **`compareTo()` 메서드가 `0`을 반환하면**, 두 원소는 **동일한 것으로 간주되어 새로운 원소로 추가되지 않는다.**

3. **해결 방법** 🍁

* **`compareTo()`와 `equals()`의 구현을 일관되게 수정**한다.
  * 하지만 `BigDecimal` 클래스는 Java 표준 라이브러리의 클래스이므로 우리가 수정할 수 없다.
* **커스텀 Comparator를 사용하여 `TreeSet`의 동작을 수정**한다.
  * 스케일까지 고려하도록 Comparator를 정의하면 `TreeSet`에서도 원소의 개수가 2개가 됨

> `compareTo` 메서드를 올바르게 구현하고, `equals`와의 일관성을 유지하는 방법을 이해할 수 있다. 이는 코드의 신뢰성과 유지보수성을 높이고, 컬렉션 프레임워크를 사용할 때 발생할 수 있는 오류를 예방하는 데 중요

🔥 **컬렉션 사용 시 주의사항**

* 컬렉션에 원소를 추가할 때, 해당 컬렉션이 원소의 동일성을 어떤 기준으로 판단하는지 알아야 한다.
* equals()와 compareTo() 메서드의 구현이 일관되지 않으면 컬렉션에서 예기치 않은 동작이 발생할 수 있다.

## 3. `compareTo` 메서드 작성 요령

`compareTo` 메서드를 작성할 때는 `equals` 메서드와 비슷한 요령을 따르지만, 몇 가지 차이점을 주의해야 한다.

1. **타입 검사와 형변환 불필요:**
   * `Comparable`은 제네릭 인터페이스이므로, `compareTo` 메서드의 인수 타입은 **컴파일 타임에 정해진다.**
   * 인수의 타입이 잘못되면 컴파일 자체가 되지 않으므로, 런타임 타입 검사나 형변환이 필요 없다.
2. **`null` 처리:**
   * `compareTo` 메서드에 `null`을 인수로 넣으면 **`NullPointerException`을 던지는 것이 일반적이다.**
   * 실제로도 인수(`null`)의 멤버에 접근하려는 순간 이 예외가 발생한다.
3. **필드 비교 방법:**
   * `compareTo` 메서드는 각 필드가 **동치인지**를 비교하는 것이 아니라, **순서를 비교한**다.
   * 기본 타입 필드는 `<`, `>` 연산자를 사용하여 비교한다.
   * **객체 참조 필드**는 해당 클래스의 `compareTo` 메서드를 재귀적으로 호출하여 비교한다.
   * Comparable을 <mark style="color:red;">구현하지 않은 필드나 표준이 아닌 순서로 비교해야 하는 경우</mark>에는 `Comparator`를 사용한다.

## 4. `compareTo` 메서드 구현과 `Comparator` 활용

{% hint style="info" %}
자바  7이후부터는compareTo 메서드에서 관계 연산자 ＜와 ＞ 를 사용하는 이전 방식은 거추장스럽고 오류를 유발하니 , 이제는 추천하지 않는다. **박싱된 기본 타입 클래스**들에 새로 추가된 정적 메서드인 `compare`를 이용하면 된다.
{% endhint %}

### 1) 여러 필드를 비교하는 `compareTo` 메서드 구현

클래스에 **핵심 필드가 여러 개**라면 **어느 것을 먼저 비교하느냐**가 중요해진다. **가장 중요한 필드부터 비교**해나가는 것이 좋다.

* **비교 결과가 0이 아니라면**, 즉 순서가 결정되면 **그 결과를 곧장 반환**한다.
* 가장 중요한 필드가 같다면, **그다음으로 중요한 필드**를 비교해나간다.

예시: `PhoneNumber` 클래스의 `compareTo` 메서드

```java
public int compareTo(PhoneNumber pn) {
    int result = Short.compare(areaCode, pn.areaCode); // 가장 중요한 필드 비교
    if (result == 0) {
        result = Short.compare(prefix, pn.prefix); // 두 번째로 중요한 필드 비교
        if (result == 0) {
            result = Short.compare(lineNum, pn.lineNum); // 세 번째로 중요한 필드 비교
        }
    }
    return result;
```

* `short.compare`를 사용하여 `short` 타입 필드를 비교한다.
* 이렇게 하면 코드가 간결해지고, 오버플로우 등의 문제를 방지할 수 있다.

### **2) `Comparator` 생성 메서드를 활용한 `compareTo` 구현**

**자바 8부터는** `Comparator` 인터페이스에 **비교자 생성 메서드**들이 추가되어, 메서드 연쇄 방식으로 비교자를 생성할 수 있다. 이를 활용하면 `compareTo` 메서드를 더 간결하게 구현할 수 있다.

**예시: `PhoneNumber` 클래스의 `compareTo` 메서드**

```java
import static java.util.Comparator.comparingInt;

private static final Comparator<PhoneNumber> COMPARATOR =
    comparingInt((PhoneNumber pn) -> pn.areaCode)
        .thenComparingInt(pn -> pn.prefix)
        .thenComparingInt(pn -> pn.lineNum);

@Override
public int compareTo(PhoneNumber pn) {
    return COMPARATOR.compare(this, pn);
}
```

* `comparingInt` 메서드는 키 추출 함수를 받아 그 키를 기준으로 비교하는 `Comparator`를 생성
* `thenComparingInt` 메서드를 사용하여 추가적인 필드를 순차적으로 비교한다.

**주의사항:**

* 이 방식은 코드의 간결함을 제공하지만, **약간의 성능 저하**가 있을 수 있다. 테스트 결과 약 **10% 정도 느려질 수 있다**.
* 따라서 성능이 중요한 상황에서는 전통적인 방식으로 구현하는 것이 좋을 수 있습니다.
* 전통방식

```java
public class PhoneNumber implements Comparable<PhoneNumber> {
    private final short areaCode;
    private final short prefix;
    private final short lineNum;

    // 생성자 및 기타 메서드 생략

    @Override
    public int compareTo(PhoneNumber pn) {
        int result = Short.compare(areaCode, pn.areaCode);
        if (result == 0) {
            result = Short.compare(prefix, pn.prefix);
            if (result == 0) {
                result = Short.compare(lineNum, pn.lineNum);
            }
        }
        return result;
    }
}

```

### 3) `Comparator`의 다양한 메서드 활용

`Comparator`는 다양한 **보조 생성 메서드**들을 제공한다.

* **숫자 타입** 필드를 비교하기 위한 메서드:
  * `comparingInt`, `comparingLong`, `comparingDouble`
  * `thenComparingInt`, `thenComparingLong`, `thenComparingDouble`
* **객체 참조 타입** 필드를 비교하기 위한 메서드:
  * `comparing` 메서드: 키 추출자를 받아 키의 자연 순서나 지정한 `Comparator`로 비교
  * `thenComparing` 메서드: 추가적인 비교 기준을 지정

**예시: 객체 참조 필드 비교**

```java
java코드 복사Comparator<Person> personComparator = Comparator
    .comparing(Person::getLastName)
    .thenComparing(Person::getFirstName)
    .thenComparingInt(Person::getAge);
```

* `Person` 클래스의 `lastName`, `firstName`, `age` 필드를 순차적으로 비교

### 4) 잘못된 `compareTo` 구현 방식 피하기

가끔 **값의 차이**를 반환하여 비교하는 `compareTo` 메서드를 볼 수 있다.&#x20;

```java
// 잘못된 구현 - 사용하지 말 것!
public int compareTo(PhoneNumber pn) {
    return areaCode - pn.areaCode;
}
```

* 이 방식은 **오버플로우**나 **언더플로우**가 발생할 수 있어 **신뢰할 수 없다**.
* 또한, 부동소수점 타입에서는 **정밀도 손실**이 발생할 수 있다.

**올바른 구현 방식:**

```java
public int compareTo(PhoneNumber pn) {
    int result = Short.compare(areaCode, pn.areaCode);
    if (result == 0) {
        result = Short.compare(prefix, pn.prefix);
        if (result == 0) {
            result = Short.compare(lineNum, pn.lineNum);
        }
    }
    return result;
}
```

* **정적 `compare` 메서드**를 사용
* 또는 **`Comparator` 생성 메서드**를 활용한다.

### 5) 정적 compare 메서드를 활용한 비교자&#x20;

### &#x20;비교자 생성 메서드를 활용한 비교자&#x20;

```
// Some code
```

### 유의 사항

* **`compareTo` 메서드에서 필드를 비교할 때는**:
  * **기본 타입 필드**는 해당 **박싱 클래스의 정적 `compare` 메서드**를 사용
  * **객체 참조 필드**는 해당 필드의 `compareTo` 메서드를 재귀적으로 호출한다.
  * **`Comparable`을 구현하지 않은 필드**는 적절한 `Comparator`를 사용한다.
* **필드의 비교 순서**는 **중요도에 따라** 정하기
  * 가장 중요한 필드부터 비교하여 순서가 결정되면 즉시 반환
* **`Comparator` 생성 메서드**를 사용하면 코드가 간결해짐
  * 그러나 **성능 저하**가 있을 수 있으므로 상황에 맞게 선택
* **값의 차이를 반환하는 방식은 피해야 한다.**
  * 오버플로우, 언더플로우 등의 문제를 일으킬 수 있다.

## **✨ 결론**

1. <mark style="color:red;">순서를 고려해야 하는 값 클래스를 작성한다면 꼭 Comparable 인터페이스를 구현</mark>하여, 그 인스턴스들을 쉽게 정렬하고, 검색하고, 비교 기능을 제공하는 컬렉션과 어우러 지도록 해야 한다.
2. compareTo 메서드에서 필드의 값을 비교할 때 <와 > 연산자는 쓰지 말아야 한다.

* 그 대신 박싱된 기본 타입 클래스가 제공하는 정적 compare 메서드나 Comparator 인터페이스가 제공하는 비교자 생성 메서드를 사용하자.



[^1]: 비교할 때 타입에 관계없이 사용할 수 있음을 의미합니다. 즉, 다양한 타입에 적용 가능하다는 의미

[^2]: `String` 클래스는 `Comparable<String>` 인터페이스를 구현하고 있으며, 이로 인해 `compareTo` 메서드를 사용하여 문자열을 사전식(알파벳 순서)으로 비교할 수 있다. `TreeSet`은 내부적으로 삽입된 요소들을 자동으로 정렬하는 자료구조이다. 따라서 `TreeSet`에 `String` 객체들을 삽입하면 `String`의 `compareTo` 메서드를 기반으로 사전순으로 정렬
