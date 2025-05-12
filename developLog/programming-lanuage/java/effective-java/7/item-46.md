# item 46 : 스트림에서는 부작용 없는 함수를 사용하라

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## 1. 스트림의 핵심 :  스트림을 이해하기 어려울 수 있음

**스트림**은 <mark style="color:red;">그저 또 하나의 API가 아닌, 함수형 프로그래밍에 기초한 패러다임이기 때문</mark>이다. 스트림이 제공하는 표현력, 속도,  상황에 따라서는 병렬성을 얻으려면 API는 말할 것 도 없고 이 패러다임까지 함께 받아들여야 한다.

* 스트림 패러다임의 핵심은 **계산을 일련의 변환(transformation)으로 재구성 하는 부분**이다.
* 이때 각 변환 단계는 가능한 한 이전 단계의 결과를 받아 처 리 하는 순수 함수여야 한다.

### 함수형 프로그래밍이란?

{% hint style="info" %}
함수형 프로그래밍(functional programming)은 **자료 처리를 함수의 계산으로 취급하고**\
**상태와 가변 데이터를 멀리하는 프로그래밍 패러다임**
{% endhint %}

데이터 처리를 원시적인 계산 코드가 아닌 정형화된 함수로 처리하는 것이다. 사용하는 함수는 상태 값이나 가변 데이터를 멀리하도록 구현해야하는데 이러한 함수를 `순수 함수`라고 한다.

#### 순수함수란?

> **순수 함수**란 <mark style="color:red;">오직 입력만이 결과에 영향을 주는 함수</mark> 즉, 다른 가변 상태를 참조하지 않고, 함수 스스로도 다른 상태를 변경하지 않는 함수를 순수 함수라 한다.&#x20;

순수 함수는 가변 및 상태 값을 참조하지 않기에 입력 값에 의해 결과 값이 정해지는 특성을 갖는**다. 만약 상태 값을 참조하게 된다면 입력 값에 의해 결과 값이 달라지는 '부작용'이 발**생할 수 있다. 결국 이번 주제인 부작용 없는 함수는 순수 함수를 말한다.

순수 함수는 개발자가 커스텀하여 만들 수도 있지만, 스트림 API에서 제공하는 함수를 사용하는 것도 좋은 방법이다. 스트림에서 제공하는 공식적인 함수이므로 부작용이 없고, 성능 최적화가 되어있으며, 40가지 이상의 다양한 함수를 제공하기 때문이다.

먼저 스트림 API를 사용했지만, 저자는 스트림 코드라고 인정하지 않는 코드를 살펴보자.

## 2. 스트림 함수의 변화&#x20;

### 1) 단어 빈도표 예제 : 사용법만 아는 단계

다음은 텍스트 파일에서 단어별 수를 세어 빈도표로 만드는 코드이다. 스트림, 람다, 메서드 참조를 사용했고, 결과도 올바르지만 이를 스트림 코드라 하지 않는다. 스트림을 잘못 사용했고, 사용하지 않았을 때보다 가독성이 떨어지기 때문이다.

> **스트림 내부에서 외부 상태(`freq`)를 변경하는 작업을 수행**하고 있다는 점이다. 이는 스트림의 주요 설계 철학인 **함수형 프로그래밍**에 어긋난다. 스트림은 데이터의 변환(Transformation)과 평가(Evaluation)를 수행하는 데 초점이 맞춰져 있으며, 외부 상태를 변경하는 사이드 이펙트(Side-Effect)를 최소화하는 것이 좋다.

```java
@Test
public void wordFreq1Test() {
    List<String> words = Arrays.asList(
        "stop", "spot", "trim", "meet", 
        "ball", "free", "trim", "meet"
    );

    Map<String, Long> freq = new HashMap<>();

    // 문제 1: 외부 상태(freq)를 수정하는 스트림 사용
    words.stream().forEach(
            word -> freq.merge(word.toLowerCase(), 1L, Long::sum) 
            // 외부 상태(freq)를 변경 -> 스트림의 함수형 패러다임에 위배
    );

    System.out.println("words = " + words);
    System.out.println("freq = " + freq);
}
```

* 스트림의 forEach() 메서드를 이용한 코드이다.
* 배열을 처리해야 한다면 스트림으로 처리하겠다는 생각에서 출발했을 것이다.

