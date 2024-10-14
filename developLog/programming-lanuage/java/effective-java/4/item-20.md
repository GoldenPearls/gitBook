# item 20 :추상클래스보다는 인터페이스를 고려하라

## 1. 자바에서의다중 구현 매커니즘

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



### 4) 인터페이스는 기능을 향상 시키는 안전하고 강력한 수단이 된다.



## **✨** 최종 정리

{% hint style="success" %}
일반적으로 다중 구현용 타입으로는 **인터페이스**가 가장 적합하다.
{% endhint %}

복잡한 인터페이스라 면 구현하는 수고를 덜어주는 골격 구현을 함께 제공하는 방법을 꼭 고려해보자. 골격 구현은 `가능한 한` 인터페이스의 **디폴트 메서드로 제공**하여 그 인터페이스를 구현한 모든 곳에서 활용하도록 하는 것이 좋다. `가능한 한`이라고 한 이유는, 인터페이스에 걸려 있는 <mark style="color:red;">구현상의 제약 때문에 골격 구현을 추상 클래스로 제공하는 경우가 더 흔하기 때문</mark>이다.
