# item 20 : 추상클래스보다는 인터페이스를 고려하라

## 1. 자바에서의 다중 구현 매커니즘

{% hint style="info" %}
자바가 제공하는 다중 구현 메커니즘은 **인터페이스와 추상 클래스**
{% endhint %}

자바8부터는 인터페이스도 디폴트 메서드를 제공할 수 있게되어 인터페이스와 추상 클래스 **모두 인스턴스 메서드**를 구현 형태로 제공할 수 있다.

둘의 가장 큰 차이는 추상 클래스가 정의한 타입을 구현하는 클래스는 **반드시 추상 클래스의 하위 클래스가 되어야 한다**. 자바는 단일 상속만 지원하니 큰 제약이다.

**인터페이스는 선언한 메서드를 모두 정의하고 그 규약을 지킨 클래스라면 다른 어떤 클래스를 상속해도 같은 타입으로 취급한다.**

## 2. 인터페이스를 사용했을 때의 장점

### 1) 기존 클래스에도 손쉽게 새로운 인터페이스를 구현해넣을 수 있다

{% hint style="success" %}
인터페이스가 요구하는 메서드를 (아직 없다면) 추가하고, 클래스 선언에 **implements 구문만 추가하면 끝**이다.&#x20;
{% endhint %}

자바 플랫폼에서도 Comparable, Iterable, AutoCloseable 인터페이스가 새로 추가됐을 때 표준 라이브러리의 수많은 기존 클래스가 이 인터페이스들을 구현한 채 릴리스됐다.&#x20;

```javascript
// 새로운 인터페이스 Comparable 추가
// Employee 클래스는 Person을 상속하면서 Comparable 인터페이스를 구현한다
interface Comparable<T> {
    int compareTo(T o);
}

// 기존 클래스
class Person {
    private String name;

    public Person(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

// Person 클래스가 인터페이스 Comparable을 구현할 수 있음
class Employee extends Person implements Comparable<Employee> {
    private int id;

    public Employee(String name, int id) {
        super(name);
        this.id = id;
    }

    @Override
    public int compareTo(Employee o) {
        return Integer.compare(this.id, o.id); // Employee의 id로 비교
    }
}

public class Main {
    public static void main(String[] args) {
        Employee e1 = new Employee("Alice", 100);
        Employee e2 = new Employee("Bob", 200);

        System.out.println(e1.compareTo(e2)); // -1 출력
    }
}

```

반면 기존 클래스 위에 **새로운 추상 클래스를 끼워넣기는** 어려운 게 일반적이다. 두 클래스가 같은 추상 클래스를 확장하길 원한다면, 그 추상 클래스는 계층구조상 두 클래스의  `공통 조상`이어야 한다. 새로 추가된 추상 클래스의 모든 자손이 이를 상속하게 되는 것이 다. 그렇게 하는 것이 적절하지 않은 상황에서도 강제로 말이다.

```java
// 새로운 추상 클래스 추가
abstract class Animal {
    public abstract void makeSound();
}

// 기존 클래스
class Dog {
    public void bark() {
        System.out.println("멍멍");
    }
}

// 기존 클래스
class Cat {
    public void meow() {
        System.out.println("야옹");
    }
}

// 새로운 추상 클래스를 적용하기 어려운 상황
// Dog과 Cat은 Animal을 상속하지 않기 때문에 계층 구조를 변경해야 함

class AnimalDog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("멍멍");
    }
}

class AnimalCat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("야옹");
    }
}

public class Main {
    public static void main(String[] args) {
        AnimalDog dog = new AnimalDog();
        AnimalCat cat = new AnimalCat();

        dog.makeSound(); // 멍멍 출력
        cat.makeSound(); // 야옹 출력
    }
}

```

위와 같은 코드처럼 되면, 계층 구조가 강제로 변경되며, 기존 설계와 호환되지 않거나 불필요한 상속이 발생할 수 있다.

### 2) 인터페이스는 믹스인(mixin) 정의에 안성맞춤이다.

{% hint style="info" %}
`믹스인`이란 **클래스가 구현할 수 있는 타입**으로, 믹스인을 구현한 클래스에 <mark style="color:red;">원래의 주된 기능외에 도 특정 선택적 기능을 더하는 효</mark>
{% endhint %}

