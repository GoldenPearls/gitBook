# item 21 : 인터페이스는 구현하는 쪽을 생각해 설계하라

{% hint style="info" %}
기존 인터페이스에 <mark style="color:red;">디폴트 메서드 구현을 추가하는 것은 위험한 일이다.</mark> 디폴트 메서드는 구현 클래스에 대해 아무것도 모른 채 합의 없이 무작정 '삽입' 될 뿐이다. 디폴트 메서드는 기존 구현체에 런타임 오류를 일으킬 수 있다.

<mark style="color:blue;">인터페이스를 설계할 때는 세심한 주의를 기울여야 한다.</mark> 서로 다른 방식으로 최소한 세 가지는 구현을 해보자.
{% endhint %}

## 1. 기존 인터페이스에 디폴트 메서드 구현을 추가하는 것은 위험한 일이다.

자바 8 이전에는 인터페이스에 새로운 메서드를 추가할 때마다 해당 인터페이스를 구현한 모든 클래스에서 컴파일 오류가 발생할 수 있었다. 하지만 자바 8에서는 **디폴트 메서드**(default method)가 도입되어, <mark style="color:red;">기존 인터페이스에 새로운 메서드를 추가하면서도 이를 재정의하지 않은 모든 구현 클래스에 기본 구현을 제공할 수 있게 되었다.</mark>

이로 인해 기존 코드에 큰 변경 없이 새로운 기능을 추가할 수 있지만, 문제는 디폴트 메서드가 기존 구현체와 항상 잘 맞지는 않을 수 있다는 것이다. 특히, **동기화**가 필요한 클래스를 다루는 경우에는 주의가 필요하다.

인터페이스에 `default 메서드`를 구현하면, 이 인터페이스를 구현한 모든 클래스에 해당 default 기능을 강제적으로 삽입하게 되고 이로 인해 문제가 발생할 수 있다. 대표적인 예는 **Collection에 있는 removeIf 메서**드다.

### 1) Collection의 removeIf 메서드

