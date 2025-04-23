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

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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



<figure><img src="../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<mark style="color:red;">모든 타입은 자기 자신의 상위 타입이</mark>므로 `Collection<? super Number>`선언은 \`Collection을 비롯하여  Collection\` 타입의 매개변수가 전달되어도 오류가 발생하지 않는다.

{% hint style="info" %}
Collection\<? super E>의 뜻
{% endhint %}

* **`? super E`**: 이 표현에서 `?`는 와일드카드로, 임의의 타입을 나타낸다. `super E`는 이 와일드카드가 `E` 타입의 **상위 타입**을 나타내야 한다는 것을 의미한다.
* **`Collection<? super E>`**: `E` 타입의 상위 타입들을 요소로 가질 수 있는 컬렉션을 의미한다. 즉, 이 컬렉션은 `E` 또는 `E`의 **상위 타입**을 요소로 허용한다.

이렇게 하면 `Collection<? super E>`는 `E` 타입이나 그 상위 타입의 객체를 담을 수 있는 컬렉션에 대해 작업할 수 있는 유연성을 제공한다.&#x20;

예를 들어, `popAll(Collection<? super E> dst)` 메서드가 있다고 할 때, 이 메서드는 스택에서 꺼낸 요소들을 전달받은 컬렉션에 추가할 수 있다. `E`의 상위 타입을 허용함으로써 더 넓은 범위의 컬렉션 타입을 처리할 수 있게 된다.

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

<pre class="language-java"><code class="lang-java">public void pushAll(<a data-footnote-ref href="#user-content-fn-2">Iterable&#x3C;?</a> extends E> src) {
    for (E e : src) {
        push(e);
    }
}
</code></pre>

여기서 `Iterable<? extends E>`는 `E`의 **하위 타입**을 허용한다. 그 이유는 :

1. **하위 타입은 상위 타입으로&#x20;**<mark style="color:red;">**변환**</mark>**될 수 있습니다**: 예를 들어, `Integer`는 `Number`의 하위 타입이기 때문에, `Integer`를 `Number` 타입으로 사용해도 문제가 없다. 따라서, `E` 타입을 수용할 수 있는 하위 타입의 데이터를 받아들일 수 있다.
2. **생산자 관점에서 데이터 제공**: `pushAll` 메서드는 외부에서 제공된 데이터를 `Stack`에 추가한다. 이때, 하위 타입의 데이터를 허용하면, 다양한 타입의 데이터를 `Stack`에 추가할 수 있다. `extends`를 사용하면 하위 타입도 수용할 수 있는 유연성을 제공힌다.
3. 위 코드는 `Iterable<E>` 대신 `Iterable<? extends E>`를 사용함으로써 `Stack<Number>`에 `Iterable<Integer>`와 같은 `Number`의 하위 타입 컬렉션을 전달할 수 있게 만들어준다.



### 2) 소비자(`Consumer`)와 와일드카드 `super`

`Consumer`의 경우에는 <mark style="color:red;">데이터를 받아들이는 역할을 한다.</mark>&#x20;

> 즉, 다른 곳에서 제공한 데이터를 `Consumer`가 수용하게 되는 것이다.&#x20;

이때 `super` 와일드카드를 사용하면 상위 타입까지 허용할 수 있다.

**예를 들어:**

반대로, `popAll` 메서드는 `Stack`의 모든 원소를 `Collection`에 추가하는 역할을 한다. 이 경우, 메서드가 `Collection`을 '소비'하는 것이므로 `? super E`를 사용해야 한다.

<pre class="language-java"><code class="lang-java">public void popAll(<a data-footnote-ref href="#user-content-fn-3">Collection&#x3C;?</a> super E> dst) {
    while (!isEmpty()) {
        dst.add(pop());
    }
}
</code></pre>

여기서 `Collection<? super E>`는 `E`의 **상위 타입**을 허용한다. 그 이유는&#x20;

