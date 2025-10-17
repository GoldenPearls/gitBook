# item 52 : 다중정의는 신중히 사용하라

## 1. 다중정의

### 1) 문제가 되는 코드

```java
import org.junit.Test;
import java.util.*;

public class OverloadingProblem {

    static class CollectionClassifier {
        public static String classify(Set<?> s) {
            return "집합";
        }

        public static String classify(List<?> s) {
            return "리스트";
        }

        public static String classify(Collection<?> s) {
            return "그 외 컬렉션";
        }
    }

    @Test
    public void collectionClassifierTest() {
        Collection<?>[] collections = {
                new HashSet<>(),            // Set
                new ArrayList<>(),          // List
                new HashMap<>().values()    // Collection
        };

        for (Collection<?> collection : collections) {
            System.out.println(CollectionClassifier.classify(collection));
        }
    }
}

```

* 예제 코드에서는 `CollectionClassifier` 클래스가 `classify`라는 이름으로 **다중 정의된 메서드**를 3개 가지고 있다:
  1. `classify(Set<?> s)` - "집합"
  2. `classify(List<?> s)` - "리스트"
  3. `classify(Collection<?> s)` - "그 외 컬렉션"
* 테스트 메서드 `collectionClassifierTest()`에서 `HashSet`, `ArrayList`, 그리고 `HashMap의 values()`를 포함하는 **컬렉션 배열**을 정의한다. 이후 `for` 문에서 각 컬렉션을 `classify` 메서드를 사용하여 분류한다.

### 2) 문제점과 실행 결과

> 실행 결과는 `"그 외 컬렉션"`이 3번 출력된다.

* 이는 **다중 정의된 메서드**들 중에서 **어떤 메서드를 호출할지 컴파일타임에 결정**되기 때문이다.
* `Collection<?>[]` 배열에 저장된 각 객체는 모두 컴파일타임에는 **`Collection<?>`** 타입으로 간주되므로, 항상 `classify(Collection<?> s)` 메서드가 호출된다.&#x20;

> 즉, **런타임의 실제 객체 타입**(e.g., `Set`, `List`)을 고려하지 않는다.

### 3) 정적 바인딩(Static Binding)과 동적 바인딩(Dynamic Binding)

* 재정의(Overriding)된 메서드는 **동적 바인딩(Dynamic Binding)**&#xC73C;로 선택된다. 즉, 런타임에 객체의 실제 타입을 기반으로 적절한 메서드가 호출된다.
* 반면에 **다중 정의(Overloading)된 메서드**는 정적 바인딩(Static Binding)으로 선택된다. **컴파일타임에 매개변수의 정적 타입**을 기준으로 호출할 메서드가 결정된다.

### 4) 오버로딩이 혼란을 일으키는 이유

* 다중 정의는 **컴파일타임에 메서드 선택이 이루어지기 때문에**, 매개변수의 실제 타입(런타임 타입)이 아닌 정적 타입(컴파일타임 타입)만 고려된다.
* 위 예제에서는 모든 컬렉션이 `Collection<?>` 타입으로 저장되었기 때문에, 항상 `classify(Collection<?> s)`가 호출되는 문제가 발생한 것

### **5) 재정의된 메서드 호출 메커니즘**

#### 재정의(오버라이딩)의 경우

```java
static class Wine {
    String name() { return "포도주"; }
}

static class SparklingWine extends Wine {
    @Override String name() { return "발포성 포도주"; }
}

static class Champagne extends SparklingWine {
    @Override String name() { return "샴페인"; }
}

@Test
public void wineTest() {
    List<Wine> wineList = List.of(new Wine(), new SparklingWine(), new Champagne());

    for (Wine wine : wineList) {
        System.out.println("wine.name() = " + wine.name());
    }
}
```

* `Wine` 클래스는 `name()` 메서드를 정의하며, 그 하위 클래스인 `SparklingWine`과 `Champagne`은 `name()` 메서드를 재정의하고 있다.
* 테스트 메서드에서는 `Wine` 객체들을 리스트에 넣고 순회하면서 각각의 `name()` 메서드를 호출한다.

#### 실행 결과

```
wine.name() = 포도주
wine.name() = 발포성 포도주
wine.name() = 샴페인
```

* 출력 결과는 `"포도주"`, `"발포성 포도주"`, `"샴페인"`이 차례로 출력된다.
* 이는 각 객체가 실제로 어떤 타입의 인스턴스인지를 기준으로, **가장 하위에서 정의된 메서드**가 호출되기 때문이다.