그런데, 일반적으로 Stream 루프에서 외부 상태를 변경할 것이라곤 쉽게 생각하지 못한다. 그리고, Stream 루프 내부에서 Side-Effect 가 있는 내용을 작성하면 여러가지 문제가 발생한다.

문제는 **외부 상태인 빈도 수(freq)를 수정하는 람다 부분**이다. <mark style="color:red;">이 코드의 모든 데이터 처리 작업이 최종 연산(종단 연산)인 forEach 구문에서 일어나고 있는데, forEach는 스트림 계산 결과를 보고할 때만 사용하는 것이 권장된다.</mark> 연산 결과를 보여주는 일 이상을 하니 좋은 코드라 할 수 없다.

#### Stream 내부에서 사이드이펙트를 사용하면 발생하는 문제&#x20;

* 가독성: Stream 을 사용한 순간 데이터의 변환과 평가가 이뤄질 것이라 기대하는데 그런 코드가 아니어서 읽기 어렵다.
* 재사용성: 외부 상태에 의존해버리기 때문에 쉽게 재사용이 불가능해진다.
* 테스트 가능성: Stream 은 일반 로직과 다른 흐름을 가진다. 병렬로도 실행될 수 있는 것이라 테스트하기도 어려워진다.
* 동시성: 멀티 스레드 프로그래밍에서 흔히 발생하는 공유 가변 상태와 관련된 문제를 피하기 어려워진다.

### 2) 필요 없는 스트림 빼기

> 일반적인 **`for` 루프**를 사용하여 단어의 빈도를 계산하는 코드이다. 스트림의 사용 여부와 비교했을 때, **스트림을 억지로 사용하는 경우 가독성이나 효율성이 오히려 떨어질 수 있다.**

#### **스트림 사용 vs 일반 for 루프**

1. **스트림의 적절한 사용**
   * 스트림은 데이터 변환과 평가를 **함수형 스타일**로 간결하게 표현할 수 있다.
   * 그러나 스트림 내부에서 **외부 상태를 변경하는 작업**은 스트림의 원래 목적에 맞지 않으며, 코드의 명확성을 해친다.
   * 사실 많이 돌리지 않으면 for문과 성능적 차이가 별로 없다.
2. **일반 for 루프의 장점**
   * 일반적인 자바 개발자들에게 친숙하며, **명확하고 직관적**이다.
   * 데이터를 순회하며 외부 상태를 수정하는 작업에는 스트림보다 `for` 루프가 더 적합할 수 있다.
   * 스트림의 `forEach`를 사용한 코드보다 가독성과 유지보수성이 높은 경우가 많다.

#### **스트림을 억지로 사용하는 문제**

스트림의 `forEach`는 **데이터의 최종 결과를 보고할 때** 적합하지만, **계산 그 자체를 스트림 내부에서 처리하는 것은 적절하지 않을 수 있다**. 예를 들어:

**이전 스트림 코드 (잘못된 스트림 사용)**

```java
사words.stream().forEach(
    word -> freq.merge(word.toLowerCase(), 1L, Long::sum)
);
```

* 이 코드는 외부 상태(`freq`)를 수정하는 부작용(Side-Effect)이 발생한다.
* 스트림을 사용하는 이유(데이터 변환과 평가)가 약화되었으며, 단순히 `forEach`를 억지로 사용한 것에 불과하다.
* 일반 for 루프보다 가독성이 낮아질 수 있다.

#### **일반 for 루프 코드**

아래 코드는 스트림을 사용하지 않고 일반적인 `for` 루프를 사용하여 단어의 빈도를 계산하는 코드

```java
@Test
public void wordFreq1Test() {
    List<String> words = Arrays.asList(
        "stop", "spot", "trim", "meet",
        "ball", "free", "trim", "meet"
    );

    Map<String, Long> freq = new HashMap<>();

    // 일반적인 for 루프를 사용
    for (String word : words) {
        // 단어를 소문자로 변환하고 빈도수를 갱신
        freq.merge(word.toLowerCase(), 1L, Long::sum);
    }

    System.out.println("words = " + words); // 입력 단어 리스트 출력
    System.out.println("freq = " + freq);  // 단어 빈도 출력
}
```

* 외부 상태를 수정하는 작업에는 `for` 루프가 더 적합하다.
* 가독성과 유지보수성을 고려했을 때, 익숙한 코드 스타일을 사용하는 것이 바람직하다.

