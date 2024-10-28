# item 32 : 제네릭과 가변인수를 함께 쓸 때는 신중하라

## 1. 가변인수의 허점

### 1) 가변인수란?

{% hint style="info" %}
`가변인수(Variable Arguments)`란, 메서드나 함수에서 인수의 개수를 가변적으로 지정할 수 있는 기능을 말한다.
{% endhint %}

즉, 호출 시 전달되는 인수의 개수에 따라 메서드가 동작하도록 할 수 있다. Java에서는 가변인수를 `...` 문법을 통해 사용할 수 있으며, 이를 이용해 메서드 호출 시 여러 개의 인수를 배열 형태로 전달할 수 있다.

### 2) 가변인수의 문법

가변인수를 사용하기 위해서는 메서드의 매개변수 타입 뒤에 `...`을 붙여 선언한다. 예를 들어, 다음과 같이 가변인수를 선언할 수 있다:

```java
public void printNumbers(int... numbers) {
    for (int number : numbers) {
        System.out.println(number);
    }
}
```

위 메서드 `printNumbers`는 가변인수 `int... numbers`를 받아, 전달된 모든 숫자를 출력합니다. 호출할 때는 인수를 여러 개 전달할 수 있다:

```java
printNumbers(1); // 인수가 1개
printNumbers(1, 2, 3, 4); // 인수가 여러 개
```

위 예제에서 `numbers`는 배열로 취급되며, 전달된 인수들을 반복문을 통해 처리할 수 있다.

### 3) 가변인수의 규칙

1. **가변인수는 메서드에서 한 번만 사용 가능**: 메서드에 가변인수는 하나만 선언할 수 있다.
2. **가변인수는 마지막 매개변수여야 함**: 다른 매개변수와 함께 사용할 때 가변인수는 항상 마지막에 위치해야 한다.

```java
public void exampleMethod(String message, int... numbers) {
    System.out.println(message);
    for (int number : numbers) {
        System.out.println(number);
    }
}
```

하지만 다음과 같은 사용은 허용되지 않는다:

```java
// 오류: 가변인수는 마지막에 와야 함
public void invalidMethod(int... numbers, String message) {
    // 코드 내용
}
```

### 4) 가변인수의 장점

* **유연한 메서드 호출**: 메서드를 호출할 때 전달하는 인수의 개수를 유연하게 조절할 수 있어 편리하다.
* **코드 가독성 향상**: 여러 인수를 배열로 직접 전달하는 것보다 가변인수를 통해 메서드를 호출하면 가독성이 좋아진다.

### 5) 가변인수와 배열의 차이

가변인수는 내부적으로 배열로 처리되지만, 메서드를 호출할 때 배열을 직접 전달하는 것과는 다르다. 가변인수를 사용하면 메서드를 호출할 때 인수를 콤마로 구분해 나열할 수 있는 반면, 배열을 사용하는 경우 배열을 명시적으로 만들어서 전달해야 한다.

#### 예제

```java
public class VarargsExample {
    public static void main(String[] args) {
        sum(1, 2);           // 2개의 인수 전달
        sum(1, 2, 3, 4, 5);  // 5개의 인수 전달
    }

    // 가변인수를 이용한 메서드
    public static void sum(int... numbers) {
        int total = 0;
        for (int number : numbers) {
            total += number;
        }
        System.out.println("Sum: " + total);
    }
}
```

위 예제에서 `sum` 메서드는 전달받은 모든 숫자를 더해 합계를 출력한다. 가변인수를 사용함으로써 호출 시 인수의 개수에 제한이 없으며, 각기 다른 개수의 인수를 전달할 수 있다.

가변인수는 메서드를 더 유연하고 편리하게 호출할 수 있게 해주며, 다양한 상황에서 사용할 수 있는 강력한 기능

### 6) 가변인수의 허점

{% hint style="danger" %}
가변인수는 메서드에 넘기는 인수의 개수를 클라이언트가 조절할 수 있게 해주는데, 구현 방식에 허점이 있다.&#x20;
{% endhint %}