이 역할을 보통 **인터페이스**로 구현할 수 있습니다. 반면, **추상 클래스**로는 믹스인을 정의할 수 없다는 내용은 **다중 상속**을 지원하지 않는 자바의 특성 때문에 발생하는 문제입니다.

> 🧐 믹스인(Mixin)이란?

믹스인은 **하나의 주된 클래스에 선택적인 기능을 추가**할 수 있는 방식으로, 여러 클래스에서 공통적으로 구현할 수 있는 기능을 제공하는 인터페이스를 의미한다. 예를 들어, `Comparable` 인터페이스는 어떤 클래스든 상관없이, 그 클래스 인스턴스들 사이에 **비교 기능**을 더할 수 있게 해준다.

> 🧐 왜 추상 클래스로 믹스인을 정의할 수 없을까?

자바는 **다중 상속**을 허용하지 않기 때문에, 클래스가 이미 다른 클래스를 상속받고 있으면 **추가적으로 다른 추상 클래스를 상속받을 수 없다**. 하지만 <mark style="color:red;">인터페이스는 여러 개를 구현할 수 있으므로</mark>, **인터페이스로는 믹스인 역할을 할 수 있지만, 추상 클래스로는 불가능**하다.

#### 예시 코드

인터페이스로 믹스인을 구현하는 경우와 추상 클래스를 사용하려 할 때의 문제점

**1. 인터페이스로 믹스인 구현 (가능)**

```java
// Comparable 인터페이스는 '믹스인' 역할을 할 수 있다.
interface Comparable<T> {
    int compareTo(T o);
}

// 주된 클래스: Person
class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}

// Person 클래스에 믹스인 인터페이스(Comparable)를 구현할 수 있다.
class Employee extends Person implements Comparable<Employee> {
    private int id;

    public Employee(String name, int age, int id) {
        super(name, age);
        this.id = id;
    }

    @Override
    public int compareTo(Employee o) {
        return Integer.compare(this.id, o.id); // Employee의 id로 비교
    }

    public int getId() {
        return id;
    }
}

public class Main {
    public static void main(String[] args) {
        Employee emp1 = new Employee("Alice", 30, 101);
        Employee emp2 = new Employee("Bob", 25, 102);

        System.out.println(emp1.compareTo(emp2)); // -1 출력
    }
}
```

**설명**:

* `Person` 클래스가 `Employee`로 확장되었고, **추가적으로** `Comparable<Employee>`라는 **믹스인 인터페이스**를 구현할 수 있다.
* 이를 통해 `Employee` 클래스 인스턴스들끼리 **비교**할 수 있는 기능을 추가할 수 있다.

**2. 추상 클래스로 믹스인 정의 (불가능)**

```java
// 추상 클래스 정의
abstract class ComparableMixin {
    public abstract int compare(Object o);
}

// 이미 상속받고 있는 주된 클래스
class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}

// 자바는 다중 상속을 허용하지 않으므로, Person 클래스가 ComparableMixin을 상속받을 수 없다.
class Employee extends Person /*, ComparableMixin */ {
    private int id;

    public Employee(String name, int age, int id) {
        super(name, age);
        this.id = id;
    }

    // compare 메서드가 필요하지만 다중 상속 불가로 추상 클래스 적용 불가
    /*
    @Override
    public int compare(Object o) {
        Employee emp = (Employee) o;
        return Integer.compare(this.id, emp.id);
    }
    */
}
```

* `Employee` 클래스는 이미 `Person` 클래스를 상속받고 있기 때문에, `ComparableMixin`이라는 **추상 클래스**를 추가적으로 상속받을 수 없다.
* 자바는 **다중 상속을 허용하지 않기 때문**에, 이 상황에서 **추상 클래스를 믹스인으로 사용할 수 없다**.

**인터페이스**는 여러 개를 구현할 수 있기 때문에, 클래스의 주된 기능 외에 선택적 기능을 믹스인으로 제공하는 데 적합하다. 즉, `Comparable`과 같은 인터페이스는 클래스에 **필요한 기능을 자유롭게 추가**할 수 있는 방식으로 사용된다.

반면, **추상 클래스**는 클래스 계층 구조를 강하게 제한하므로, 새로운 추상 클래스를 도입하여 믹스인 역할을 하려면 **상속 제약**에 걸리게 됩니다. 이는 자바가 **다중 상속**을 지원하지 않기 때문이다.