> 스트림의 forEach 코드는 println() 과 같은 메서드를 이용해 **계산의 결과를 보고할 때는 유용**하다.그러나 계산 그 자체에는 오히려 두번째 코드처럼 일반적인 자바의 for 문을 활용하는 것이 더 깔끔한 경우도 있다.

### 3) 단어 개수 세기 2 : 종단 연산 이용하기(`Collectors.groupingBy`를 사용한 코드)

```java
import static java.util.stream.Collectors.*;

@Test
public void wordFreqCollectorTest() {
    List<String> words = Arrays.asList(
        "stop", "spot", "trim", "meet", 
        "ball", "free", "trim", "meet"
    );

    // Collectors를 사용해 단어 빈도 계산
   // groupingBy: 단어를 소문자로 그룹화
  // counting: 각 그룹의 요소 개수 계산
    Map<String, Long> freq = words.stream()
                                  .collect(groupingBy(String::toLowerCase, counting()));

    System.out.println("freq = " + freq);
}
```

> `groupingBy`와 `counting`을 조합하여 **스트림 내부에서 직접 데이터 처리와 결과 생성**을 수행한다.

```
freq = {stop=1, spot=1, trim=2, meet=2, ball=1, free=1}
```

* **의도가 명확**: 단어를 소문자로 변환한 뒤 그룹화하여 각 그룹의 빈도를 계산하는 작업임을 코드에서 쉽게 이해할 수 있다.
* 외부 상태를 수정하지 않으므로 병렬 처리에서도 안전하다.
* 문자열을 toLowerCase() 로 변환하여 그룹핑하겠다는 의도가 보인다.
* 그룹핑된 key 에 대한 value 는 단어의 개수인 counting() 이 들어갈 것이다.
* groupingBy() 나 counting() 같은 메서드를 가독성 좋게 쓸 수 있는 이유는 **Collectors 의 멤버를 정적 임포트했기 때문**이다.

#### **Stream의 forEach와 수집기(Collector)의 차이점**

* `forEach`는 **스트림의 최종 연산** 중 하나로, 스트림의 각 요소를 소비하며 반복적으로 처리할 수 있다. 그러나 기능이 제한적이고, 스트림의 본래 의도인 **병렬 처리**와는 거리가 멀며, 결과 데이터를 생성하는 데 적합하지 않다.
* 스트림을 통해 **데이터를 처리하고 결과를 생성**하는 작업에는 `Collectors`를 사용하는 것이 더 적합하다. 특히, 데이터 변환이나 그룹핑 작업에서 `Collectors`는 강력한 도구를 제공한다.

#### **forEach의 한계**

1. **병렬 처리 어려움**:
   * `forEach`는 본질적으로 반복적이기 때문에 스트림의 병렬 처리를 효과적으로 활용하지 못한다.
   * 데이터를 **결과 컬렉션으로 수집하거나 그룹화하는 작업**에 적합하지 않다.
2. **의도 전달 부족**:
   * `forEach`는 스트림의 데이터를 반복적으로 처리하는 용도로 사용되지만, 그 자체로 데이터 변환이나 그룹화의 의도를 명확히 전달하지 못한다.
3. **가독성 저하**:
   * 스트림 연산의 중간 단계에서 데이터를 처리하고 결과를 생성하기 위해 외부 상태를 변경하는 방식은 가독성을 떨어뜨리고, 코드의 유지보수성을 저하시킨다.

#### **수집기(Collector)를 활용한 스트림 코드**

Java의 `java.util.stream.Collectors` 클래스는 스트림 데이터를 처리한 후 **결과 데이터를 생성**하는 데 필요한 다양한 메서드를 제공한다. 이를 통해 데이터를 그룹화하거나 집계하며, 명확하고 가독성 높은 코드를 작성할 수 있다.

> forEach 연산은 스트림 계산 결과를 보고할 때만 사용하고, 계산하는 데는 쓰지 말자. 수집기를 사용하면 스트림의 원소를 손쉽게 컬렉션으로 모을 수 있다. <mark style="color:red;">수집기는 총 세 가지로,</mark> <mark style="color:red;"></mark><mark style="color:red;">**toListO, toSetO, toCollection(collectionFactory)**</mark><mark style="color:red;">가 그 주인공이다.</mark>

### 4)  상위 2개의 단어 추출하기

