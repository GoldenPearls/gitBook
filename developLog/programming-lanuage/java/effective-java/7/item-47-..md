# item 47 : 반환타입으로는 스트림보다는 컬렉션이 낫다.

## 1. 스트림 도입 이전과 이후의 차이

### **1) 스트림 도입 이전**

스트림이 도입되기 이전에 원소 시퀀스, 즉 일련의 원소를 반환하는 메서드의 반환 타입으로 Collection, Set, List과 같은 컬렉션 인터페이스나 Iterable 또는 배열을 사용했다.

#### **일반적인 반환 타입**

* **`Collection`, `Set`, `List`**:
  * 가장 많이 사용되는 기본 인터페이스.
  * 반복(iteration)과 다양한 유틸리티 메서드(`size`, `contains`, `add`, `remove` 등)를 제공.
* **`Iterable`**:
  * 단순 반복을 위한 인터페이스.
  * 컬렉션보다 간단한 인터페이스로, for-each 루프에서 사용 가능.
* **배열(Array)**:
  * 기본 타입을 다루거나 성능이 중요한 경우 사용.
  * 크기가 고정되고, 컬렉션 인터페이스의 유연성을 제공하지 않음.

#### **선택 기준**

1. **반복과 컬렉션 메서드 제공 여부**:
   * 컬렉션 인터페이스가 적합: 대부분의 경우, 컬렉션을 사용.
   * 단순 반복만 필요한 경우: `Iterable` 사용.
2. **성능 민감도**:
   * 배열을 사용하여 오버헤드를 줄임.

### **2) 스트림 도입 이후 : `Stream`을 `Iterable`로 변환 문제점**

> Java 8에서는 스트림(Stream)이 등장하며, 원소 시퀀스를 반환할 때 선택지가 복잡해졌다.

스트림은 **반복(iteration)**을 지원하지 않는다. 따라서 원소 시퀀스를 반환할때 스트림을 사용하면 아래와 같이 `for-each` 로 반복을 수행할 수 없다.

```java
for (ProcessHandle ph : ProcessHandle.allProcesses()::iterator) {
// 프로세스를 처리한다.
}
}
```

`for-each` 와 같이 향상된 for 문이 가능한 컬렉션은 `Iterable` 인터페이스를 구현하고 있어야 하기 때문이다. `Stream` 인터페이스는 `Iterable` 인터페이스가 정의한 추상 메서드를 포함하고 정의한 방식대로 동작하지만, **확장(extend)** 하지 않았기 때문에 반복이 불가능하다.

\
하지만 `ProcessHandle.allProcesses()`는 스트림(`Stream<ProcessHandle>`)을 반환하므로, 이를 `for-each`에서 바로 사용할 수 없다.

**해결 방법**:

1. **`::iterator`를 사용**: 스트림을 `Iterable`로 변환하여 `for-each` 사용.
2. **`collect()`로 컬렉션으로 변환**: `Stream`을 명시적으로 `List` 또는 `Set`으로 변환하여 처리.

**기존 방법: `::iterator`로 Iterable 변환**

```java
@Test
public void processHandleTestUsingIterator() {
    // Stream을 Iterable로 변환
    Iterable<ProcessHandle> processHandles = ProcessHandle.allProcesses()::iterator;

    // for-each 사용
    for (ProcessHandle processHandle : processHandles) {
        System.out.println("Process Info: " + processHandle.info());
    }
}
```

1. **타입 추론이 불편**:
   * IDE가 기본적으로 타입을 정확히 추론하지 못하며, 수동 캐스팅이 필요할 수 있음.
   * `Runnable`, `Executable` 등 다른 타입을 추천하는 경우도 발생.
2. **가독성 저하**:
   * `::iterator` 방식은 직관적이지 않으며, 코드를 읽는 사람에게 혼란을 줄 수 있음.

```java
public interface Stream<T> extends BaseStream<T, Stream<T>> {
} 
```

그렇다면 스트림을 반복할 수 있게 하려면 어떻게 해야 할까?

## 2. 스트림 반복을 위한 해결법

### **1) 어댑터 메서드를 이용한 Stream과 Iterable 간 변환**

Java에서 스트림과 Iterable 타입을 필요에 따라 변환할 수 있도록 **어댑터 메서드**를 제공하여 유연하게 사용할 수 있다. 이는 데이터를 처리하거나 반복하려는 상황에서 코드의 활용성을 높여준다.

