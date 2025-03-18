# item 29 : 이왕이면 제네릭 타입으로 만들라.

## 1. 기존  단순 스택 코드 뭐가 문제 일까? : 오브젝트 기반

```java
import java.util.Arrays;
import java.util.EmptyStackException;

public class Stack {
    private Object[] elements; // 스택 요소를 담을 배열
    private int size = 0; // 스택의 현재 크기
    private static final int DEFAULT_INITIAL_CAPACITY = 16; // 기본 초기 용량

    // 기본 생성자: 초기 용량을 가진 배열 생성
    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    // 요소를 스택에 추가
    public void push(Object e) {
        ensureCapacity(); // 배열의 크기를 확인하여 필요 시 확장
        elements[size++] = e; // 요소를 배열에 추가하고 크기 증가
    }

    // 스택에서 요소를 제거하고 반환
    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException(); // 스택이 비어 있을 경우 예외 발생
        }
        Object result = elements[--size]; // 스택의 마지막 요소 반환
        elements[size] = null; // 사용이 끝난 참조 해제
        return result;
    }

    // 스택이 비어 있는지 확인
    public boolean isEmpty() {
        return size == 0;
    }

    // 배열의 크기를 확장하여 용량을 보장
    private void ensureCapacity() {
        if (elements.length == size) {
            // 배열의 크기를 2배 + 1로 확장
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}

```

기존의 `Stack` 클래스는 다음과 같은 문제점이 있다.

<figure><img src="../../../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

* **타입 안전성이 보장되지 않음**: `Stack` 클래스는 `Object[]` 타입의 배열을 사용한다. 이로 인해 `push` 메서드로 <mark style="color:red;">어떤 타입의 객체든 추가할 수 있고,</mark> `pop` 메서드를 호출한 후 반환된 값을 형변환해야 한다.
* **ClassCastException 발생 가능성**: 형변환 시 런타임에 타입이 맞지 않으면 `ClassCastException`이 발생할 수 있다. 예를 들어, 코드에서 `String` 타입으로 형변환을 시도했지만, 실제로는 `Integer` 타입이 들어 있다면 예외가 발생한다.

**문제점이 드러나는 예제 코드**

```java
public static void main(String[] args) {
    Stack stack = new Stack(); // Stack은 제네릭을 사용하지 않은 일반 클래스
    stack.push(1); // 정수형 값 1을 스택에 추가
    String item = (String) stack.pop(); // pop 후 String으로 형변환
    System.out.println(item); // ClassCastException 발생 가능성 있음
}
```

위 예제에서 `push` 메서드로 `Integer` 값을 추가하고, `pop` 후 `String`으로 형변환하려고 한다. 이로 인해 `ClassCastException`이 발생할 수 있다.

> 즉, 클라이언트는 스택에서 꺼낸 객체를 형변환해야 하는데, 이때 런타임 오류가 날 위험이 있다.

## 2. 우선 object를 Generic으로 바꾸기

{% hint style="success" %}
Java에서 제네릭을 사용하면 타입 안전성을 보장할 수 있다. 클래스를 제네릭으로 바꾼다고 해도 현재 버전을 사용하는 클라이언트 에는 아무런 해가 없다.
{% endhint %}

제네릭으로 `Stack` 클래스를 수정하여 타입을 명시적으로 지정할 수 있도록 하겠다.

### 1) 제네릭 스택으로 가는 첫 단계 : `Stack<E>`로 제네릭 타입을 추가하여 스택의 요소 타입을 지정할 수 있도록 했다.

{% hint style="info" %}
클래스 선언에 타입 매개 변수를 추가하는 일이다. 스택이 담을 원소의 타입 하나만 추가하면 되는데 보통 타입 이름은 e를 사용한다.&#x20;
{% endhint %}

```java
public class Stack<E> {
    private E[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new E[DEFAULT_INITIAL_CAPACITY];
    }
```

