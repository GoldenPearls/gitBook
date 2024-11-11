# item 43 : 람다보다는 메서드 참조를 사용하라

## 1. 메서드 참조

### 1) 메서드 참조란?

람다가 익명 클래스보다 나은 점 중에서 가장 큰 특징은 **간결함**이다. 그런데 자바에는 함수 객체를 심지어 람다보다도 더 간결하게 만드는 방법이 있으니, 바로 `메서드 참조(method reference)`다.&#x20;

메서드 참조를 사용하면 기존에 존재하는 메서드를 람다 표현식 대신 호출할 수 있어, 코드가 더 짧고 읽기 쉬워진다. 메서드 참조는 **`::` 기호**를 사용해 작성된다.

### 2) `Map.merge` 메서드와 람다 표현식

자바 8에서는 `Map.merge` 메서드가 추가되어, 특정 키가 존재하지 않을 경우 해당 키에 대해 초기 값을 설정하고, 이미 존재하는 경우에는 값을 갱신하는 동작을 더 간결하게 표현할 수 있게 되었다.

예를 들어, 키가 맵에 없으면 `{key, 1}` 쌍을 저장하고, 이미 존재하면 해당 값에 1을 더한다. 람다 표현식을 활용하여 다음과 같이 작성할 수 있다

```java
map.merge(key, 1, (count, incr) -> count + incr);
```

이 코드는 `count`와 `incr` 두 매개변수를 더하는 간단한 연산을 수행한다. 하지만 자바 8부터 `Integer` 클래스에 추가된 `sum` 메서드를 메서드 참조로 이용하면 더 간결하게 작성할 수 있다.

```java
map.merge(key, 1, Integer::sum);
```

메서드 참조는 두 매개변수를 더하는 기능을 명확하게 표현하며, 코드가 훨씬 깔끔해진다. 이처럼 람다식이 지나치게 길거나 복잡할 때는 메서드 참조가 좋은 대안이 될 수 있다.

### 3) 메서드 참조와 람다 표현식의 장단점

* **메서드 참조**는 람다에 비해 코드가 짧고 명확해질 때 사용하면 좋다. IDE는 람다를 메서드 참조로 대체할 것을 권장하는 경우가 많지만, 메서드 참조가 꼭 더 간결하거나 명확하지 않은 경우에는 람다 표현식을 유지하는 것이 더 좋을 때도 니다.
*   예를 들어, <mark style="color:red;">클래스 내부에서 사용되는 코드가 있을 때</mark> 람다로 표현하는 것이 더 간결하고 명확할 수 있다.

    ```java
    service.execute(() -> action());
    ```

    이 경우 메서드 참조로 표현하면 오히려 가독성이 떨어질 수 있다.
* 또한, `Function.identity()` 같은 제네릭 팩터리 메서드보다 `(x -> x)`와 같은 짧은 람다 표현식이 더 명확할 수 있다.

## 2. 메서드 참조의 다섯 가지 유형

메서드 참조는 다섯 가지 유형이 있으며, 각 유형은 해당 기능을 수행하는 람다 표현식으로 쉽게 대체할 수 있다.

| 유형               | 예시                       | 같은 기능의 람다 표현식                   |
| ---------------- | ------------------------ | ------------------------------- |
| 정적 메서드 참조        | `Integer::parseInt`      | `str -> Integer.parseInt(str)`  |
| 한정적 인스턴스 메서드 참조  | `Instant.now()::isAfter` | `t -> Instant.now().isAfter(t)` |
| 비한정적 인스턴스 메서드 참조 | `String::toLowerCase`    | `str -> str.toLowerCase()`      |
| 클래스 생성자 참조       | `TreeMap<K,V>::new`      | `() -> new TreeMap<K,V>()`      |
| 배열 생성자 참조        | `int[]::new`             | `len -> new int[len]`           |

메서드 참조에는 **정적 메서드 참조**, **한정적 인스턴스 메서드 참조**, **비한정적 인스턴스 메서드 참조**, **클래스 생성자 참조**, 그리고 **배열 생성자 참조**의 다섯 가지 유형이 있다.&#x20;

### 1) 정적 메서드 참조 (Static Method Reference)

**정적 메서드**는 특정 객체가 아닌 **클래스 자체**에 속한 메서드로, 객체를 생성하지 않고도 클래스 이름만으로 호출할 수 있다. 메서드 참조에서 정적 메서드를 참조하면, 메서드 이름을 람다 표현식처럼 사용할 수 있다.

```java
Function<String, Integer> strToInt = Integer::parseInt;
// 같은 기능의 람다 표현식
Function<String, Integer> strToIntLambda = str -> Integer.parseInt(str);

// 사용 예시
Integer number = strToInt.apply("123"); // 결과: 123
```

