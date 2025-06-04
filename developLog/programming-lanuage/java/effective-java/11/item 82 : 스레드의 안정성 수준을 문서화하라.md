# item 82 : 스레드의 안정성 수준을 문서화하라

## 메서드의 스레드 안전성과 문서화의 중요성

여러 스레드가 하나의 메서드를 동시에 호출할 때 그 메서드가 어떻게 동작하는지는 **클래스와 클라이언트 간의 중요한 계약**이다. API 문서에 스레드 안전성에 대한 명확한 설명이 없으면 사용자는 스레드 안전성 여부를 추측할 수밖에 없다. 이러한 가정이 틀리면 프로그램은 심각한 동기화 오류를 일으킬 수 있다.&#x20;

> 따라서 스레드 안전성은 명확히 정의하고 문서화해야 한다.

## synchronized 한정자와 스레드 안전성

`synchronized` 한정자가 메서드에 붙어있다고 해서 그 메서드가 스레드 안전하다고 가정하는 것은 위험하다. 몇 가지 이유가 있다:

1. **구현 이슈**: `synchronized` 여부는 구현 세부 사항이며, API 문서의 일부가 아니다. 자바독(JavaDoc)은 기본적으로 `synchronized` 키워드를 표시하지 않는다.
2. **스레드 안전성 수준**: 스레드 안전성은 단순히 "안전하다" 또는 "안전하지 않다"로 구분되지 않으며, 여러 수준으로 나뉜다.

따라서 클래스나 메서드가 제공하는 스레드 안전성 수준을 정확하게 명시해야 한다. **스레드 안전성의 수준**은 다음과 같이 나뉜다.

### 1) 스레드 안전성의 수준

스레드 안전성의 수준은 높은 수준부터 낮은 수준까지 아래와 같이 나눌 수 있다:

#### 1. 불변 (Immutable)

불변 클래스는 한 번 인스턴스가 생성되면 상태가 절대 변경되지 않는다. 이러한 클래스는 외부 동기화가 필요 없으므로 스레드 안전하다.

* **특징**: 모든 필드가 `final`로 선언되고 생성자에서 초기화된다.
* **스레드 안전성 애너테이션**: `@Immutable`
* **대표적인 예**: `String`, `Long`, `BigInteger`

```java
public final class ImmutableExample {
    private final int value;

    public ImmutableExample(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }
}
```

#### 2. 무조건적 스레드 안전 (Unconditionally Thread-Safe)

내부적으로 철저히 동기화되므로 외부에서 동기화를 추가할 필요 없이 안전하게 사용할 수 있다.

* **스레드 안전성 애너테이션**: `@ThreadSafe`
* **대표적인 예**: `AtomicLong`, `ConcurrentHashMap`

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
map.put("key", 1);
System.out.println(map.get("key"));
```

#### 3. 조건부 스레드 안전 (Conditionally Thread-Safe)

일부 메서드는 스레드 안전하지만, 특정 상황에서는 외부 동기화가 필요하다.

* **스레드 안전성 애너테이션**: `@ThreadSafe`
* **대표적인 예**: `Collections.synchronizedMap`이 반환한 컬렉션

**문서화의 중요성**: 어떤 메서드가 외부 동기화를 필요로 하는지, 동기화에 필요한 **락**이 무엇인지 명시해야 한다.

```java
Map<String, String> map = Collections.synchronizedMap(new HashMap<>());
Set<String> keySet = map.keySet();

synchronized (map) { // 반드시 map을 동기화
    for (String key : keySet) {
        System.out.println(key);
    }
}
```

#### 4. 스레드 안전하지 않음 (Not Thread-Safe)

클래스의 인스턴스를 여러 스레드가 동시에 사용하면 데이터 무결성이 깨질 수 있다. 따라서 외부 동기화가 필요하다.

* **스레드 안전성 애너테이션**: `@NotThreadSafe`
* **대표적인 예**: `ArrayList`, `HashMap`

```java
List<Integer> list = new ArrayList<>();