### 6) 재정의(Overriding)와 다중 정의(Overloading)의 차이

1. **재정의(Overriding)**:
   * **상위 클래스의 메서드를 하위 클래스에서 같은 시그니처로 다시 정의**하는 것을 말한다.
   * <mark style="color:red;">**런타임**</mark>**에 객체의 실제 타입**에 따라 호출되는 메서드가 결정된다. 이를 `동적 바인딩(Dynamic Binding)`이라고 한다.
   * 예제에서는 `Wine`, `SparklingWine`, `Champagne` 클래스가 모두 `name()` 메서드를 재정의하고 있기 때문에, `wineList`의 각 객체는 실제 인스턴스 타입에 따라 적절한 `name()` 메서드를 호출한다.
2. **다중 정의(Overloading)**:
   * **같은 이름의 메서드를 여러 개 정의하되, 매개변수의 개수나 타입을 달리하여** 구분하는 것을 말한다.
   * <mark style="color:red;">**컴파일타임**</mark>**에 호출할 메서드가 결정**된다. 즉, 매개변수의 정적 타입(컴파일타임 타입)을 기준으로 어떤 메서드를 호출할지 선택한다. 이를 정적 바인딩(Static Binding)이라고 한다.
   * 이전에 다룬 `CollectionClassifier` 예제에서는 런타임 타입과 상관없이 **항상 컴파일타임 타입**(`Collection<?>`)에 따라 호출할 메서드가 결정되므로, 의도와 다르게 `"그 외 컬렉션"`이 출력되었다.
   * Collectionclassifier 예에서 프로그램의 원래 의도는 매개변수의 런타임 타입에 기초해 적절한 다중정의 메서드로 자동 분배하는 것이었다. Wine 예에서의 name 메서드와 똑같이 말이다. 하지만 다중정의는 이렇게 동작하지 않는다.

### 7) 오버로딩 없는 해결법

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

*   **instanceof를 사용하여 명시적으로 타입 검사**: `instanceof` 연산자를 사용하여 컬렉션 타입을 명시적으로 검사하면, **다중 정의 없이 하나의 메서드**로 문제를 해결할 수 있다.

    ```java
    public static String classify(Collection<?> c) {
        return c instanceof Set ? "집합" :
               c instanceof List ? "리스트" : "그 외 컬렉션";
    }
    ```

    * 이 방식은 런타임에 컬렉션의 실제 타입에 따라 **올바른 결과를 반환**하기 때문에 더 직관적이다.
* **다중 정의의 위험성**:
  * 다중 정의는 **컴파일타임에 결정**되므로, 런타임의 객체 타입에 따라 올바른 메서드를 호출해야 하는 경우에는 **혼란을 초래**할 수 있다.
  * 특히 **공개 API**의 경우, 사용자가 어떤 메서드가 호출될지 명확하게 알 수 없다면 문제를 진단하는 데 긴 시간이 소요될 수 있으며, **혼란을 줄 가능성**이 있다.
* **메서드 이름의 명확화**:
  * **다중 정의 대신 다른 이름의 메서드**를 사용하는 것이 좋다. 예를 들어, 각각의 타입에 대해 구체적인 메서드 이름을 붙여 혼란을 피할 수 있다.
* **정적 팩토리 메서드 활용**:
  * 생성자가 여러 개 필요할 경우 **정적 팩토리 메서드**를 사용하여 이름을 붙이는 것으로 생성 의도를 명확히 할 수 있다.

## 2. 다중 정의 사용시 주의사항

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

1. **다중 정의가 혼란을 일으킬 수 있는 상황 피하기**:
   * 다중 정의는 메서드를 **컴파일타임에 선택**하므로, 런타임 타입이 아닌 **매개변수의 정적 타입**에 따라 호출할 메서드가 결정된다.
   * 이로 인해 **사용자가 의도한 것과 다른 메서드가 호출**될 수 있으며, 특히 런타임에 예상과 다른 동작을 하게 되어 **오동작**할 가능성이 높아진다.
2. **매개변수 수가 같은 다중 정의를 피하기**:
   * 다중 정의된 메서드 중 **매개변수 수가 같은 메서드**는 컴파일러가 **정적 타입만을 고려**하여 선택하기 때문에 혼란을 줄 수 있다. 따라서 매개변수의 수가 같다면 다중 정의를 피해야 합니다.