```java
@Test
public void topTwoWordsTest() {
    // 단어 리스트 생성
    List<String> words = Arrays.asList("stop", "spot", "trim", "meet", "ball", "free", "trim", "trim", "meet");

    // 단어 빈도 수 계산
    Map<String, Long> freq = words.stream()
                                  .collect(groupingBy(String::toLowerCase, counting()));

    // 단어를 빈도 수 기준으로 내림차순 정렬 후 상위 2개만 추출
    List<String> topTwo = freq.keySet() // Map의 keySet을 스트림으로 변환
                              .stream()
                              .sorted(Comparator.comparing(freq::get).reversed()) // 빈도 수 기준 내림차순 정렬
                              .limit(2) // 상위 2개만 추출
                              .collect(Collectors.toList()); // 결과를 리스트로 수집

    // 결과 출력
    System.out.println("topTwo = " + topTwo); // 상위 2개의 단어 출력
}

```

```
topTwo = [trim, meet]
```

마지막 toList는 **Collectors의 메서드**다. 이처럼 Collectors의 멤버를 정적 임포트하여 쓰면 **스트림 파이프라인 가독성이 좋아져, 흔히들 이렇게 사용한다.**

* 이 코드에서 어려운 부분은 sorted에 넘긴 비교자, 즉 comparing(freq::get). reversed()뿐이다&#x20;
* `comparing 메서드`는 키 추출 함수를 받는 **비교자 생성 메서**드다. 한정적 메서드 참조이자, 여기서 키 추출 함수로 쓰인 freq::get은 입력받은 단어(키)를 빈도표에서 찾아(추출) 그 빈도를 반환한다.&#x20;

## 3. Collectors API와 활용 정리

### **1) Collectors의 주요 메서드**

Collectors는 스트림 데이터를 특정 컬렉션, 맵, 문자열 등으로 변환하거나, 데이터를 그룹화하거나, 집계 작업을 수행하는 데 사용된다.

#### **수집기 메서드**

| 메서드                | 설명                                       | 예시                                                |
| ------------------ | ---------------------------------------- | ------------------------------------------------- |
| `toList()`         | 스트림의 데이터를 리스트로 수집.                       | `List<String> result = stream.collect(toList());` |
| `toSet()`          | 데이터를 집합(Set)으로 수집.                       | `Set<String> result = stream.collect(toSet());`   |
| `toMap()`          | 키와 값을 매핑하여 맵 생성. 키 충돌 시 병합 로직을 지정할 수 있음. | `toMap(keyMapper, valueMapper, mergeFunction)`    |
| `groupingBy()`     | 데이터를 그룹화하여 맵 생성.                         | `groupingBy(String::toLowerCase, counting())`     |
| `partitioningBy()` | 프레디케이트를 기준으로 데이터를 true/false 두 그룹으로 나눔.  | `partitioningBy(word -> word.equals("stop"))`     |
| `joining()`        | 문자열 스트림을 연결. 구분자, 접두사, 접미사 지정 가능.        | `joining(", ", "맛있는 ", "이 좋아")`                   |

***

#### **집계 메서드**

| 메서드                | 설명                               | 예시                                           |
| ------------------ | -------------------------------- | -------------------------------------------- |
| `counting()`       | 요소의 개수를 셈.                       | `collect(counting())`                        |
| `summingInt()`     | 요소의 합계를 구함.                      | `collect(summingInt(Student::getScore))`     |
| `averagingInt()`   | 요소의 평균을 구함.                      | `collect(averagingInt(Student::getScore))`   |
| `summarizingInt()` | 합계, 평균, 최소, 최대, 개수를 한 번에 통계로 구함. | `collect(summarizingInt(Student::getScore))` |

### **2) Operation 열거 타입을 이용한 연산자 매핑**

> symbol별 연산자 기호 매핑하기: 인자 2개짜리 toMap()

