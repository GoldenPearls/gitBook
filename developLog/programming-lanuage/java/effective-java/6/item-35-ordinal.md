# item 35 : ordinal 메서드 대신 인스턴스 필드를 사용하라

## 1. ordinal이라는 메서드

대부분의 열거 타입 상수는 자연스럽게 하나의 정숫값에 대응된다.&#x20;

### 1) ordinal 메서드

모든 열거 타입은 **해당 상수가 그 열거 타입에서 몇 번째 위치인지를 반환**하는 `ordinal`이라는 메서드를 제공한다.

### 2) ordinal 메서드 잘못 사용한 예 : 유지 보수의 끔찍

아래 `Ensemble` 열거 타입은 각 연주 그룹의 인원 수를 반환하기 위해 `ordinal()` 메서드를 사용했다.

```java
public enum Ensemble {
    SOLO, DUET, TRIO, QUARTET, QUINTET,
    SEXTET, SEPTET, OCTET, NONET, DECTET;

    public int numberOfMusicians() {
        return ordinal() + 1;
    }
}

@Test
public void ensembleTest() {
    Ensemble solo = Ensemble.SOLO;
    System.out.println("solo = " + solo);
    System.out.println("solo.numberOfMusicians() = " + solo.numberOfMusicians());
}
```

**문제점:**

1. **순서 의존성**: 상수의 선언 순서가 바뀌면 `numberOfMusicians`의 값도 함께 바뀌어 불일치가 발생한다.
2. **값 중복 불가**: 동일한 인원 수를 가진 상수(`OCTET`, `DOUBLE_QUARTET`)는 추가할 수 없다. 만일 중간에 다른 상수라도 추가되면 numberOfMusicians()는 혼란에 빠진다.
3. **값을 건너뛸 수 없음**: 중간에 비어 있는 값을 사용하지 못해 의미 없는 상수를 추가해야 할 수 있다.
4. `ordinal()`은 EnumSet, EnumMap과 같이 **열거타입 기반의 범용 자료구조에 쓰일 목적으로 설계**되었다.



## 2. EnumSet, EnumMap **범용 자료구조에 쓰일 목적의 ordinal()**

`ordinal()` 메서드는 `EnumSet`과 `EnumMap`에서 열거 타입의 상수를 내부적으로 저장할 때 유용하게 활용된다. `EnumSet`은 열거 타입의 상수를 비트 벡터로 관리하고, `EnumMap`은 열거 타입을 키로 사용하는 맵으로 `ordinal` 값을 기반으로 효율적으로 동작한다.

### 1) EnumSet에서의 ordinal() 활용

```javascript
public boolean contains(Object e) {
    if (e == null)
        return false;
    Class<?> eClass = e.getClass();
    if (eClass != elementType && eClass.getSuperclass() != elementType)
        return false;
    return (elements & (1L << ((Enum<?>)e).ordinal())) != 0;
}
```

* EnumSet은 ordinal()의 값을 **bit-vector로 이용하여 중복 판단**을 한다.

### 2) EnumMap에서의 ordinal() 활용

```java
public boolean containsKey(Object key) {
return isValidKey(key) && vals[((Enum<?>)key).ordinal()] != null;
}
```

* EnumMap은 ordinal()의 값을 값 배열의 인덱스로 활용한다.



## 3. ordinal 메서드 대신 인스턴스 필드를 사용하자

이 문제는 각 상수에 **고유한 값을 할당하는 인스턴스 필드를 통해 해결할 수 있다.** `ordinal` 메서드 대신, 상수별로 고정된 값을 필드에 저장하여 사용하는 방식이다.

```java
public enum Ensemble {
    SOLO(1), DUET(2), TRIO(3), QUARTET(4), QUINTET(5),
    SEXTET(6), SEPTET(7), OCTET(8), DOUBLE_QUARTET(8),
    NONET(9), DECTET(10), TRIPLE_QUARTET(12);

    private final int numberOfMusicians;

    Ensemble(int size) {
        this.numberOfMusicians = size;
    }

    public int numberOfMusicians() {
        return numberOfMusicians;
    }
}
```

* **순서에 독립적**: 상수 선언 순서가 바뀌어도 `numberOfMusicians` 값은 바뀌지 않는다.
* **값 중복 허용**: `OCTET`과 `DOUBLE_QUARTET`처럼 같은 인원수를 가지는 상수를 추가할 수 있다.
* **값의 유연성**: 중간에 빈 값을 남기거나 추가할 수 있다.



> 춡처
>
> * [https://jake-seo-dev.tistory.com/55?category=906605](https://jake-seo-dev.tistory.com/55?category=906605)