1. **상위 타입은 하위 타입을&#x20;**<mark style="color:red;">**수용**</mark>**할 수 있다**: 예를 들어, `Number`가 `Integer`의 상위 타입이기 때문에 `Number` 타입의 변수에는 `Integer` 값을 저장할 수 있다. 즉, 더 넓은 범위의 타입을 허용할 수 있기 때문에 상위 타입을 사용할 수 있는 것이다.
2. **소비자 관점에서 데이터의 수용**: `popAll` 메서드는 `Stack`의 모든 원소를 `Collection`에 추가한다. 이때, `E`의 상위 타입(`Collection<? super E>`)을 사용하면 `Stack`의 원소들을 더 넓은 범위의 타입으로 수용할 수 있다.&#x20;
3. <mark style="color:red;">하위 타입은 더 많은 정보를 담고 있는 반면, 상위 타입은 더 일반적이다. 따라서, 상위 타입이 하위 타입의 데이터를 수용할 수 있는 것이다.</mark>
4. `popAll` 메서드의 실행 과정에서 **`Collection`이 소비자 역할을 수행**한다.

이제 `popAll` 메서드는 `Collection<E>`뿐만 아니라 `E`의 상위 타입인 `Collection<Object>`와 같은 컬렉션에도 데이터를 추가할 수 있게 된다. 모든 타입은 자기 자신의 상위 타입이기 때문에 `Collection<? super Number>`는 `Collection<Number>`뿐만 아니라 `Collection<Object>`와 같은 더 상위 타입의 컬렉션을 매개변수로 받을 수 있다.



### 3) 요약

* **PECS 원칙**은 제네릭 타입을 사용할 때, **생산자(Producer)는 `extends`를 사용하고, 소비자(Consumer)는 `super`를 사용하라**는 원칙이다.
* 예를 들어, 어떤 타입이 값을 **생산**하는 역할을 한다면 `? extends T` 와일드카드를 사용해 하위 타입도 허용할 수 있도록 해야 하고, 어떤 타입이 값을 **소비**하는 역할을 한다면 `? super T` 와일드카드를 사용해 상위 타입도 허용할 수 있도록 해야 한다.

{% hint style="success" %}
쉽게 농산물에 비유해보자
{% endhint %}

#### 생산자(Producer) 입장에서

생산자는 농산물을 생산하고 도매시장(소비자)에게 제공하는 농부들이다. 각 농부는 자신이 생산한 특정 농산물만 **제공**할 수 있다. 예를 들어, **배추를 재배하는 농부, 쌀을 재배하는 농부, 딸기를 재배하는 농부** 등이 있습니다. 이때, 각 농부는 **자신이 생산한 농산물만 제공**할 수 있다.

와일드카드 `? extends E`를 사용하면, 더 좁은 범위의 농산물(하위 타입)도 `E`로 처리할 수 있게 된다. 예를 들어, **도매시장이 '채소'라는 카테고리의 농산물**을 받는다면, **배추, 시금치, 상추 같은 구체적인 채소들도** 수용할 수 있게 되는 것이다. 즉, **생산자 입장에서 더 좁은 범위의 농산물들을 제공**할 수 있다.

#### 소비자(Consumer) 입장에서

소비자는 다양한 농산물을 받아들이는 **도매시장**이다. 도매시장은 여러 농산물(타입)을 받아들이는 역할을 한다. 따라서, **`super` 와일드카드**를 사용하면 **상위 타입의 농산물까지도 허용**할 수 있다.

예를 들어, **소비자가 배추, 시금치, 상추 등 다양한 채소를 받을 수 있다면,** 이들을 **'채소'라는 상위 범주**로 받을 수 있다. 즉, `Collection<? super E>`는 **E의 상위 타입(예: 채소)을 허용**하여 더 많은 종류의 농산물을 수용할 수 있다. **상위 타입을 사용하면 다양한 농산물을 받아들일 수 있는 유연성**을 가지게 되는 것이다.

#### 요약하자면

* **생산자(Producer):** 특정한 농산물(좁은 범위의 하위 타입)을 생산하여 도매시장에 제공 (`? extends E` 사용). 예: 배추 농부가 배추를 도매시장에 제공.
* **소비자(Consumer):** 넓은 범위의 농산물을 수용할 수 있는 도매시장 (`? super E` 사용). 예: 도매시장이 '채소' 범주에 속하는 모든 농산물을 수용



## 3. 와일드카드와 제네릭 타입을 활용한 API 유연성 향상

### 1) 생산자와 소비자에 대한 적용 예시

**예시 1: Chooser 생성자**