```java
enum Operation {
    PLUS("+", "plus", (x, y) -> x + y),
    MINUS("-", "minus", (x, y) -> x - y),
    TIMES("*", "times", (x, y) -> x * y),
    DIVIDE("/", "divide", (x, y) -> x / y);

    private final String symbol; // 연산자 기호
    private final String english; // 연산자의 영어 표현
    private final BinaryOperator<Double> op; // 연산자 로직

    // 생성자
    Operation(String symbol, String english, BinaryOperator<Double> op) {
        this.symbol = symbol;
        this.english = english;
        this.op = op;
    }

    @Override
    public String toString() {
        return symbol; // 연산자 기호를 반환
    }

    public String toEnglish() { return english; } // 연산자의 영어 표현 반환

    public double apply(double x, double y) {
        return op.apply(x, y); // 연산자 로직 실행
    }
}

@Test
public void stringToEnumTest() {
    // Operation 열거형을 영어 표현을 키, Operation 자체를 값으로 매핑
    Map<String, Operation> operationMap = Stream.of(Operation.values())
                                                .collect(toMap(e -> e.english, e -> e));

    System.out.println("operationMap = " + operationMap);
    // 결과: {minus=-, times=*, divide=/, plus=+}
}
```

* `toMap`을 사용하여 `english`를 키로, 열거형 인스턴스를 값으로 매핑.
* `toMap(keyMapper, valueMapper)` 형태로 사용되며, 키와 값을 각각 어떻게 매핑할지 정의.
* **결과**: `{minus=-, times=*, divide=/, plus=+}`

***

### **3) 아티스트의 베스트 앨범 찾기**

> List 데이터 Map\<String, Album>으로 merge()하기: 인자 3개짜리 toMap()

```java
class Album {
    private final String artist;
    private final String name;
    private final int sales;

    public Album(String artist, String name, int sales) {
        this.artist = artist;
        this.name = name;
        this.sales = sales;
    }

    public String artist() { return artist; }
    public int sales() { return sales; }

    @Override
    public String toString() {
        return "Album{name='" + name + "', sales=" + sales + "}";
    }
}

@Test
public void bestAlbumPerArtist() {
    // 앨범 데이터 생성
    List<Album> albums = Arrays.asList(
        new Album("Jake", "제이크 1집", 100),
        new Album("Jake", "제이크 2집", 250),
        new Album("Jack", "잭 1집", 990),
        new Album("Jack", "잭 2집", 140)
    );

    // 아티스트별 베스트 앨범 찾기
    Map<String, Album> topHits = albums.stream()
                                       .collect(toMap(
                                           Album::artist, // Key: 아티스트 이름
                                           album -> album, // Value: 앨범 객체
                                           BinaryOperator.maxBy(Comparator.comparing(Album::sales)) // 병합: 최고 판매량 기준
                                       ));

    System.out.println("topHits = " + topHits);
    // 출력: {Jake=Album{name='제이크 2집', sales=250}, Jack=Album{name='잭 1집', sales=990}}
}
```

* `toMap(keyMapper, valueMapper, mergeFunction)` 형태를 사용.
* 병합 함수(`mergeFunction`)로 `BinaryOperator.maxBy`를 사용해 각 아티스트의 판매량이 가장 많은 앨범을 선택.
* **결과**: `{Jake=Album{name='제이크 2집', sales=250}, Jack=Album{name='잭 1집', sales=990}}`

### **4) 기타 활용 사례**

#### **단어 리스트를 알파벳 순서로 그룹화**

```java
@Test
public void wordFreqGroupingTest() {
    List<String> words = Arrays.asList("stop", "spot", "trim", "meet", "ball", "free", "triM", "TriM", "meet");

    // 단어를 알파벳 순으로 정렬한 결과를 Key로 사용하여 그룹화
    Map<String, List<String>> alphabetizedMap = words.stream()
                                                     .collect(groupingBy(this::alphabetize));

    System.out.println("alphabetizedMap = " + alphabetizedMap);
    // 출력: {eefr=[free], opst=[stop, spot], imrt=[trim], ...}
}

private String alphabetize(String s) {
    char[] a = s.toCharArray();
    Arrays.sort(a); // 단어의 문자를 정렬
    return new String(a); // 정렬된 문자열 반환
}
```

* `groupingBy`를 사용하여 알파벳 순으로 정렬된 단어를 키로 그룹화.
* **결과**: `{eefr=[free], opst=[stop, spot], imrt=[trim], eemt=[meet, meet], ...}`

#### **학생 점수 통계**

```java
@Test
public void studentScoreStatistics() {
    List<Student> students = Arrays.asList(
        new Student("Jake", 15),
        new Student("Jackson", 55),
        new Student("Joe", 85),
        new Student("Amy", 21),
        new Student("Roy", 33)
    );

    // 학생 점수 통계
    IntSummaryStatistics stats = students.stream()
                                         .collect(summarizingInt(Student::getScore));

    System.out.println(stats);
    // 출력: IntSummaryStatistics{count=5, sum=209, min=15, average=41.800000, max=85}
}
```

