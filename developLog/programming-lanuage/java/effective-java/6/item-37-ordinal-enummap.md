# item 37 : ordinal 인덱싱 대신 EnumMap을 사용하라

## 1. ordinal() 을 배열 인덱스로 이용한 예제

과거에는 **비트 필드**와 `ordinal()` 메서드를 활용해 **Enum을 정수 값**으로 다루는 방식이 흔했다. 예를 들어, 식물(Plant) 클래스에서 식물을 **생애 주기**(한해살이, 여러해살이, 두해살이)별로 관리한다고 할 때, `ordinal()` 메서드로 `Enum`의 순서를 정수로 받아 배열의 인덱스로 사용하는 방식이 종종 사용되었다.

### **1) 비트 필드 및 `ordinal()` 방식의 주요 문제점**

1. **타입 안전성 문제**: `ordinal()`은 Enum의 순서 값을 반환하지만, `정수`이기 때문에 잘못된 인덱스를 사용할 가능성이 있다. 이러한 문제로 **ArrayIndexOutOfBoundsException**이나 **NullPointerException**이 발생할 수 있다.
2. **명확하지 않은 코드**: `ordinal()`을 통해 얻은 정수를 배열의 인덱스로 사용하는 방식은 코드의 의미가 불분명해져, 유지보수성과 가독성이 떨어진다.
3. **형변환 문제**: Set 클래스는 제네릭 타입을 받는데, 제네릭 타입은 배열과 호환성이 좋지 않다. 그래서 비검사 형변환을 수행해야 하고, 깔끔히 컴파일되지 않는다.
4. **배열의 크기 관리**: `Enum` 값이 추가될 때마다 배열의 크기와 값이 일치하도록 조정해야 하므로 실수가 발생하기 쉽다.

```java
class Plant {
    enum LifeCycle {ANNUAL, PERENNIAL, BIENNIAL}

    final String name;
    final LifeCycle lifeCycle;

    Plant(String name, LifeCycle lifeCycle) {
        this.name = name;
        this.lifeCycle = lifeCycle;
    }

    @Override public String toString() { return name; }
}

// 문제 코드: 생애주기별 식물을 배열로 관리하는 코드
@Test
public void plantsByLifeCycleTest() {
    Set<Plant>[] plantsByLifeCycle = (Set<Plant>[]) new Set[Plant.LifeCycle.values().length];

    List<Plant> garden = new ArrayList<>(List.of(
            new Plant("A", Plant.LifeCycle.ANNUAL),
            new Plant("B", Plant.LifeCycle.PERENNIAL),
            new Plant("C", Plant.LifeCycle.BIENNIAL),
            new Plant("D", Plant.LifeCycle.ANNUAL)
    ));

    for (int i = 0; i < plantsByLifeCycle.length; i++) {
        plantsByLifeCycle[i] = new HashSet<>();
    }

    for (Plant plant : garden) {
        plantsByLifeCycle[plant.lifeCycle.ordinal()].add(plant);
    }

    for (int i = 0; i < plantsByLifeCycle.length; i++) {
        System.out.printf("%s: %s%n", Plant.LifeCycle.values()[i], plantsByLifeCycle[i]);
    }
}
```

위의 코드에서 `plantsByLifeCycle` 배열은 `LifeCycle` Enum의 `ordinal()` 값을 인덱스로 사용하여 생애 주기별로 식물을 분류한다. 하지만 이 코드는 비검사 형변환이 필요하고, 잘못된 `ordinal()` 값을 사용할 경우 예외가 발생할 수 있다.

## `2. EnumMap`을 사용한 대안

> 위에서의  배열은 실질적으로 **열거 타입 상수를 값으로 매핑하는 일을** 한다. 그러니 Map을 사용할 수도 있을 것이다.

`EnumMap`은 **열거 타입을 키로 사용**하여 데이터를 매핑할 때 최적의 성능을 제공한다.&#x20;

> `EnumMap`은 내부적으로 배열을 사용하지만, `Enum` 타입을 <mark style="color:red;">키로만 사용할 수 있도록 제한</mark>하여 안전하고 효율적인 방식으로 구현되었다.&#x20;

이를 활용하면 비트 필드 방식에서 발생하던 문제들을 해결할 수 있다.

**`EnumMap`을 사용한 코드**

```java
@Test
public void plantEnumMapTest() {
    List<Plant> garden = new ArrayList<>(List.of(
            new Plant("A", Plant.LifeCycle.ANNUAL),
            new Plant("B", Plant.LifeCycle.PERENNIAL),
            new Plant("C", Plant.LifeCycle.BIENNIAL),
            new Plant("D", Plant.LifeCycle.ANNUAL)
    ));

    Map<Plant.LifeCycle, Set<Plant>> plantsByLifeCycle = new EnumMap<>(Plant.LifeCycle.class);

    for (Plant.LifeCycle lifeCycle : Plant.LifeCycle.values()) {
        plantsByLifeCycle.put(lifeCycle, new HashSet<>());
    }

    for (Plant plant : garden) {
        plantsByLifeCycle.get(plant.lifeCycle).add(plant);
    }

    System.out.println("plantsByLifeCycle = " + plantsByLifeCycle);
}
```