Chooser는 `Collection<T>`를 받아서 내부에 저장하고, 나중에 무작위로 선택하는 클래스이다. 이 경우, `Collection<T>`는 **T 타입의 데이터를 제공하는 생산자**의 역할을 하므로, 즉 `?` choices 컬렉션은 T 타입의 값을 생산하기만 하고  나중을 위해 저장해두기에 `extends T` <mark style="color:red;">와일드카드를 사용하여 T의 하위 타입도 받을 수 있도록 선언할 수 있다.</mark>

```java
// T를 생산하는 역할을 하므로 extends를 사용해 유연성을 높임
public Chooser(Collection<? extends T> choices) {
    // 초기화 코드
}
```

이렇게 수정하면, `Chooser<Number>`를 생성할 때 `List<Integer>`와 같은 하위 타입의 컬렉션도 사용할 수 있다. 수정 전 생성자로는 컴파일조차 되지 않겠지만, 한정적 와일드카드 타입으로 선언한 수정 후 생성자에서는 문제 가 사라진다.

**예시 2: Union 메서드**

Union 메서드는 두 개의 집합을 합쳐서 새로운 집합을 반환하는 메서드이다. `s1`과 `s2` 모두 **E 타입의 데이터를 제공하는 생산자**이기 때문에, `? extends E` 와일드카드를 사용하여 유연성을 높일 수 있다.

```java
// 두 매개변수가 모두 T를 생산하므로 extends를 사용
public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```

이렇게 하면, `Set<Number>`의 합집합을 만들 때, `Set<Integer>`와 같은 하위 타입의 집합을 전달할 수 있게 된다.

### 2) 메서드의 반환값에는 와일드카드 타입을 사용하지 않는다

메서드의 반환값으로 와일드카드 타입을 사용하면 메서드를 호출하는 클라이언트 코드에서도 와일드카드 타입을 신경 써야 합니다. 따라서 <mark style="color:red;">메서드의 반환값은 명확하게 제네릭 타입을 사용해야 한다.</mark>

예를 들어, 반환 타입을 `Set<? extends E>`로 사용하면 클라이언트가 해당 타입을 명확히 알 수 없고, 그로 인해 코드가 복잡해질 수 있다. 따라서 반환 타입은 명확한 제네릭 타입인 `Set<E>`로 지정한다.

두 개의 Set 컬렉션을 매개변수로 받아서 합치는(union)하는 메서드의 경우에도 아래와 같이 `Producer`의 역할을 하므로 `extends`를 사용하여 처리한다. <mark style="color:red;">하지만 메서드를 사용하는 main 메서드를 보면 와일드카드 타입을 전혀 신경쓰지 않아도 된다.</mark>

```java
public class Union {
    public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2) {
        Set<E> result = new HashSet<>(s1);
        result.addAll(s2);
        return result;
    }

    public static void main(String[] args) {
        // Set.of 메서드는 java 9 이상부터 지원
        Set<Double> doubleSet = Set.of(1.0, 2.1);
        Set<Integer> integerSet = Set.of(1, 2);
        Set<Number> unionSet = union(doubleSet, integerSet);
    }
}
```

위 코드는 `Java 9` 버전으로 컴파일하였으나 만일 Java 8 이전 버전을 사용한다면 컴파일러가 타입을 올바르게 추론하지 못하므로 명시적으로 타입 인수를 지정해야 정상 컴파일이 된다.

```java
public class Union {
    public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2) {
        Set<E> result = new HashSet<>(s1);
        result.addAll(s2);
        return result;
    }

    public static void main(String[] args) {
        // java 7 버전으로 컴파일
        Set<Double> doubleSet = new HashSet<>(Arrays.asList(1.0, 2.1));
        Set<Integer> integerSet = new HashSet<>(Arrays.asList(1, 2));
        Set<Number> unionSet = Union.<Number>union(doubleSet, integerSet);
    }
```

### 3) 재귀적 타입 한정(Recursive Type Bound)

{% hint style="info" %}
**재귀적 타입 한정**은 제네릭 타입이 자기 자신을 한정하는 타입이다.&#x20;
{% endhint %}

예를 들어, `Comparable<E>`와 같은 경우, `E` 타입의 객체끼리 비교할 수 있도록 재귀적 타입 한정을 사용한다.

**예시: `max` 메서드**

다음과 같이 재귀적 타입 한정을 적용하여 `max` 메서드를 작성할 수 있다:

```java
// 변경 전
public static <E extends Comparable<E>> E max(Collection<E> collection)

// 변경 후(PECS 법칙 2번 적용)
public static <E extends Comparable<? super E>> E max(Collection<? extends E> collection)
```

* 매개변수는 `Collection<? extends E>`로 선언합니다. 이는 foreach 루프에서 **E 인스턴스를 생산하는 생산자**이기 때문에 `? extends E`를 사용한다.
* `Comparable<? super E>`로 선언합니다. 이는 **E 인스턴스를 소비하는 소비자**이기 때문에 `? super E`를 사용한다.

이렇게 수정하면, `Comparable`을 직접 구현하지 않고 다른 클래스를 상속한 타입도 처리할 수 있다.

> 복잡하지만 위와 같은 방식은 `Comparable`을 예로 들었을 때, `Comparable`을 직접 구현하지 않고 직접 구현한 다른 클래스를 확장한 타입을 지원할 때 필요하다.

### &#x20;4) 타입 매개변수와 와일드카드의 차이

* **타입 매개변수:** 메서드 선언에서 **여러 번 사용될 때 적합**하다. 동일한 타입의 값을 비교하거나 교환할 때 유용하다.
* **와일드카드 타입:** **매개변수화된 타입이 한 번만 사용되거나, 불특정한 타입을 처리할 때 적합**하다.

**예시: `swap` 메서드**

와일드카드를 사용하는 `swap` 메서드는 다음과 같이 구현할 수 있다:

```java
@Test
public void methodSignatureWildcardTest() {
    List<String> strings = new ArrayList<>(List.of("a", "b", "c", "d", "e", "f"));
    swap(strings, 0, 1);
    System.out.println("strings = " + strings);
}

// 와일드카드 타입을 사용하는 swap 메서드
public static void swap(List<?> list, int i, int j) {
    swapHelper(list, i, j); // private 도우미 메서드를 호출
}

// 타입 매개변수를 사용한 private 도우미 메서드
private static <E> void swapHelper(List<E> list, int i, int j) {
    list.set(i, list.set(j, list.get(i)));
}
```

* `swapHelper` 메서드는 리스트가 `List<E>`임을 알고 있기 때문에 타입 안전성을 제공하며, 클라이언트는 와일드카드에 대해 신경 쓸 필요가 없다.
* 그냥 `?` 와일드카드 타입만 쓰면, `list.add()`가 불가능하기에, `showHelper()`라는 도우미 메서드를 만들었다.
* 위와 같이 코드를 구성하면, <mark style="color:red;">API 외부</mark>로는 `?` 와일드카드가 보이고, <mark style="color:red;">내부에서는</mark> `<E>` 타입을 쓰게 된다.
* 메서드 선언에 타입 매개변수가 단 한번만 나온다면, 와일드 카드를 썼을 때 그 의도가 더 명확하다.

> 메서드 선언에 타입 매개변수가 한번만 나온다면 와일드 카드를 쓰자.

### 5) 특이한 케이스: Comparable의 제네릭 타입

`Comparable<E>`는 일반적으로 **소비자**의 역할을 한다. 따라서, `? super E`를 사용하는 것이 좋다. 만약 `Comparable<E>` 대신 `Comparable<? super E>`를 사용하면 상위 타입을 기준으로 비교할 수 있는 유연성을 제공한다.

예를 들어서 `Java 5` 부터 지원한 `ScheduledFuture` 인터페이스의 구현 코드를 살펴보면 아래와 같다. `Delayed`의 하위 인터페이스이며 `Delayed`인터페이스는 `Comparable<Delayed>`를 확장했다. 반면에 `ScheduledFuture` 인터페이스는 `Comparable<ScheduledFuture>`를 확장(extends)하지 않는다.

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```java
// ScheduledFuture interface
public interface ScheduledFuture<V> extends Delayed, Future<V> {
    // ...
}

// Delayed interface
public interface Delayed extends Comparable<Delayed> {
    // ...
}

// Comrable interface
public interface Comparable<T> {
    // ...
}
```

PECS 공식을 적용하지 않은 max 예제 메서드에서는 아래와 같은 코드가 동작하지 않을 것! 다음 코드는 `Comparable<E>`를 사용하는 경우로, <mark style="color:red;">E 타입과 정확히 일치하는 타입만 비교할 수 있기 때문</mark>이다.