다음과 같은 컴파일 에러 발생

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> <mark style="color:red;">제네릭은 E와 같은 실체화 불가 타입으로는 배열을 만들 수 없다.</mark>

**즉, 제네릭 타입 매개변수로 배열을 생성하는 것이 허용되지 않기 때문에** 발생한다. 제네릭 배열을 생성하는 코드는 타입 안전성을 보장할 수 없기 때문에 컴파일가 금지한다.

* **`elements = new E[DEFAULT_INITIAL_CAPACITY];`**:
  * 이 부분에서 제네릭 타입 `E`로 배열을 생성하려고 시도한다.
  * Java에서는 제네릭 타입 `E`로 배열을 생성할 수 없으므로, **"generic array creation"** 오류가 발생한다.
* **오류의 이유**:
  * Java의 **타입 소거(erasure)** 메커니즘 때문에 제네릭 타입의 정보는 **컴파일 시점에만 존재**하고, 런타임에는 사라진다. 이로 인해 `E[]`와 같은 제네릭 배열은 **런타임에 타입을 알 수 없어 안전하지 않기 때문에 생성할 수 없다**.

> 제네릭은 실체화 할 수 없다. 그렇기에 **제네릭 배열 생성 오류 문제**를 해결해주기 위해서는 두 가지 방법이 있다.

전체 코드

```java
public class Stack {
    private E[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(E e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public E pop() {
        if (size == 0)
            throw new EmptyStackException();
        E result = elemtns[--size];
        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }
    ...
}
```

### 2) 우회 방법 1 : 제네릭 배열 생성을 금지하는 제약을 대놓고 우회

{% hint style="info" %}
배열을 Object로 생성하고 제네릭으로 형 변환
{% endhint %}

제네릭 배열을 생성할 때 `E[]`를 `Object[]`로 생성하고 형변환하는 방식이다. 배열의 선언은 여전히 `E[] elements`로 유지하면서, 배열 생성 시에만 `Object[]`로 만들고 `(E[])`로 형변환하여 사용하는 것이다.

> 즉, 배열을 생성할 때는 `Object[]`로 만들고, 형변환을 통해 `E[]` 타입으로 사용하는 것이다. 이렇게 하면 제네릭 배열을 사용할 수 있게 된다.

<figure><img src="../../../../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

**배열 생성 문제 해결하기 위해** 제네릭 배열 생성은 불가능하므로 `(E[]) new Object[DEFAULT_INITIAL_CAPACITY]`로 배열을 생성한 후, <mark style="color:red;">비검사 형변환 경고를 억제</mark>하기 위해 `@SuppressWarnings("unchecked")`를 사용했다. 애너테이션은 **경고를 억제하려는 범위를 최소로 좁혀** 적용하는 것이 좋다.

{% hint style="success" %}
여기서 중요한 점은 **컴파일러가 비검사 형변환이 안전한지 검증할 수 없을 때, 프로그래머가 스스로 그 안전성을 증명해야 한다는 것**이다.
{% endhint %}

**비검사 형변환(Unchecked Cast)이란?**

* **비검사 형변환**은 컴파일러가 형변환의 타입 안전성을 보장할 수 없을 때 발생하는 경고이다.
* 제네릭을 사용할 때, 런타임에 타입 정보가 소거되어 컴파일러가 형변환의 안전성을 확인할 수 없는 경우에 발생한다.

**안전성을 검증하는 방법**

비검사 형변환이 발생할 때, 프로그래머가 직접 **형변환이 안전함을 증명**해야 한다.&#x20;

1. **형변환 대상이 되는 배열이나 컬렉션이 외부에 노출되지 않음**을 확인
   * 예를 들어, 배열이 `private` 필드에 저장되어 있고, 외부로 반환되거나 다른 메서드에 전달되지 않는다면 안전하다.
2. **배열에 저장되는 모든 원소의 타입이 일관성 있게 유지됨**을 확인
   * `push` 메서드를 통해 배열에 저장되는 모든 원소의 타입이 항상 동일한 제네릭 타입임을 보장할 수 있다면, 형변환은 안전하다.