synchronized (list) {
    list.add(1);
    list.add(2);
}
```

#### 5. 스레드 적대적 (Thread-Hostile)

외부 동기화로도 스레드 안전성을 보장할 수 없는 클래스다. 보통 설계 결함에 의해 발생하며, 이러한 클래스는 수정하거나 **자제(deprecated)** API로 지정해야 한다.

### 2) 조건부 스레드 안전 클래스 문서화

조건부 스레드 안전 클래스는 외부 동기화가 필요한 메서드나 호출 순서를 정확히 문서화해야 한다. 예를 들어 `Collections.synchronizedMap`의 경우 다음과 같은 규칙이 있다:

<figure><img src="../../../../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

> synchronizedMap이 반환한 맵의 컬렉션 뷰를 순회하려면 **반드시 그 맵을 락으로 사용해 수동으로 동기화**하라.
>
> (코드 중략)
>
> 코드대로 따르지 않으면 동작을 예측할 수 없다.

&#x20;

클래스의 스레드 안전성은 보통 클래스의 문서화 주석에 기재하지만, 독특한 특성의 메서드라면 해당 메서드의 주석에 기재하도록 하자. 반환 타입만으로는 명확히 알 수 없는 정적 팩터리라면 자신이 반환하는 객체의 스레드 안전성을 반드시 문서화해야 한다. (위 사진처럼)

```java
Map<String, String> map = Collections.synchronizedMap(new HashMap<>());
Set<String> keys = map.keySet();

synchronized (map) { // 반드시 map을 동기화
    for (String key : keys) {
        System.out.println(key);
    }
}
```

동기화 규칙을 따르지 않으면 결과를 예측할 수 없게 된다.

### 3) 비공개 락 객체 관용구

외부에서 사용할 수 있는 락(Lock)을 제공하면 클라이언트에서 일련의 메서드 호출을 원자적으로 수행할 수 있는 유연함을 얻을 수 있다. 하지만 이를 사용함으로써 내부에서 처리하는 고성능 동시성 제어 메커니즘과 혼용할 수 없게 된다. 즉, ConcurrentHashMap 같은 고성능 동시성 컬렉션을 함께 사용하지 못한다는 뜻이다.

또한, 클라이언트가 서비스 거부 공격(denial-of-service attack)을 수행할 수도 있다.

{% hint style="danger" %}
서비스 거부 공격이란 클라이언트가 공개된 락을 오래 쥐고 놓지 않는 것을 의미한다.
{% endhint %}

서비스 거부 공격을 막으려면 비공개 락 객체를 사용해야 한다. (synchronized 메서드는 공개된 락으로 볼 수 있다.)

{% hint style="success" %}
lock 필드는 항상 **final**로 선언하자. final로 선언하면 우연히라도 락 객체가 교체되는 일을 예방할 수 있다.
{% endhint %}

#### 비공개 락 객체의 사용법

```java
private final Object lock = new Object();

public void someMethod() {
    synchronized (lock) {
        // 동기화된 작업 수행
        System.out.println("Thread-safe operation");
    }
}
```

**장점:**

1. **클라이언트가 동기화에 개입하지 못한다**: 비공개 락 객체는 클래스 내부에 캡슐화된다.
2. **서비스 거부 공격 방지**: 외부에서 락을 오랜 시간 동안 잡는 것을 방지한다.

#### 제한사항

* 조건부 스레드 안전 클래스에서는 사용할 수 없다. 특정 호출 순서에 따라 필요한 락을 명확히 알려줘야 하기 때문이다.
* 상속을 위한 클래스에서는 비공개 락 객체를 사용하는 것이 특히 중요하다. 상속 구조에서는 하위 클래스가 의도치 않게 동기화를 깨뜨릴 수 있기 때문이다.

## 📚 핵심 정리

1. **스레드 안전성을 명확히 문서화하라**: 클래스의 스레드 안전성 수준과 필요한 동기화 방법을 API 문서에 명확히 기록하라.
2. **비공개 락 객체를 사용하라**: 공개된 락을 피하고, 비공개 락 객체를 사용해 서비스를 안전하게 보호하라.
3. **상속용 클래스에는 비공개 락 객체를 사용하라**: 상위 및 하위 클래스의 동기화 메커니즘이 충돌하지 않도록 보호하라.
4. **조건부 스레드 안전 클래스는 동기화 규칙을 명확히 문서화하라**: 동기화가 필요한 메서드와 락을 명확하게 설명해야 한다.

스레드 안전성을 정확하게 정의하고 문서화하면 **안전하고 확장 가능한 멀티스레드 시스템**을 구현할 수 있다. 이 원칙을 지키면 개발자는 동기화 문제를 최소화하면서도 성능과 신뢰성을 동시에 확보할 수 있다.