### 3) 인터페이스로는 계층구조가 없는 타입 프레임워크를 만들 수 있다.

{% hint style="success" %}
**타입 계층 구조**를 만들 때, **인터페이스**를 사용하면 **계층의 제약 없이 다양한 기능을 조합**할 수 있어 유연하다.
{% endhint %}

반면, **클래스 기반**으로 <mark style="color:red;">동일한 타입을 정의하려고 하면 조합의 수가 급격히 늘어나면서 복잡해지는 문제</mark>(조합 폭발)가 발생한다.

1. **인터페이스의 유연성**:
   * 인터페이스는 다중 상속이 가능하므로, **여러 인터페이스를 자유롭게 조합**할 수 있다.
   * 예를 들어, 가수(Singer)와 작곡가(Songwriter)라는 인터페이스를 각각 정의하고, **가수이면서 작곡가인 사람**을 나타내기 위해 두 인터페이스를 구현하는 클래스를 만들 수 있다.
   * 더 나아가, `Singer`와 `Songwriter`를 **확장**하고 새로운 기능을 추가하는 <mark style="color:red;">제3의 인터페이스(SingerSongwriter)를 정의할 수도 있다.</mark>

```java
// 가수 인터페이스
public interface Singer {
    AudioClip sing(Song s);  // 가수는 노래를 부름
}

// 작곡가 인터페이스
public interface Songwriter {
    Song compose(int chartPosition);  // 작곡가는 노래를 만듦
}

// 가수이자 작곡가인 인터페이스
public interface SingerSongwriter extends Singer, Songwriter {
    AudioClip strum();  // 추가 기능: 악기를 연주할 수 있음
    void actSensitive();  // 추가 기능: 감성적인 행동을 할 수 있음
}
```

* `Singer`와 `Songwriter` 인터페이스는 각각 **가수**와 **작곡가**의 역할을 정의한다.
* `SingerSongwriter` 인터페이스는 두 인터페이스를 **확장**하여 **가수이면서 작곡가인 사람**을 표현한다. 또한, 악기 연주(`strum()`)와 감성적 행동(`actSensitive()`) 같은 추가 기능도 정의한다.
* **인터페이스를 사용**함으로써, **하나의 클래스**가 가수이면서 작곡가인 동시에 추가적인 기능을 쉽게 구현할 수 있다.

2. **클래스 계층 구조의 제한**:

* 클래스 기반으로 비슷한 조합을 만들면, **다중 상속이 불가능**하기 때문에 가수이면서 작곡가인 클래스를 정의하려면 별도의 클래스를 만들어야 한다.
* 속성의 수가 많아질수록, <mark style="color:red;">모든 조합을 클래스로 표현하려면 그만큼 많은 클래스를 만들어야 해서</mark> 클래스 계층 구조가 비대해지는 문제(조합 폭발)가 발생한다.

**2. 클래스 기반 구조의 문제점**

```java
// 가수 클래스
class SingerClass {
    public AudioClip sing(Song s) {
        // 가수의 노래 부르기 기능
    }
}

// 작곡가 클래스
class SongwriterClass {
    public Song compose(int chartPosition) {
        // 작곡가의 작곡 기능
    }
}

// 가수이자 작곡가인 클래스를 만들려면 다중 상속이 안되므로 불편함이 발생
class SingerSongwriterClass extends SingerClass {
    // 작곡가 기능을 구현하려면 추가 메서드를 작성해야 함
    public Song compose(int chartPosition) {
        // 작곡가의 작곡 기능 추가
    }

    // 추가적인 기능도 직접 구현해야 함
    public AudioClip strum() {
        // 악기 연주 기능
    }

    public void actSensitive() {
        // 감성적 행동 기능
    }
}
```

**설명**:

* `SingerClass`와 `SongwriterClass`를 클래스로 정의하려면, 다중 상속이 불가능하므로 **하나의 클래스에 모든 기능을 직접 추가**해야 한다.
* 예를 들어, 가수이자 작곡가인 클래스를 정의하려면, **`SingerClass`를 상속**하고 별도로 **`SongwriterClass`의 기능을 추가로 구현**해야 한다.
* 클래스가 많아질수록 **각 조합마다 새로운 클래스를 정의**해야 하므로, **조합이 많아지면 관리가 어려워지고 복잡해지는 문제**가 발생한다.