> 위 코드는 `Object` 배열을 `E[]`로 형변환하는 부분에서 비검사 형변환 경고가 발생한다. 이 형변환이 안전함을 증명하려면 다음을 확인해야 한다:

* **배열 `elements`는 `private` 필드로, 외부에 노출되지 않음**:
  * `elements`는 `Stack` 클래스 내부에서만 사용되며, 외부에서 직접 접근할 수 없다.
* **`push` 메서드가 배열에 저장하는 모든 원소의 타입이 항상 `E`임**:
  * `push(E e)` 메서드를 통해 추가되는 원소의 타입은 항상 제네릭 타입 `E`로 제한한다.

이 두 가지 조건을 만족하므로, 배열 형변환은 안전하다.&#x20;

### 3) 우회 방법 2 :  elements 필드의 타입을 E\[] 에서 Object\[]로 바꾸는 것

{% hint style="success" %}
elements 필드의 **타입만 E\[]가 아닌, Object로 사용하는 것**이다.
{% endhint %}

이 경우 pop 메서드에서 오류가 발생한다. e는 **실체화 불가 타입**이므로 컴파일러는 런타임에 이뤄지는 **형변환이 안전한 지 증명할 방법이 없다.**  result를 E로 받아야하는데, 배열이 `Object`이기 때문.. 따라서 형변환을 해주어야한다.

형변환을 하면 경고가 뜨는데, 다시 `@SuppressWarnings` 어노테이션으로 해결

> 이번에도 마찬가지로 우리가 직접 증명하고 경고를 숨길 수 있다. pop 메서드 전체에서 경고를 숨기지 말고, 아이템 27의 조언을 따라 **비검사 형변환을 수행하는 할당문에서만** 숨겨보자.

<figure><img src="../../../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

pop에서 형변환을 하는 형태

### 4) 위의 2가지 방법의 차이는?

**방법 1:**

<figure><img src="../../../../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

* 배열을 `E[]`로 선언하여 타입에 대한 일관성을 명시한다. (타입 안전)
* 하지만 런타임에 `E[]`가 아닌 `Object[]`로 동작힌다.
* 힙 오염의 가능성이 존재하며, `Object[]`로 형변환할 때 문제가 발생할 수 있다.

> 여기서 잠깐! 힙오염(Heap Pollution)이란?

*   <mark style="color:red;">주로 매개변수화 타입의 변수가 타입이 다른 객체를 참조하게 되어, 힙 공간에 문제가 생기는 현상을 의미한다.</mark> 즉 컴파일 중에 정상적으로 처리되며, 경고를 발생시키지 않고 나중에 런타임 시점에 `ClassCastException`이 발생하는 문제를 나타낸다.

    **힙 오염이 나타나는 경우**

    <figure><img src="https://blog.kakaocdn.net/dn/bdNhvq/btsFlUMRrgo/r0sKkXY8uEE2Tx1jilPixK/img.png" alt=""><figcaption></figcaption></figure>

    해당 코드는 `ArrayList` 에 상속 관계도 아닌 서로 다른 두 타입의 객체가 추가되었다. 말이 안되지만, 컴파일러는 잘못된 상황을 다음과 같은 두 가지 이유로 알아차리지 못하게 된다.

    1. **타입 캐스팅 체크는 컴파일러가 하지 않는다.** 오직 대입되는 참조변수에 저장할 수 있느냐만 검사한다.
    2. 제네릭의 소거 특성으로 인해 컴파일이 끝난 클래스 파일의 코드에는 타입 파라미터 대신 `Object` 가 남아있게 된다. `ArrayList<Object>` 에는 `String` 이나 `Integer` 모두 넣는 것이 가능해진다.

    따라서 실행을 시키고 `get 코드`를 통해 **요소를 꺼낼 때가 되어서야 런타임 캐스팅 예외가 발생하게 된다.** 근본적인 원인을 잡기 위해서는, **꺼낼때가 아닌 넣을 때를 검사해야 한다.** 더 자세한 내용은 아이템 32에서..