3. **가변인수 사용 시 다중 정의 피하기**:
   * 가변인수(varargs)를 사용하는 경우, 메서드를 다중 정의하면 컴파일러가 어느 메서드를 호출할지 모호해진다. <mark style="color:red;">따라서 가변인수를 사용하는 경우에는</mark> <mark style="color:red;"></mark><mark style="color:red;">**다중 정의를 하지 않는 것이 좋습니다**</mark><mark style="color:red;">.</mark>
4. **다중 정의 대신 메서드 이름을 다르게 짓기**:
   * **다중 정의 대신 의미가 명확한 메서드 이름을 사용**하는 것이 좋다. 예를 들어, `ObjectOutputStream` 클래스는 `writeBoolean()`, `writeInt()` 등의 이름을 가진 메서드들을 제공하여 **각기 다른 동작을 명확하게 구분한**다.
5. **생성자 다중 정의의 대안 - 정적 팩토리 메서드**:
   * 생성자는 **다중 정의**가 불가피할 수 있습니다. 이런 경우에는 **정적 팩토리 메서드**를 이용하여 목적을 명확히 하는 것이 좋다. 정적 팩토리 메서드는 이름을 지을 수 있기 때문에 생성의 목적을 더 명확히 전달할 수 있다.
6. **다중 정의가 명확하게 다른 타입인 경우 괜찮다**:
   * 다중 정의된 메서드의 매개변수가 **명확하게 다른 타입**을 가진 경우라면 혼란이 줄어들기 때문에 괜찮다. 하지만 **매개변수 타입이 비슷하거나 같은 수의 매개변수**를 가지는 경우에는 사용을 피해야 한다.

## 3. 다중 정의의 함정

### 1) 다중 정의의 함정 1: 오토박싱

* **문제 코드**:

> 간단히 눈으로 코드를 해석해보면, `-3 ~ 2`까지 숫자를 넣고, `0 ~ 2`까지의 숫자를 지우려는 의도가 보인다.

```java
@Test
public void boxingTest() {
    Set<Integer> set = new TreeSet<>();
    List<Integer> list = new ArrayList<>();

    for (int i = -3; i < 3; i++) {
        set.add(i);
        list.add(i);
    }

    for (int i = 0; i < 3; i++) {
        set.remove(i);
        list.remove(i);
    }

    System.out.println("set = " + set);
    System.out.println("list = " + list);
}
```

**실행 결과**

```subunit
set = [-3, -2, -1, 0, 1, 2]
list = [-3, -2, -1, 0, 1, 2]
set = [-3, -2, -1]
list = [-2, 0, 2]
```

* `set`의 결과는 의도와 같은데, `list`의 결과는 의도와 다르다.
* 그 이유는 `list`에는 `remove(Object element)`와 `remove(int index)` 두가지 메서드가 다중 정의되어 있기 때문이다.
  * 이 중 우리가 의도한 것은 첫번째 메서드인데, 두번째 메서드가 적용되었다.
  * 이 코드는 `List<Integer>`에서 `remove()` 메서드가 다중 정의된 탓에 혼란이 발생한다. `remove(int index)`와 `remove(Object element)`가 다중 정의되어, `list.remove(i)`가 **인덱스를 기반으로 호출**된다.
* 반면 `set`에는 `remove(Object element)` 메서드밖에 존재하지 않아서 우리의 의도대로 메서드가 적용됐다.
*   **정상 작동하도록 수정**:

    ```java
    @Test
    public void boxingTest2() {
        Set<Integer> set = new TreeSet<>();
        List<Integer> list = new ArrayList<>();

        for (int i = -3; i < 3; i++) {
            set.add(i);
            list.add(i);
        }

        for (int i = 0; i < 3; i++) {
            set.remove(i);
            list.remove((Integer) i);  // (Integer)로 명시적 캐스팅
        }

        System.out.println("set = " + set);
        System.out.println("list = " + list);
    }
    ```

    * `list.remove((Integer) i)`로 명시적 **캐스팅**을 통해 다중 정의된 메서드 중 **의도한 메서드**를 호출하게 만든다.
    *   **실행 결과**

        ```subunit
        set = [-3, -2, -1, 0, 1, 2]
        list = [-3, -2, -1, 0, 1, 2]
        set = [-3, -2, -1]
        list = [-3, -2, -1]
        ```

### 2) 다중 정의의 함정 2: 람다와 메서드 참조

**문제 코드**:

```java
@Test
public void lambdaTest1() {
    new Thread(System.out::println).start();
}

@Test
public void lambdaTest2() {
    ExecutorService exec = Executors.newCachedThreadPool();
    exec.submit(System.out::println);  // 컴파일 오류 발생 가능
}
```

* `lambdaTest1()`은 `Runnable`을 요구하기 때문에 문제없이 컴파일된다.
* `lambdaTest2()`에서는 `submit()` 메서드가 `Runnable`과 `Callable<T>`를 모두 받을 수 있어 **컴파일러가 어떤 메서드를 선택해야 할지 모호해** 컴파일 오류가 발생한다.
* **서로 다른 함수형 인터페이스**라도 **같은 위치의 인수**로 받을 경우, 다중 정의는 혼란을 초래할 수 있다.
  * 이 말은 서로 다른 함수형 인터페이스라도 서로 근본적으로 다르지 않다는 뜻이다. 컴파일할 때 명령줄 스위치로 -Xlint:overloads를 지정하면 이런 종류의 다중정의를 경고해줄 것이다.

{% hint style="danger" %}
자바 4와 그 이후에 대해서
{% endhint %}

> 제네릭이 도입되기 전인 자바 4까지의 List에서는 Object와 int가 근본적으로 달라서 문제가 없었다. <mark style="color:red;">그런데 제네릭과 오토 박싱이 등장하면서 두 메서드의 매개변수 타입이 더는 근본적으로 다르지 않게 되었다</mark>. 정리하자면, 자바 언어에 제네릭과 오토박싱을 더한 결과 List 인터페이스가 취약해졌다. 다행히 같은 피해를 입은 API는 거의 없지만, 다중정의 시 주의를 기울여야 할 근거로는 충분하다.

#### 자바 4 이전의 상황

* 자바 4까지는 **제네릭과 오토박싱이 없었기 때문에** 클래스와 기본 타입 간의 구분이 명확했다.
* 예를 들어, `List` 인터페이스에서 두 가지 형태의 `remove` 메서드가 있었다:
  1. `remove(Object element)`: 주어진 객체를 삭제한다.
  2. `remove(int index)`: 주어진 인덱스 위치의 요소를 삭제한다.
* 기본 타입(int)과 참조 타입(Object)은 서로 **근본적으로 달랐기 때문에** 컴파일러가 어느 메서드를 호출할지 쉽게 구분할 수 있었다. 즉, `int` 타입은 `remove(int index)` 메서드로 호출되고, `Object` 타입은 `remove(Object element)` 메서드로 호출되었다.

#### 자바 5 이후의 변화

* 자바 5부터 **제네릭**과 **오토박싱**이 도입되었다.
  * **제네릭(Generic)**: 타입 안정성을 높이기 위해, `List<Integer>`와 같이 컬렉션에 담기는 객체의 타입을 지정할 수 있게 하는 기능이다.
  * **오토박싱(Auto-Boxing)**: 기본 타입과 해당 참조 타입 간에 자동으로 형변환이 이루어지게 하는 기능이다. 예를 들어, `int` 타입이 자동으로 `Integer` 객체로 변환된다.
* 이로 인해 `List<Integer>`와 같은 형태로 사용할 수 있게 되었고, 이때 `remove` 메서드의 다중 정의에서 문제가 발생할 가능성이 생겼다.
  * 예를 들어, `list.remove(i)`가 있을 때, `i`가 **기본 타입 int**일 경우 자동으로 **참조 타입 Integer**로 **오토박싱**되므로, 컴파일러는 이 `i`가 인덱스를 의미하는지, 값을 의미하는지 **혼동**할 수 있게 되었다.
  * 이로 인해 `remove(int index)`와 `remove(Object element)` 중 어떤 메서드를 호출할지 명확하지 않게 되고, 컴파일러는 `remove(int index)` 메서드를 호출하게 되어, 의도와는 다른 결과가 발생할 수 있다.
* 예제에서 `List<Integer>`가 있을 때, `list.remove(i)`를 호출하면 컴파일러가 `i`를 **오토박싱**하여 `Integer`로 처리할 수도 있고, 단순히 인덱스로 해석할 수도 있다.
*   예를 들어:

    ```java
    List<Integer> list = new ArrayList<>(Arrays.asList(-3, -2, -1, 0, 1, 2));
    list.remove(1);  // 여기서 1은 인덱스로 해석되어 두 번째 요소(-2)가 제거됨
    ```

    * 하지만 의도는 `1`이라는 **값**을 삭제하는 것이었다면, `list.remove((Integer) 1)`로 **명시적 캐스팅**을 통해 컴파일러가 혼동하지 않도록 해야 한다.