#### **어댑터 메서드 구현**

```java
// Stream -> Iterable 변환 메서드
public static <E> Iterable<E> iterableOf(Stream<E> stream) {
    // 스트림의 iterator() 메서드를 이용해 Iterable 반환
    return stream::iterator;
}

// Iterable -> Stream 변환 메서드
public static <E> Stream<E> streamOf(Iterable<E> iterable) {
    // Iterable을 Stream으로 변환 (Spliterator 사용)
    return StreamSupport.stream(iterable.spliterator(), false);
}
```

* `iterableOf()`와 `streamOf()`라는 두가지 어댑터 메서드로 서로의 타입을 쉽게 오갈 수 있게 만들어 문제를 해결할 수도 있다.
* Collection 인터페이스는 <mark style="color:red;">stream() 과 Iterable 구현 모두 하기 때문에</mark> 기왕이면 **Collection 이나 그 하위 타입을 반환 혹은 파라미터 타입에 사용하는 게 최선**이다.
* 단지 컬렉션을 반환한다는 이유로 [덩치 큰 시퀀스](#user-content-fn-1)[^1]를 메모리에 올려서는 안된다.

> 시퀀스가 크지만, 표현 방식이 간단해질 수 있다면, 전용 컬렉션을 구현해보자.

#### **어댑터 메서드 활용 예제 :** 어댑터 메서드를 통한 Stream -> Iterable -> Stream 변환 예제

```java
@Test
void streamIterableTest() {
    // ProcessHandle 스트림 생성
    Stream<ProcessHandle> handleStream = ProcessHandle.allProcesses();

    // 스트림을 이용한 프로세스 정보 출력
    handleStream.forEach(p -> System.out.println(p.info()));

    // Stream -> Iterable 변환 후 for-each 문 사용
    Iterable<ProcessHandle> handles = iterableOf(handleStream);
    for (ProcessHandle handle : handles) {
        System.out.println(handle.info());
    }

    // Iterable -> Stream 변환 후 다시 스트림 처리
    Stream<ProcessHandle> stream = streamOf(handles);
    stream.forEach(p -> System.out.println(p.info()));
}
```

* **스트림 활용이 간단한 경우**: 스트림을 사용하고 직접 `forEach()`를 이용해 데이터를 처리하자.
* **Iterable 타입을 반드시 사용해야 하는 경우**: 어댑터 메서드를 이용하여 스트림을 Iterable로 변환하거나 컬렉션으로 수집(`collect()`)하는 방법을 고려하자.

### **2) 컬렉션이 더 적합한 경우: 멱집합 구하기**

`멱집합(Power Set)`은 <mark style="color:blue;">모든 부분 집합을 원소로 가지는 집합</mark>을 말한다. 이 경우 모든 데이터를 메모리에 올리기보다는 **필요한 시점에 데이터를 제공하는 것이 더 효율적이다.**

#### **멱집합을 위한 전용 컬렉션 구현**

> 입력 집합의 원소 수가 30을 넘으면 PowerSet.of가 예외를 던진다. 이는 Stream이나 Iterable이 아닌 Collection을 반환 타입으로 쓸 때의 단점을 잘 보여준다. 다시 말해, Collection의 size 메서드가 int 값을 반환하므로 PowerSet.of가 반환되는 시퀀스의 최대 길이는 **Integer.MAX\_VALUE 혹은 2^n-1로 제한**된다. Collection 명세에 따르면 컬렉션이 더 크거나 심지어 무한대일 때 size가 2^n-1을 반환해도 되지만 완전히 만족 스러운 해법은 아니다.

```java
static class PowerSet {
    public static final <E> Collection<Set<E>> of(Set<E> s) {
        List<E> src = new ArrayList<>(s);
        int numberOfMaximumElements = 30; // 원소 개수 제한 (최대 30)

        if (src.size() > numberOfMaximumElements) {
            throw new IllegalArgumentException("집합에 원소가 너무 많습니다. (최대 " + numberOfMaximumElements + " 개)");
        }

        // AbstractList를 이용해 필요한 부분집합을 동적으로 생성
        return new AbstractList<>() {
            @Override
            public Set<E> get(int index) {
                Set<E> result = new HashSet<>();
                for (int i = 0; index != 0; i++, index >>= 1) {
                    if ((index & 1) == 1) {
                        result.add(src.get(i));
                    }
                }
                return result;
            }

            @Override
            public int size() {
                // 멱집합의 크기 = 2^원소의 수
                return 1 << src.size();
            }

            @Override
            public boolean contains(Object o) {
                return o instanceof Set && src.containsAll((Set<?>) o);
            }
        };
    
```

* 멱집합을 구해야 하는 경우엔 **굳이 항상 모든 컬렉션 요소를 메모리상에 올리고 있을 필요는 없다.**
* `get() 메서드`를 통해 필요한 시점에 필요한 엘리먼트를 얻으면 된다.
* 모든 요소를 가지고 있기엔 2^length만큼의 공간을 확보해야 하는 부담이 있다.
* 모든 요소를 가지고 있으면 매번 변경사항이 생길 때마다 멱집합을 새로 구해야 한다.
* AbstractCollection 은 contains() 와 size() 만 구현해주면 구현 조건이 충족된다.
* 이 경우가 Collection 을 반환하기 적당한 형태다.
  * Stream 은 size 를 구할 수 없기 때문에 이 경우에 적합하지 않다.

{% hint style="info" %}
추천사항
{% endhint %}

* **큰 시퀀스를 처리할 때**: 모든 데이터를 메모리에 올리기보다 **필요한 시점에 동적으로 생성**하도록 설계하자.
* **컬렉션을 사용하는 이유**: 멱집합과 같이 **데이터 재사용**이 중요하거나, 반복(iteration)이 많이 발생하는 경우에 적합하다.

{% hint style="danger" %}
Stream에 `size()` 또는 `length`가 없는 이유
{% endhint %}

> 게으른 평가로 인해 **전체 데이터의 크기를 알 수 없기 때문**

1. **게으른 평가로 인해 정확한 크기 예측 불가**:
   * 스트림은 데이터를 필요할 때만 **게으르게 평가**한다.
   * 평가가 이루어지기 전까지, 데이터가 몇 개의 원소를 포함할지 알 수 없기 때문에, 정확한 크기를 반환하는 `size()`와 같은 메서드를 제공할 수 없다.
2. **데이터 변환의 불확실성**:
   * 스트림은 **데이터 변환**을 여러 단계에 걸쳐 수행할 수 있다.
   * 변환 과정에서 필터링되거나 추가되는 데이터가 있을 수 있기 때문에, 최종 크기를 예측하기 어렵다.
3. **대신 `count()` 메서드 제공**:
   * 스트림은 `size()` 대신 **`count()`** 메서드를 제공한다.
   * `count()` 메서드는 스트림의 원소 개수를 계산하여 반환하지만, **스트림을 소모**한다. 즉, 데이터를 한 번 소비하여 개수를 계산하기 때문에 이후에는 해당 스트림을 다시 사용할 수 없다.

#### **`count()` 사용 예제**

```java
Stream<String> names = Stream.of("Alice", "Bob", "Charlie");
long count = names.count();  // 스트림의 원소 개수 반환 (3)
```

* `count()`를 사용하여 **스트림의 개수를 계산**할 수 있지만, 이 과정에서 스트림이 **소모**되기 때문에 이후에 동일한 스트림을 다시 사용할 수 없다.

### **3) 스트림이 더 적합한 경우: 부분 리스트 생성**

리스트의 부분 리스트를 생성하고 처리하는 작업은 스트림을 사용하면 간결하고 가독성이 좋다.

#### **부분 리스트 스트림 생성**

```java
static class SubLists {
    // 주어진 리스트의 모든 접두사와 접미사를 스트림으로 반환
    public static <E> Stream<List<E>> of(List<E> list) {
        Stream<List<E>> prefixes = prefixes(list);
        Stream<List<E>> suffixes = suffixes(list);

        return Stream.concat(prefixes, suffixes);
    }

    // 리스트의 모든 접두사 반환
    private static <E> Stream<List<E>> prefixes(List<E> list) {
        return IntStream.rangeClosed(1, list.size())
                .mapToObj(end -> list.subList(0, end));
    }

    // 리스트의 모든 접미사 반환
    private static <E> Stream<List<E>> suffixes(List<E> list) {
        return IntStream.range(0, list.size())
                .mapToObj(start -> list.subList(start, list.size()));
    }
}
```

#### **부분 리스트 스트림 활용 예제**

```java
@Test
void subListStreamTest() {
    List<String> list = Arrays.asList("A", "B", "C", "D");

    // 부분 리스트 생성 및 처리
    SubLists.of(list).forEach(subList -> System.out.println("SubList: " + subList));
}
```

* **데이터 변환이나 필터링**이 주요한 경우, **스트림을 사용**하여 데이터를 처리
* **부분 리스트**와 같은 반복이 자연스러운 경우 스트림이 **가독성과 성능** 모두에서 더 유리다.

### **4) 추가 코드 예제와 주석**

#### **스트림을 이용한 간단한 필터링 및 변환**

```java
@Test
void streamFilteringTest() {
    List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");

    // 이름이 'A'로 시작하는 이름만 필터링하고 모두 대문자로 변환
    names.stream()
         .filter(name -> name.startsWith("A"))    // 'A'로 시작하는 이름 필터링
         .map(String::toUpperCase)                // 대문자로 변환
         .forEach(System.out::println);           // 출력

    // 출력 결과: ALICE
}
```

#### **컬렉션을 사용한 재사용 가능한 데이터 처리**

```java
@Test
void collectionReuseTest() {
    List<String> items = Arrays.asList("apple", "banana", "cherry");

    // 첫 번째 반복: 각 아이템을 출력
    for (String item : items) {
        System.out.println("Item: " + item);
    }

    // 두 번째 반복: 각 아이템의 길이 출력
    for (String item : items) {
        System.out.println("Item length: " + item.length());
    }
}
```

* **컬렉션 사용 이유**: 데이터를 **여러 번 반복하거나 재사용**해야 할 때 컬렉션이 더 적합하다.
* 스트림은 일회성 사용이 기본이기 때문에 반복 사용이 필요한 경우 컬렉션이 더 효율적이다.

## 📚 **핵심 정리**

{% hint style="success" %}
Stream 은 **반환 타입으로 사용하기보다는 단순히 컬렉션 처리를 위해 사용하는 것이 좋다.** 반환은 다시 컬렉션으로 변경해주는 것이 활용성이 좋다. `someStream.collect(Collectors.toList())` 와 같은 함수를 이용하면 쉽다.
{% endhint %}

반환 전부터 이미 원소들을 컬렉션에 담아 관리하고 있거나 원소 개수가 적다면 `ArrayList` 과 같은 표준 컬렉션에 담아 반환하자. 그렇지 않다면, 전용 컬렉션을 구현할 수도 있다. 컬렉션을 반환하는게 불가능하다면 스트림과 `Iterable` 중 더 자연스러운 것을 반환하면 된다.

> 컬렉션은 **반복**과 **재사용**에 적합하고, 스트림은 **변환**과 **처리**에 더 강력

* 스트림은 나름대로의 장단점이 있어서, 경우에 맞게 사용하는 것이 좋다.
* 가장 큰 장점이자 단점이 `지연 평가`가 된다는 것이다.
* 기본적으로는 컬렉션을 반환하는 게 유연하다. 가능한 경우 **컬렉션을 반환**하여 `stream()`과 `for-each`를 모두 지원하도록 하고, 데이터가 크거나 일회성이라면 스트림을 반환하는 것이 좋다.

{% hint style="success" %}
**컬렉션 사용 추천 시점**:
{% endhint %}

* 데이터가 **여러 번 반복**되거나 **재사용**이 필요할 때.
* 데이터의 **전체 크기를 파악**해야 할 때.

{% hint style="success" %}
**스트림 사용 추천 시점**:
{% endhint %}

* **데이터 변환** 및 **필터링**이 주요 작업일 때.
* **지연 실행**을 통해 효율적으로 데이터를 처리해야 할 때.

## 📚 참고 : 스트림에서 평가란?

### **1) 스트림에서 평가(Evaluation)란?**

평가(Evaluation)는 **스트림이 연산을 끝내고 결과를 반환하는 과정**을 의미한다.\
스트림은 <mark style="color:red;">지연 실행(Lazy Evaluation)을 통해 필요한 데이터만 처리</mark>하며, 최종 연산(Terminal Operation)이 호출되기 전까지는 데이터 처리를 수행하지 않는다. 평가 단계에서 스트림은 더 이상 스트림 타입(Stream)이 아니며, 결과를 특정 자바 객체로 반환한다.

#### **평가의 의미**

평가는 **스트림 연산이 완료되어 결과가 반환되는 상태**를 말한다.\
다음과 같은 최종 연산(Terminal Operation)을 통해 평가가 이루어진다:

1. **데이터를 컬렉션, 배열 등으로 변환**:
   * 예: `toArray()`, `collect()`
2. **연산 결과를 하나의 값으로 축소**:
   * 예: `reduce()`
3. **데이터를 소비하거나 출력**:
   * 예: `forEach()`
4. **단일 조건을 평가**:
   * 예: `findFirst()`, `anyMatch()`, `allMatch()`

### **2) 평가의 특징**

**스트림은 평가 전까지 실행되지 않는다 (지연 실행)**

* `중간 연산(Intermediate Operation)`은 **데이터를 변환하는 작업을 정의**하지만, <mark style="color:red;">실제로 데이터를 처리하지는 않는다.</mark>
* 스트림은 최종 연산(Terminal Operation)이 호출될 때만 실행된다.

**예제: 지연 실행**

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// 중간 연산: 실행되지 않음
Stream<String> stream = names.stream()
                             .filter(name -> {
                                 System.out.println("Filtering: " + name);
                                 return name.startsWith("A");
                             });

// 최종 연산: 여기서 평가가 발생
List<String> result = stream.collect(Collectors.toList());
// 출력:
// Filtering: Alice
// Filtering: Bob
// Filtering: Charlie
```

* `filter`는 중간 연산으로 정의만 되었으며, 실제로 데이터는 처리되지 않는다.
* `collect`가 호출되면서 스트림이 평가되고 결과가 반환된다.

**단락 조건 (Short-Circuiting Condition)**

* 일부 최종 연산은 모든 데이터를 처리하지 않고도 결과를 반환할 수 있다.
* **단락 조건**은 데이터 처리가 완료될 수 있는 조건을 말한다.

**단락 조건이 적용되는 연산**

<table><thead><tr><th>연산</th><th width="248">설명</th><th>예시</th></tr></thead><tbody><tr><td><code>findFirst()</code></td><td>첫 번째 요소를 찾으면 스트림 처리가 종료됩니다.</td><td><code>stream.findFirst()</code></td></tr><tr><td><code>anyMatch()</code></td><td>조건을 만족하는 요소를 하나라도 찾으면 처리 종료.</td><td><code>stream.anyMatch(x -> x > 10)</code></td></tr><tr><td><code>allMatch()</code></td><td>조건이 모든 요소에 만족하지 않을 경우 즉시 처리 종료.</td><td><code>stream.allMatch(x -> x > 0)</code></td></tr><tr><td><code>noneMatch()</code></td><td>조건을 만족하는 요소가 하나도 없을 경우 처리 종료.</td><td><code>stream.noneMatch(x -> x &#x3C; 0)</code></td></tr><tr><td><code>limit(n)</code></td><td>최대 <code>n</code>개의 요소만 처리하고 평가를 종료.</td><td><code>stream.limit(5).collect(Collectors.toList())</code></td></tr></tbody></table>

**단락 조건 예제**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

// 단락 조건: 첫 번째 요소 발견 시 처리 종료
Optional<Integer> first = numbers.stream()
                                 .filter(n -> n > 3)
                                 .findFirst();
System.out.println(first.get()); // 출력: 4
```

* `findFirst`는 조건에 맞는 첫 번째 요소를 찾으면 스트림 처리를 종료한다.

#### **3. 평가가 발생하는 최종 연산**

최종 연산은 스트림을 평가하며, 결과를 반환하거나 데이터 소비를 완료합니다.

**3-1. 컬렉션으로 변환**

* **`toList()`**: 스트림 데이터를 `List`로 변환.
* **`toSet()`**: 스트림 데이터를 `Set`으로 변환.
* **`toMap()`**: 키-값 매핑을 기반으로 `Map`으로 변환.

**예제**

```java
List<String> result = Stream.of("a", "b", "c").collect(Collectors.toList());
System.out.println(result); // 출력: [a, b, c]
```

**3-2. 축소 (Reduction)**

* 스트림 데이터를 하나의 값으로 축소.
* 예: `reduce()`, `count()`, `max()`, `min()`.

**예제**

```java
int sum = Stream.of(1, 2, 3, 4, 5)
                .reduce(0, Integer::sum); // 초기값 0, 합산 연산
System.out.println(sum); // 출력: 15
```

**3-3. 조건 검사**

* 데이터가 특정 조건을 만족하는지 검사.
* 예: `anyMatch()`, `allMatch()`, `noneMatch()`.

**예제**

```java
boolean hasNegative = Stream.of(1, 2, -3, 4, 5)
                            .anyMatch(n -> n < 0);
System.out.println(hasNegative); // 출력: true
```

**3-4. 데이터 소비**

* 데이터를 순회하며 소비.
* 예: `forEach()`, `forEachOrdered()`.

**예제**

```java
Stream.of("a", "b", "c").forEach(System.out::println);
// 출력:
// a
// b
// c
```

#### **4. 평가와 병렬 스트림**

스트림은 기본적으로 **순차 처리**를 수행하지만, **병렬 스트림**을 사용하면 여러 데이터 요소를 병렬로 처리할 수 있습니다. 평가 단계에서도 병렬 처리가 가능합니다.

**병렬 스트림 평가 예제**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

// 병렬 스트림으로 sum 계산
int sum = numbers.parallelStream()
                 .reduce(0, Integer::sum);
System.out.println(sum); // 출력: 21
```

* 병렬 스트림은 데이터 요소를 병렬로 처리하며, 최종 결과를 결합합니다.
* 평가 과정에서도 병렬 처리가 가능하므로 대용량 데이터 처리에 유리합니다.

#### **5. 요약**

* **평가(Evaluation)**: 스트림 데이터가 최종 연산을 통해 특정 자바 객체로 변환되는 과정.
* **평가 발생 조건**: 최종 연산이 호출될 때만 평가가 이루어짐.
* **단락 조건**: 특정 조건에 따라 스트림 처리가 중단될 수 있음 (`findFirst`, `anyMatch` 등).
* **지연 실행**: 스트림은 평가 전까지 실행되지 않으며, 정의만 이루어짐.
* **최종 연산 종류**:
  * 데이터 소비: `forEach`
  * 데이터 변환: `collect`, `toList`, `toMap`
  * 데이터 축소: `reduce`, `count`, `max`, `min`
  * 조건 검사: `anyMatch`, `allMatch`, `findFirst`

스트림 평가를 잘 활용하면 대규모 데이터를 효율적으로 처리하고, 가독성과 성능을 모두 높일 수 있습니다.



> 출처 및 참고
>
> * [https://jake-seo-dev.tistory.com/449](https://jake-seo-dev.tistory.com/449)
> * [https://jake-seo-dev.tistory.com/459?category=906605](https://jake-seo-dev.tistory.com/459?category=906605)
> * [https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-47-%EB%B0%98%ED%99%98-%ED%83%80%EC%9E%85%EC%9C%BC%EB%A1%9C%EB%8A%94-%EC%8A%A4%ED%8A%B8%EB%A6%BC%EB%B3%B4%EB%8B%A4-%EC%BB%AC%EB%A0%89%EC%85%98%EC%9D%B4-%EB%82%AB%EB%8B%A4](https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-47-%EB%B0%98%ED%99%98-%ED%83%80%EC%9E%85%EC%9C%BC%EB%A1%9C%EB%8A%94-%EC%8A%A4%ED%8A%B8%EB%A6%BC%EB%B3%B4%EB%8B%A4-%EC%BB%AC%EB%A0%89%EC%85%98%EC%9D%B4-%EB%82%AB%EB%8B%A4)

[^1]: **덩치 큰 시퀀스**란, **데이터의 크기(원소의 개수나 데이터 양)가 매우 큰 경우**를 의미합니다. 즉, 리스트, 배열, 스트림 등에서 **원소의 개수가 많아져 메모리 사용량이나 계산량이 매우 커진 시퀀스**를 말합니다. 이러한 덩치 큰 시퀀스를 처리할 때는 메모리 효율성, 성능 문제, 처리 속도 등을 고려해야 합니다.

    #### **덩치 큰 시퀀스의 예시**

    * **빅 데이터**:
      * 수백만 개의 데이터 레코드를 포함하는 경우.
      * 예: 사용자 로그, 센서 데이터 등.
    * **대용량 컬렉션**:
      * 원소의 수가 수십만, 수백만 이상인 컬렉션.
      * 예: 여러 해 동안의 날씨 데이터, 국가의 모든 인구 데이터를 저장한 컬렉션 등.
    * **무한 스트림**:
      * 끝이 없이 생성되는 스트림.
      * 예: `Stream.generate()`나 `Stream.iterate()`로 생성된 무한 스트림.
    * **영상 및 음성 데이터**:
      * 픽셀 정보, 오디오 샘플 등이 포함된 데이터로, 각 프레임이나 샘플이 매우 큰 시퀀스를 형성.
