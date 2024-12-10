# item 26 : 로 타입은 사용하지 말라

## 1. 제네릭과 와일드카드

### 1) 공변과 불공변

{% hint style="info" %}
배열은 `공변`이고, 제네릭은 `불공변이다.`**`공변`**`은`자기 자신과 자식 객체로 타입 변환을 허용해주는 것이다.
{% endhint %}

* 공변(convariant) : A가 B의 하위 타입일 때, T\<A>가 T\<B>의 하위 타입**이면** T는 공변이라고 한다. 타입 A가 B의 하위 타입(subtype)이라면, `A[]`는 `B[]`의 하위 타입이 될 수 있는 성질이다.
* 불공변(invariant) : A가 B의 하위 타입일 때, T\<A>가 T\<B>의 하위 타입이 **아니면**, T는 불공변이라고 한다. 제네릭 타입 `List<A>`와 `List<B>`가 있을 때, `A`가 `B`의 하위 타입이더라도 `List<A>`는 `List<B>`의 하위 타입이 아니다. 즉, 제네릭 타입은 불공변성을 가진다.

대표적으로 배열은 공변, 제네릭은 불공변인데 예를 들어 배열의 요소들을 출력하는 메소드가 있다고 하자.&#x20;

**\[배열의 공변성 예제]**

배열은 공변성이 있으므로, 하위 타입의 배열을 상위 타입의 배열로 사용할 수 있다.

```java
@Test
void arrayCovarianceTest() {
    Integer[] integers = new Integer[]{1, 2, 3}; // Integer 배열 생성
    printArray(integers); // Integer[]를 Object[]로 전달 가능
}

void printArray(Object[] arr) {
    for (Object e : arr) {
        System.out.println(e); // 배열 요소 출력
    }
}
```

위의 코드에서 `Integer[]`는 `Object[]`의 하위 타입으로 간주된다. 따라서 `printArray(Object[] arr)` 메서드에 `Integer[]`를 인자로 전달해도 문제가 발생하지 않는다.



**\[제네릭의 불공변성 예제]**

제네릭 타입은 불공변성을 가지므로, `List<Integer>`를 `List<Object>`로 사용할 수 없다.

<pre class="language-java"><code class="lang-java">@Test
void genericInvarianceTest() {
    List&#x3C;Integer> list = Arrays.<a data-footnote-ref href="#user-content-fn-1">asList</a>(1, 2, 3); // List&#x3C;Integer> 생성
    printCollection(list); // 컴파일 에러 발생
}

void printCollection(Collection&#x3C;Object> c) {
    for (Object e : c) {
        System.out.println(e); // 컬렉션 요소 출력
    }
}
</code></pre>

위의 코드에서 `List<Integer>`는 `List<Object>`의 하위 타입이 아니기 때문에, `printCollection(Collection<Object> c)` 메서드에 `List<Integer>`를 전달하면 컴파일 오류가 발생한다.&#x20;

{% hint style="danger" %}
제네릭 타입은 불공변성이기 때문에, `List<Integer>`와 `List<Object>`는 아무런 관계가 없다.
{% endhint %}

이러한 제네릭의 불공변 때문에 와일드카드[^2]\(제네릭의 ?타입)가 등장할 수 밖에 없었다.&#x20;

### 2) **제네릭의 등장 이전  및 도입 배경**

> 제네릭이 도입되기 전, <mark style="color:red;">컬렉션의 요소를 다루는 메서드(= 로 타입)는 타입 안전성을 보장하지 못했다.</mark> 컬렉션의 타입 매개변수를 명시할 수 없기 때문에, 모든 요소는 `Object` 타입으로 처리되었고, 타입 캐스팅이 필요한 상황에서 문제가 발생할 수 있었다.

**예시 1: 컬렉션 요소 출력**

```java
void printCollection(Collection c) {
    Iterator i = c.iterator(); // 타입 지정 없이 Iterator 사용
    while (i.hasNext()) {
        System.out.println(i.next()); // 모든 요소는 Object 타입으로 간주
    }
}
```

위 코드에서 컬렉션의 요소들은 `Object` 타입으로 취급되기 때문에, 특정 타입으로 다루려면 타입 캐스팅이 필요하다. 이는 런타임에 타입 에러를 발생시킬 수 있는 잠재적인 위험을 내포하고 있다다.



**예시 2: 컬렉션 요소 합 구하기**

```java
int sum(Collection c) {
    int sum = 0;
    Iterator i = c.iterator();
    while (i.hasNext()) {
        // 문제: 컬렉션의 요소가 Integer가 아닐 수도 있음
        sum += Integer.parseInt(i.next().toString()); // 런타임 오류 가능성
    }
    return sum;
}
```