**설명**:

* `Integer::parseInt`는 `parseInt`라는 정적 메서드를 참조하는 메서드 참조
* `strToInt`는 `Function<String, Integer>` 타입으로 문자열을 받아 정수로 변환한다.
* `str -> Integer.parseInt(str)` 람다 표현식과 동일하게 동작한다.

### 2) 한정적 인스턴스 메서드 참조 (Bound Instance Method Reference)

**한정적 인스턴스 메서드 참조**는 **특정 객체에 바인딩된 인스턴스 메서드**를 참조하는 방식이다. 즉, 참조 대상 객체(수신 객체)를 정해놓고 해당 객체의 메서드를 호출하는 방식이다.

{% hint style="info" %}
한정적 인스턴스 메서드 참조는 **특정 객체의 메서드를 미리 참조하여**, 이후에 그 객체의 메서드를 사용할 수 있도록 하는 방식이다. 말이 조금 복잡하게 들릴 수 있지만, 핵심은 **특정한 객체를 지정한 상태에서 그 객체의 메서드를 참조**하는 것이다.
{% endhint %}

```java
Instant now = Instant.now();
Predicate<Instant> isAfterNow = now::isAfter;
```

이 코드는 `now`라는 `Instant` 객체에 `isAfter` 메서드를 **미리 연결해둔** 것이다. 즉, `isAfterNow`는 **항상 `now`라는 특정 객체의 `isAfter` 메서드를 실행**한다.

위 예제의 동작 방식을 하나하나 풀어보면 다음과 같다.

1. `Instant now = Instant.now();`\
   이 코드로 `now`라는 특정 시점(현재 시간)을 가지는 `Instant` 객체를 생성한다.
2. `Predicate<Instant> isAfterNow = now::isAfter;`\
   이 줄에서는 `now` 객체의 `isAfter` 메서드를 참조하는 `isAfterNow`라는 **Predicate**를 만든다.
3. `isAfterNow`는 `now` 객체의 `isAfter` 메서드를 참조하므로, 호출할 때마다 항상 `now`라는 객체와 비교하게 된다.

> 따라서 이 `isAfterNow`는 `now`보다 미래의 시점인지 여부를 확인하는 역할을 한다. `isAfterNow.test(Instant)`와 같이 `isAfterNow`에 `Instant` 객체를 전달하면, 그 시점이 `now` 이후인지 확인하는 것이다.

**한정적 인스턴스 메서드 참조의 실제 동작 예시**

```java
Instant now = Instant.now(); // 현재 시간을 저장한 Instant 객체

// isAfterNow라는 Predicate를 생성합니다.
Predicate<Instant> isAfterNow = now::isAfter;

// 이후에 다른 시점을 비교할 때 사용
Instant oneMinuteLater = now.plusSeconds(60);
System.out.println(isAfterNow.test(oneMinuteLater)); // 결과: false (60초 뒤는 now보다 미래이기 때문)
```

여기서 `isAfterNow`는 `now::isAfter`로 지정된 한정적 메서드 참조로, `isAfterNow.test(...)`를 호출할 때마다 `now`가 정해진 기준 시간이다. `now`보다 나중의 시간을 비교하는 로직이 필요한 곳에서 언제든지 `isAfterNow`를 사용하여 `now` 시점과 비교할 수 있게 만들어준 것이다.

> 이런 방식으로 **특정 객체와 메서드를 한 번에 묶어서 참조**하는 것이 한정적 인스턴스 메서드 참조이다.

**예제**:

```java
Instant now = Instant.now();
Predicate<Instant> isAfterNow = now::isAfter;
// 같은 기능의 람다 표현식
Predicate<Instant> isAfterNowLambda = t -> now.isAfter(t);

// 사용 예시
Instant futureTime = now.plusSeconds(60);
boolean result = isAfterNow.test(futureTime); // 결과: false (현재 시간보다 미래인지 확인)
```

**설명**:

* `now::isAfter`는 `now` 객체의 `isAfter` 인스턴스 메서드를 참조하는 메서드 참조
* `isAfterNow`는 `Predicate<Instant>` 타입으로, 특정 `Instant` 값이 `now`보다 이후인지 검사하는 역할을 한다.
* `t -> now.isAfter(t)` 람다 표현식과 동일하게 동작한다.

### 3) 비한정적 인스턴스 메서드 참조 (Unbound Instance Method Reference)

**비한정적 인스턴스 메서드 참조**는 **수신 객체가 특정되지 않은 상태**로 인스턴스 메서드를 참조하는 방식이다. 참조된 메서드는 사용 시점에 객체를 전달받아 그 객체의 메서드를 호출하게 된다.

