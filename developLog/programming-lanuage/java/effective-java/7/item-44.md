# item 44 : 표준 함수형 인터페이스를 사용하라

자바에서 람다 지원이 추가되면서 API 작성 방식에도 큰 변화가 생겼다. 특히, 과거의 템플릿 메서드 패턴 대신 **함수형 인터페이스를 활용하는 방식**이 주목받고 있다.&#x20;

> 이로 인해 <mark style="color:red;">함수 객체를 매개변수로 받는 생성자와 메서드를 더 많이 작성</mark>하게 되었으며, 올바른 **함수형 인터페이스 타입**을 선택하는 것이 중요해졌다. 즉, 함수형 매개변수 타입을 올바르게 선택해야 한다.

## 1. 템플릿 메서드 패턴에서 함수형 인터페이스로 전환

과거에는 <mark style="color:blue;">상위 클래스의 기본 메서드를 재정의하여 동작을 맞춤 설정</mark>하는 **템플릿 메서드 패턴**을 많이 사용했다. 하지만 함수형 인터페이스와 람다를 이용하면 이보다 간결하고 유지보수하기 좋은 API를 만들 수 있다.

아래 예제는 템플릿 메서드 패턴을 사용한 전통적인 방식과 함수형 인터페이스를 활용한 현대적인 방식의 비교이다.&#x20;

```java
// 템플릿 메서드 패턴 방식
class CustomCache<K, V> extends LinkedHashMap<K, V> {
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > 100;
    }
}
```

위 코드는 `LinkedHashMap`을 확장해 `removeEldestEntry` 메서드를 재정의하여 캐시 기능을 추가한 것이다.  맵에 새로운 키를 추가하는 put 메서드는 이 메서드를 호출하여 true가 반환되면 맵에서 **가장 오래된 원소를 제 거한다.** 예컨대 removeEldestEntry를 다음처럼 재정의하면 맵에 원소가 100개 가 될 때까지 커지다가, 그 이상이 되면 새로운 키가 더해질 때마다 가장 오래 된 원소를 하나씩 제거한다.&#x20;

> 즉, 가장 최근 원소 100개를 유지한다.

잘 동작하지만 람다를 사용하면 훨씬 잘 해낼 수 있다. removeEldestEntry 선언을 보면 이 함수 객체는 `Map.Entry<K, V>`를 받아 <mark style="color:red;">boolean 을 반환해야 할 것 같지만, 꼭 그렇지는 않다</mark>.「emoveEldestEntry는 size()를 호 출해 맵 안의 원소 수를 알아내는데, removeEldestEntry가 <mark style="color:blue;">인스턴스 메서드라서 가능한 방식</mark>이다. 하지만 생성자에 넘기는 함수 객체는 이 맵의 인스턴스 메서드가 아니다.&#x20;

> <mark style="color:red;">팩터리나 생성자를 호출할 때는 맵의 인스턴스가 존재하지 않기 때문이다.</mark> 따라서 맵은 자기 자신도 함수 객체에 건네줘야 한다.&#x20;

이를 반영하여 **함수형 인터페이스로 전환하면 다음과 같이 구현할 수 있다.**&#x20;

```java
// 함수형 인터페이스 BiPredicate를 사용하여 캐시의 동작을 유연하게 설정하는 방식
import java.util.function.BiPredicate;
import java.util.LinkedHashMap;
import java.util.Map;

class CustomCache<K, V> extends LinkedHashMap<K, V> {
    // 제거 기준을 정의하는 BiPredicate 인터페이스를 사용하여 조건을 저장
    private final BiPredicate<Map<K, V>, Map.Entry<K, V>> removalCriteria;

    // 생성자에서 제거 기준을 받음
    public CustomCache(BiPredicate<Map<K, V>, Map.Entry<K, V>> removalCriteria) {
        this.removalCriteria = removalCriteria;
    }

    // LinkedHashMap의 removeEldestEntry 메서드를 재정의하여
    // 설정한 조건에 따라 오래된 항목을 제거할지 결정
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        // BiPredicate를 사용하여 조건에 따라 true/false 반환
        return removalCriteria.test(this, eldest);
    }
}

// 사용 예: 캐시 객체 생성
// 캐시 객체를 생성하면서 map의 크기가 100을 초과하면 가장 오래된 항목을 제거하도록 설정
CustomCache<String, String> cache = new CustomCache<>((map, entry) -> map.size() > 100);

```