* **타입 안전성**: `EnumMap`은 `Enum` 타입만을 키로 허용하므로 잘못된 인덱스 사용 문제를 방지할 수 있다.
* **명확한 코드**: 배열의 인덱스를 사용하지 않으므로 의미가 명확하며, 유지보수하기 쉽다.
* **제네릭 타입**: `EnumMap`은 제네릭을 지원하므로 컴파일 경고 없이 사용할 수 있다.
* **효율성**: 내부적으로 배열을 사용해 빠른 성능을 제공한다.

## 3. 스트림과 `EnumMap`을 함께 사용하는 방식

`EnumMap`을 사용할 때 **스트림을 함께 사용**하면 코드가 더욱 간결진진다. 특히 `Collectors.groupingBy` 메서드를 사용하면 각 항목을 `EnumMap`에 그룹화할 수 있다.

### **1) 스트림과 `EnumMap`을 사용한 코드 예제**&#x20;

```java
// EnumMap<LifeCycle, Set<Plant>>을 생성하여 생애 주기별로 식물을 그룹화
        EnumMap<Plant.LifeCycle, Set<Plant>> lifeCycleSetEnumMap = 
            garden.stream() // garden 리스트의 요소들을 스트림으로 변환
                .collect(
                    groupingBy(
                        p -> p.lifeCycle, // 각 Plant 객체의 lifeCycle 속성을 기준으로 그룹화
                        () -> new EnumMap<>(Plant.LifeCycle.class), // EnumMap을 사용하여 그룹화된 결과를 저장
                        toSet() // lifeCycle이 같은 Plant 객체들을 Set으로 수집
                    )
                );

        // 생애 주기별로 그룹화된 결과 출력
        System.out.println("lifeCycleSetEnumMap = " + lifeCycleSetEnumMap);
```

1. **garden.stream()**:
   * `garden` 리스트를 스트림으로 변환하여 각 `Plant` 객체를 순회할 수 있도록 한다.
2. **collect(groupingBy(...))**:
   * `Collectors.groupingBy` 메서드를 사용하여 `Plant` 객체를 `lifeCycle` 속성값 기준으로 그룹화한다.
3. **p -> p.lifeCycle**:
   * 각 `Plant` 객체의 `lifeCycle` 속성을 기준으로 그룹화한다. `lifeCycle` 값이 동일한 `Plant` 객체들이 한 그룹으로 모인다.
4. **() -> new EnumMap<>(Plant.LifeCycle.class)**:
   * 결과를 저장할 맵의 구현체로 `EnumMap`을 사용한다. `EnumMap`은 `LifeCycle` Enum을 키로 가지는 맵을 생성하여, 그룹화된 데이터를 `EnumMap`에 저장하도록 한한다.
5. **toSet()**:
   * 같은 `lifeCycle`을 가진 `Plant` 객체들을 `Set`으로 수집하여 `EnumMap`의 값으로 저장한한다.
6. **System.out.println(...)**:
   * `lifeCycleSetEnumMap`을 출력하여, 각 `LifeCycle` Enum 값에 해당하는 `Plant` 객체들의 그룹을 확인

* 이 코드는 `EnumMap`을 사용하여 `Plant` 객체들을 생애 주기(`LifeCycle`)에 따라 그룹화하므로, 타입 안전성과 효율성을 모두 갖춘 코드이다.
* 스트림과 `EnumMap`을 조합하여 작성한 코드는 더 짧고 가독성이 뛰어나며, EnumMap이 제공하는 성능 이점을 유지할 수 있다.

## 4. 상태 전이 관리에 중첩된 `EnumMap` 사용하기

> `상태 전이(Transition)`를 Enum으로 관리할 때, 열거형 Enum 간의 전이 상태를 중첩된 `EnumMap`을 통해 관리할 수 있다.&#x20;

### **1) `ordinal()`을 이용한 2차원 배열 인덱스 예제**java코드 복사enum Phase {

```java
    SOLID, LIQUID, GAS;

    enum Transition {
        MELT, FREEZE, BOIL, CONDENSE, SUBLIME, DEPOSIT;

        // 2차원 배열을 사용하여 전이 관계를 정의
        private static final Transition[][] TRANSITIONS = {
                {null, MELT, SUBLIME}, // SOLID
                {FREEZE, null, BOIL},  // LIQUID
                {DEPOSIT, CONDENSE, null} // GAS
        };

        // Phase 상태 전이 메서드
        public static Transition from(Phase from, Phase to) {
            return TRANSITIONS[from.ordinal()][to.ordinal()];
        }
    }
}
```