가변인수 메서드를 호출하면 가변인수를 담기 위한 배열이 자동으로 하나 만들어진다. **그런데 내부로 감춰야 했을 이 배열을 그만 클라이 언트에 노출하는 문제가 생겼다.** 그 결과 varargs 매개변수에 제네릭이나 매개 변수화 타입이 포함되면 알기 어려운 컴파일 경고가 발생한다.



## 2. 제네릭 가변인수

### 1) 제네릭과 varargs를 혼용하면 타입 안전성이 깨진다!

```java
import java.util.ArrayList;
import java.util.List;

public class Example {
    static void dangerous(List<String>... stringLists) {
        List<Integer> intList = List.of(42);
        Object[] objects = stringLists;
        objects[0] = intList; // 힙 오염 발생
        String s = stringLists[0].get(0); // ClassCastException
    }

    public static void main(String[] args) {
        List<String> stringList = new ArrayList<>();
        stringList.add("Hi there");
        dangerous(stringList);
    }
}
```

* 실체화 불가 타입은 <mark style="color:red;">런타임에는 컴파일타임보다 타입 관련 정보를 적게 담고 있음을 배웠다.</mark>&#x20;
* 거의 모든 제네릭과 매개변수화 타입 은 실체화되지 않는다.&#x20;
* 메서드를 선언할 때 실체화 불가 타입으로 varargs 매개 변수를 선언하면 컴파일러가 **경고**를 보낸다.&#x20;
* 가변인수 메서드를 호출할 때도 varargs 매개변수가 실체화 불가 타입으로 추론되면, 그 호출에 대해서도 경고 를 낸다.

<mark style="color:red;">매개변수화 타입의 변수가 타입이 다른 객체를 참조하면 힙 오염이 발생한다.</mark> 이렇게 다른 타입 객체를 참조하는 상황에서는 컴파일러가 자동 생성한 형변환이 실패할 수 있으니, 제네릭 타입 시스템이 약속한 타입 안 전성의 근간이 흔들려버린다.

```java
List<String>[] stringLists = new List<String>[l]; // (1)
List<Integer> intList = List.of(42); // (2)
Object[] objects = stringLists; // (3)
objects[0] = intList; // (4)
String s = stringLists[이.get(0); // (5)
```

{% hint style="danger" %}
제네릭 varargs 매개변수를 받는 메서드를 선언할 수 있게 한 이유는 무엇일까? 달리 말하면, 코드 위의 코드는 오류를 내 면서 **밑의 코드는 경고로 끝내는 이유**는 뭘까?&#x20;
{% endhint %}

```java
static void dangerous(List<String>... stringLists) {
        List<Integer> intList = List.of(42);
        Object[] objects = stringLists;
        objects[0] = intList; // 힙 오염 발생
        String s = stringLists[0].get(0); // ClassCastException
}
```

그 답은 <mark style="color:red;">제네릭이나 매개변수화 타입의 varargs 매개변수를 받는 메서드가 실무에서 매우 유용하기 때문 이다.</mark> 그래서 언어 설계자는 이 모순을 수용해위의 예제처럼 제네릭 varargs 매개변수를 받는 메서드를 선언할 수 있도록 했다. 대표적으로 아래와 같이 `Arrays.list(T... a)`, `EnumSet.of(E first, E... set)`과 같은 메서드가 있다.

```java
// Arrays.Java
@SafeVarargs
@SuppressWarnings("varargs")
public static <T> List<T> asList(T... a) {
    return new ArrayList<>(a);
}

// EnumSet.java
@SafeVarargs
public static <E extends Enum<E>> EnumSet<E> of(E first, E... rest) {
    EnumSet<E> result = noneOf(first.getDeclaringClass());
    result.add(first);
    for (E e : rest)
        result.add(e);
    return result;
}
```

### **2) 제네릭 가변인수의 문제점**

Java에서는 제네릭 배열을 직접 생성할 수 없지만, 제네릭 가변인수 메서드는 허용된다.&#x20;

> 가변인수 메서드를 호출할 때마다 내부적으로 배열이 생성되며, 이 <mark style="color:red;">배열의 타입은 컴파일 시에 결정되지 않고 런타임에 결정된다.</mark>&#x20;