따라서 **다중 정의된 메서드를 사용할 때는 매개변수의 타입이 혼동을 줄 수 있는 상황을 피하거나, 명시적 형변환을 통해 의도를 명확하게 표현하는 것이 중요**하다.

### 3) 다중 정의의 함정 피하기 1: 인수 포워드하기

*   다중 정의로 여러 타입을 지원해야 하는 경우 **인수 포워딩**을 사용한다.

    ```java
    public boolean contentEquals(StringBuffer sb) {
        return contentEquals((CharSequence) sb);
    }
    ```

    * 위와 같이 **명시적 캐스팅**을 통해 **덜 특수한 다중 정의 메서드로 포워딩**하여 동일한 작업을 수행하도록 한다.

### 4) 잘못된 설계 사례: String 클래스의 다중 정의

*   **String 클래스**의 `valueOf` 메서드들은 같은 객체를 넘기더라도 **전혀 다른 일을 수행**

    ```java
    public static String valueOf(Object obj) {
        return (obj == null) ? "null" : obj.toString();
    }

    public static String valueOf(char[] data) {
        return new String(data);
    }
    ```

    * 이러한 **다중 정의**는 예상과 다르게 동작할 수 있어 잘못된 설계 사례로 남아 있다.

### 5) 올바른 다중 정의의 예: ObjectOutputStream 클래스

* **ObjectOutputStream** 클래스는 모든 기본 타입에 대해 `writeBoolean(boolean)`, `writeInt(int)` 같은 **다른 이름을 가진 메서드**들을 제공한다.
* 이 방식을 통해 **각 메서드의 역할을 명확히** 구분하며, **`read` 메서드와 이름을 짝지어 읽기/쓰기가 대응**됩니다.
  * 예: `writeInt(int)`와 `readInt()`

### 6) 생성자의 다중 정의와 정적 팩토리 메서드

* 생성자는 이름을 다르게 지을 수 없기 때문에 두 번째 생성자부터는 **다중 정의**가 불가피하다.
* 이 경우에는 **정적 팩토리 메서드**를 사용하는 것이 대안이 될 수 있다.
* **정적 팩토리 메서드**는 이름을 가질 수 있기 때문에 **생성의 의도를 명확히** 전달할 수 있다.

### 7) 혼동 없는 다중 정의 조건

* **매개변수 타입이 근본적으로 다른 경우**에는 다중 정의를 사용해도 혼란이 없다.
  * 예를 들어 `ArrayList`에는 `int`를 받는 생성자와 `Collection`을 받는 생성자가 있는데, 두 생성자 중 어떤 것이 호출될지 **명확히 구분**된다.
* 그러나, **오토박싱**이 도입되면서 기본 타입과 참조 타입 간에 혼란이 생기게 되었고, 이를 피하기 위해 **명시적 캐스팅**이 필요할 때가 있다.



## 📚 핵심 정리

* 다중 정의를 허용한다고 해서 남발하지 말자.
* 매개변수 수가 같은 다중 정의는 웬만하면 만들지 말자.
* **다중 정의의 함정**은 오토박싱, 람다와 메서드 참조 등의 상황에서 발생할 수 있다. 특히, **컴파일러가 정적 타입을 기준으로 메서드를 선택**하기 때문에 예상과 다른 메서드가 호출될 수 있다.
  * 이를 피하기 위해 다음과 같은 방법을 사용할 수 있다:
    * **명시적 캐스팅**을 사용하여 의도한 메서드 호출을 유도.
    * **함수형 인터페이스가 다른 경우에도 같은 위치의 인수로 받지 않도록** 설계
    * 다중 정의 대신 **메서드 이름을 다르게 정의**하여 명확성을 높인다.
    * 생성자의 경우 **정적 팩토리 메서드**를 사용하여 이름을 명확히 한다.
    * 다중 정의 시에는 **매개변수 타입이 근본적으로 다른 경우**에만 사용하는 것이 안전하다.

> 다중정의가 필요하다면, 형변환하여 정확한 다중정의 메서드가 선택되도록 하자



> 출처 및 참고
>
> * [https://jake-seo-dev.tistory.com/524](https://jake-seo-dev.tistory.com/524)
