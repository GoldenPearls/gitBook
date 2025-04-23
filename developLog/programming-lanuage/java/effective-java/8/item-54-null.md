# item 54 : null이 아닌, 빈 컬렉션이나 배열을 반환하라

## 1. null을 반환하는 것의 단점

**문제 코드**:

```java
private final List<Cheese> cheesesInStock = new ArrayList<>();

public List<Cheese> getCheeses() {
    return cheesesInStock.isEmpty() ? null : new ArrayList<>(cheesesInStock);
}
```

* 위 메서드는 `cheesesInStock`에 **원소가 없을 때 null을 반환한**다.
* 이로 인해 메서드를 호출하는 클라이언트 코드에서 **null 체크**를 필수적으로 해야 한다. 방어코드 필수!

**문제 확인**:

```java
@Test
public void getCheesesClient() {
    List<Cheese> cheeses = getCheeses();

    if (cheeses != null && cheeses.contains(Cheese.STILTON)) {
        System.out.println("좋았어, 바로 그거야.");
    }
}
```

* `getCheeses()` 메서드를 사용하는 클라이언트는 반드시 `cheeses != null`과 같은 조건문을 사용해 **null을 처리**해야 한다.
* 이러한 null 처리는 코드를 복잡하게 하고 **NullPointerException(NPE)을 유발할 가능성을 높인다.**
* **null을 반환하지 않는 대안**

> **빈 리스트를 반환**하면 클라이언트 코드에서 추가적인 null 처리를 하지 않아도 되며, 코드가 간단해지고 **안전해진다**.

{% hint style="danger" %}
**null을 반환하는 것이 성능상 낫지 않은가?**:
{% endhint %}

* 책에서는 이 물음이 틀렸다고 답한다.
* 물론 새로운 객체를 하나 만들지만 사실상 성능상의 커다란 손해가 없다. (미미하다.)
* 빈 컬렉션과 배열은 굳이 새로 할당하지 않고도 반환 가능하다.
  * 빈 컬렉션을 불변으로 선언해놓고 같은 케이스에 계속 재활용해서 할당해도 된다.
  * Collections.emptyList(), Collections.emptySet(), Collections.emptyMap()도 있다.

## 2. null을 반환하지 않는 대안

### **1) 컬렉션 생성자를 이용하는 방법**:

```java
public List<Cheese> getCheeses2() {
    return new ArrayList<>(cheesesInStock);
}
```

* 이 방법은 새로운 리스트를 만들어 반환합
* 사용 패턴에 따라 성능에 영향을 줄 수 있지만, 대부분의 경우 큰 문제가 되지 않는다.

### **2) 길이 0짜리 배열을 반환하는 방법**:

```java
public Cheese[] getCheeses3() {
    return cheesesInStock.toArray(new Cheese[0]);
}
```

* 배열을 반환할 때도 **null 대신 길이 0짜리 배열**을 반환하는 것이 더 좋다.

### **3) 불변인 빈 배열을 사용하는 방법**:

```java
private static final Cheese[] EMPTY_CHEESE_ARRAY = new Cheese[0];

public Cheese[] getCheeses4() {
    return cheesesInStock.toArray(EMPTY_CHEESE_ARRAY);
}
```

* `EMPTY_CHEESE_ARRAY`를 사용해 **재사용 가능한 빈 배열**을 반환한다.
* 성능상 **배열을 매번 생성하지 않기 때문에 효율적**

1. **toArray()의 동작 방식**:
   * `toArray()`는 매개변수로 받은 배열의 크기에 따라 동작이 달라진다.
     * 배열이 **충분히 크면** 원소를 담아 반환한다.
     * 그렇지 않으면 배열을 **새로 생성**하여 그 안에 원소를 담아 반환한다.
   * **길이 0짜리 배열**을 매개변수로 사용하면, JVM이 더 효율적으로 새 배열을 생성해주는 최적화가 이루어질 수 있다.

## 📚 핵심 정리

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

1. **null 반환을 피하고 빈 컬렉션이나 배열을 반환하자**:
   * 배열이나 컬렉션을 반환할 때는 **null 대신 빈 컬렉션이나 배열**을 반환하는 것이 좋다.
   * 이를 통해 API 사용자에게 **추가적인 null 체크 부담**을 줄여줄 수 있으며, 코드의 안전성을 높인다.
2. **null 반환의 단점**:
   * **API 사용 시 고려할 점**이 많아지고, **null을 처리하지 않으면 NPE**가 발생할 수 있는 위험이 크다.
   * 이러한 **불필요한 null 처리는 코드의 가독성을 떨어뜨리고 오류를 유발**할 가능성이 높아진다.
3. **성능 최적화**:
   * **빈 컬렉션이나 배열**을 반환하는 것이 성능 면에서 큰 차이가 없으며, 때로는 **재사용 가능한 불변 객체**를 활용해 최적화할 수도 있다.
   * 예를 들어 `Collections.emptyList()`, `Collections.emptySet()`, `Collections.emptyMap()`은 **JVM에서 미리 준비된 객체를 재사용하는 방식**으로 매우 효율적

## 😲 고려해야 할 점

1. **코드의 안전성**:
   * null을 반환하는 대신 **빈 객체를 반환**함으로써 코드가 **보다 안전하고 간결**해진다. NPE와 같은 **런타임 예외 발생을 방지**할 수 있어 API의 신뢰성이 높아진다.
2. **읽기 전용 컬렉션 사용**:
   * 가능하면 **불변 컬렉션**을 반환하는 것이 좋다. 예를 들어, `Collections.emptyList()`나 `Collections.unmodifiableList()`를 사용해 반환된 컬렉션을 수정하지 못하도록 하면, **불필요한 사이드 이펙트**를 방지할 수 있다.
3. **성능과 재사용성**:
   * 빈 컬렉션이나 배열을 재사용함으로써 **성능적인 이점**을 얻을 수 있다. 빈 객체를 매번 새로 생성하지 않고, 미리 선언한 빈 객체를 재사용하는 것이 더 효율적다.
4. **클라이언트 코드의 간결성**:
   * null을 반환하면 클라이언트 코드가 이를 **명시적으로 체크**해야 하므로 코드가 복잡해진다. 빈 컬렉션을 반환하면 이런 체크가 필요 없어져 **코드가 간결**해지고 **유지보수**가 쉬워진다.

> 결론적으로, **null을 반환하지 말고 빈 컬렉션이나 배열을 반환하는 것이** 성능과 안전성 면에서 더 좋다. null 체크를 최소화하고, 클라이언트 코드의 간결함과 유지보수성을 높이며, 성능상으로도 큰 차이가 없기 때문에 **최선의 선택**



> 참고 및 출처
>
> * [https://jake-seo-dev.tistory.com/527](https://jake-seo-dev.tistory.com/527)