예를 들어, `Arrays.asList(T... a)`와 같은 메서드는 가변인수를 사용해 편리하게 호출할 수 있지만, 내부적으로는 배열이 사용됩니다. 이 과정에서 타입 안전성이 깨질 수 있다.

예를 들어, 제네릭 배열을 반환하는 메서드가 있다고 가정해 보자

```java
static <T> T[] toArray(T... args) {
    return args;
}
```

위의 메서드는 가변인수로 전달된 배열을 그대로 반환한다. 문제는 메서드가 반환하는 배열의 타입이 런타임에 결정되며, 이때 타입이 잘못 판단될 수 있다는 점이다.&#x20;

```java
public static void main(String[] args) {
    String[] attributes = toArray("좋은", "빠른", "저렴한");
}
```

위 코드는 컴파일될 때는 문제가 없지만, 런타임에 `ClassCastException`이 발생할 수 있다. 이는 `toArray` 메서드가 반환하는 배열이 실제로는 `Object[]` 타입이지만, `String[]` 타입으로 형변환이 이루어지기 때문이다.

### **3)** 안전하게 제네릭 가변인수 메서드를 사용하는 방법

Java 7에서는 `@SafeVarargs` 애너테이션을 도입하여<mark style="color:red;">, 메서드 작성자가 메서드가 타입 안전함을 보장할 수 있을 때 사용하는 것이 가능해졌다.</mark> 이 애너테이션을 사용하면 클라이언트 측에서 발생하는 경고를 숨길 수 있다.

> 하지만, `@SafeVarargs`를 사용할 때는 메서드가 실제로 타입 안전한지 확인해야 한다.

메서드가 타입 안전하다고 판단할 수 있는 경우는 다음과 같다:

* **가변인수 배열에 값을 저장하지 않는 경우**: 가변인수 배열의 요소를 수정하거나 덮어쓰지 않는다면 안전하다.
* **배열의 참조가 외부에 노출되지 않는 경우**: 메서드가 가변인수 배열을 외부에 노출하지 않고, 내부적으로만 사용한다면 안전하다.

```java
@SafeVarargs
@SuppressWarnings("varargs")
public static <T> List<T> asList(T... a) {
    return new ArrayList<>(a);
}
```

### **4)  안전한 제네릭 Varargs 메서드의 조건**

다음 두 조건을 모두 만족하는 제네릭 Varargs 메서드는 안전하다고 볼 수 있다. 만약 두 조건 중 하나라도 위반된다면 메서드를 수정해야 한다.

1. **Varargs 매개변수 배열에 값을 저장하지 않는다.** 배열의 요소를 수정하거나 덮어쓰지 않는다.
2. **배열(혹은 배열의 복제본)을 신뢰할 수 없는 코드에 노출하지 않는다.** 배열을 외부에 노출하지 않고, 메서드 내부에서만 사용해야 한다.

### **5) 안전한 제네릭 가변인수 메서드 예시**

아래 예시는 안전한 제네릭 가변인수 메서드를 보여준다. 여러 리스트를 인수로 받아 하나의 리스트로 합치는 메서드

```java
@SafeVarargs
static <T> List<T> flatten(List<? extends T>... lists) {
    List<T> result = new ArrayList<>();
    for (List<? extends T> list : lists) {
        result.addAll(list);
    }
    return result;
}
```

위 메서드는 `@SafeVarargs` 애너테이션을 사용하여 타입 안전성을 보장하며, 가변인수 배열의 내용을 수정하지 않고, 배열을 외부에 노출하지 않기 때문에 안전하다.

### **6) 안전하지 않은 예시**

아래 코드는 가변인수를 사용해 생성된 배열을 그대로 반환하기 때문에 안전하지 않다

```java
static <T> T[] pickTwo(T a, T b, T c) {
    switch (ThreadLocalRandom.current().nextInt(3)) {
        case 0: return toArray(a, b);
        case 1: return toArray(a, c);
        case 2: return toArray(b, c);
    }
    throw new AssertionError(); // 도달할 수 없다.
}
```

