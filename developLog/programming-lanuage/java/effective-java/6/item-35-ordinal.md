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

### 3) 예제: EnumSet과 EnumMap에서 ordinal()의 사용 예시

```java
import java.util.EnumSet;
import java.util.EnumMap;
import java.util.Map;

public class EnumExample {

    // 열거 타입 정의
    public enum Day {
        MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
    }

    public static void main(String[] args) {
        // EnumSet 예제: 주중과 주말을 구분
        EnumSet<Day> weekdays = EnumSet.range(Day.MONDAY, Day.FRIDAY);
        EnumSet<Day> weekend = EnumSet.of(Day.SATURDAY, Day.SUNDAY);

        System.out.println("Weekdays: " + weekdays);
        System.out.println("Weekend: " + weekend);

        // EnumMap 예제: 각 요일에 할당된 할일을 저장
        EnumMap<Day, String> dayTasks = new EnumMap<>(Day.class);
        dayTasks.put(Day.MONDAY, "Study Java");
        dayTasks.put(Day.TUESDAY, "Go to gym");
        dayTasks.put(Day.WEDNESDAY, "Grocery shopping");
        dayTasks.put(Day.THURSDAY, "Clean house");
        dayTasks.put(Day.FRIDAY, "Watch a movie");
        
        System.out.println("Tasks for the week:");
        for (Map.Entry<Day, String> entry : dayTasks.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }

        // ordinal() 출력 예제
        System.out.println("\nOrdinal values:");
        for (Day day : Day.values()) {
            System.out.println(day + " ordinal: " + day.ordinal());
        }
    }
}
```

#### 설명

1. **EnumSet**: `EnumSet.range()` 메서드는 `ordinal()`을 사용해 `Day` 열거 타입의 상수 순서에 따라 월요일부터 금요일까지의 범위를 지정한다. `EnumSet.of()`로 주말만 포함된 집합을 만들 수도 있다. `EnumSet`은 내부적으로 비트 벡터를 이용해 `ordinal()`을 활용해 고속 연산을 한다.
2. **EnumMap**: `EnumMap`은 열거 타입 상수를 키로 사용하여, `ordinal` 값으로 인덱스를 계산하여 배열처럼 효율적으로 값을 저장하고 검색한다.
3. **ordinal()**: `ordinal()` 메서드를 통해 열거 타입 상수의 순서(정수 값)를 확인할 수 있으며, 출력된 결과를 통해 각 상수의 `ordinal` 값을 확인할 수 있다.

```plaintext
Weekdays: [MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY]
Weekend: [SATURDAY, SUNDAY]

Tasks for the week:
MONDAY: Study Java
TUESDAY: Go to gym
WEDNESDAY: Grocery shopping
THURSDAY: Clean house
FRIDAY: Watch a movie

Ordinal values:
MONDAY ordinal: 0
TUESDAY ordinal: 1
WEDNESDAY ordinal: 2
THURSDAY ordinal: 3
FRIDAY ordinal: 4
SATURDAY ordinal: 5
SUNDAY ordinal: 6
```

> `EnumSet`과 `EnumMap`은 `ordinal`을 내부적으로 사용해 고성능 자료구조를 제공하며, 이를 통해 열거 타입 상수를 효율적으로 관리할 수 있다.

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