* `summarizingInt`를 사용하여 학생 점수의 통계 정보를 한 번에 수집.
* **결과**: `count`, `sum`, `min`, `average`, `max`가 포함된 `IntSummaryStatistics` 반환.

#### **문자열 연결**

```java
@Test
public void joiningTest() {
    List<String> food = Arrays.asList("피자", "햄버거", "치킨");

    // 문자열 연결: 구분자, 접두사, 접미사 지정
    String result = food.stream()
                        .collect(joining(", ", "맛있는 ", "이 좋아"));

    System.out.println(result); // 출력: 맛있는 피자, 햄버거, 치킨이 좋아
}
```

* `joining(delimiter, prefix, suffix)`로 문자열 연결.
* **결과**: `"맛있는 피자, 햄버거, 치킨이 좋아"`

### **5) Collectors의 특징과 활용 가이드**

#### **Collectors의 장점**

1. **가독성**:
   * `groupingBy`, `toMap` 등은 데이터 처리 의도를 코드에서 명확히 드러낸다.
2. **효율성**:
   * 스트림을 병렬로 처리할 수 있으며, 데이터를 한 번 순회하면서 필요한 결과를 생성한다.
3. **유연성**:
   * 다양한 수집기 조합을 통해 복잡한 데이터 처리도 간결하게 표현할 수 있다.

#### **Collectors 사용 가이드**

1. **`toMap` 사용 시 주의점**:
   * 키가 중복될 경우 `IllegalStateException`이 발생하므로, 병합 함수(`mergeFunction`)를 제공해야한다.
2. **`groupingBy`와 `partitioningBy` 차이**:
   * `groupingBy`는 여러 카테고리로 그룹화.
   * `partitioningBy`는 true/false 두 그룹으로 분류.
3. **`joining` 활용**:
   * 구분자, 접두사, 접미사를 지정하여 문자열을 쉽게 연결 가능.
4. **병렬 처리**:
   * `groupingByConcurrent`, `toConcurrentMap`을 사용하면 병렬 스트림 결과를 `ConcurrentHashMap`에 저장할 수 있다.

### **6) 이펙티브 자바와 Collectors**

**forEach의 한계**

* `forEach`는 결과를 출력하거나, 보고 목적으로만 사용하는 것이 좋다.
* 데이터 변환이나 집계 작업은 `Collectors`를 활용해야 가독성과 효율성을 높일 수 있다.

**스트림 활용 원칙**

1. **의도를 명확히 표현**:
   * 데이터를 변환하거나 집계할 때 스트림의 각 단계가 수행하는 작업을 명확히 보여야 한다.
2. **함수형 스타일 유지**:
   * 외부 상태를 수정하는 사이드 이펙트를 피하고, 스트림 내부에서 작업을 완료하도록 설계한다.



> `Collectors`는 스트림 API와 결합하여 데이터를 수집, 그룹화, 집계하는 데 있어 필수적인 도구이다. 이를 효과적으로 사용하려면 다양한 수집기 메서드를 이해하고 적재적소에 활용해야 한다. **스트림의 본질**은 데이터를 변환하고 결과를 생성하는 것이며, 이를 통해 명확하고 가독성 높은 코드를 작성할 수 있다.

## 핵심 정리

* 스트림 파이프라인 프로그래밍의 핵심은 **부작용 없는 함수 객체**에 있다.&#x20;
* 스트림뿐 아니라 스트림 관련 객체에 건네지는 모든 함수 객체가 부작용이 없어야 한다.&#x20;
* <mark style="color:red;">종단 연산 중 forEach는 스트림이 수행한 계산 결과를 보고할 때만 이용해야 한다. 계산 자체에는 이용하지 말자.</mark>&#x20;
* 스트림을 올바로 사용하려면 수집기를 잘 알아둬야 한다. 가장 중요한 수집기 팩터리는 toList, toSet, toHap, groupingBy, joining이다



> 출처 및 참고
>
> * [https://tlatmsrud.tistory.com/154](https://tlatmsrud.tistory.com/154)
> * [https://jake-seo-dev.tistory.com/457](https://jake-seo-dev.tistory.com/457)