{% hint style="info" %}
즉, 인터페이스의 장점은&#x20;

* **다중 상속 가능**: 자바에서 인터페이스는 여러 개를 동시에 구현할 수 있기 때문에, 여러 기능을 쉽게 조합할 수 있다.
* **유연성**: 인터페이스를 사용하면 **코드의 재사용성과 확장성**이 높아지고, 다양한 기능을 독립적으로 구현할 수 있다.
* **낮은 결합도**: 인터페이스를 사용하면 **구현 세부 사항을 분리**할 수 있어, 클래스 간 결합도가 낮아진다.
{% endhint %}

### 4) 래퍼 클래스 관용구와 함께 사용하면 인터페이스는 기능을 향상시키는 안전하고 강력한 수단이 된다.

타입을 추상 클래스로 정의해두면 그 타입에 기능을 추가하는 방법은 `상속`뿐이다. 상속해서 만든 클래스는 래퍼 클래스보다 활용도가 떨어지고 깨지기 쉽다.

인터페이스의 메서드 중 구현 방법이 명백한 것이 있다면, `디폴트 메소드`로 만들 수 있다. 그러나 **디폴트 메서드는 제약**이 있다.

* equals와 hashcode를 디폴트 메소드로 제공 안함
* 인터페이스는 인스턴스 필드를 가질 수 없고, private 정적 메소드를 가질 수 없다.
* 본인이 만든 인터페이스가 아니면 디폴트 메소드 추가 불가능

그리고 디폴트 메서드를 제공할 때는 상속하려는 사람을 위한 설명을 `@implSpec` 자바독 태그를 붙여 문서화해야 한다.

## 3. 인터페이스와 추상 골격 구현 클래스

{% hint style="success" %}
**골격 구현 클래스**는 복잡한 인터페이스를 구현하는 데 필요한 메서드를 미리 구현해두는 추상 클래스로, 이를 확장하면 인터페이스를 쉽게 구현할 수 있다.
{% endhint %}

### **1) 인터페이스와 디폴트 메서드, 골격 구현에 대한 설명**

1. **인터페이스와 추상 클래스**:
   * **인터페이스**는 다중 구현을 가능하게 해준다. 클래스는 다중 상속이 불가능하지만, <mark style="color:red;">인터페이스는 여러 개를 동시에 구현할 수 있다. 그래서 다양한 타입의 기능을 구현하는 데 적합하다.</mark>
   * **추상 클래스**는 단일 상속만 허용되기 때문에 클래스 계층 구조에 제한이 있다. 또한, 한 번 상속되면 다른 추상 클래스를 함께 상속할 수 없어서 확장성이 떨어진다.
2. **디폴트 메서드**

{% hint style="info" %}
**목적**: 인터페이스에서 일부 메서드의 **기본 구현**을 제공하여, 인터페이스를 확장하거나 구현할 때, 프로그래머가 모든 메서드를 반드시 구현할 필요를 없애준다.
{% endhint %}

* **도입 시기**: 자바 8
* 하지만 디폴트 메서드로 **`equals`**, **`hashCode`**, **`toString`** 같은 **Object 메서드**를 제공할 수 없다. 이러한 메서드들은 여전히 추상 클래스에서 구현해야 한다.
*   **사용 예**:

    ```java
    public interface MyInterface {
        // 추상 메서드
        void abstractMethod();

        // 디폴트 메서드
        default void defaultMethod() {
            System.out.println("This is a default method.");
        }
    }
    ```
* **주요 장점**
  * **기존 인터페이스를 변경하지 않고도** 새로운 기능을 추가할 수 있다.
  * **다중 상속**이 가능하여, 여러 인터페이스에서 디폴트 메서드를 상속받을 수 있다.
  * 기존 클래스를 유지한 채 **하위 호환성을 보장**하면서도 새로운 기능을 추가할 수 있다.

3. **골격 구현(Skeletal Implementation)**

{% hint style="info" %}
**목적**: 인터페이스에서 일부 메서드의 **기본 구현**을 제공하여, 인터페이스를 확장하거나 구현할 때, 프로그래머가 모든 메서드를 반드시 구현할 필요를 없애준다.
{% endhint %}