이 메서드는 `pickTwo` 메서드를 호출하는 쪽에서 `ClassCastException`을 유발할 수 있니다. 내부적으로 생성된 배열이 `Object[]` 타입이기 때문이다.

### **7) 도우미 메서드를 사용한 해결 방법**

제네릭 가변인수 메서드의 안전성을 보장하기 위해 도우미 메서드를 사용할 수 있다. 도우미 메서드는 와일드카드 타입을 타입 매개변수로 변환하여 타입 안전성을 확보하는 역할을 한다.

```java
public static void wildcardSwap(List<?> list, int i, int j) {
    wildcardSwapHelper(list, i, j);
}

private static <E> void wildcardSwapHelper(List<E> list, int i, int j) {
    list.set(i, list.set(j, list.get(i)));
}
```

위 코드에서 `wildcardSwapHelper` 메서드는 `List<E>` 타입을 사용하여, `List<?>` 타입에서 꺼낸 원소의 타입을 타입 매개변수로 변환한다. 이렇게 하면 외부에서는 `List<?>` 타입을 사용해 유연하게 처리하고, 내부에서는 `List<E>` 타입을 사용해 타입 안전성을 보장할 수 있다.

### 8) 이전 예제의 문제 해결 방법

가변인수 메서드 대신, Java 표준 API인 `List.of()` 메서드를 사용하는 것이 더 안전하다.

```java
public static <T> List<T> pickTwo2(T a, T b, T c) {
    switch (ThreadLocalRandom.current().nextInt(3)) {
        case 0: return List.of(a, b);
        case 1: return List.of(a, c);
        case 2: return List.of(b, c);
    }
    throw new AssertionError(); // 도달할 수 없다.
}

@Test
public void test2() {
    List<String> pickTwo = pickTwo2("일", "이", "삼");
    System.out.println("pickTwo = " + pickTwo);
}
```

* **`List.of()` 사용:** `List.of()`는 Java 9 이상에서 사용할 수 있으며, 내부적으로 `@SafeVarargs` 애너테이션이 적용되어 있어 안전하게 가변인수 리스트를 생성할 수 있다.
* **장점:** 표준 라이브러리를 활용함으로써 가변인수 메서드보다 더욱 안전하고 일관된 코드를 작성할 수 있다.

## **✨ 최종 정리**

> 가변인수와 제네릭은 잘 어울리지 않는다.

제네릭 가변인수는 실무에서 매우 유용하지만, 타입 안전성을 신경 써야 한다. `@SafeVarargs` 애너테이션을 사용하거나 도우미 메서드를 통해 와일드카드 타입을 타입 매개변수로 변환하여 타입 안전성을 확보할 수 있다. 항상 가변인수 배열에 값을 저장하지 않도록 주의하고, 배열을 외부에 노출하지 않는 것이 안전성을 보장하는 핵심

* 가변인수 기능은 배열을 노출하여 추상화가 완벽하지 못하고, 배열과 제네릭 타입의 규칙이 서로 다르다.
* 제네릭 `varargs 매개변수`는 타입 안전하지는 않지만, 허용된다.
* 매우 편리하지만, 외부에 varargs 배열을 노출하거나, 내부적으로 다른 배열에 저장해놓고 변조하는 등의 일을 자제해야 한다.
* 안전이 보장되었다면 `@SafeVarargs` 애너테이션을 이용하여 안전함을 표시하자.

> 결국 쓰지 말라는 거잖아...? 음 실제 코드에서도 정의되어 있는 클래스 빼고는 안 쓰는 듯?

아래는 Collection 클래스

<figure><img src="../../../../.gitbook/assets/화면 캡처 2024-10-28 134558 (1).png" alt=""><figcaption></figcaption></figure>

> 출처
>
> * [https://madplay.github.io/post/effectivejava-chapter5-generics](https://madplay.github.io/post/effectivejava-chapter5-generics)
> * [https://jake-seo-dev.tistory.com/52?category=906605](https://jake-seo-dev.tistory.com/52?category=906605)