1. B**iPredicate 사용**: `BiPredicate<Map<K, V>, Map.Entry<K, V>>`를 사용하여 맵과 엔트리를 인수로 받아 `boolean` 값을 반환하는 조건을 설정한다. 이 조건을 `removalCriteria`라는 변수에 저장한다.
2. **생성자**: `CustomCache` 클래스의 생성자에서 `removalCriteria`라는 `BiPredicate`를 인수로 받아, 캐시의 항목 제거 기준을 설정한다. 이는 동적으로 조건을 지정할 수 있게 한다.
3. **removeEldestEntry 메서드**: `LinkedHashMap`의 `removeEldestEntry` 메서드를 재정의하여, `removalCriteria`에 따라 오래된 항목을 제거할지 결정한다. 이때 `this`(맵 자체)와 `eldest`(가장 오래된 항목)를 인수로 `test` 메서드를 호출하여 기준을 확인한다.
4. **사용 예**: `CustomCache` 객체를 생성할 때 람다식 `(map, entry) -> map.size() > 100`을 전달하여, 맵의 크기가 100을 초과할 경우 가장 오래된 항목을 제거하도록 설정한다.

이 코드 구조는 캐시의 항목 제거 기준을 동적으로 설정할 수 있어 유연성을 높이며, 기존 템플릿 메서드 패턴보다 간결하고 모듈화된 방식으로 구현할 수 있게 해준다.

람다를 활용한 이 방식은 더 유연하며, `removeEldestEntry`를 재정의할 필요 없이 다른 조건으로 쉽게 맞춤 설정할 수 있다.

## 2. 표준 함수형 인터페이스 활용

자바의 `java.util.function` 패키지에는 다양한 **표준 함수형 인터페이스**가 제공되며, 이를 활용하면 API 설계가 단순하고 일관되게 유지된다.

> &#x20;필요한 용도에 맞는 게 있다면, 직접 구현하지 말고 표준 함수형 인터페이스를 활용하라. 그러면 API가 다루는 개념의 수가 줄어들어 익히기 더 쉬워 진다.

예컨대 `Predicate 인터페이스`는 **프레디키트(predicate)들을 조합하는 메서드를 제공**한다. 앞의 LinkedHashMap 예에서는 직접 만든 EldestEntryRemovalFunction 대신 표준 인터페이스인 BiPredicate\<Map\<K,V>, Map.Entry\<K,V>를 사용할 수 있다.

### **1) 주요 표준 함수형 인터페이스**

표준 함수형 인터페이스는 크게 다음과 같은 유형이 있다.

| 인터페이스               | 함수 시그니처               | 설명                    |
| ------------------- | --------------------- | --------------------- |
| `UnaryOperator<T>`  | `T apply(T t)`        | 인수를 하나 받아 같은 타입을 반환   |
| `BinaryOperator<T>` | `T apply(T t1, T t2)` | 인수를 두 개 받아 같은 타입을 반환  |
| `Predicate<T>`      | `boolean test(T t)`   | 인수를 하나 받아 boolean을 반환 |
| `Function<T,R>`     | `R apply(T t)`        | 인수와 반환 타입이 다른 함수      |
| `Supplier<T>`       | `T get()`             | 인수를 받지 않고 값을 반환       |
| `Consumer<T>`       | `void accept(T t)`    | 인수를 하나 받고 값을 반환하지 않음  |

**예제**

1.  **Predicate** - 조건 검사

    ```java
    Predicate<String> isEmpty = String::isEmpty;
    System.out.println(isEmpty.test(""));   // true
    System.out.println(isEmpty.test("abc")); // false
    ```