위의 메서드는 `Collection`에 있는 요소들이 `Integer` 타입이라고 가정하고 작성된 것이다. 하지만 만약 `String`과 같은 다른 타입의 요소를 가진 컬렉션을 전달하면, 컴파일 시에는 문제가 없지만 **런타임에** `ClassCastException`이 발생할 수 있다.

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> 위와 같은 문제를 해결하기 위해 Java 개발자들은 **타입을 지정하여 컴파일 시점에 타입 안전성을 보장**할 수 있는 방법을 고안하였고, 그 결과 제네릭이 등장하게 되었다.

제네릭을 사용하면, 컬렉션이나 메서드에 타<mark style="color:red;">입 매개변수를 지정할 수 있어, 컴파일 시점에 타입을 검사할 수 있다.</mark> 이렇게 하면 런타임 오류의 가능성을 줄이고, 코드의 안정성과 가독성을 높일 수 있다.

### 3) 제네릭(Generic)이란?

제네릭은 **런타임 형변환 오류를 방지**하기 위해, **자바 5(JDK 1.5)**&#xBD80;터 도입되었다. 컴파일러가 **안전하게 자동으로 형변환을 추가**해줄 수 있게 되었다.&#x20;

**제네릭** 클래스 혹은 인터페이스란, **클래스와 인터페이스 선언에 타입 매개변수(`T`)가 쓰인 것**을 말한다. 각각의 제네릭 타입은 일련의 매개변수화 타입을 정의한다.

> **제네릭 형식 :** 클래스/인터페이스 이름<실제 타입 매개변수>

제네릭 타입을 하나 정의하면, 그에 딸린 **로 타입(Raw Type)**&#xB3C4; 함께 정의된다.

### 4) 와일드 카드의 등장 이유

**제네릭 도입 후 코드 수정**

제네릭을 사용하면 컬렉션에 타입을 지정할 수 있어, 컴파일 시점에 타입 안전성을 보장할 수 있다. 예를 들어, `Collection<Integer>` 타입을 사용하여 숫자들의 합을 구하는 메서드를 작성할 수 있다.

**수정된 코드 예제**

```java
int sum(Collection<Integer> c) {
    int sum = 0;
    for (Integer e : c) { // Collection의 요소 타입을 Integer로 제한
        sum += e;
    }
    return sum;
}
```

* 위 코드에서는 `Collection<Integer>` 타입을 사용하여, 컬렉션이 `Integer` 타입의 요소만 포함하도록 제한했다.
* 컴파일 시점에 타입 검사가 이루어져, 다른 타입의 컬렉션이 전달되면 컴파일 오류가 발생합니다. 이로써 타입 안전성을 보장할 수 있다.

{% hint style="danger" %}
제네릭 타입은 **불공변성(Invariance)** 을 가진다. 즉, `Collection<Integer>`와 `Collection<Object>`는 아무런 관계가 없다. 제네릭이 도입되기 전에는 가능했던 작업이 이제는 불가능해진 경우가 발생할 수 있다.
{% endhint %}

아래와 같이 `printCollection` 메서드를 작성하고 `List<Integer>`를 전달하려고 하면, 컴파일 오류가 발생한다.

```java
@Test
void genericTest() {
    List<Integer> list = Arrays.asList(1, 2, 3);
    printCollection(list); // 컴파일 오류: Collection<Object>는 Collection<Integer>와 호환되지 않음
}

void printCollection(Collection<Object> c) {
    for (Object e : c) {
        System.out.println(e);
    }
}
```

* `Collection<Object>`는 `Collection<Integer>`의 상위 타입이 아니기 때문에, 제네릭 타입에서는 서로 호환되지 않는다. 이로 인해 `printCollection` 메서드에 `List<Integer>`를 전달하려고 하면 컴파일 오류가 발생한다.
* 이는 제네릭의 불공변성으로 인한 문제이다.

**와일드카드의 도입**

위와 같은 문제를 해결하기 위해 `와일드카드(?)`가 도입되었다. <mark style="color:red;">와일드카드를 사용하면 제네릭 타입을 보다 유연하게 사용할 수 있으며, 모든 타입의 컬렉션에서 공통으로 사용할 수 있는 메서드를 작성할 수 있다.</mark>

**와일드카드 타입 사용 예제**