{% hint style="success" %}
`즉, invalidPush` 메서드를 통해 잘못된 타입의 데이터를 삽입할 수 있다면, 런타임에 `ClassCastException`이 발생할 수 있다.
{% endhint %}

> 왜 런타임에서 `E[]`가 `Object[]`로 바뀌는가??

* 타입 소거(Type Erasure) 때문에 발생한다.
* 제네릭은 컴파일 후에 `Object` 타입으로 변환된다. 런타임에서는 구체적인 타입 정보가 소거된다.
* 따라서, 컴파일러는 제네릭 타입을 `Object` 배열로 변환하여 실행다.



**방법 2:**

<figure><img src="../../../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

* 애초에 `Object[]` 배열로 타입을 선언하고 힙 오염의 가능성을 피한다.
* 그 후 필요한 시점에서만 형변환을 수행한다.
* `pop` 메서드에서 데이터를 반환할 때마다 `Object` 배열의 값을 `E`로 형변환해야 한다.

## 3. 제네릭 타입 사용 시 주의점

* 타입 매개변수에 제약이 없는 경우, 기본 타입인 `Object`를 사용하는 방식으로 처리할 수 있다.
* 제약을 추가할 수도 있다. 예를 들어, `Comparable` 인터페이스를 구현한 타입만 받을 수 있게 설정할 수 있다.

## 4. 타입 매개변수 제약

**타입 매개변수 제약**은 제네릭을 사용할 때 타입의 범위를 제한하여 안전성과 유연성을 높이기 위해 사용된다. 제네릭 타입을 선언할 때, 허용되는 타입을 특정 타입의 하위 타입이나 상위 타입으로 제한할 수 있는데, 이를 통해 제네릭 코드의 타입 안정성을 강화할 수 있다.

제네릭에서 타입 매개변수 제약은 안전한 타입 사용과 더불어 코드의 재사용성을 높이는 중요한 역할을 한한다.

**`<? extends T>` 형 (공변성)**

{% hint style="success" %}
공변성은 `<? extends T>`를 사용하여 나타낼 수 있다. 여기서 `T`는 특정 타입이며, `<? extends T>`는 `T`를 상속받은 타입들을 허용한다.
{% endhint %}

<figure><img src="../../../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

* 상위 타입에 대한 관계를 유지하며, 주로 데이터를 읽을 때 사용
* `T`를 상속받은 타입이 올 수 있다.
* `List<String>`이 `List<Object>`의 하위 타입이 될 수 있는 상황이 공변성이다.

<figure><img src="../../../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

* 런타임에서 잘못된 형변환이 되지 않을까? => T로 타입 업캐스팅한 데이터는 읽기만 가능(READ- ONLY)

**`<? super T>` 형 (반공변성)**

{% hint style="success" %}
반공변성은 `<? super T>`를 사용하여 나타낼 수 있다. 여기서 `T`는 특정 타입이며, `<? super T>`는 `T`의 상위 타입을 허용한다.
{% endhint %}

<figure><img src="../../../../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

* 하위 타입에 대한 관계를 유지하며, 주로 데이터를 쓸 때 사용
* `T`의 부모 타입이 올 수 있다.
* `List<Object>`가 `List<String>`의 상위 타입이 될 수 있는 상황이 반공변성다.

<figure><img src="../../../../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

* 데이터를 쓰기 전용(write-only)으로 사용할 때 유용

### 한정적 타입 매개변수

`Stack` 처럼 대다수의 제네릭 타입은 타입 매개변수의 아무런 제약을 두지 않지만, 간혹 받을 수 있는 하위 타입에 제약이 있는 제네릭 타입도 존재한다.

```javascript
class DelayQueue<E extends Delayed> implements BlockingQueue<E>
```