* `Phase`는 `SOLID`, `LIQUID`, `GAS`라는 세 가지 상태를 정의한 열거형
* 각 상태 전이에 해당하는 `Transition` 값들을 `2차원 배열 TRANSITIONS`로 정의하고, `from`과 `to`의 `ordinal()` 값을 사용해 배열 인덱스로 전이 상태를 가져온다.
* 예를 들어, `Phase.SOLID`에서 `Phase.LIQUID`로 전이 시 `MELT` 상태가 반환된된다.

**문제점**

* **타입 안전성**: `ordinal()`은 정수 값을 반환하기 때문에 인덱스 오류 가능성이 있다.
* **유지보수성**: 배열의 인덱스와 `ordinal()`의 관계를 컴파일러가 인식하지 못하므로, `Phase`나 `Transition` 수정 시 `TRANSITIONS` 배열을 함께 수정해야 한다.
* **비효율성**: 테이블이 커질수록 `null`로 채워지는 공간이 증가하여 메모리 낭비가 발생할 수 있다.

### 2) EnumMap을 사용한 개선 예제

2차원 배열 대신 `EnumMap`을 사용하여 이러한 문제를 해결할 수 있다.

> 2중 중첩첩

```java
import java.util.*;
import java.util.stream.*;
import static java.util.stream.Collectors.*;

public enum Phase {
    SOLID, LIQUID, GAS, PLASMA;

    public enum Transition {
        MELT(SOLID, LIQUID),
        FREEZE(LIQUID, SOLID),
        BOIL(LIQUID, GAS),
        CONDENSE(GAS, LIQUID),
        SUBLIME(SOLID, GAS),
        DEPOSIT(GAS, SOLID),
        IONIZE(GAS, PLASMA),
        DEIONIZE(PLASMA, GAS);

        private final Phase from;
        private final Phase to;

        Transition(Phase from, Phase to) {
            this.from = from;
            this.to = to;
        }

        // EnumMap을 중첩하여 두 상태 간의 전이를 매핑
        private final static Map<Phase, Map<Phase, Transition>> map =
                Stream.of(values()).collect(
                        groupingBy(
                                t -> t.from,  // 'from' Phase를 기준으로 그룹화
                                () -> new EnumMap<>(Phase.class), // 외부 EnumMap 생성
                                toMap(t -> t.to, t -> t, 
                                      (x, y) -> y, 
                                      () -> new EnumMap<>(Phase.class)) // 내부 EnumMap 생성
                        )
                );

        public static Transition from(Phase from, Phase to) {
            return map.get(from).get(to);
        }
    }
}
```

**코드 설명**

1. **`Transition` Enum 정의**:
   * 각 전이의 시작 상태 (`from`)와 끝 상태 (`to`)를 나타냅니다. 예를 들어, `MELT`는 `SOLID`에서 `LIQUID`로의 전이를 의미한다.
2. **중첩 `EnumMap` 사용**:
   * `EnumMap`을 중첩하여 `from` 상태와 `to` 상태 간의 전이를 매핑합
   * `Stream.of(values())`와 `Collectors.groupingBy`를 사용하여 `from` 상태를 기준으로 그룹화하고, `toMap`으로 `to` 상태와 `Transition`을 매핑하여 중첩 `EnumMap`을 생성한다.
   * `(x, y) -> y`는 중복 키가 없기 때문에 병합 함수로 단순히 기존 값 반환을 설정한 것이다.
3. **전이 상태 가져오기**:
   * `from(Phase from, Phase to)` 메서드는 `from`과 `to` 상태 간의 전이를 반환한다. 예를 들어, `Phase.from(SOLID, LIQUID)`는 `MELT`를 반환한다.

**개선된 코드의 장점**

* **타입 안전성**: `ordinal()` 대신 Enum을 직접 사용하므로 타입 안전성을 확보할 수 있다.
* **유지보수성**: `Phase`나 `Transition`이 변경되더라도 `EnumMap`의 키로 관리되므로 코드 변경 범위가 줄어든다.
* **효율성**: `EnumMap`은 내부적으로 배열을 사용하여 빠르면서도 메모리를 절약할 수 있다.
* (x, y) -> y 부분은 원래 중복된 키에 값이 들어왔을 때 어떻게 합칠까를 관여하는 부분인데, 여기서는 중복된 키가 없으므로 쓰이지 않고 있다.

## 핵심 정리

* **비트 필드나 `ordinal()`을 사용하는 배열 기반 방식**은 오류 발생 가능성이 높고, 코드의 가독성과 안전성이 떨어진다. 즉, 배열의 인덱스를 위해 `ordinal()`을 쓰는 것은 일반적으로 좋지 않다.
* 대신 `EnumMap`을 사용하자. **`EnumMap`은 열거 타입을 키로 사용하여 데이터를 관리하는 최적의 방식**으로, 명확하고 성능이 우수하다.
* 다차원 관계는 `EnumMap<..., EnumMap<...>>`으로 표기하자.
* `ordinal()`은 웬만해선 쓰지 말자.

> 출처&#x20;
>
> * [https://jake-seo-dev.tistory.com/57](https://jake-seo-dev.tistory.com/57)