* **도입 시기**: 자바 1.2 (컬렉션 프레임워크)
* **골격 구현 클래스**는 <mark style="color:red;">복잡한 인터페이스를 구현하는 데 필요한 많은 메서드를 미리 구현해두는</mark> **추상 클래스**이다. 이 클래스는 템플릿 메서드 패턴을 따른다.
* 인터페이스를 직접 구현하는 것보다 골격 구현 클래스를 확장하면 인터페이스의 구현 부담이 크게 줄어든다.
* **컬렉션 프레임워크**에서 제공하는 **`AbstractCollection`**, **`AbstractSet`**, **`AbstractList`**, **`AbstractMap`** 등이 대표적인 골격 구현 클래스의 예

4. **골격 구현의 제약 사항**

* **상속 구조**를 가지며, **단일 상속**만 가능하기 때문에, 클래스는 한 번에 하나의 골격 구현 클래스만 확장할 수 있다.
* **추상 클래스**로 구현되기 때문에, **필드**를 가질 수 있고, `protected`나 `private` 접근자를 사용할 수 있다.

5. **골격 구현의 장점**

* **복잡한 인터페이스의 구현을 단순화**합니다. 예를 들어, 컬렉션 프레임워크의 `AbstractList`는 `List` 인터페이스의 복잡한 메서드들(예: `contains`, `isEmpty`)을 기본적으로 제공하여, 하위 클래스가 간단하게 필요한 메서드만 구현할 수 있도록 도와준다.
* **필드를 포함할 수 있고** 내부 구현을 캡슐화할 수 있다.
* **protected** 메서드나 필드를 제공하여 **하위 클래스가 유연하게** 사용하거나 재정의할 수 있다.

6. **골격 구현 클래스 작성 방법**:

```java
abstract class AbstractList<E> implements List<E> {
    @Override
    public boolean isEmpty() {
        return size() == 0;
    }

    @Override
    public boolean contains(Object o) {
        for (E e : this) {
            if (e.equals(o)) return true;
        }
        return false;
    }

    // 추가적으로 더 많은 메서드를 골격 구현으로 제공
}

```

* 먼저 인터페이스를 분석해, **기반 메서드**를 선정하고, 이 메서드들은 골격 구현에서 **추상 메서드**로 남긴다.
* 그다음, 기반 메서드들로 구현할 수 있는 메서드는 **디폴트 메서드**로 제공한다.
* `equals`, `hashCode`, `toString` 등 **Object 메서드**는 디폴트 메서드로 제공할 수 없으므로, **추상 클래스**에서 구현해야 한다.

### 2) 예시 코드&#x20;

**1. 골격 구현을 활용한 `List` 구현**

다음 코드는 `int[]` 배열을 받아 **`List<Integer>`** 형태로 변환하는 골격 구현을 사용하는 예시

```java
// 골격 구현을 사용해 완성한 List 클래스
static List<Integer> intArrayAsList(int[] a) {
    Objects.requireNonNull(a); // null 값 방지

    return new AbstractList<>() {
        @Override
        public Integer get(int i) {
            return a[i]; // 오토박싱
        }

        @Override
        public Integer set(int i, Integer val) {
            int oldVal = a[i];
            a[i] = val; // 오토언박싱
            return oldVal; // 오토박싱
        }

        @Override
        public int size() {
            return a.length;
        }
    };
}
```

* **설명**: 이 코드는 `int[]` 배열을 받아 `List<Integer>` 형태로 변환하는 어댑터[^1] 역할을 한다. `AbstractList`는 골격 구현 클래스로, 이를 상속받아 필요한 `get()`, `set()`, `size()` 메서드를 구현했다. 이렇게 골격 구현 클래스를 활용하면 리스트 기능을 구현하는 데 필요한 작업이 크게 줄어든다.

**2. 골격 구현 클래스의 예시 (Map.Entry)**

다음 코드는 자바의 **`Map.Entry`** 인터페이스를 위한 골격 구현 클래스