```java
class RecursiveTypeBound {
    public static <E extends Comparable<E>> E max(Collection<E> collection) {
        // ...
    }
}
```

하지만, 아래와 같이 `ScheduledFuture<?>` 타입의 리스트를 전달하면 컴파일 오류가 발생한다. 왜냐하면 `ScheduledFuture`는 `Comparable<ScheduledFuture>`를 구현한 것이 아니라 `Comparable<Delayed>`를 구현했기 때문이다.

```java
class Item28Test {
    public static void main(String[] args) {
        List<ScheduledFuture<?>> scheduledFutureList = ...

        // incompatible types...
        RecursiveTypeBound.max(scheduledFutureList);
    }
}
```

> 즉, 밑에처럼 바꿔줘야 함 `Comparable<? super E>`를 사용하면 `ScheduledFuture`의 상위 타입인 `Delayed`로 비교할 수 있기 때문에 컴파일 오류가 해결된다.

```java
public static <E extends Comparable<? super E>> E max(Collection<? extends E> collection) {
    // E의 상위 타입과도 비교할 수 있어 더 유연해짐
}
```

## 4. 타입 매개변수와 와일드카드 타입 사이의 차이점을 비교

### 1) 비한정적 타입 매개변수와 비한정적 와일드카드의 차이

* **비한정적 타입 매개변수 (`<E>`) 사용**:
  * 타입 매개변수 `E`를 사용하여 리스트의 원소 타입을 명시한다.
  * 제네릭 타입 매개변수를 사용하면 타입 안정성을 보장할 수 있다.
  *   아래 예시에서 `typeArgSwap` 메서드는 리스트의 두 원소를 교환하며, `E` 타입을 사용해 타입 안전성을 유지한다.

      ```java
      // 방법1) 비한정적 타입 매개변수 사용
      // 타입 매개변수 E를 사용해 리스트의 두 원소를 교환
      // 여기서 E는 리스트의 원소 타입을 나타내며, 타입 안정성이 보장됨
      public static <E> void typeArgSwap(List<E> list, int i, int j) {
          list.set(i, list.set(j, list.get(i)));
      }
      ```
* **비한정적 와일드카드 (`<?>`) 사용**:
  * 와일드카드 타입을 사용하면 메서드를 호출할 때 <mark style="color:red;">타입 매개변수를 명시할 필요가 없기 때문에 더 간단하게 사용할 수 있다.</mark>
  *   그러나 `List<?>`는 타입 안전성 문제로 인해 `null` 외의 값을 리스트에 추가할 수 없다. 따라서 값을 설정하거나 변경할 때는 타입 안정성을 보장할 수 없다.

      ```java
      // 방법2) 비한정적 와일드카드 사용
      // 와일드카드 타입을 사용하여 리스트의 두 원소를 교환
      public static void wildcardSwap(List<?> list, int i, int j) {
          wildcardSwapHelper(list, i, j); // 도우미 메서드를 사용해 타입 안전성을 확보
      }
      ```

### 2) 도우미 메서드를 사용하여 와일드카드 타입의 한계를 극복하기

*   **도우미 메서드 (`wildcardSwapHelper`)의 필요성**:

    * `List<?>` 타입에서는 `null` 외의 값을 설정할 수 없기 때문에, <mark style="color:red;">실제 타입을 명확히 알 수 있는 도우미 메서드를 사용해야 한다.</mark>
    * 도우미 메서드를 사용하면 리스트의 실제 타입을 추론할 수 있으며, 타입 안전성을 확보할 수 있다.
    * 아래 예시에서 `wildcardSwapHelper` 메서드는 제네릭 타입 매개변수 `E`를 사용하여 와일드카드로 전달된 리스트를 안전하게 처리한다.

    ```java
    // 도우미 메서드
    // 와일드카드 타입의 리스트를 제네릭 타입 매개변수로 변환하여 타입 안전성을 확보
    private static <E> void wildcardSwapHelper(List<E> list, int i, int j) {
        list.set(i, list.set(j, list.get(i)));
    }
    ```

