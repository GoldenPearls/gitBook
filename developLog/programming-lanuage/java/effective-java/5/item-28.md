# item 28 : 배열보다는 리스트를 사용하라

배열을 꼭 반환해야 하는 상황이 아니라면 컬렉션[^1]을 사용하자..!

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

그 이유가 뭘까?

## 1. 배열과 제네릭 타입의 차이

### 1) 배열은 공변이다. 공변 불공변이란?

> item 26에서 한 번 정리했던 내용

**배열은 공변(covariant)다.** `공변성`이란 자신이 상속받은 부모 객체로 타입을 변화시킬 수 있다라는 것을 뜻한다.

Sub라는 클래스가 Super라는 클래스의 하위클래스라고 할때, 배열 `Sub[]`는 `Super[]`의 하위타입이 된다.

```
Object[] objectArray = new Long[1];
objectArray[0] = "저장 안되는 문자열"; //ArrayStoreException을 던진다.
```

<figure><img src="https://camo.githubusercontent.com/66d654769226c7d6be1ed00da26afc3dda26500a1cccfc68c3738d9a780eda75/68747470733a2f2f696d616765732e76656c6f672e696f2f696d616765732f696e6a6f6f6e323031392f706f73742f39656636373936612d383862652d343438312d396565382d3232316465663465373534302f696d6167652e706e67" alt=""><figcaption></figcaption></figure>