이러한 타입 매개변수 `E` 를 한정적 타입 매개변수라 한다.



매개변수화 타입을 특정한 타입으로 한정짓고 싶을 때 사용할 수 있다.



> 타입 매개변수 목록인 는 java.util.concurrent.Delayed 의 하위 타입만 받는다는 뜻이다.

## 5. 스터디에서 나온 질문

> List\<Number>와 List\<E extends Number>의 차이점

`List<Number>`와 `List<E extends Number>`는 둘 다 제네릭 타입을 사용하지만, 그 의미와 사용 방식에서 중요한 차이점이 있습니다.&#x20;

#### 1. `List<Number>`

`List<Number>`는 `Number` 타입을 요소로 가지는 리스트입니다.&#x20;

> 여기서 중요한 점은, 리스트의 제네릭 타입이 정확히 `Number`여야 한다는 것입니다.

* **`Number` 클래스**: `Number`는 자바에서 모든 숫자 클래스(`Integer`, `Float`, `Double`, `Long` 등)의 부모 클래스입니다. 숫자와 관련된 다양한 클래스는 모두 `Number`를 상속받습니다.
* **동작 방식**:
  * `List<Number>`는 `Number` 타입이나 그 하위 타입의 객체를 담을 수 있습니다. 하지만 **제네릭 타입이 정확히 `Number`여야 하므로**, `List<Integer>`, `List<Double>`와 같은 타입은 호환되지 않습니다.
  * 즉, `List<Number>`는 `List<Integer>`나 `List<Double>`와는 다른 타입으로 간주됩니다.

예를 들어 다음 코드는 유효합니다:

```java
List<Number> numberList = new ArrayList<>();
numberList.add(10); // Integer를 추가 (자동 박싱)
numberList.add(10.5); // Double을 추가
```

하지만, 다음 코드는 컴파일되지 않습니다:

```java
List<Integer> intList = new ArrayList<>();
List<Number> numList = intList; // 컴파일 오류, 서로 다른 타입
```

위 코드가 컴파일되지 않는 이유는 **제네릭 타입의 불공변성(invariance)** 때문입니다. `List<Number>`와 `List<Integer>`는 서로 다른 제네릭 타입으로 간주됩니다.

#### 2. `List<E extends Number>`

`List<E extends Number>`는 제네릭 타입 매개변수 `E`가 `Number` 클래스의 하위 타입이라는 제약을 가집니다.&#x20;

> 즉, `E`는 `Number`를 상속받는 타입이어야 합니다.

* **제네릭 타입 매개변수 사용**:
  * `E`는 `Number`와 그 하위 타입 중 하나가 됩니다. 따라서, `E`는 `Integer`, `Float`, `Double` 등을 포함할 수 있습니다.
  * 이를 사용하여 특정 타입의 숫자에 대한 제네릭 메서드나 클래스를 정의할 수 있습니다.
* **유연성**:
  * `List<E extends Number>`는 `Number` 타입을 상속받은 모든 타입의 리스트를 받을 수 있습니다. 예를 들어, `List<Integer>`, `List<Double>`, `List<Long>` 등이 가능합니다.
  * 이를 통해 제네릭 메서드나 클래스를 작성할 때 다양한 숫자 타입에 대해 처리할 수 있는 범용적인 코드를 작성할 수 있습니다.

예를 들어 다음과 같은 제네릭 메서드를 작성할 수 있습니다:

```java
public <E extends Number> void processList(List<E> list) {
    for (E element : list) {
        System.out.println(element);
    }
}

List<Integer> intList = new ArrayList<>();
intList.add(1);
intList.add(2);

List<Double> doubleList = new ArrayList<>();
doubleList.add(3.14);
doubleList.add(2.71);

// 다양한 타입의 리스트를 처리할 수 있습니다.
processList(intList); // Integer 타입의 리스트 전달
processList(doubleList); // Double 타입의 리스트 전달
```

#### 주요 차이점 요약

