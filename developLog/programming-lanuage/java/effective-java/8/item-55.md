# item 55 : 옵셔널 반환은 신중히 하라

## 1. null 반환의 문제

1. 자바 8 이전에는 메서드가 특정 조건에서 값을 반환할 수 없는 경우 **두 가지 선택지**가 있었다:

* **예외를 던지기**: 예외는 진짜 예외적인 상황에서 사용해야 하며, **스택 추적 정보를 캡처**하는 비용이 크기 때문에, 자주 사용하기에는 부적절하다.
* **null 반환**: null을 반환하는 경우, 메서드 호출 후 **항상 null 체크**를 해야 하며, 이를 누락하면 NullPointerException(NPE)을 발생시킬 위험이 크다.

2. null 반환 시 클라이언트는 메서드가 null을 반환할 가능성을 염두에 두고 코드를 작성해야 한다:

```java
if (object != null) {
    return object.method();
}
```

* 이와 같은 null 체크는 **코드의 복잡성을 증가**시키며, **진짜 예외 처리**에 집중할 수 없게 만든다.

## 2. Optional의 등장

> 자바 8부터 **Optional** 클래스가 등장하여 null 반환 대신 사용할 수 있는 새로운 대안을 제공한다.

### Optional\<T>의 등장

* **Optional**은 **최대 1개의 원소를 담을 수 있는 불변 컬렉션**으로 볼 수 있다. **null 대신 비었음을 명확하게 표현**할 수 있으며, 메서드 사용 시 값이 없을 수 있음을 명확히 전달한다.
* `null` 이 아닌 `T` 타입 참조 하나를 담거나 아무것도 담지 않는다.
  * `null` 을 피하기 위한 `Optional` 에 `null` 을 담는 것은 안티 패턴이다.
* `Optional<T>` 는 원소를 최대 1개 가질 수 있는 '불변 컬렉션' 이다.
* 예외를 던지는 메서드보다 유연하고 사용하기 쉽다.
* `null` 을 반환하는 메서드보다 오류 가능성이 적다.

## 3. Optional을 활용한 코드 개선

### **예제 1: 최대값 찾기 메서드**

**null을 사용한 기존 코드**:

```java
public static <E extends Comparable<E>> E max(Collection<E> c) {
    // 컬렉션이 비어 있으면 예외를 던진다.
    if (c.isEmpty()) {
        throw new IllegalArgumentException("빈 컬렉션");
    }

    // 결과 값을 저장할 변수
    E result = null;

    // 컬렉션을 순회하며 최대값을 찾는다.
    for (E e : c) {
        // 현재의 결과가 null이거나 더 큰 값을 발견하면 갱신
        if (result == null || e.compareTo(result) > 0) {
            result = Objects.requireNonNull(e);
        }
    }

    // 최대값 반환 (null이 아닌 값이어야 함)
    return result;
}
```

* `max()` 메서드는 주어진 컬렉션에서 최대값을 찾는다.
* 컬렉션이 비어 있으면 **IllegalArgumentException**을 던지며, 이는 호출자가 반드시 예외 처리를 해야 하는 부담을 준다.
* **null 체크**와 **예외 처리**가 필요하여 코드가 복잡해진다.

**Optional을 사용한 코드**:

```java
public static <E extends Comparable<E>> Optional<E> max2(Collection<E> c) {
    // 컬렉션이 비어 있는 경우, 빈 Optional 반환
    if (c.isEmpty()) {
        return Optional.empty();
    }

    // 결과 값을 저장할 변수
    E result = null;

    // 컬렉션을 순회하며 최대값을 찾는다.
    for (E e : c) {
        // 현재의 결과가 null이거나 더 큰 값을 발견하면 갱신
        if (result == null || e.compareTo(result) > 0) {
            result = Objects.requireNonNull(e);
        }
    }

    // 최대값을 Optional로 감싸서 반환
    return Optional.of(result);
}
```

* 컬렉션이 비어 있으면`Optional.empty()`를 반환하여 **null 반환으로 인한 NPE** 문제를 피한다.
* `Optional.of(result)`는 결과가 null이 아님을 보장한다.
* **Optional**을 사용하면 클라이언트 코드에서 **null 체크 없이 Optional의 메서드들**을 통해 안전하게 값을 처리할 수 있다.

### **예제 2: Stream과 Optional 활용**

**Stream API와 Optional을 이용한 코드**:

```java
public static <E extends Comparable<E>> Optional<E> max3(Collection<E> c) {
    // 컬렉션의 스트림을 이용하여 최대값 찾기
    return c.stream().max(Comparator.naturalOrder());
}
```