> [관련 참고 하면 좋은 글로 멀티스레드에서의   문제 ](https://sejoung.github.io/2018/12/2018-12-17-Design\_interfaces\_for\_posterity/#%EC%95%84%EC%9D%B4%ED%85%9C-21-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EB%A5%BC-%EA%B5%AC%ED%98%84%ED%95%98%EB%8A%94-%EC%AA%BD%EC%9D%84-%EC%83%9D%EA%B0%81%ED%95%B4-%EC%84%A4%EA%B3%84%ED%95%98%EB%9D%BC)

이 코드는 컬렉션의 각 요소를 반복하며, 주어진 프레디케이트(Predicate) 조건이 참이면 해당 요소를 제거하는 기본 구현이다.

```java
// Collection.java
default boolean removeIf(Predicate<? super E> filter) {
    Objects.requireNonNull(filter);
    boolean removed = false;
    final Iterator<E> each = iterator();
    while (each.hasNext()) {
        if (filter.test(each.next())) {
            each.remove();
            removed = true;
        }
    }
    return removed;
}
```

위는 Collection 인터페이스의 removeIf default 메서드를 보여준다. removeIf 자체는 굉장히 편리한 기능이다. 하지만 <mark style="color:red;">Collection을 구현한 클래스 입장에서 removeIf는 위험한 기능이 될 수도 있다.</mark>

### 2) **SynchronizedCollection과의 충돌**

{% hint style="danger" %}
문제는 **SynchronizedCollection**과 같은 동기화 컬렉션에서 발생할 수 있다.&#x20;
{% endhint %}

`SynchronizedCollection`은 `synchronized` 키워드를 사용하여 메서드가 호출될 때마다 동기화 처리한다. 그러나 `removeIf` 메서드는 동기화를 전혀 고려하지 않고 있기 때문에, `SynchronizedCollection`에서 이 메서드를 사용하면 **ConcurrentModificationException** 같은 동시성 문제가 발생할 수 있다.

```java
static class SynchronizedCollection<E> implements Collection<E>, Serializable {
    private final Collection<E> c;
    private final Object mutex;

    public SynchronizedCollection(Collection<E> c, Object mutex) {
        this.c = c;
        this.mutex = mutex;
    }

    @Override
    public int size() {
        synchronized (mutex) {
            return c.size();
        }
    }

    // 동기화를 위한 다른 메서드들 생략...
}
```

위 `SynchronizedCollection` 클래스는 컬렉션의 메서드를 동기화하여 스레드 안전성을 보장하지만, `removeIf` 메서드는 이 동기화 처리 없이 호출될 수 있기 때문에 문제가 된다.

### 3) **디폴트 메서드의 위험 해결 방법**

이 문제를 해결하기 위해서는, **기존 클래스**에서 디폴트 메서드를 **재정의**(override)하여 의도한 동작을 보장해야 한다. 예를 들어 `SynchronizedCollection` 클래스에서 `removeIf` 메서드를 재정의하여 해당 기능을 비활성화할 수 있다

```java
public class SafeSynchronizedCollection<E> extends SynchronizedCollection<E> {
    @Override
    public boolean removeIf(Predicate<? super E> filter) {
        throw new UnsupportedOperationException("removeIf is not supported");
    }
}
```

{% hint style="danger" %}
즉, 특정 클래스 관점에서 바라본 default 메서드는 특정 클래스에 해가 될 수 있다. 만약 어쩔 수 없이 default 메서드가 추가되었다면, **의도치 않은 동작을 막기 위해 해당 메서드를 오버라이딩해야한다.**
{% endhint %}

## 2. Default 메서드는 기존 구현체에 런타임 에러를 발생시킬 수 있다.

디폴트 메서드는 기존 클래스에 새로운 메서드를 삽입하는 방식이기 때문에, 기존 코드와 충돌할 가능성도 있다. 예를 들어, 클래스가 이미 같은 시그니처의 메서드를 가지고 있는 경우, 예상치 못한 런타임 오류가 발생할 수 있습니다. 아래 코드는 이러한 경우를 설명하는 예시

```java
public class SuperClass {
    private void hello() {
        System.out.println("hello class");
    }
}

public interface MarkerInterface {
    default void hello() {
        System.out.println("hello interface");
    }
}

public class SubClass extends SuperClass implements MarkerInterface {
    public static void main(String[] args) {
        SubClass subClass = new SubClass();
        subClass.hello();  // 컴파일은 성공하지만 런타임에 에러 발생
    }
}
```

* SuperClass는 구현체다. 이 때 hello()라는 메서드를 가지고, 이 메서드는 비공개 메서드다.
* MarkerInterface는 default 메서드 hello()를 가진다. 이 메서드는 인스턴스에서 접근 가능하다.

SubClass는 SuperClass를 상속받고, MarkerInterface를 구현한다.&#x20;

* 이 때, SuperClass의 hello()는 비공개이기 때문에 접근할 수 없어야 한다.&#x20;
* 이 때, MarkerInterface의 default 메서드인 hello()는 공개되었기 때문에 접근할 수 있다.&#x20;

따라서 SubClass가 <mark style="color:red;">hello()를 호출하면, 컴파일 시점에는 에러가 발생하지 않는다. 그렇지만 호출하는 시점에는 에러가 발생한다.</mark>&#x20;



이 코드에서는 `SuperClass`의 `hello()` 메서드가 **private**이고, `MarkerInterface`의 `hello()` 메서드는 **default**이다. 자바의 메서드 호출 우선순위 규칙에 따르면 클래스 메서드가 우선 호출되지만, `SuperClass`의 `hello()` 메서드는 `private`이므로 **IllegalAccessError**가 발생한다.



이것은 자바의 메서드 접근 규칙에 따라 발생하는 버그로 볼 수 있다.

> 자바는 인터페이스보다 클래스가, 상속한 클래스가 우선순위를 가진다.&#x20;

위의 경우 상속한 클래스의 hello() 자체는 접근할 수 없고, 보여지는 메서드는 default 메서드 hello() 일 것이다. 하지만 <mark style="color:red;">자바의 메서드 접근 규칙은 클래스가 메서드를 가지고 있다면 먼저 접근한다.</mark> 위에서 SubClass는 SuperClass를 상속했기 때문에 인터페이스의 메서드보다 클래스의 메서드로 먼저 접근한다.&#x20;

그런데 <mark style="color:red;">접근하고보니 접근한 메서드는 private이었기 때문에 위와 같은 에러가 발생한다.</mark>&#x20;



## **✨** 최종 정리

* **디폴트 메서드의 장점**은 새로운 기능을 추가할 때 기존의 모든 구현체를 수정할 필요 없이 기본 기능을 제공할 수 있다는 것이다. 이는 자바 8의 큰 개선 중 하나이다.
* 하지만 **디폴트 메서드의 위험성**은 기존 클래스와의 호환성 문제이다. 새로운 디폴트 메서드가 기존의 클래스와 충돌하거나, 그 클래스가 의도치 않은 방식으로 동작할 수 있기 때문에 위험할 수 있다.
* **런타임 에러**는 특히 <mark style="color:red;">메서드 접근 우선순위에서 발생할 수 있다.</mark> 클래스에서 제공하는 메서드가 우선 호출되지만, 그 메서드가 `private`일 때 인터페이스의 디폴트 메서드가 호출되지 않고 런타임에 에러가 발생할 수 있다.

이러한 이유로, 디폴트 메서드를 사용할 때는 매우 신중해야 하며, 디폴트 메서드가 추가된 후 기존 구현체들이 의도한 대로 동작하는지 충분히 검토해야 한다.



> 참고 글 : [https://ojt90902.tistory.com/1390](https://ojt90902.tistory.com/1390)
