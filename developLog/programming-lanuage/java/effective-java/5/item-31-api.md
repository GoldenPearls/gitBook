# item 31 : 한정적 와일드카드를 사용해 API 유연성을 높이라

## 1. 와일드 카드를 사용해야 하는 이유

### 1) 제네릭은 불공변

[이펙티브자바  item 28](https://mellona-log.gitbook.io/log/programming-lanuage/java/effective-java/5/item-28)에서 살펴본 것처럼 **매개변수화 타입은** `불공변(invariant)` 이다. 예를 들어 Type1과 Type2가 있을 때, `List<Type1>`은 `List<Type2>`의 하위 타입 또는 상위 타입이라는 관계가 성립될 수 없다.

조금 더 풀어보면 `List<Object>`에는 어떠한 객체도 넣을 수 있지만 `List<String>`에는 문자열만 넣을 수 있다. 즉 `List<String>`이 `List<Object>`의 기능을 제대로 수행하지 못하므로 하위 타입이라고 말할 수 없다. 리스코프 치환 원칙에 어긋난다.

즉, 제네릭은 불공변이며, 하위 혹은 상위 타입을 기본적으로는 포용하지 않게 되어있다.

### 2) 생산자(Producer)와 와일드카드 : 한정적 와일드 카드를 통해 불공변 제네릭 유연하게 만들기

```java
public class Stack<E> {
    public Stack();
    public void push(E e);
    public E pop();
    public boolean isEmpty(); 
}
```

이 Stack의 public API에 일련의 원소를 스택에 넣는 메서드인 pushAll 메서드를 넣는다고 생각해보자

```java
public void pushAll(Iterable<E> src) {
    for (E e : src) 
        push(e);
}
```

이 메서드는 깨끗이 컴파일되지만 완벽하진 않다. Iterable src의 원소 타입 이 스택의 원소 타입과 일치하면 잘 작동한다.&#x20;

{% hint style="info" %}
**Iterable src의 원소 타입이 스택의 원소 타입과 일치하면 잘 작동한다"는 뜻**

`Stack<E>` 클래스는 특정 타입 `E`를 담을 수 있는 스택을 나타낸다. 이때 `pushAll` 메서드의 매개변수인 `Iterable<E> src`는 `E` 타입의 원소들로 이루어진 컬렉션을 받는다. 이 말은 `src`의 원소 타입이 스택의 원소 타입(`E`)과 일치해야만 해당 메서드가 잘 작동한다는 뜻이다. 예를 들어, `Stack<Number>` 타입의 스택에 `Iterable<Number>` 타입의 원소들을 추가할 때는 문제가 없다. 하지만, `Iterable<Integer>`와 같이 `Number`의 하위 타입을 사용하려 하면 타입 불일치 오류가 발생한다..
{% endhint %}

{% hint style="danger" %}
하지만 Stack로 선언한 후 `pushAll(intVal)`을 호출하면 어떻게 될까?
{% endhint %}

integer은 Number의 하위 타입이니 잘 동작해야 한다 하지만 클라이언트  코드에서 실제로는 오류 메세지가 뜬다.

<figure><img src="../../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

```java
import java.util.Arrays;

/**
 * 아이템29 소스코드 참고
 */
class Stack<E> {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(E e) {
        ensureCapacity();
        elements[size++] = e;
    }

    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }

    // 매개변수의 원소들을 스택에 넣는 메서드
    public void pushAll(Iterable<E> src) {
        for (E e : src) {
            push(e);
        }
    }
}

class Item28Test {
    public static void main(String[] args) {
        Stack<Number> numberStack = new Stack<>();
        Iterable<Integer> integers = Arrays.asList(
                Integer.valueOf(1), Integer.valueOf(2));

        // 이 코드는 컴파일 오류를 일으킨다.
        numberStack.pushAll(integers);
    }
}
```

이유는 <mark style="color:red;">매개변수화 타입이 불공변이기 때문</mark>이다.

제네릭의 매개변수화 타입은 불공변이기 때문에 상위-하위 자료형의 관계가 없다. 이러한 문제를 해결하려면 한정적 와일드카드(bounded wildcard) 자료형을 사용하면 된다. `Integer 클래스는 Number를 상속한 구현체` 이므로 아래와 같이 매개변수 부분에 선언한다.

<pre class="language-java"><code class="lang-java">public void pushAll(Iterable&#x3C;? <a data-footnote-ref href="#user-content-fn-1">extends </a>E> src) {
    for (E e : src) {
        push(e);
    }
}
</code></pre>

위의 선언을 해석하면 매개변수는 E의 Iterable이 아니라 `E의 하위 타입의 Iterable` 이라는 뜻이다. **Number 클래스를 상속하는** Integer, Long, Double 등의 타입 요소를 가질 수 있게 된다.

* 단순히 E만 이용했을 때는 Number만 받을 수 있지만, 이제 Integer도 받을 수 있다.
* ? 와일드카드 타입으로 E를 상속한 아무 타입이나 받아줄 수 있다.
* 제네릭의 불공변 때문에 이렇게 한정적 와일드카드 타입(? extends E)을 이용해주는 것이 좋다.

> 약간, 코드 뒤져보니 PolicyUtils 등 자바 코드 기본 제공 클래스들에서 많이 쓰는 듯? 실제 코드에서는 잘 안쓰는 느낌임

<figure><img src="../../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

직접 정의한 Stack 클래스는 `push(E)` 메서드를 통해서만 요소를 추가할 수 있다. 따라서 타입 안전성은 확인되지만 elements 배열은 런타임 시에 `E[]`가 아닌 `Object[]`가 된다. 역시나 이부분도 런타임 시에 제네릭 타입이 소거되기 때문임

{% hint style="success" %}
즉, 아래와 같은 이유로 와일드 카드를 써야 함&#x20;
{% endhint %}

1.  **타입 안전성 확보**

    제네릭 클래스로 정의한 `Stack<E>`에서, `E`는 타입 매개변수로 `Stack`이 다루는 원소의 타입을 나타낸다. `push(E)` 메서드를 통해 `Stack`에 요소를 추가할 때, `E` 타입만 추가할 수 있으므로 컴파일 시에 타입 안전성이 보장된다. <mark style="color:red;">즉, 잘못된 타입의 객체를 추가하려고 하면 컴파일 오류가 발생한다.</mark>
2.  **타입 소거와 런타임의 배열 타입**

    자바에서 제네릭 타입은 `컴파일러`에 의해 컴파일 시에만 타입 정보를 사용한다. 컴파일 후, 이 타입 정보는 제거(소거)되어 런타임에는 존재하지 않게 된다. 이를 타입 소거라고 한다.

    예를 들어, `Stack<String>`으로 정의된 스택이 있다고 하더라도, 런타임 시에는 `Stack` 내부의 배열(`elements`)은 단순히 `Object[]` 타입으로 취급된다. `Stack`의 타입 매개변수 `E` 정보가 런타임에 사라지기 때문이다.
3.  **배열 타입의 한계**

    배열은 런타임 시점에 타입을 검사한다. 예를 들어, `String[]` 배열에는 `Integer` 객체를 넣을 수 없고, 컴파일 시점과 런타임 시점 모두에서 타입 불일치 오류를 확인할 수 있다. 하지만 제네릭에서는 타입 소거 때문에 컴파일 시점에만 타입이 검사되고, 런타임 시에는 원소가 `Object`로 다루어진다.

    `Stack` 클래스에서는 `elements` 배열을 `Object[]`로 선언하고, 제네릭 타입의 요소를 추가할 때 이 배열에 저장한다.&#x20;

> 즉, `push(E)` 메서드로 `E` 타입의 요소를 추가하지만, 실제로는 `Object[]` 배열에 저장된다. 이런 이유로 런타임에는 `elements` 배열이 `E[]`가 아닌 `Object[]`로 동작하게 된다.

제네릭 타입을 사용하여 `Stack<E>` 클래스가 컴파일 시점에 타입 안전성을 제공할 수 있지만, 런타임 시에는 타입 소거로 인해 실제 배열은 `Object[]` 타입으로 다뤄진다는 것을 다시한 번 말함

### 3) 소비자(Consumer)와 와일드카드 : 한정적 와일드 카드 타입을 통해 불공변 제네릭 유연하게 만들기

Stack 인스턴스의 모든 원소를 매개변수로 받은 컬렉션으로 모두 옮기는 `popAll` 메서드

```java
// 모든 원소를 매개변수로 전달받은 컬렉션에 옮긴다.
    public void popAll(Collection<E> dst) {
        while(!isEmpty()) {
            dst.add(pop());
        }
    }
}
```

제네릭 타입은 불공변(invariant)한다. 즉, `Stack<Number>`는 `Stack<Object>`와 상속 관계가 없으며, 서로 다른 타입으로 간주된다. 때문에 `Stack<Number>`에 대해 `Collection<Object>`를 인수로 넘기려고 하면 컴파일 에러가 발생한다.

```java
Stack<Number> numberStack = new Stack<>();
Collection<Object> objects = Arrays.asList(new Object());
numberStack.popAll(objects); // 컴파일 에러 발생
```

위 예제에서 `Number`는 `Object`의 하위 타입이지만, `Collection<Number>`와 `Collection<Object>`는 제네릭 타입이기 때문에 서로 상속 관계가 성립하지 않는다. 따라서, 타입 매개변수의 차이로 인해 컴파일 에러가 발생한다.

{% hint style="success" %}
불공변덕에 numberStack 의 제네릭이 Number였어서 유연성 없이 Collection\<Number> 타입만을 인수로 받을 수 있는데, 상위 타입인 Object가 와서 문제인 것이다.
{% endhint %}

* 소비자의 경우, 생산자와 다르게 상위 클래스로만 유연하게 확장해야 한다.
* 클래스가 가진 컬렉션 필드에 수용할 때는 많은 정보 중 필요한 정보만 취사선택 하면 된다. (생산자 관점)
  * 정보가 더 많아서 나쁠 게 없다. (상위 타입은 하위 타입을 수용할 수 있다. 상속 덕에 필요한 정보가 이미 다 있다.)
* 클래스가 가진 컬렉션 필드를 꺼내올 때는 해당 컬렉션이 가진 정보를 받아줄 객체가 필요하다. (소비자 관점)
  * 상위 객체만이 하위 객체를 받아줄 수 있다. (하위 타입은 상위 타입을 수용할 수 없다. 더 많은 정보가 필요하다.)
* 그러므로 와일드카드에 super 키워드를 사용해야 한다.

```java
// E의 상위 타입의 Collection이어야 한다.
public void popAll(Collection<? super E> dst) {
    while(!isEmpty()) {
        dst.add(pop());
    }
}
```



<figure><img src="../../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<mark style="color:red;">모든 타입은 자기 자신의 상위 타입이</mark>므로 `Collection<? super Number>`선언은 \`Collection을 비롯하여  Collection\` 타입의 매개변수가 전달되어도 오류가 발생하지 않는다.

{% hint style="info" %}
Collection\<? super E>의 뜻
{% endhint %}



> 유연성을 극대화하려면 원소의 생산자나 소비자용 입력 매개변수에 와일드카드 타입을 사용하라

## 2.소비자-생산자 원칙(PECS)

{% hint style="info" %}
Producer Extends, Consumer Super의 약자로 이는 생산자(`Producer`) 역할을 하는 메서드에서는 `? extends T` 와일드카드를 사용하고, 소비자(`Consumer`) 역할을 하는 메서드에서는 `? super T` 와일드카드를 사용해야 한다는 원칙이다.
{% endhint %}

**와일드카드 사용 시의 생산자(Producer)와 소비자(Consumer) 관점**을 이해하는 데 도움이 된다. `extends`와 `super` 와일드카드의 차이를 명확히 하려면 PECS 원칙을 생각해보면 좋다.

1. **생산자(Producer) 역할**: `? extends E`는 '생산자'의 역할을 한다. 이 와일드카드는 메서드가 데이터를 제공할 때 사용된다.
2. **소비자(Consumer) 역할**: `? super E`는 '소비자'의 역할을 한다. 메서드가 데이터를 받아들이는 경우 사용된다.

### 1) 생산자(`Producer`)와 와일드카드 `extends`

`Producer`는 <mark style="color:red;">데이터를 제공하는 역할을 한다</mark>. 이때는 `extends` 와일드카드를 사용하여 하위 타입까지 허용한다. `pushAll` 메서드는 `Stack`에 원소를 추가하는 역할을 한다. 즉, `Iterable` 매개변수로 제공된 원소를 '생산'하는 역할을 하므로, `? extends E`를 사용해 유연성을 높일 수 있다.

**예를 들어:**

```java
public void pushAll(Iterable<? extends E> src) {
    for (E e : src) {
        push(e);
    }
}
```

여기서 `Iterable<? extends E>`는 `E`의 **하위 타입**을 허용한다. 그 이유는 :

1. **하위 타입은 상위 타입으로 **<mark style="color:red;">**변환**</mark>**될 수 있습니다**: 예를 들어, `Integer`는 `Number`의 하위 타입이기 때문에, `Integer`를 `Number` 타입으로 사용해도 문제가 없다. 따라서, `E` 타입을 수용할 수 있는 하위 타입의 데이터를 받아들일 수 있다.
2. **생산자 관점에서 데이터 제공**: `pushAll` 메서드는 외부에서 제공된 데이터를 `Stack`에 추가한다. 이때, 하위 타입의 데이터를 허용하면, 다양한 타입의 데이터를 `Stack`에 추가할 수 있다. `extends`를 사용하면 하위 타입도 수용할 수 있는 유연성을 제공힌다.
3. 위 코드는 `Iterable<E>` 대신 `Iterable<? extends E>`를 사용함으로써 `Stack<Number>`에 `Iterable<Integer>`와 같은 `Number`의 하위 타입 컬렉션을 전달할 수 있게 만들어준다.



### 2) 소비자(`Consumer`)와 와일드카드 `super`

`Consumer`의 경우에는 <mark style="color:red;">데이터를 받아들이는 역할을 한다.</mark>&#x20;

> 즉, 다른 곳에서 제공한 데이터를 `Consumer`가 수용하게 되는 것이다.&#x20;

이때 `super` 와일드카드를 사용하면 상위 타입까지 허용할 수 있다.

**예를 들어:**

반대로, `popAll` 메서드는 `Stack`의 모든 원소를 `Collection`에 추가하는 역할을 한다. 이 경우, 메서드가 `Collection`을 '소비'하는 것이므로 `? super E`를 사용해야 한다.

```java
public void popAll(Collection<? super E> dst) {
    while (!isEmpty()) {
        dst.add(pop());
    }
}
```

여기서 `Collection<? super E>`는 `E`의 **상위 타입**을 허용한다. 그 이유는&#x20;

1. **상위 타입은 하위 타입을 **<mark style="color:red;">**수용**</mark>**할 수 있다**: 예를 들어, `Number`가 `Integer`의 상위 타입이기 때문에 `Number` 타입의 변수에는 `Integer` 값을 저장할 수 있다. 즉, 더 넓은 범위의 타입을 허용할 수 있기 때문에 상위 타입을 사용할 수 있는 것이다.
2. **소비자 관점에서 데이터의 수용**: `popAll` 메서드는 `Stack`의 모든 원소를 `Collection`에 추가한다. 이때, `E`의 상위 타입(`Collection<? super E>`)을 사용하면 `Stack`의 원소들을 더 넓은 범위의 타입으로 수용할 수 있다.&#x20;
3. <mark style="color:red;">하위 타입은 더 많은 정보를 담고 있는 반면, 상위 타입은 더 일반적이다. 따라서, 상위 타입이 하위 타입의 데이터를 수용할 수 있는 것이다.</mark>

이제 `popAll` 메서드는 `Collection<E>`뿐만 아니라 `E`의 상위 타입인 `Collection<Object>`와 같은 컬렉션에도 데이터를 추가할 수 있게 된다. 모든 타입은 자기 자신의 상위 타입이기 때문에 `Collection<? super Number>`는 `Collection<Number>`뿐만 아니라 `Collection<Object>`와 같은 더 상위 타입의 컬렉션을 매개변수로 받을 수 있다.



### 3) 요약

* **PECS 원칙**은 제네릭 타입을 사용할 때, **생산자(Producer)는 `extends`를 사용하고, 소비자(Consumer)는 `super`를 사용하라**는 원칙이다.
*   예를 들어, 어떤 타입이 값을 **생산**하는 역할을 한다면 `? extends T` 와일드카드를 사용해 하위 타입도 허용할 수 있도록 해야 하고, 어떤 타입이 값을 **소비**하는 역할을 한다면 `? super T` 와일드카드를 사용해 상위 타입도 허용할 수 있도록 해야&#x20;

    * **`? super E`**: 이 표현에서 `?`는 와일드카드로, 임의의 타입을 나타냅니다. `super E`는 이 와일드카드가 `E` 타입의 **상위 타입**을 나타내야 한다는 것을 의미합니다.
    * **`Collection<? super E>`**: `E` 타입의 상위 타입들을 요소로 가질 수 있는 컬렉션을 의미합니다. 즉, 이 컬렉션은 `E` 또는 `E`의 **상위 타입**을 요소로 허용합니다.

    이렇게 하면 `Collection<? super E>`는 `E` 타입이나 그 상위 타입의 객체를 담을 수 있는 컬렉션에 대해 작업할 수 있는 유연성을 제공합니다. 이는 \*\*소비자(Consumer)\*\*의 역할을 할 때 유용합니다.

    예를 들어, `popAll(Collection<? super E> dst)` 메서드가 있다고 할 때, 이 메서드는 스택에서 꺼낸 요소들을 전달받은 컬렉션에 추가할 수 있습니다. `E`의 상위 타입을 허용함으로써 더 넓은 범위의 컬렉션 타입을 처리할 수 있게 됩니다.

    다.

    * **`? super E`**: 이 표현에서 `?`는 와일드카드로, 임의의 타입을 나타냅니다. `super E`는 이 와일드카드가 `E` 타입의 **상위 타입**을 나타내야 한다는 것을 의미합니다.
    * **`Collection<? super E>`**: `E` 타입의 상위 타입들을 요소로 가질 수 있는 컬렉션을 의미합니다. 즉, 이 컬렉션은 `E` 또는 `E`의 **상위 타입**을 요소로 허용합니다.

    이렇게 하면 `Collection<? super E>`는 `E` 타입이나 그 상위 타입의 객체를 담을 수 있는 컬렉션에 대해 작업할 수 있는 유연성을 제공합니다. 이는 \*\*소비자(Consumer)\*\*의 역할을 할 때 유용합니다.

    예를 들어, `popAll(Collection<? super E> dst)` 메서드가 있다고 할 때, 이 메서드는 스택에서 꺼낸 요소들을 전달받은 컬렉션에 추가할 수 있습니다. `E`의 상위 타입을 허용함으로써 더 넓은 범위의 컬렉션 타입을 처리할 수 있게 됩니다.



> 참고 글 및 출처
>
> * 이펙티브 자바 책
> * [https://madplay.github.io/post/use-bounded-wildcards-to-increase-api-flexibility](https://madplay.github.io/post/use-bounded-wildcards-to-increase-api-flexibility)
> * [https://jake-seo-dev.tistory.com/51#%EC%--%--%EC%-D%BC%EB%--%-C%--%EC%B-%B-%EB%--%-C%--%ED%--%--%EC%-E%--%EC%-D%--%--%EC%--%AC%EC%-A%A-%ED%--%B-%EC%--%BC%--%ED%--%--%EB%-A%--%--%EC%-D%B-%EC%-C%A-](https://jake-seo-dev.tistory.com/51#%EC%--%--%EC%-D%BC%EB%--%-C%--%EC%B-%B-%EB%--%-C%--%ED%--%--%EC%-E%--%EC%-D%--%--%EC%--%AC%EC%-A%A-%ED%--%B-%EC%--%BC%--%ED%--%--%EB%-A%--%--%EC%-D%B-%EC%-C%A-)



[^1]: 사실 extends라는 키 워드는 이 상황에 딱 어울리지는 않는다. 하위 타입이란 자기 자신도 포함하지 만, 그렇다고 자신을 확장(extends)한 것은 아니기 때문