와일드 카드 타입의 실제 타입을 알기 위하여 제네릭 메서드(위 코드에서 wildcardSwapHelper)의 도움이 필요하다. 이 메서드는 매개변수로 넘어오는 리스트가 `List<E>`에서 꺼낸 값의 타입이 항상 E 임을 알고 있으며 이는 리스트에 넣어도 타입 안전함을 알고 있다. 물론 와일드카드 메서드를 지원하기 위하여 추가적인 메서드가 작성되었지만 클라이언트의 입장에서는 **타입 매개변수에 신경쓰지 않는 메서드를** 사용할 수 있게 된다.

### 3) 요약

* `List<?>`와 같은 와일드카드 타입은 타입 안전성 문제로 인해 `null` 외의 값을 추가할 수 없다.
* 도우미 메서드를 사용하여 와일드카드 타입을 제네릭 타입 매개변수로 변환함으로써 타입 안전성을 확보할 수 있다.
* 클라이언트 코드에서는 와일드카드를 사용해 간편하게 메서드를 호출할 수 있고, 내부적으로는 도우미 메서드를 사용해 타입 안전하게 처리할 수 있다.

### 4) 코드 전체 예시

```java
import java.util.List;

class SwapTest {
    // 방법1) 비한정적 타입 매개변수 사용
    // 타입 매개변수 E를 사용해 리스트의 두 원소를 교환
    public static <E> void typeArgSwap(List<E> list, int i, int j) {
        list.set(i, list.set(j, list.get(i)));
    }

    // 방법2) 비한정적 와일드카드 사용
    // 와일드카드 타입을 사용하여 리스트의 두 원소를 교환
    public static void wildcardSwap(List<?> list, int i, int j) {
        wildcardSwapHelper(list, i, j); // 도우미 메서드를 사용해 타입 안전성을 확보
    }

    // 도우미 메서드
    // 와일드카드 타입의 리스트를 제네릭 타입 매개변수로 변환하여 타입 안전성을 확보
    private static <E> void wildcardSwapHelper(List<E> list, int i, int j) {
        list.set(i, list.set(j, list.get(i)));
    }
}
```

이 예제에서는 두 가지 방식의 리스트 교환 메서드를 제공하며, 와일드카드의 한계를 도우미 메서드를 통해 극복하고 있습니다.









## **✨ 최종** 정리

* 조금 복잡하더라도 와일드카드 타입을 적용하면 API가 훨씬 유연해진다.
* **와일드카드를 적용하면 API가 훨씬 유연해질 수 있다.** 널리 쓰일 라이브러리를 작성할 때는 반드시 와일드카드를 적절히 사용해야 한
* **메서드의 반환값에는 와일드카드를 사용하지 않는다.** 클라이언트 코드에서도 와일드카드를 사용해야 하는 복잡성이 생기기 때문이다.
* 생산자에는 `extends` 소비자에는 `super`를 사용하는 PECS를 기억하자.
* **Comparable과 Comparator**는 모두 소비자(Consumer)라는 사실도 잊지 말자



> 참고 글 및 출처
>
> * 이펙티브 자바 책
> * [https://madplay.github.io/post/use-bounded-wildcards-to-increase-api-flexibility](https://madplay.github.io/post/use-bounded-wildcards-to-increase-api-flexibility)
> * [https://jake-seo-dev.tistory.com/51#%EC%--%--%EC%-D%BC%EB%--%-C%--%EC%B-%B-%EB%--%-C%--%ED%--%--%EC%-E%--%EC%-D%--%--%EC%--%AC%EC%-A%A-%ED%--%B-%EC%--%BC%--%ED%--%--%EB%-A%--%--%EC%-D%B-%EC%-C%A-](https://jake-seo-dev.tistory.com/51#%EC%--%--%EC%-D%BC%EB%--%-C%--%EC%B-%B-%EB%--%-C%--%ED%--%--%EC%-E%--%EC%-D%--%--%EC%--%AC%EC%-A%A-%ED%--%B-%EC%--%BC%--%ED%--%--%EB%-A%--%--%EC%-D%B-%EC%-C%A-)



[^1]: 사실 extends라는 키 워드는 이 상황에 딱 어울리지는 않는다. 하위 타입이란 자기 자신도 포함하지 만, 그렇다고 자신을 확장(extends)한 것은 아니기 때문

[^2]: 생산자임 `Iterable`이 데이터를 "생산"\*\*하여 **`Stack`으로 전달**하는 구조 `Stack<E>`가 소비자(Consumer)

[^3]: &#x20;이게 소비자임 그리고  Stack이 생산자