[![](https://camo.githubusercontent.com/6a398992c03ee212104a7f5a5bb1b0e22b52d20c38b026d0b1dc0af48ab91651/68747470733a2f2f696d616765732e76656c6f672e696f2f696d616765732f696e6a6f6f6e323031392f706f73742f37326135353365362d663439372d343033322d616565322d3434386335336135653932612f696d6167652e706e67)](https://camo.githubusercontent.com/6a398992c03ee212104a7f5a5bb1b0e22b52d20c38b026d0b1dc0af48ab91651/68747470733a2f2f696d616765732e76656c6f672e696f2f696d616765732f696e6a6f6f6e323031392f706f73742f37326135353365362d663439372d343033322d616565322d3434386335336135653932612f696d6167652e706e67)

이것을 시도하면 런타임에 오류가 난다.

**반면 제네릭은 불공변이다.** `List<Type1>`은 `List<Type2>`의 하위 타입도 아니고 상위 타입도 아니다.

```
List<Object> o1 = new ArrayList<Long>(); // 호환되지 않는 타입
o1.add("저장 안되는 문자열");
```

<figure><img src="https://camo.githubusercontent.com/3b8bcf6568b828edbee6913f012e8e68d1224fd64f50db87232fff17a932bb69/68747470733a2f2f696d616765732e76656c6f672e696f2f696d616765732f696e6a6f6f6e323031392f706f73742f38366638383835612d646534352d346436352d626535652d6263326533653031306231612f696d6167652e706e67" alt=""><figcaption></figcaption></figure>

`리스트`를 사용하면 애초에 <mark style="color:red;">컴파일 시에 오류가 난다.</mark> 어느쪽이든 Long용 저장소에 String은 못넣는다. 하지만 컴파일 에러는 가장 값이 싼 오류다.

정리하자면,&#x20;

* **공변(convariant)** : A가 B의 하위 타입일 때, T\<A>가 T\<B>의 하위 타입**이면** T는 공변이라고 한다. 타입 A가 B의 하위 타입(subtype)이라면, `A[]`는 `B[]`의 하위 타입이 될 수 있는 성질이다.
* **불공변(invariant)** : A가 B의 하위 타입일 때, T\<A>가 T\<B>의 하위 타입이 **아니면**, T는 불공변이라고 한다. 제네릭 타입 `List<A>`와 `List<B>`가 있을 때, `A`가 `B`의 하위 타입이더라도 `List<A>`는 `List<B>`의 하위 타입이 아니다. 즉, 제네릭 타입은 불공변성을 가진다.

### 2) 배열은 실체화（reify）된다.

{% hint style="info" %}
배열은 **런타임에도 자신이 담기로 한 원소의 타입을 인지하고 확인**한다.
{% endhint %}

**배열은 런타임에도 자신의 원소 타입을 인지**하고, 타입 안전성을 검사할 수 있다. 즉, `배열`은 공변성을 가진다. 예를 들어, `String[]` 배열은 `Object[]`로 사용할 수 있다.



즉, `배열`은 런타임 시 해당 오류를 알고 `리스트`는 컴파일 시 바로 알 수 있다고 함



> **제네릭 타입은 컴파일 시점에만 타입을 검사**하고, <mark style="color:red;">런타임에는 타입 정보가 소거(Erasure)</mark>된다.&#x20;

**제네릭**은 컴파일 시점에 타입 검사를 수행해 타입 안전성을 보장하지만, 런타임에는 제네릭 타입 정보가 사라진다. 이는 제네릭이 도입되기 전의 레거시 코드와 호환성을 유지하기 위한 메커니즘이다.

{% hint style="danger" %}
**제네릭 배열 생성이 허용되지 않는 이유**는 **타입 안전하지 않기 때문**이다.
{% endhint %}

제네릭 배열을 생성하면 런타임에 `ClassCastException`이 발생할 수 있다. 이는 제네릭의 핵심 목표인 "런타임에 타입 안전성을 보장한다"는 취지에 어긋나게 된다.&#x20;

배열은 **공변**이기 때문에, 예를 들어 `String[]` 배열을 `Object[]`로 취급할 수 있지만, 제네릭 타입은 **불공변**이어서 `List<String>`과 `List<Object>`는 아무런 관계가 없다.



이상의 주요 차이로 인해 배열과 제네릭은 잘 어우러지지 못한다. **배열은 제네릭 타입, 매개변수화 타입, 타입 매개변수로 사용할 수 없다**.&#x20;

> 즉, 코드를 new List\[], new List\[], new E\[] 식으로 작성하면 컴파일할 때 **제네릭 배열 생성 오류를 일으킨다.**

## 2. 만약 제네릭 배열을 허용한다면 어떤 일이 발생할까?

### 1) 발생하는 문제

```java
List<String>[] stringLists = new List<String>[1]; // 1. 가정상 허용한다고 가정
List<Integer> intList = List.of(42);              // 2. Integer 리스트 생성
Object[] objects = stringLists;                   // 3. List<String>[]를 Object[]에 할당
objects[0] = intList;                             // 4. List<Integer>를 배열의 첫 원소로 저장
String s = stringLists[0].get(0);                 // 5. 첫 원소를 String으로 가져옴
```

1. **1번 라인**: `List<String>[]` 배열을 생성한다고 가정하자. 이는 제네릭 배열 생성을 허용하지 않기 때문에, 실제로는 컴파일 오류가 발생한다.
2. **2번 라인**: `List<Integer>`를 생성한다. 이는 정상적인 코드다.
3. **3번 라인**: `List<String>[]`를 `Object[]`로 할당한다. 배열은 공변이므로 문제가 없다.
4. **4번 라인**: `List<Integer>`를 `Object[]`의 첫 원소로 추가한다. 컴파일러는 이를 허용하며, 런타임에 `List<Integer>`는 단순히 `List`로 간주된다. **제네릭은 소거 방식으로 구현되어서 이 역시 성공한다.**
5. <mark style="color:red;">**5번 라인**</mark><mark style="color:red;">:</mark> `stringLists[0]`에는 원래 `List<String>`이 들어 있어야 하는데, 실제로는 `List<Integer>`가 들어 있다. 컴파일러는 꺼낸 원소를 <mark style="color:red;">자동으로 String으로 형변환하는데, 이 원소는 Integer이므로</mark> `stringLists[0].get(0)`에서 `String` 타입으로 가져오려고 시도하면 `ClassCastException`이 발생할 수 있다.

### 2) 실체화 불가 타입(Non-Reifiable Type)

{% hint style="success" %}
**런타임에 타입 정보를 유지하지 않는 제네릭 타입을 의미**하며, 제네릭 배열 생성과 관련된 문제의 원인이다.
{% endhint %}

* **실체화 불가 타입**은 **컴파일 시점보다 런타임에 타입 정보가 소거된 타입**을 의미한다. 예를 들어, `E`, `List<E>`, `List<String>` 같은 제네릭 타입은 런타임에는 타입 정보를 유지하지 않는다.
* 제네릭 타입은 **런타임에 실체화되지 않기 때문에**, 제네릭 배열을 생성하면 런타임에 `ClassCastException`이 발생할 가능성이 커진다.
* **실체화될 수 있는 타입**은 `List<?>`, `Map<?,?>`와 같은 **비한정적 와일드카드 타입**뿐이다. 비한정적 와일드카드 타입은 런타임에 타입 정보가 소거되어도 안전하게 사용할 수 있다.

## 3. 제네릭 배열 대신 컬렉션 사용

> 제네릭 배열 생성이 불가능하므로, 배열 대신 컬렉션(`List<E>`)을 사용하는 것이 더 안전하다.

* 배열을 사용하려고 할 때 **제네릭 배열 생성 오류**나 **비검사 형변환 경고**가 발생하면, 배열 대신 컬렉션을 사용하면 해결될 수 있다.
* 컬렉션은 제네릭 타입을 지원하며, 배열보다 유연하게 사용할 수 있다.

```java
// 배열 대신 List 사용 예
List<String> stringList = new ArrayList<>();
stringList.add("Hello");
stringList.add("World");
```

## 4. 제네릭과 가변인수 메서드의 문제

### 1) 제네릭 가변인수 메서드란

{% hint style="info" %}
**제네릭 가변인수 메서드**는 **제네릭 타입 매개변수를 사용하면서,** [**가변인수(varargs)**](#user-content-fn-2)[^2]**를 지원하는 메서드**를 의미이다.
{% endhint %}

* 가변인수 메서드는 메서드를 호출할 때 **임의의 개수의 인자를 전달할 수 있는 기능**이다. 인자의 개수를 정하지 않고 여러 개의 인수를 받을 수 있다.
* 제네릭 가변인수 메서드를 사용하면 **제네릭 타입의 여러 개의 인수를 전달**할 수 있다.

```java
import java.util.Arrays;
import java.util.List;

public class GenericVarargsExample {

    // 제네릭 가변인수 메서드
    @SafeVarargs
    public static <T> List<T> toList(T... elements) {
        return Arrays.asList(elements);
    }

    public static void main(String[] args) {
        List<String> stringList = toList("Apple", "Banana", "Cherry");
        System.out.println(stringList); // 출력: [Apple, Banana, Cherry]

        List<Integer> integerList = toList(1, 2, 3, 4, 5);
        System.out.println(integerList); // 출력: [1, 2, 3, 4, 5]
    }
}
```

1. **`<T>`**: 제네릭 타입 매개변수 `T`를 사용하여, 메서드의 인수 타입을 유연하게 지정하다.
   * `T`는 호출 시점에 **타입이 결정되는 제네릭 타입**이다. 예를 들어, `String`이나 `Integer` 등이 될 수 있다.
2. **가변인수 `T... elements`**:
   * `T` 타입의 **가변인수**를 받는다. 이로 인해 **임의의 개수의 인수를 전달할 수 있다**. 전달된 인수는 배열로 처리된다.
3. **`@SafeVarargs` 애너테이션**:
   * 이 애너테이션은 **제네릭 가변인수 메서드에서 발생하는 경고를 억제**하다. 제네릭 타입의 가변인수는 컴파일 시 경고가 발생할 수 있기 때문에, **메서드가 타입 안전함을 보장할 수 있을 때** 사용해야 한다.
4. **`Arrays.asList(elements)`**:
   * 전달된 가변인수 배열을 리스트로 변환
5. **`main` 메서드**:
   * `toList` 메서드를 사용해 여러 개의 `String`이나 `Integer` 타입의 인수를 전달하여 리스트를 생성

### **2)** 가변인수 메서드의 특징

```java
public class VarargsExample {

    // 가변인수 메서드
    public static int sum(int... numbers) {
        int total = 0;
        for (int number : numbers) {
            total += number; // 전달된 모든 인수를 더함
        }
        return total;
    }

    public static void main(String[] args) {
        // 가변인수 메서드를 호출할 때, 여러 개의 인수를 전달할 수 있음
        System.out.println(sum(1, 2, 3)); // 출력: 6
        System.out.println(sum(4, 5, 6, 7, 8)); // 출력: 30
        System.out.println(sum()); // 출력: 0 (인수를 전달하지 않으면 0 반환)
    }
}
```

1. **가변인수는 배열로 처리**:
   * 메서드 내부에서 가변인수 매개변수는 배열로 간주되며, 배열의 길이와 요소에 접근할 수 있다.
2. **하나의 가변인수만 사용 가능**:
   * 메서드의 매개변수 목록에서 **하나의 가변인수만 사용할 수 있으며, 항상 마지막에 위치해야** 한다.
   * 예를 들어, `public void example(int fixed, String... varargs)`와 같이 가변인수는 마지막 매개변수여야 한다.
3. **인수를 전달하지 않을 수 있음**:
   * 가변인수는 호출 시 인수를 하나도 전달하지 않아도 된다. 이 경우, 메서드 내부에서 해당 배열의 길이는 `0`이 된다.

### 3) 제네릭과 가변인수 메서드의 문제

* **제네릭 타입과 가변인수 메서드(varargs method)**&#xB97C; 함께 사용하면 경고 메시지가 발생할 수 있다. 가변인수 메서드를 호출할 때마다 매개변수를 담을 배열이 생성되는데, 그 배열의 원소가 실체화 불가 타입일 경우 경고가 발생하다.
* 이 문제는 `@SafeVarargs` 애너테이션을 사용하여 해결할 수 있다. `@SafeVarargs`는 메서드의 <mark style="color:red;">가변인수 매개변수가 안전하다고 확신될 때 사용한다.</mark>

> 아래 예제에서는 가변인수를 사용해 여러 개의 리스트를 결합하는 메서드를 정의하고, `@SafeVarargs` 애너테이션을 사용해 경고를 억제한다.

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class SafeVarargsExample {

    // @SafeVarargs 애너테이션을 사용하여 경고를 억제
    @SafeVarargs
    public static <T> List<T> combineLists(List<T>... lists) {
        List<T> result = new ArrayList<>();
        for (List<T> list : lists) {
            result.addAll(list);
        }
        return result;
    }

    public static void main(String[] args) {
        List<String> list1 = Arrays.asList("Apple", "Banana");
        List<String> list2 = Arrays.asList("Carrot", "Date");
        List<String> list3 = Arrays.asList("Eggplant", "Fig");

        // combineLists 메서드 호출 - 여러 리스트를 결합
        List<String> combinedList = combineLists(list1, list2, list3);
        System.out.println(combinedList); // 출력: [Apple, Banana, Carrot, Date, Eggplant, Fig]
    }
}
```

1. **`@SafeVarargs` 애너테이션**:
   * `combineLists` 메서드는 제네릭 가변인수 메서드이기 때문에, `@SafeVarargs` 애너테이션을 사용하여 **경고를 억제한**다.
   * 이 애너테이션은 메서드가 **안전하게 동작한다고 확신할 때만** 사용해야 한다. 즉, 배열을 조작하거나 노출하지 않는 등, 타입 안전성을 깨뜨리지 않는 경우에만 사용해야 한다.
2. **`combineLists` 메서드**:
   * 이 메서드는 가변인수로 전달된 여러 `List<T>`를 하나의 `List<T>`로 결합하여 반환한다.
   * 가변인수 `List<T>... lists`를 사용하면 호출할 때 여러 개의 리스트를 전달할 수 있다.
   * 각 리스트를 순회하면서, `result` 리스트에 모든 요소를 추가
3. **`main` 메서드**:
   * 여러 개의 리스트를 생성하고, `combineLists` 메서드를 사용하여 결합한다.
   * 결합된 리스트는 `[Apple, Banana, Carrot, Date, Eggplant, Fig]`로 출력된다.

### 4) 경고가 발생하는 이유와 `@SafeVarargs`의 필요성

* 가변인수 메서드를 호출할 때마다 매개변수를 담을 배열이 생성되는데, 그 배열의 타입이 **실체화 불가 타입**(제네릭 타입)일 경우 컴파일러가 경고를 발생시킨다.
* `@SafeVarargs`는 **메서드가 타입 안전성을 보장할 수 있는 경우에만 사용**해야 하며, 배열 조작이나 노출이 없는 경우에 사용하여 경고를 억제할 수 있다.

## 5.  `E[]`대신 컬렉션 `List<E>`

배열로 형변환할 때 오류나 경고가 뜨는 경우 `E[]`대신 컬렉션 `List<E>`를 사용하면 해결된다. 코드가 복잡해지고 성능이 살짝 나빠질 수 있지만, 타입 안정성과 상호운용성은 좋아진다.

```java
public class Chooser {
    private final Object[] choiceArray;
    
    public Chooser(Collection choices) {
        choiceArray = choices.toArray();
    }
    
    public Object choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceArray(rnd.nextInt[choiceArray.length)];
}
```

이 클래스를 사용하려면 choose 메서드를 호출할 때마다 반환된 Object를 원한는 타입으로 형변환해야 한다. **만약 다른 타입의 원소가 들어있었다면 런타임에 형변환 오류가 날 것이다.** 제네릭으로 만들어보자.

```java
public class Chooser<T> {
    private final T[] choiceArray;
    
    public Chooser(Collection<T> choices) {
        choiceArray = choice.toArray();
    }
}
```

위의 코드를 컴파일하면 오류 메시지가 출력된다.

[![](https://camo.githubusercontent.com/95596988f81ebe1c2c1e626a825862e3c605008120d9d913637f07d210b14d45/68747470733a2f2f696d616765732e76656c6f672e696f2f696d616765732f696e6a6f6f6e323031392f706f73742f62356136373239612d633039642d346139662d393065352d3637303439306330356335302f696d6167652e706e67)](https://camo.githubusercontent.com/95596988f81ebe1c2c1e626a825862e3c605008120d9d913637f07d210b14d45/68747470733a2f2f696d616765732e76656c6f672e696f2f696d616765732f696e6a6f6f6e323031392f706f73742f62356136373239612d633039642d346139662d393065352d3637303439306330356335302f696d6167652e706e67)

Object 배열을 T 배열로 변환해도 또 다른 경고가 뜬다. **T가 무슨 타입인지 알 수 없으니 컴파일러는 이 형변환이 런타임에도 안전을 보장하지 못한다는 것이다**. 제네릭에서는 원소의 타입 정보가 소거되어 런타입에는 무슨 타입인지 알 수 없다. **동작은 하지만 원인을 제거한 것은 아니다.**

```java
public class Chooser<T> {
    private final List<T> choiceList;
    
    public Chooser(Collection<T> choices) {
        choiceList = new ArrayList<>(choices);
    }
    
    public T choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceList.get(rnd.nextInt(choiceList.size()));
    }
}
```

이제 런타임에 ClassCastException을 만날 일이 없다.

## **✨최종 정리**

* **제네릭과 배열의 차이**: 배열은 런타임에 타입을 인지하지만, 제네릭은 런타임에 타입 정보가 소거된다.
* **제네릭 배열 생성이 불가능한 이유**: 타입 안전성을 보장하기 위해 제네릭 배열 생성을 막아 놓았다.
* **실체화 불가 타입**: 런타임에 타입 정보를 유지하지 않는 제네릭 타입을 의미하며, 제네릭 배열 생성과 관련된 문제의 원인이다.
* **대안**: 배열 대신 컬렉션(`List<`[`E`](#user-content-fn-3)[^3]`>`)을 사용하는 것이 타입 안전성과 상호운용성을 높이는 방법이이다.

> 참고 글 : [https://github.com/woowacourse-study/2022-effective-java/blob/main/05%EC%9E%A5/%EC%95%84%EC%9D%B4%ED%85%9C\_28/%EB%B0%B0%EC%97%B4%EB%B3%B4%EB%8B%A4%EB%8A%94\_%EB%A6%AC%EC%8A%A4%ED%8A%B8%EB%A5%BC\_%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC.md](https://github.com/woowacourse-study/2022-effective-java/blob/main/05%EC%9E%A5/%EC%95%84%EC%9D%B4%ED%85%9C_28/%EB%B0%B0%EC%97%B4%EB%B3%B4%EB%8B%A4%EB%8A%94_%EB%A6%AC%EC%8A%A4%ED%8A%B8%EB%A5%BC_%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC.md)

[^1]: List, Set, Map

[^2]: 메서드가 임의의 개수의 인수(arguments)를 받을 수 있도록 하는 기능

[^3]: **`E`는 제네릭 타입 매개변수**로, 일반적으로 **Element**의 약자로 사용됩니다. 이는 주로 **컬렉션(Collection)** 클래스에서 **요소의 타입을 나타내기 위해** 사용됩니다.

    Java에서 제네릭은 **타입 매개변수를 사용하여 코드의 타입 안전성을 높이고, 코드의 재사용성을 개선하기 위한 기능**입니다. `E`는 이러한 제네릭 타입 매개변수 중 하나이며, **타입을 유연하게 지정할 수 있도록** 해줍니다.