```java
public abstract class AbstractMapEntry<K,V> implements Map.Entry<K,V> {

    // 변경 가능한 엔트리는 이 메서드를 재정의해야 한다.
    @Override
    public V setValue(V value) {
        throw new UnsupportedOperationException(); // 기본적으로 값을 수정할 수 없음
    }

    // equals 메서드의 일반 규약을 구현한다.
    @Override
    public boolean equals(Object o) {
        if (o == this) return true;
        if (!(o instanceof Map.Entry)) return false;
        Map.Entry<?,?> e = (Map.Entry<?,?>) o;
        return Objects.equals(e.getKey(), getKey()) &&
               Objects.equals(e.getValue(), getValue());
    }

    // hashCode 메서드의 일반 규약을 구현한다.
    @Override
    public int hashCode() {
        return Objects.hashCode(getKey()) ^ Objects.hashCode(getValue());
    }

    // toString 메서드 구현
    @Override
    public String toString() {
        return getKey() + "=" + getValue();
    }
}
```

* **설명**: 이 클래스는 `Map.Entry` 인터페이스의 골격 구현이다. `equals`, `hashCode`, `toString` 메서드를 구현해두었으며, `setValue()`는 기본적으로 값을 수정할 수 없도록 예외를 던지게 되어 있다. 하위 클래스는 이 메서드들을 재정의할 수 있다.

**3. 단순 구현의 예 (AbstractMap.SimpleEntry)**

`단순 구현`은 골격 구현과 유사하지만, **추상 클래스가 아니며 바로 사용할 수 있는 가장 기본적인 구현**

```java
public class SimpleEntry<K,V> implements Map.Entry<K,V> {
    private final K key;
    private V value;

    public SimpleEntry(K key, V value) {
        this.key = key;
        this.value = value;
    }

    @Override
    public K getKey() {
        return key;
    }

    @Override
    public V getValue() {
        return value;
    }

    @Override
    public V setValue(V value) {
        V old = this.value;
        this.value = value;
        return old;
    }
}
```

* **설명**: `SimpleEntry`는 **골격 구현 클래스**는 아니지만, `Map.Entry` 인터페이스를 구현한 가장 기본적인 클래스입니다. 이를 그대로 사용하거나, 필요에 따라 확장할 수 있다.

{% hint style="success" %}
디폴트 메소드를 사용하지 않고 추상 골격 구현 클래스(AbstractCharacter)를 구현하여 중복을 없앨 수 있다.
{% endhint %}

### **3) 디폴트 메서드와 골격 구현 비교**

| **특징**            | **디폴트 메서드**                             | **골격 구현 (Skeletal Implementation)**     |
| ----------------- | --------------------------------------- | --------------------------------------- |
| **구현 위치**         | 인터페이스 안에서 제공                            | 추상 클래스에서 제공                             |
| **상속 구조**         | **다중 상속 가능** (여러 인터페이스에서 디폴트 메서드 상속)    | **단일 상속** (한 번에 하나의 골격 구현 클래스만 상속 가능)   |
| **필드 사용**         | 인스턴스 필드 사용 불가                           | 인스턴스 필드 사용 가능                           |
| **메서드 제공 범위**     | 주로 간단한 메서드 구현 (구현 코드가 적음)               | 복잡한 메서드 구현 가능 (구현 코드가 많을 수 있음)          |
| **주된 활용**         | 주로 **작은 기능 추가** 또는 **간단한 기본 동작** 제공     | 인터페이스의 복잡한 동작을 구현하는 데 사용                |
| **Object 메서드 제약** | `equals`, `hashCode`, `toString`은 구현 불가 | `equals`, `hashCode`, `toString`은 구현 가능 |
| **하위 호환성**        | 기존 인터페이스에 새로운 메서드를 추가할 때 유용             | 클래스 확장을 통해 상속된 메서드를 구현                  |



## **✨** 최종 정리

{% hint style="success" %}
일반적으로 다중 구현용 타입으로는 **인터페이스**가 가장 적합하다.
{% endhint %}

복잡한 인터페이스라 면 구현하는 수고를 덜어주는 골격 구현을 함께 제공하는 방법을 꼭 고려해보자. 골격 구현은 `가능한 한` 인터페이스의 **디폴트 메서드로 제공**하여 그 인터페이스를 구현한 모든 곳에서 활용하도록 하는 것이 좋다. `가능한 한`이라고 한 이유는, 인터페이스에 걸려 있는 <mark style="color:red;">구현상의 제약 때문에 골격 구현을 추상 클래스로 제공하는 경우가 더 흔하기 때문</mark>이다.



