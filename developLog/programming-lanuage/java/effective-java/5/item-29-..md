# item 29 : 이왕이면 제네릭 타입으로 만들라.

## 1. 기존  단순 스택 코드 뭐가 문제 일까?

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

### 1) 제네릭 스택으로 가는 첫 단계 : 컴파일되지 않는다.

{% hint style="info" %}
클래스 선언에 타입 매개 변수를 추가하는 일이다. 스택이 담을 원소의 타입 하나만 추가하면 되는데 보통 타입 이름은 e를 사용한다.
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

<figure><img src="../../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

> <mark style="color:red;">제네릭은 E와 같은 실체화 불가 타입으로는 배열을 만들 수 없다.</mark>

**즉, 제네릭 타입 매개변수로 배열을 생성하는 것이 허용되지 않기 때문에** 발생한다. 제네릭 배열을 생성하는 코드는 타입 안전성을 보장할 수 없기 때문에 컴파일  가금지한 다

* **`elements = new E[DEFAULT_INITIAL_CAPACITY];`**:
  * 이 부분에서 제네릭 타입 `E`로 배열을 생성하려고 시도합니다.
  * Java에서는 제네릭 타입 `E`로 배열을 생성할 수 없으므로, **"generic array creation"** 오류가 발생합니다.
* **오류의 이유**:
  * Java의 **타입 소거(erasure)** 메커니즘 때문에 제네릭 타입의 정보는 **컴파일 시점에만 존재**하고, 런타임에는 사라집니다. 이로 인해 `E[]`와 같은 제네릭 배열은 **런타임에 타입을 알 수 없어 안전하지 않기 때문에 생성할 수 없습니다**.
* **해결 방법**:
  * 제네릭 배열을 직접 생성할 수 없으므로, `Object[]` 배열을 생성한 후, 이를 `(E[])`로 형변환하여 사용하는 방법이 있습니다. 이 방법은 컴파일러가 비검사 형변환 경고를 발생시킬 수 있지만, 안전성을 확인하고 사용하면 됩니다.

.

2\)&#x20;
