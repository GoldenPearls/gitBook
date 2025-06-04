# item 83 : 지연 초기화는 신중히 사용하라

## 지연 초기화 (Lazy Initialization)의 개념과 활용

**지연 초기화**는 필드의 초기화 시점을 해당 값이 **처음 필요할 때까지 미루는 기법**이다. 이 기법을 사용하면 **필요하지 않은 경우 초기화 자체를 생략**할 수 있다. 주로 **최적화** 용도로 사용되며, 클래스와 인스턴스 초기화 시 발생할 수 있는 **위험한 순환 문제**를 해결하는 효과도 있다.

하지만 지연 초기화는 **양날의 검**이다. 인스턴스 생성 시 초기화 비용은 줄어들지만, 필드 접근 시의 비용이 커진다. 초기화 시점과 접근 빈도를 고려해 성능을 검토하지 않으면 **오히려 성능 저하를 초래**할 수 있다.

### 1) 지연 초기화의 특징

* **장점**: 초기화 비용이 클 때, 그 필드를 사용할 확률이 낮다면 성능 최적화에 도움을 준다.
* **단점**: 필드 접근 시마다 초기화 여부를 확인해야 하므로 **호출 비용이 증가**할 수 있다.
* **주의사항**: 멀티 스레드 환경에서 지연 초기화를 하려면 반드시 **동기화**가 필요하다.

멀티 스레드 환경에서는 지연 초기화가 까다롭기 때문에, 일반적으로 **지연 초기화보다는 즉시 초기화**가 권장된다. 진짜 필요할 때만 지연 초기화를 선택해야 한다.

### 2) 초기화 방법

#### 1. 일반적인 초기화

대부분의 상황에서 권장되는 방식이다. 필드를 `final`로 선언하고 **즉시 초기화**한다.

```java
private final FieldType field = computeFieldValue();
```

* **특징**: 간단하고 명확하며 스레드 안전하다.
* **사용 시점**: 필드가 항상 사용될 경우.

#### 2. synchronized 접근자를 사용한 지연 초기화

**초기화 순환성**이 걱정될 때 사용할 수 있다. 초기화 메서드를 `synchronized`로 감싸 동기화를 보장한다.

```java
private FieldType field;

private synchronized FieldType getField() {
    if (field == null) {
        field = computeFieldValue();
    }
    return field;
}
```

* **특징**: 간단하지만 **동기화 비용**이 발생한다.
* **적용 대상**: 인스턴스 필드와 정적 필드 모두에 적용 가능하다.

#### 3. 정적 필드 지연 초기화 홀더 클래스 관용구

정적 필드의 성능을 위해 사용하는 기법으로, 클래스의 **초기화 시점**을 지연시킨다.

```java
private static class FieldHolder {
    static final FieldType field = computeFieldValue();
}

private static FieldType getField() {
    return FieldHolder.field;
}
```

* **동작 원리**: `FieldHolder` 클래스는 **필드가 처음 읽힐 때 초기화**된다.
* **장점**: 동기화를 사용하지 않으므로 **성능 저하가 없다**.
* **적용 대상**: 정적 필드에 최적화된 초기화 방식.

#### 4. 이중검사 관용구 (Double-Check Idiom)

인스턴스 필드의 성능을 위해 사용하는 기법으로, 초기화된 필드에 접근할 때의 **동기화 비용을 제거**한다.

```java
private volatile FieldType field;

private FieldType getField() {
    FieldType result = field; // 첫 번째 검사 (동기화 X)
    if (result != null) {
        return result;
    }

    synchronized (this) {
        if (field == null) { // 두 번째 검사 (동기화)
            field = computeFieldValue();
        }
        return field;
    }
}
```

* **설명**:
  * **첫 번째 검사**: 동기화 없이 필드가 초기화되었는지 확인한다.
  * **두 번째 검사**: 동기화한 후 필드가 초기화되지 않은 경우에만 초기화한다.
* **필드 선언**: `volatile` 키워드가 필요하다.
* **장점**: 동기화 비용을 최소화하며 스레드 안전하다.
* **단점**: 코드가 복잡하다.

**`result` 지역 변수를 사용하는 이유**: 필드를 한 번만 읽어 성능을 최적화한다.

#### 5. 단일검사 관용구 (Single-Check Idiom)

이중검사 관용구의 변형으로, **반복 초기화가 허용되는 경우**에 사용된다.

```java
private volatile FieldType field;

private FieldType getField() {
    FieldType result = field;
    if (result == null) {
        field = result = computeFieldValue();
    }
    return result;
}
```

* **특징**: 두 번째 검사를 생략하므로 코드가 단순하다.
* **단점**: 필드 초기화가 **중복해서 발생할 수 있다**.

#### 6. 짜릿한 단일검사 관용구 (Racy Single-Check Idiom)

**모든 스레드가 필드를 다시 초기화해도 상관없을 때** 사용한다. 이 경우 `volatile`을 제거할 수 있다.

```java
private FieldType field;

private FieldType getField() {
    FieldType result = field;
    if (result == null) {
        field = result = computeFieldValue();
    }
    return result;
}
```

* **적용 조건**:
  * 필드의 타입이 `long`과 `double`을 제외한 **기본 타입**이거나, 초기화가 중복되어도 문제가 없는 경우.
* **장점**: **접근 속도**를 극대화할 수 있다.
* **단점**: 초기화가 **스레드당 최대 한 번 더 발생**할 수 있다.
* **활용도**: 거의 사용되지 않는 기법이다.

### 3) 핵심 정리

1. **대부분의 경우** 필드는 지연 초기화 대신 **즉시 초기화**하는 것이 좋다.
2. **정적 필드**를 지연 초기화해야 한다면 **홀더 클래스 관용구**를 사용하라.
3. **인스턴스 필드**의 경우 **이중검사 관용구**를 사용하면 동기화 비용을 최소화할 수 있다.
4. **반복 초기화가 허용되는 상황**에서는 **단일검사 관용구**를 고려할 수 있다.
5. **짜릿한 단일검사 관용구**는 매우 특수한 경우에만 사용해야 하며, 일반적으로 피하는 것이 좋다.

지연 초기화는 신중하게 선택해야 한다. 잘못 사용할 경우 **복잡한 코드와 성능 저하**로 이어질 수 있으므로, 필요한 경우에만 올바른 기법을 선택해 사용하자.

> 참고
>
> * [https://jjingho.tistory.com/134](https://jjingho.tistory.com/134)