* **Stream API**를 사용하여 컬렉션에서 최대값을 찾는다.
* **`max()`** 메서드는 **Comparator**를 사용하여 스트림에서 최대값을 계산하며, 결과를 **Optional**로 반환다.
* **코드의 간결함**과 **가독성**을 높이는 동시에, **null 처리 문제**도 피할 수 있다.

## 4. Optional 사용법 및 예제

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### **1) 기본 값 정하기 (`optional.orElse()`)**

**기본 값을 반환**하는 예제:

```java
@Test
public void optionalDefaultValue() {
    List<Integer> integers = new ArrayList<>();
    // 컬렉션이 비어 있으면 기본 값 0을 반환
    Integer optional = max3(integers).orElse(0);
    System.out.println("optional = " + optional);
}
```

**부가 설명**:

* **`orElse()`** 메서드를 사용하여, **Optional이 비어 있을 경우 기본 값을 반환**한다.
* 이 경우 최대값이 없으면 **기본 값으로 0**을 반환한다.

### **2) 기본 예외 던지기 (`optional.orElseThrow()`)**

**예외를 던지는 예제**:

```java
@Test
public void optionalDefaultThrow() {
    List<Integer> integers = new ArrayList<>();
    // 컬렉션이 비어 있으면 IllegalArgumentException을 던짐
    Integer optional = max3(integers).orElseThrow(IllegalArgumentException::new);
    System.out.println("optional = " + optional);
}
```

**부가 설명**:

* **`orElseThrow()`** 메서드를 사용하여 **값이 없을 때 예외를 던진다**.
* 이 경우, 빈 컬렉션이라면 **IllegalArgumentException**이 발생다.

### **3) 항상 값이 있다고 가정하기 (`optional.get()`)**

**값이 반드시 있다고 가정하는 예제**:

```java
@Test
public void optionalDefaultGet() {
    List<Integer> integers = new ArrayList<>(List.of());
    // 값이 있다고 가정하고 Optional에서 값을 가져옴
    Integer optional = max3(integers).get(); // 빈 경우 예외 발생
    System.out.println("optional = " + optional);
}
```

**부가 설명**:

* **`get()`** 메서드는 **값이 반드시 있다고 가정**하고 사용힌다.
* 값이 없을 경우 `NoSuchElementException`이 발생하므로 사용 시 주의

### **4) 초기 생성 비용이 큰 경우 (`optional.orElseGet()`)**

**초기 생성 비용을 줄이기 위한 예제**:

```java
Connection connection = getConnection(dataSource).orElseGet(() -> getLocalConnection());
```

**부가 설명**:

* `orElseGet()`은 값이 필요할 때 **지연 초기화** 방식으로 `Supplier`를 사용하여 생성한다.
* **초기 생성 비용이 큰 객체**를 필요할 때만 생성하도록 하여 **비용을 줄일 수 있다**.

## 5. Optional 사용 시 주의사항

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Optional 사용을 피해야 하는 경우**:
  1. **컨테이너 타입** (예: `List`, `Stream`, 배열)에 Optional을 사용하지 말 것.
     * 예를 들어, `Optional<List<T>>` 대신 **빈 리스트를 반환**하는 것이 더 간결하고 명확
  2. **박싱된 기본 타입** (예: `Integer`)을 Optional로 감싸지 말고, 전용 클래스 (`OptionalInt`, `OptionalLong`, `OptionalDouble`)를 사용할 것.
     * 기본 타입을 박싱하면 **성능 저하**가 발생하므로 전용 클래스를 사용하는 것이 더 효율적
  3. **Optional을 컬렉션의 키, 값, 배열의 원소로 사용하지 말 것**.
     * **Optional을 맵의 값**으로 사용하면 맵에 없는 키와 **값이 빈 Optional인 경우**를 구분해야 하므로 **복잡성이 증가**

## 📚 핵심 정리

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 반환값이 없을 가능성이 있는 메서드라면 **Optional을 반환**하는 것이 좋다.
  * 이를 통해 **반환값이 없을 가능성을 명확히 알리고**, **null 처리를 통한 NPE 위험**을 줄일 수 있다.
* 그러나 **Optional 반환**에도 **객체 생성 비용**이 추가되므로, **성능이 중요한 상황**에서는 **null 반환**이나 **예외 처리**가 더 적합할 수 있다.
* Optional은 **반환값 이외의 용도로는 사용하지 않는 것이 권장**되며, 특히 **컨테이너 타입을 감싸는 것**은 지양해야 한다.

> Optional을 적절히 사용함으로써 **코드의 안전성**과 **가독성**을 높일 수 있지만, 사용 시 주의할 점도 많으므로 상황에 따라 신중하게 선택