1. **제네릭 타입의 고정성**:
   * `List<Number>`는 `Number` 타입에 고정됩니다. `Number`의 하위 타입(`Integer`, `Double` 등)의 리스트를 사용할 수 없습니다.
   * `List<E extends Number>`는 `Number`와 그 하위 타입을 모두 지원할 수 있는 유연성을 제공합니다.
2. **제네릭 메서드 및 클래스에서의 사용**:
   * `List<E extends Number>`는 제네릭 메서드나 클래스를 설계할 때 유용합니다. 예를 들어, 숫자 타입에 대한 범용적인 코드를 작성할 때 다양한 숫자 타입을 모두 지원할 수 있게 해줍니다.
3. **불공변성(Invariance)**:
   * 자바에서 제네릭 타입은 불공변성을 갖습니다. 즉, `List<Number>`와 `List<Integer>`는 서로 다른 타입으로 간주됩니다.
   * 반면, `List<E extends Number>`는 `E`가 특정 숫자 타입(`Integer`, `Double`, `Float` 등)으로 제한되는 유연성을 제공하여 여러 타입의 리스트를 받을 수 있습니다.

#### 예제: 불공변성의 문제

자바의 제네릭 타입은 불공변성을 갖습니다. 이는 다음과 같은 이유로 문제가 될 수 있습니다.

```java
List<Integer> intList = new ArrayList<>();
List<Number> numberList = intList; // 컴파일 오류
```

위 코드에서 `List<Integer>`를 `List<Number>`로 할당할 수 없는 이유는, 만약 이를 허용한다면 다음과 같은 코드가 컴파일될 수 있기 때문입니다:

```java
numberList.add(3.14); // 만약 numberList가 intList를 가리킨다면, intList에 Double이 추가되는 문제가 발생
```

이 경우, `intList`에는 `Integer` 타입만 포함되어야 하는데, `Double` 타입이 추가되어 메모리 안전성을 해칩니다. 따라서 제네릭 타입은 불공변성을 유지해야 합니다.

#### 요약

* `List<Number>`는 `Number` 타입의 요소를 담는 리스트로, 정확히 `Number` 타입으로 고정됩니다.
* `List<E extends Number>`는 `Number`와 그 하위 타입을 지원하며, 제네릭 메서드나 클래스를 설계할 때 다양한 숫자 타입을 처리할 수 있는 유연성을 제공합니다.
* 자바의 제네릭 타입은 불공변성을 가지므로, 서로 다른 타입(`List<Number>`와 `List<Integer>` 등)의 리스트를 호환할 수 없습니다. `List<E extends Number>`를 사용하면 이러한 제약을 유연하게 해결할 수 있습니다.



## **✨** 최종 정리

클라이언트에서 직접 형변환해야 하는 타입보다 **제네릭 타입이 더 안전하고 쓰기 편하다.**&#x20;

> 새로운 타입을 설계할 때는 형변환 없이도 사용할 수 있도록 하라. 그렇게 하려면 제네릭 타입으로 만들어야 할 경우가 많다.&#x20;

기존 타입 중 제네릭이었어야 하는 게 있다면 **제네릭 타입으로 변경**하자. 기존 클라이언트에는 아무 영향을 주지 않으면서, 새 로운 사용자를 훨씬 편하게 해주는 길이다.



> 참고 글&#x20;
>
> * [이왕이면 제네릭 타입으로 만들라.pdf](https://github.com/woowacourse-study/2022-effective-java/blob/main/05%EC%9E%A5/%EC%95%84%EC%9D%B4%ED%85%9C_29/%EC%9D%B4%EC%99%95%EC%9D%B4%EB%A9%B4%20%EC%A0%9C%EB%84%A4%EB%A6%AD%20%ED%83%80%EC%9E%85%EC%9C%BC%EB%A1%9C%20%EB%A7%8C%EB%93%A4%EB%9D%BC.pdf)
> * [https://kangmanjoo.tistory.com/136](https://kangmanjoo.tistory.com/136)