2.  **Function** - 타입 변환

    ```java
    Function<String, Integer> toLength = String::length;
    System.out.println(toLength.apply("Hello")); // 5
    ```
3.  **Supplier** - 값 생성

    ```java
    Supplier<Double> randomSupplier = Math::random;
    System.out.println(randomSupplier.get()); // 0.12345 (임의의 값)
    ```
4.  **Consumer** - 값 소비

    ```java
    Consumer<String> print = System.out::println;
    print.accept("Hello, world!"); // Hello, world!
    ```
5.  **UnaryOperator** - 값 변환

    ```java
    UnaryOperator<String> toLowerCase = String::toLowerCase;
    System.out.println(toLowerCase.apply("HELLO")); // hello
    ```

## 3. 기본 타입용 함수형 인터페이스

기본 타입을 위해 `java.util.function` 패키지에서는 기본 타입용 함수형 인터페이스도 제공한다. 예를 들어, `IntPredicate`, `LongFunction`, `DoubleConsumer` 등이다. 기본 타입을 지원하는 인터페이스를 사용하면 **박싱 오버헤드**를 피할 수 있어 성능이 개선된다.

**예제**

```java
IntPredicate isEven = x -> x % 2 == 0;
System.out.println(isEven.test(4)); // true
System.out.println(isEven.test(5)); // false
```

## 4. 직접 작성해야 하는 경우

표준 함수형 인터페이스가 거의 대부분의 경우를 커버하지만, **다음과 같은 경우**에는 직접 작성하는 것이 더 나을 수 있다.

* **특정 규약을 따라야 할 경우**: 예를 들어, `Comparator`는 `ToIntBiFunction`과 구조가 비슷하지만, 비교 기능을 명확히 나타내고 있으며 특정 규약(순서 비교)을 따르도록 설계되어 있다.
* **자주 사용되며 이름만으로 용도가 명확한 경우**: 자주 사용되며 명확한 이름을 가지면 코드 가독성이 높아진다.
* **추가적인 디폴트 메서드가 필요한 경우**: 인터페이스 내에서 메서드를 조합하거나 변형하는 디폴트 메서드가 필요한 경우가 있다.

**예제 - 커스텀 함수형 인터페이스**

```java
@FunctionalInterface
interface TriFunction<T, U, V, R> {
    R apply(T t, U u, V v);
}

TriFunction<Integer, Integer, Integer, Integer> sumThree = (a, b, c) -> a + b + c;
System.out.println(sumThree.apply(1, 2, 3)); // 6
```

## 5. 주의사항: 함수형 인터페이스를 다중 정의하지 않기

서로 다른 함수형 인터페이스를 같은 위치의 인수로 받는 메서드를 다중 정의하는 것은 피해야 한다. 예를 들어, `ExecutorService`의 `submit` 메서드는 `Callable`과 `Runnable`을 다중 정의하는데, 이를 사용할 때 형변환이 필요한 경우가 생긴다. 이는 불필요한 혼란을 초래할 수 있다.

따라서, **같은 위치의 인수로 서로 다른 함수형 인터페이스를 받지 않도록 주의**하는 것이 좋다.

## 핵심 요약

1. **함수형 인터페이스**를 통해 람다 표현식을 활용하면 코드가 간결해지고 유연성이 높아진다.
2. **표준 함수형 인터페이스**를 적극적으로 활용하고, 필요할 때만 커스텀 인터페이스를 정의하는 것이 좋다.
3. 커스텀 함수형 인터페이스를 정의할 때는 **반드시 따라야 할 규약**이나 **유용한 디폴트 메서드**가 필요한지 고민해보아야 한다.
4. 함수형 인터페이스를 다중 정의하는 것을 피하여 **모호함**을 방지한다.

자바에서도 함수형 프로그래밍의 장점을 활용할 수 있으므로, API 설계 시 함수형 인터페이스의 활용을 염두에 두는 것이 좋다.