```java
void printCollection(Collection<?> c) {
    for (Object e : c) { // 와일드카드 타입으로 컬렉션의 요소를 다룸
        System.out.println(e);
    }
}

@Test
void genericTest() {
    List<Integer> list = Arrays.asList(1, 2, 3);
    printCollection(list); // 이제 컴파일 오류 없이 호출 가능
}
```

* `Collection<?>`는 [**비한정적 와일드카드 타입**](#user-content-fn-3)[^3]으로, 어떤 타입의 컬렉션이라도 인자로 받을 수 있다.
* `List<Integer>`, `List<String>`, `List<Object>` 등 다양한 타입의 컬렉션을 모두 전달할 수 있어, 보다 유연한 메서드를 작성할 수 있다.
* 단, 와일드카드 타입에서는 컬렉션에 새로운 요소를 추가할 수 없고, `null`만 허용됩니다. 이는 타입 안전성을 유지하기 위함이다.

> 와일드 카드에 대한 설명 블로그 : [https://mangkyu.tistory.com/241](https://mangkyu.tistory.com/241)

## 2. 로(raw) 타입이란?

### 1) 로 타입의 정의

{% hint style="info" %}
**로 타입**은 제네릭(Generic) 타입에서 타입 매개변수를 전혀 사용하지 않은 때를 말한다.   (ex)`List`). 또한 타입 선언에서 제네릭 타입 정보가 전부 지워진 것처럼 동작한다.
{% endhint %}

### 2) 로 타입을 사용하면 문제가 뭘까?

로 타입의 단점을 나타내주는 몇 가지 예시를 들어보자.

```java
// Stamp 인스턴스만 취급한다.
private final Collection stamps = ...;
stamps.add(new Coin(...));
```

실수로 도장 대신 동전을 넣어도 오류 없이 컴파일 되고 실행되는 문제가 발생한다.

```java
for(Iterator i = stamps.iterator(); i.hasNext(); ){
	Stamp stamp = (Stamp) i.next();  //ClassCastException
    stamp.cancle();
 }
```

오류는 이상적으로 **컴파일할때 발견**하는 것이 좋지만, 로 타입을 사용한다면 **런타임에나 오류를 발견할 수 있다.**

### 3) 제네릭 지원 이후에는?

제네릭을 지원한 이후에는 매개변수화된 컬렉션 타입으로 타입 안전성을 확보한다. 제네릭을 사용하면 타입 선언 자체에 **Stamp 인스턴스만 취급한다**라는 것이 녹아든다.

```java
private final Collection<Stamp> stamps = ...;
stamps.add(new Coin()); // 컴파일 오류 발생
```

**컴파일 오류**가 바로 발생한다. 컴파일러는 컬렉션에서 원소를 꺼내는 모든 곳에 보이지 않는 `형변환`을 **추가** 하여 절대 실패하지 않음을 보장한다.

> 제네릭을 사용하여 컬렉션의 타입을 지정함으로써, 컴파일러가 타입 안전성을 보장할 수 있다.

### **4)** 로 타입(Raw Type)과 제네릭 코드의 비교

**로 타입을 사용한 코드**

```java
import java.util.ArrayList;
import java.util.List;

public class RawTypeExample {
    public static void main(String[] args) {
        // 로 타입 사용
        List list = new ArrayList(); // 타입 매개변수를 지정하지 않음
        list.add("Hello");
        list.add(123); // 문자열과 숫자를 모두 추가할 수 있음

        // 컬렉션의 요소를 가져올 때마다 타입 캐스팅이 필요함
        String str = (String) list.get(0);
        Integer num = (Integer) list.get(1);

        System.out.println(str); // 출력: Hello
        System.out.println(num); // 출력: 123
    }
}
```

위 예제에서 `List`는 로 타입으로 사용되었다. 이 경우, 리스트에 어떤 타입의 객체든 추가할 수 있기 때문에, 각 요소를 꺼낼 때 타입 캐스팅이 필요하다. 만약 잘못된 타입으로 캐스팅하려고 하면 `ClassCastException`이 발생할 수 있다.



**제네릭을 사용한 코드**

```java
import java.util.ArrayList;
import java.util.List;

public class GenericTypeExample {
    public static void main(String[] args) {
        // 제네릭을 사용하여 List<String>으로 선언
        List<String> list = new ArrayList<>();
        list.add("Hello");
        // list.add(123); // 컴파일 오류: 정수는 추가할 수 없음

        // 타입 캐스팅이 필요 없음
        String str = list.get(0);

        System.out.println(str); // 출력: Hello
    }
}
```

위 코드에서 `List<String>`은 제네릭 타입을 사용하여 선언되었다.&#x20;

> 이제 이 리스트에는 `String` 타입만 저장할 수 있으며, 컴파일 시점에 타입 오류가 발생할 가능성을 줄일 수 있다. 요소를 가져올 때도 타입 캐스팅이 필요하지 않다.

### 5)  왜 로 타입보다 제네릭을 사용해야 할까?

1. **타입 안전성이 확보된다.**

```java
private final Collection<Stamp> stamps = ...;
```

위 예제처럼 컴파일러가 `stamps` 에는 `Stamp` 의 인스턴스만 넣어야 함을 인지하기 때문에, 다른 엉뚱한 타입의 인스턴스는 컴파일 에러를 내뱉게 된다.

올바른 인스턴스라면 컴파일러는 컬렉션에서 원소를 꺼내는 모든 곳에 **보이지 않는 형변환을 추가**하기 때문에 그 이후부터는 정상적으로 작동할 것이다.

2. **로 타입은 제네릭이 안겨주는 안전성과 표현력이 없다.**

하지만 그럼에도 로 타입이 존재하고 있는 것은, 로 타입을 사용하는 메서드에 매개변수화 타입의 인스턴스를 넘겨도 동작해야 했기 때문이다.

따라서 이러한 **마이그레이션 호환성**을 위해 로 타입을 지원하고 제네릭 구현에는 소거방식을 도입하게 된다. 소거 방식이란, 런타임에 타입 정보가 사라지는 것을 의미한다.

## 3. 로 타입은 권장되지 않는다(List와 List\<Object>의 차이)

> 로 타입을 쓰면 제네릭이 안겨주는 안전성과 표현력을 모두 잃게 된다. 제네릭이 등장하기 이전의 코드와의 **호환성**을 위해서 로 타입이 남겨져 있다.

`List`와 같은 로 타입은 권장하지 않지만 `List<Object>`는 괜찮다. 모든 타입을 허용한다는 의사를 컴파일러에게 명확하게 전달한 것이기 때문이다.&#x20;

> 그렇다면 `List와 List<Object>의 차이`는 무엇일까?

<mark style="color:red;">`List`</mark><mark style="color:red;">는 제네릭 타입과 무관한 것이고,</mark> <mark style="color:red;"></mark><mark style="color:red;">`List<Object>`</mark><mark style="color:red;">는 모든 타입을 허용한다는 것입니다.</mark>&#x20;

다시 말해서 매개변수로 `List`를 받는 메서드에 `List<String>`을 넘길 수 있지만, 제네릭의 하위 규칙 때문에 `List<Object>`를 받는 메서드에는 매개변수로 넘길 수 없다.

`List<String>`은 로 타입인 `List`의 하위 타입이지만 `List<Object>`의 하위 타입은 아니기 때문이다. 위의  공변  설명에서 함 그래서 `List<Object>`와 같은 매개변수화 타입을 사용할 때와 달리 `List`같은 로 타입을 사용하면 타입 안전성을 잃게 된다.

```java
public static void main(String[] args) {
    List<String> strings = new ArrayList<>();
    
    unsafeAdd(strings, Integer.valueOf(42));
    String s = strings.get(0);
}

// 로 타입
private static void unsafeAdd(List list, Object o) {
    list.add(o);
}
```

위의 코드는 컴파일은 성공하지만 로 타입인 `List`를 사용하여 `unchecked call to add(E) as a member of raw type List...` 라는 **경고 메시지**가 발생된다. 그런데 실행을 하게 되면 `strings.get(0)`의 결과를 형변환하려 할 때 `ClassCastException`이 발생한다. **Integer를 String으로 변환하려고 시도했기 때문이다.**

> 위 코드를 `List<Object>`로 변경하면?

```java
public static void main(String[] args) {
    List<String> strings = new ArrayList<>();

    unsafeAdd(strings, Integer.valueOf(42));
    String s = strings.get(0);
}

// List<Object>
private static void unsafeAdd(List<Object> list, Object o) {
    list.add(o);
}
```

컴파일 오류가 발생하며 `incompatible types: List<String> cannot be converted to List<Object>...` 라는 메시지가 출력된다. 실행 시점이 아닌 컴파일 시점에 오류를 확인할 수 있어 보다 안전하다.

## 4. 전체 용어 정리  및 사용 코드

#### 1. 매개변수화 타입 (Parameterized Type)

> 타입 매개변수를 사용해 실제 타입으로 지정된 제네릭 타입

```java
List<String> list = new ArrayList<>(); // List의 타입 매개변수로 String을 지정
list.add("Hello"); // String 타입의 요소를 추가
```

#### 2. 실제 타입 매개변수 (Actual Type Parameter)

> 매개변수화 타입에서 구체적으로 지정된 타입 `List<E>`

```java
// 매개변수화 타입에서 String이 실제 타입 매개변수
List<String> list = new ArrayList<>(); // 여기서 String이 실제 타입 매개변수로 사용됨
```

#### 3. 제네릭 타입 (Generic Type)

> 타입 매개변수를 가지는 클래스나 인터페이스 `E`

```java
// 제네릭 클래스를 정의할 때 타입 매개변수 T를 사용
public class Box<T> {
    private T content; // T 타입의 변수를 선언

    public void setContent(T content) {
        this.content = content; // T 타입의 값을 설정
    }

    public T getContent() {
        return content; // T 타입의 값을 반환
    }
}

// Box 클래스를 사용하는 예제
Box<Integer> intBox = new Box<>(); // Integer 타입의 Box 생성
intBox.setContent(123); // Integer 값 설정
System.out.println(intBox.getContent()); // 출력: 123

Box<String> strBox = new Box<>(); // String 타입의 Box 생성
strBox.setContent("Hello, Generics"); // String 값 설정
System.out.println(strBox.getContent()); // 출력: Hello, Generics
```

#### 4. 정규 타입 매개변수 (Formal Type Parameter)

> 제네릭 타입 또는 제네릭 메서드에서 사용되는 타입 매개변수

```java
// 제네릭 클래스 Box의 정의에서 E가 정규 타입 매개변수
public class Box<E> {
    private E content; // E 타입의 변수를 선언

    public void setContent(E content) {
        this.content = content; // E 타입의 값을 설정
    }

    public E getContent() {
        return content; // E 타입의 값을 반환
    }
}
```

#### 5. 비한정적 와일드카드 타입 (Unbounded Wildcard Type)

> 타입 매개변수를 `?`로 지정하여 어떤 타입이든 허용함 `List<?>`

```java
// 와일드카드 타입을 사용한 메서드 정의
public void printList(List<?> list) {
    // List<?>는 어떤 타입의 리스트든 받을 수 있음
    for (Object elem : list) {
        // 와일드카드 타입이므로 요소를 Object로 취급
        System.out.println(elem);
    }
}

// 사용 예제
List<String> stringList = Arrays.asList("Apple", "Banana", "Orange");
List<Integer> intList = Arrays.asList(1, 2, 3);

printList(stringList); // 출력: Apple, Banana, Orange
printList(intList);    // 출력: 1, 2, 3
```

#### 6. 로 타입 (Raw Type)

> 제네릭 타입에서 타입 매개변수를 사용하지 않은 형태 `List`

```java
// 제네릭 타입의 타입 매개변수를 사용하지 않은 경우
List rawList = new ArrayList(); // 로 타입으로 정의
rawList.add("Hello"); // String 타입의 값 추가
rawList.add(123);     // Integer 타입의 값 추가

// 로 타입 사용 시 컴파일러가 타입 안전성을 보장하지 않음
for (Object obj : rawList) {
    System.out.println(obj); // 출력: Hello, 123
}
```

#### 7. 한정적 타입 매개변수 (Bounded Type Parameter)

> 특정 타입 또는 그 하위 타입으로 제한된 타입 매개변수  `<E extends Number>`

```java
// 타입 매개변수 E가 Number 또는 그 하위 타입이어야 함
public <E extends Number> void printNumber(E number) {
    System.out.println(number); // Number 타입의 값을 출력
}

// 사용 예제
printNumber(123);     // 출력: 123 (Integer)
printNumber(45.67);   // 출력: 45.67 (Double)
// printNumber("Hello"); // 컴파일 에러: String은 Number의 하위 타입이 아님
```

#### 8. 재귀적 타입 한정 (Recursive Type Bound)

> 자기 자신을 타입 매개변수로 참조하는 타입 한정  `<T extends Comparable<T>>`

```java
// T가 Comparable<T> 인터페이스를 구현해야 함
public class Node<T extends Comparable<T>> {
    private T value; // T 타입의 값을 저장
    private Node<T> next; // 다음 노드를 가리키는 포인터

    public Node(T value) {
        this.value = value; // 노드의 값을 설정
    }

    public T getValue() {
        return value; // 노드의 값을 반환
    }
}

// 사용 예제
Node<Integer> node = new Node<>(10); // Integer 타입의 노드 생성
System.out.println(node.getValue()); // 출력: 10
```

#### 9. 한정적 와일드카드 타입 (Bounded Wildcard Type)

> 와일드카드 타입이 특정 타입 또는 그 하위/상위 타입으로 제한됨  `List<? extends Number>`

```java
// Number 또는 그 하위 타입을 요소로 갖는 리스트를 인자로 받음
public void printNumbers(List<? extends Number> list) {
    for (Number num : list) {
        System.out.println(num); // Number 타입의 요소를 출력
    }
}

// 사용 예제
List<Integer> intList = Arrays.asList(1, 2, 3);
List<Double> doubleList = Arrays.asList(1.1, 2.2, 3.3);

printNumbers(intList);    // 출력: 1, 2, 3
printNumbers(doubleList); // 출력: 1.1, 2.2, 3.3
```

#### 10. 제네릭 메서드 (Generic Method)

> 타입 매개변수를 사용하는 메서드 `static <E> List<E> asList(E[] a)`

```java
// 제네릭 타입 매개변수를 사용하는 메서드 정의
public static <E> List<E> asList(E[] array) {
    return Arrays.asList(array); // 배열을 리스트로 변환하여 반환
}

// 사용 예제
String[] stringArray = {"Hello", "World"};
List<String> stringList = asList(stringArray); // 제네릭 메서드를 사용하여 리스트 생성
System.out.println(stringList); // 출력: [Hello, World]
```

#### 11. 타입 토큰 (Type Token)

> 런타임에 제네릭 타입 정보를 제공하기 위해 사용하는 클래스 리터럴  `String.class`

```java
// 런타임에 타입 정보를 얻기 위해 사용하는 타입 토큰
public <T> T createInstance(Class<T> clazz) throws Exception {
    // 클래스 타입 T를 기반으로 새로운 인스턴스를 생성
    return clazz.getDeclaredConstructor().newInstance();
}

// 사용 예제
String str = createInstance(String.class); // String 클래스의 인스턴스 생성
System.out.println(str); // 출력: 빈 문자열 (String의 기본 생성자 사용)
```

위의 예제들은 제네릭 프로그래밍의 다양한 개념을 설명하며, 각각의 코드에 대한 주석을 통해 해당 개념이 어떻게 적용되는지 쉽게 이해할 수 있도록 돕습니다.

## 4. 원소의 타입을 모른채 쓰고 싶다면? 비한정적 와일드 카드 타입

**제네릭을 사용하는 이유**는 타입 안전성을 보장하고, 코드의 가독성과 유지보수성을 높이기 위함이다.&#x20;

> 하지만 모든 상황에서 특정 타입을 명시할 수 없는 경우가 있기 때문에 비한정적 와일드카드(`?`)와 로 타입(Raw Type)이 존재한다.

### 1) 비한정적 와일드카드 타입 (`Set<?>`)

* **비한정적 와일드카드 타입**은 제네릭 타입 매개변수가 무엇이든 상관없이 사용할 수 있도록 한다.
* 제네릭 타입의 안전성을 유지하면서도, 실제 타입 매개변수에 의존하지 않는 메서드를 작성할 수 있다.
* `Set<?>`와 같은 비한정적 와일드카드 타입은 `Set<String>`, `Set<Integer>` 등 어떤 타입의 `Set`이라도 사용할 수 있다.
* 하지만 와일드카드 타입에서는 `null` 외에는 어떤 원소도 추가할 수 없다.

```java
public class TypeTest {
    private static void addToWildList(final List<?> list, final Object o) {
        // 컴파일 오류: 제네릭 타입에 의존성이 있음
        // list.add(o);

        // null은 허용됨
        list.add(null);
    }

    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        String s = "kimtaeng";

        addToWildList(list, s); // Okay! 메서드 호출 자체는 문제없음
    }
}
```

### 2) 로 타입 (`Set`)

* **로 타입**은 제네릭 도입 이전의 컬렉션 타입으로, 타입 안전성을 보장하지 않는다.
* 로 타입을 사용할 경우, 어떤 타입의 객체든 추가할 수 있으며, 컴파일 시점에 타입 검사가 이루어지지 않아 런타임 오류의 가능성이 높다.
* 제네릭 타입의 타입 정보가 런타임에 지워지기 때문에 `Set<String>`과 `Set`은 동일하게 취급된된다.

<pre class="language-java"><code class="lang-java">public class TypeTest2 {
    public static void main(String[] args) {
        <a data-footnote-ref href="#user-content-fn-4">List raw = new ArrayList&#x3C;String>();</a> // Okay! 로 타입은 타입 안전성을 제공하지 않음
        <a data-footnote-ref href="#user-content-fn-5">List&#x3C;?> wildcard = new ArrayList&#x3C;String>();</a> // Okay! 비한정적 와일드카드

        raw.add("Hello"); // Okay! 로 타입은 어떤 타입의 원소도 추가 가능
        raw.add(1); // 컴파일러가 타입 검사를 하지 않기 때문에 가능
        // wildcard.add("Hello"); // 컴파일 오류: 비한정적 와일드카드 타입은 null 외에 추가할 수 없음
        
       <a data-footnote-ref href="#user-content-fn-6"> List&#x3C;String> list = new ArrayList&#x3C;>(); // 제네릭 타입 사용</a>
<strong>        list.add("Hello"); // String 타입의 원소만 추가 가능
</strong>        // list.add(1); // 컴파일 오류: 정수는 추가할 수 없음

        // 메서드 호출은 가능
        wildcard.size(); // Okay!
        wildcard.clear(); // Okay!
    }
}
</code></pre>

### 3) 로 타입과 비한정적 와일드카드 타입의 차이

| 특성            | 로 타입 (`Set`)                            | 비한정적 와일드카드 (`Set<?>`)      |
| ------------- | --------------------------------------- | -------------------------- |
| **타입 안전성**    | 보장되지 않음                                 | 보장됨                        |
| **타입 불변식 유지** | 위반하기 쉬움                                 | 타입 불변식 유지                  |
| **원소 추가**     | 어떤 타입의 원소도 추가 가능                        | `null` 외에는 추가할 수 없음        |
| **메서드 호출**    | 타입에 관계없이 사용 가능                          | 제네릭 타입에 의존하지 않는 메서드만 사용 가능 |
| **사용 가능 상황**  | 하위 버전과의 호환성 필요 시, 클래스 리터럴, `instanceof` | 제네릭 타입에 의존하지 않는 메서드 작성 시   |



## 6. 로 타입이 필요한 예외적인 상황

### **1) 클래스 리터럴**

제네릭 타입은 [**클래스 리터럴**](#user-content-fn-7)[^7]**에서 사용할 수 없다.** `List.class`와 같은 로 타입만 사용할 수 있으며, `List<String>.class`나 `List<?>.class`는 허용되지 않는다.

```java
Class<List> listClass = List.class; // Okay!
```

### **2) `instanceof` 연산자**

제네릭 타입 정보는 런타임에 제거되므로, `instanceof` 연산자는 로 타입이나 비한정적 와일드카드 타입에서만 사용할 수 있다. `Set<?>`을 사용해 타입 캐스팅을 할 수 있다.

```java
if (o instanceof Set) {
    Set<?> s = (Set<?>) o; // 로 타입 대신 비한정적 와일드카드 타입으로 형변환
}
```

#### 5. 정리

* **로 타입**은 타입 안전성이 보장되지 않기 때문에, 사용을 피해야 합니다. 로 타입을 사용하는 주요 이유는 하위 버전과의 호환성 때문입니다.
* \*\*비한정적 와일드카드 타입 (`<?>`)\*\*은 제네릭 타입의 타입 매개변수를 알 수 없거나 신경 쓰지 않아도 될 때 사용합니다. 타입 안전성을 유지하면서 제네릭을 사용할 수 있습니다.
* 로 타입과 비한정적 와일드카드 타입은 클래스 리터럴과 `instanceof` 연산자와 같은 특정 상황에서 로 타입이 필요할 때만 사용해야 하며, 그 외에는 제네릭 타입을 사용하는 것이 좋습니다.

## **✨** 최종 정리

{% hint style="danger" %}
**로 타입을 사용하면 런타임에 예외가 일어날 수 있으니** 사용하면 안 된다.  오직 하위 버전과 호환하기 위해서 남아있다.
{% endhint %}

1. 로 타입은 제네릭이 도입되기 이전 **코드와의 호환성을 위해 제공될 뿐**이다.&#x20;
2. `Set<Object>`는 어떤 타입의 객체도 저장할 수 있는 매개변수화 타입이고, `Set<?>`는 모종의 타입 객체만 저장할 수 있는 와일드카드 타입이다.&#x20;
3. 이들의 로 타입인 Set은 제네릭 타입 시스템에 속하지 않는다. Set\<Object>와 Set\<?>는 안전하지만, 로 타입인 Set은 안전하지 않다.

> 참고 글 :&#x20;
>
> [\[이펙티브 자바 3판\] 아이템 26. 로 타입은 사용하지 말라](https://madplay.github.io/post/dont-use-raw-types)
>
> [\[JAVA\] 제네릭과 와일드카드 타입에 대해 쉽고 완벽하게 이해하기(공변과 불공변, 상한 타입과 하한 타입)](https://mangkyu.tistory.com/241)
>
> [아이템 26](https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-26-%EB%A1%9C-%ED%83%80%EC%9E%85%EC%9D%80-%EC%82%AC%EC%9A%A9%ED%95%98%EC%A7%80-%EB%A7%90%EB%9D%BC)
>
> \[ java] generic type erasure란  무엇이띾&#x20;



[^1]: `asList`는 Java에서 배열을 리스트로 변환하는 데 사용되는 메서드입니다. `java.util.Arrays` 클래스의 정적 메서드로 제공되며, 주어진 배열을 `List` 타입으로 변환하여 반환합니다

[^2]: 모든 타입을 대신할 수 있는 와일드카드 타

[^3]: Java 제네릭에서 사용하는 와일드카드의 한 종류로, 어떤 타입이든 수용할 수 있는 제네릭 타입을 나타냅니다. 비한정적 와일드카드는 물음표 기호 `?`로 표현되며, 구체적인 타입을 알 수 없거나 타입에 상관없이 작업을 수행할 때 사용됩니다.

    #### 비한정적 와일드카드의 사용법

    * **표기법**: `?` 기호를 사용하여 나타냅니다. 예를 들어, `Collection<?>`는 "아무 타입의 `Collection`"을 의미합니다.
    * **의미**: 어떤 타입이든 수용할 수 있다는 의미입니다. 즉, `Collection<Integer>`, `Collection<String>`, `Collection<Object>`와 같은 다양한 타입의 제네릭 컬렉션을 인자로 받을 수 있습니다.

[^4]: **`List raw`**:

    * `List` 타입의 참조 변수 `raw`를 선언한 것입니다.
    * **로 타입**으로 선언되었기 때문에, 제네릭 타입 매개변수가 없는 상태입니다. 즉, `List`에 어떤 타입의 요소든 저장할 수 있습니다.
    * **로 타입**은 제네릭이 도입되기 이전의 Java 스타일로, 타입 안전성이 보장되지 않습니다.

    - **`new ArrayList<String>()`**:
      * `String` 타입을 요소로 갖는 `ArrayList` 객체를 생성합니다. 즉, 이 리스트에는 `String` 타입의 요소가 들어가도록 설계되었습니다.
      * 그러나 참조 변수가 로 타입(`List raw`)으로 선언되었기 때문에, 이 생성된 리스트의 타입 정보는 무시됩니다.

[^5]: * **`List<?> wildcard`**: `List` 타입의 참조 변수 `wildcard`를 선언하고, 이 변수는 비한정적 와일드카드 `?`를 사용합니다. 여기서 `?`는 어떤 타입이든 허용된다는 의미입니다. 즉, `List<?>`는 모든 타입의 리스트를 참조할 수 있습니다.

    - **`new ArrayList<String>()`**: 구체적으로 `String` 타입의 요소를 갖는 `ArrayList` 객체를 생성합니다.

[^6]: **`List<String>`**:

    * 제네릭 타입을 사용하여 `List` 인터페이스의 타입 매개변수를 `String`으로 지정했습니다.
    * 즉, 이 리스트는 `String` 타입의 요소만 저장할 수 있습니다. 다른 타입의 객체를 추가하려고 하면 컴파일 오류가 발생합니다. 이는 컴파일 시점에 타입을 검사하여 오류를 방지하기 위함입니다.

    - **`list`**:
      * `List<String>` 타입의 참조 변수입니다. 이 변수는 `String` 타입의 요소를 가지는 리스트를 가리키도록 설계되었습니다.
    - **`new ArrayList<>()`**:
      * `ArrayList` 클래스의 인스턴스를 생성합니다.
      * 빈 꺽쇠(`<>`)는 **다이아몬드 연산자**라고 하며, 컴파일러가 타입 추론을 통해 `ArrayList<String>`으로 간주합니다. 즉, `ArrayList<String>`과 같은 의미입니다.

[^7]: Java에서 클래스나 인터페이스의 타입 정보를 나타내기 위한 특별한 문법입니다. 클래스 리터럴을 사용하면 클래스의 타입 정보를 런타임에 참조할 수 있습니다. 이는 클래스 파일의 메타데이터를 사용하여 객체의 인스턴스를 생성하거나, 리플렉션(Reflection) 작업 등을 수행할 때 유용합니다.\
    **기본 문법**: `타입이름.class`