> 참고 글 : [https://velog.io/@injoon2019/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C20.-%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%B3%B4%EB%8B%A4%EB%8A%94-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EB%A5%BC-%EC%9A%B0%EC%84%A0%ED%95%98%EB%9D%BC](https://velog.io/@injoon2019/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C20.-%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%B3%B4%EB%8B%A4%EB%8A%94-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EB%A5%BC-%EC%9A%B0%EC%84%A0%ED%95%98%EB%9D%BC)

[^1]: 어댑터(Adapter)는 소프트웨어 디자인 패턴 중 하나로, 서로 호환되지 않는 인터페이스를 가진 클래스들이 함께 동작할 수 있도록 변환해주는 역할을 합니다. 즉, **기존 클래스의 인터페이스를 클라이언트가 원하는 다른 인터페이스로 변환**해주는 역할을 합니다.

    이 패턴은 **호환되지 않는 인터페이스**를 가진 클래스들이 함께 사용할 수 있도록 중간에서 변환 작업을 처리해주는 일종의 "변환기" 역할을 하는 것입니다. 자바에서는 특히 **컬렉션**, **스트림**, **배열** 등을 변환할 때 어댑터 패턴을 자주 사용합니다.

    #### 어댑터 패턴의 사용 목적:

    1. **호환되지 않는 인터페이스를 연결**하여 기존 코드를 변경하지 않고도 새로운 방식으로 사용할 수 있게 합니다.
    2. **재사용성**을 높이기 위해, 기존 클래스를 수정하지 않고 다른 방식으로 활용할 수 있도록 도와줍니다.

    #### 예시로 설명한 코드에서 어댑터의 역할:

    ```java
    static List<Integer> intArrayAsList(int[] a) {
        return new AbstractList<>() {
            @Override
            public Integer get(int i) {
                return a[i]; // int 배열의 값을 Integer로 변환하여 제공
            }

            @Override
            public Integer set(int i, Integer val) {
                int oldVal = a[i];
                a[i] = val; // Integer 값을 int 배열에 저장
                return oldVal;
            }

            @Override
            public int size() {
                return a.length; // 배열의 크기를 리스트의 크기로 반환
            }
        };
    }
    ```

    이 코드는 **`int[]` 배열**을 받아 \*\*`List<Integer>`\*\*로 변환해줍니다. 즉, **배열을 리스트처럼 다룰 수 있도록 변환**하는 어댑터 역할을 합니다.

    * `int[]` 배열은 기본 자료형을 사용하고, \*\*리스트(List)\*\*는 제네릭 타입을 사용합니다.
    * 이 두 가지는 **호환되지 않는** 자료구조이므로, **어댑터 패턴**을 통해 배열을 리스트로 변환하여 서로 다른 방식으로 데이터를 처리할 수 있게 만들어 줍니다.

    #### 어댑터 패턴의 일반적인 구조:

    1. **클라이언트(Client)**: 기존 클래스의 기능을 사용하려는 코드입니다.
    2. **어댑티(Adaptee)**: 기존 클래스, 즉 변환해야 하는 대상입니다.
    3. **어댑터(Adapter)**: 어댑티의 인터페이스를 클라이언트가 원하는 인터페이스로 변환하는 클래스입니다.

    #### 어댑터 패턴의 간단한 예:

    ```java
    // 기존 클래스
    class OldSystem {
        public void oldMethod() {
            System.out.println("Old method is called");
        }
    }

    // 어댑터 인터페이스
    interface NewSystem {
        void newMethod();
    }

    // 어댑터 클래스
    class Adapter implements NewSystem {
        private OldSystem oldSystem;

        public Adapter(OldSystem oldSystem) {
            this.oldSystem = oldSystem;
        }

        @Override
        public void newMethod() {
            oldSystem.oldMethod(); // 기존 메서드를 새로운 메서드로 변환
        }
    }

    // 클라이언트 코드
    public class Main {
        public static void main(String[] args) {
            OldSystem oldSystem = new OldSystem();
            NewSystem adapter = new Adapter(oldSystem);
            adapter.newMethod(); // 어댑터를 통해 oldMethod()를 호출
        }
    }
    ```

    위 예시에서, `OldSystem`은 기존 시스템을 표현하고 `Adapter`는 이를 `NewSystem` 인터페이스에 맞게 변환해줍니다. 이렇게 하면 `OldSystem`의 코드를 변경하지 않고도 새로운 방식으로 사용할 수 있게 됩니다.