```java
Function<String, String> toLowerCase = String::toLowerCase;
// 같은 기능의 람다 표현식
Function<String, String> toLowerCaseLambda = str -> str.toLowerCase();

// 사용 예시
String result = toLowerCase.apply("HELLO"); // 결과: "hello"
```

**설명**:

* `String::toLowerCase`는 비한정적 메서드 참조로, 실제 호출 시 인스턴스(여기선 `String`)가 제공
* `toLowerCase`는 `Function<String, String>` 타입으로, 문자열을 소문자로 변환
* `str -> str.toLowerCase()` 람다 표현식과 동일하게 동작한다.

### 4) 클래스 생성자 참조 (Class Constructor Reference)

**클래스 생성자 참조**는 **클래스의 생성자를 참조**하여 객체를 생성할 수 있게 해주는 방식이다. 주로 팩터리 메서드에서 사용할 수 있다.

**예제**:

```java
Supplier<List<String>> listSupplier = ArrayList::new;
// 같은 기능의 람다 표현식
Supplier<List<String>> listSupplierLambda = () -> new ArrayList<>();

// 사용 예시
List<String> list = listSupplier.get(); // 결과: 빈 ArrayList 생성
```

**설명**:

* `ArrayList::new`는 `ArrayList` 클래스의 생성자를 참조하여 빈 `ArrayList`를 생성하는 메서드 참조
* `listSupplier`는 `Supplier<List<String>>` 타입으로, 새로운 `ArrayList` 인스턴스를 제공
* `() -> new ArrayList<>()` 람다 표현식과 동일하게 동작한다.

### 5) 배열 생성자 참조 (Array Constructor Reference)

**배열 생성자 참조**는 **배열을 생성할 수 있도록 배열 생성자를 참조**하는 방식이다. 전달받은 크기만큼의 배열을 생성할 수 있다.

**예제**:

```java
IntFunction<int[]> arrayCreator = int[]::new;
// 같은 기능의 람다 표현식
IntFunction<int[]> arrayCreatorLambda = len -> new int[len];

// 사용 예시
int[] array = arrayCreator.apply(5); // 결과: 길이 5의 int 배열 생성
```

**설명**:

* `int[]::new`는 `int` 배열의 생성자를 참조하여 지정된 길이의 배열을 생성한다.
* `arrayCreator`는 `IntFunction<int[]>` 타입으로, 배열의 크기를 받아 그 크기만큼의 새로운 배열을 생성한다.
* `len -> new int[len]` 람다 표현식과 동일하게 동작

#### 요약

| 유형                   | 예시                    | 설명                                                |
| -------------------- | --------------------- | ------------------------------------------------- |
| **정적 메서드 참조**        | `Integer::parseInt`   | 특정 클래스의 정적 메서드를 참조. 객체가 필요 없으며 클래스 이름으로 호출 가능.    |
| **한정적 인스턴스 메서드 참조**  | `now::isAfter`        | 특정 객체에 바인딩된 메서드를 참조. 객체가 이미 존재하며 해당 객체의 메서드를 참조함. |
| **비한정적 인스턴스 메서드 참조** | `String::toLowerCase` | 수신 객체가 특정되지 않은 상태로 인스턴스 메서드를 참조. 호출 시점에 객체를 전달받음. |
| **클래스 생성자 참조**       | `ArrayList::new`      | 클래스의 생성자를 참조하여 객체를 생성할 수 있음. 주로 팩터리 메서드로 사용됨.     |
| **배열 생성자 참조**        | `int[]::new`          | 배열 생성자를 참조하여 지정된 길이의 배열을 생성. 함수형 인터페이스로 배열 생성 가능. |





## 3. 람다로 할 수 없는 일

<mark style="color:red;">람다로는 불가능하나 메서드 참조로는 가능한 유일한 예외</mark>는 **제네릭 함수 타입(generic function type)**입다. 자바는 제네릭 람다식을 지원하지 않기 때문에 제네릭 함수형 인터페이스의 구현은 메서드 참조로 표현할 수 있지만 람다로는 표현할 수 없다.



## 4. 핵심 정리

* 메서드 참조는 람다의 간단한 대안으로 사용할 수 있으며, 코드가 더 짧고 명확해질 때 유용하다.
* 그러나 메서드 참조가 오히려 가독성을 떨어뜨릴 수 있는 경우가 있으므로 상황에 따라 람다를 유지하는 것이 좋다.
* 메서드 참조와 람다 표현식은 코드의 간결성과 가독성을 높이기 위한 수단으로, 상황에 맞게 적절하게 선택하여 사용하는 것이 중요하다.

람다와 메서드 참조는 자바의 코드 가독성을 높이고, 함수형 프로그래밍을 효과적으로 지원하는 기능이다.
