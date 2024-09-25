---
description: 이번 아이템은 clone()을 사용할 때 주의점을 다룬다.
---

# item 13 : clone 재정의는 주의해서 진행해라

## 1. Cloneable 인터페이스

### Cloneaable 인터페이스란?

* 일종의 `maker interface`로 'cloen에 의해 복제할 수 있다'를 표시하는 인터페이스이다.
* 복제해도 되는 클래스임을 명시하는 용도의 믹스인 인터페이스

Java에서는 인스턴스의 복제를 위해 `clone()` 메서드가 구현되어 있다.

신기하게도 이 메서드는 `Cloneable` 내부에 구현되어 있을 거란 예상을 깨고 `java.lang.Object` 클래스에 protected 접근 지정자로 구현되어 있다. 내부에는 구현해야 할 메서드가 하나도 없다!

### Cloneaable 인터페이스 하는 일

* Object의 <mark style="color:red;">protected 메서드인 clone의 동작 방식</mark>을 결정한다.
* Cloneable의 경우, 일반적으로 인터페이스를 구현한다는 것 즉, 해당 클래스가 그 인터페이스에서 정의한 기능을 제공한다고 선언하는 것과 달리 상위 클래스에 정의된 <mark style="color:red;">protected 메서드의 동작방식을 변경한 것</mark>

#### **사용법**

```java
public class Pokemon implements Cloneable {
    private List<String> skills;
    private int attack;
    private int defense;
    private int hp;

    public Pokemon(List<String> skills, int attack, int defense, int hp) {
        this.skills = skills;
        this.attack = attack;
        this.defense = defense;
        this.hp = hp;
    }

    @Override // clone() 구현..!!
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    // getter, seterr ...
}
```

복사할 객체에 `Cloneable`을 상속시켜 준 뒤 오버라이딩해 주면 된다.

`clone()`을 호출하면 인스턴스와 같은 크기의 메모리를 할당하고, <mark style="background-color:red;">인스턴스의 필드를 그대로 복사한 복사본을 리턴</mark>한다.

### Clonable 인터페이스의 문제점

1. **복잡성과 위험성**

* `Cloneable`과 `clone()` 메서드는 객체 복사 메커니즘이 직관적이지 않고, 잘못 구현하면 오류가 발생하기 쉽다.
* 가변 객체의 복사, `final` 필드와의 충돌, 예외 처리 등 여러 가지 문제가 있다.

## 2. 재 정의시 문제점

> 🤔 Cloneable을 구현한 클래스는 clone 메서드를 public으로 제공하며, 사용자는 당연히 복제가 제대로 이뤄지겠지? 라고 실무에서는 기대

이 기대를 만족시키려면 그 클래스와 모든 상위 클래스는 복잡 하고, 강제할 수 없고, 허술하게 기술된 프로토콜을 지켜야만 하는데, 그 결과 로 <mark style="color:red;">깨지기 쉽고, 위험하고, 모순적인 메커니즘이 탄생</mark>한다. 생성자를 호출하지 않고도 객체를 생성할 수 있게 되는 것이다.

### 3. Clone() 메서드의 구현 방법

### 1) clone() 메서드의 일반 규약

> 약간 허술하다고 함

1. x.clone() ! =x 식은 참이어야 한다.

* 복사된 객체와 원본이랑 <mark style="color:red;">같은 주소를 가지면 안된다는 뜻</mark>

2. x.clone().getClass() == x.getClass() 식도 참이어야 한다.

* **복사된 객체가 같은 클래스**여야 한다는 소리임

3. x.clone().equals(x) 참이어야 하지만 필수는 아니다.

* 복사된 객체가 논리적 동치는 일치해야 한다고 함

> 반환된 객체와 원본 객체는 독립적이어야 한다. 이를 만족하려면 super.clone으로 얻은 객체의 필드 중 하나 이상을 반환 전에 수정해야 할 수도 있다.

### 2) `super.clone()`의 중요성

**`super.clone()`을 호출해야 하는 이유**

`clone()` 메서드를 재정의할 때 가장 먼저 해야 할 일은 `super.clone()`을 호출하는 것이다. 이는 `Object` 클래스의 `clone()` 메서드를 통해 객체의 필드 값을 복사한 새로운 객체를 생성하기 위함이다.

```java
@Override
public Object clone() throws CloneNotSupportedException {
    return super.clone();
}
```

**하위 클래스에서의 문제점**

만약 `super.clone()`을 호출하지 않고 <mark style="color:red;">생성자를 사용하여 인스턴스를 반환한다면,</mark> 하위 클래스에서 `super.clone()`을 호출할 때 올바른 클래스의 객체가 생성되지 않을 수 있다. 이는 하위 클래스의 `clone()` 메서드가 제대로 동작하지 않게 만들 수 있다.

* **예시**: 상위 클래스에서 `super.clone()`을 호출하지 않고 새로운 객체를 생성하면, 하위 클래스에서 `super.clone()`을 호출해도 상위 클래스의 인스턴스가 반환되어 하위 클래스의 필드가 초기화되지 않을 수 있다.

**`final` 클래스의 경우**

클래스가 `final`이라면 하위 클래스가 없으므로 이 규칙을 무시해도 안전하다. 그러나 이 경우에도 `Cloneable`을 구현할 필요가 없다. `Object`의 `clone()` 메서드를 사용하지 않기 때문이다.



### 3) clone() 메서드의 구현 순서

1. `super.clone()`을 호출한다.

* 정의된 모든 필드는 <mark style="color:red;">원본 필드와 똑같은 값을 갖게 된다.</mark>
* 모든 필드가 primitive 타입이거나 final 이라면, 이 상태로 끝이다.
* 사실 final은 쓸데 없는 복사를 지양하기 때문에 **clone()이 필요 없다.**

```java
@Override
public PhoneNumber clone() {
    try {
        return (PhoneNumber) super.clone();
    } catch (CloneNotSupportedException e) {
        throw new AssertionError(); // 일어날 수 없는 일이다.
    }
}
```

* PhoneNumber 타입으로 형변환하여 반환하도록 해서 편의성을 증대시켰다.
* try-catch 블록으로 감싼 이유는 Object.clone() 메서드가 `CloneNotSupportedException`을 던지기 때문이다.

> 우린 PhoneNumber 클래스가 Cloneable 인터페이스를 상속받는 것을 보고 clone()을 지원함을 알 수 있다.

**CloneNotSupportedException 예외 처리**

* `Object`의 `clone()` 메서드는 `CloneNotSupportedException`을 다.
* `Cloneable`을 구현한 클래스에서 `clone()` 메서드를 재정의할 때는 이 예외를 던질 필요가 없습니다.
* 대신 `try-catch` 블록으로 감싸고, 예외가 발생할 수 없음을 보장하기 위해 [`AssertionError`](#user-content-fn-1)[^1]를 던진다.
* 과도한 검사 예외는 API를 사용하기 불편하게 만든다.

### 4) 가변 객체를 참조하는 클래스의 `clone()` 메서드 구현

**얕은 복사의 문제점**

단순히 `super.clone()`을 호출하면 얕은 복사가 이루어져, 객체의 필드 값만 복사되고 그 필드들이 참조하는 객체들은 복사되지 않는다. 따라서 가변 객체를 참조하는 필드가 있을 경우, 원본 객체와 복제된 객체가 동일한 가변 객체를 공유하게 된다.

* **문제점**: 원본이나 복제본 중 하나에서 가변 객체를 변경하면 다른 하나에도 영향을 미쳐 불변식이 깨질 수 있다.

**깊은 복사의 필요성**

원본과 복제본이 독립적인 객체 상태를 유지하려면 <mark style="color:red;">가변 객체를 복사해야 한다.</mark> 이를 위해 해당 필드를 깊은 복사하여 새로운 객체를 생성해야 한다.

***

#### 4. 깊은 복사를 위한 방법

**4.1 배열의 `clone()` 메서드 사용**

배열은 `clone()` 메서드를 호출하면 깊은 복사가 이루어집니다. 즉, 배열 자체가 복사되고 배열의 각 요소들도 복사됩니다. 따라서 배열을 복제할 때는 배열의 `clone()` 메서드를 사용하는 것이 좋습니다.

* **예시:**

```java
java코드 복사public class Stack implements Cloneable {
    private Object[] elements;
    private int size;

    @Override
    public Stack clone() {
        try {
            Stack result = (Stack) super.clone();
            result.elements = elements.clone(); // 배열 복사
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError(); // 발생하지 않음
        }
    }
}
```

**4.2 연결 리스트의 복제 방법**

연결 리스트를 복제할 때는 각 노드를 개별적으로 복사해야 합니다.

**4.2.1 재귀적 복제의 문제점**

재귀적으로 연결 리스트를 복제하면 리스트의 길이만큼 스택 프레임을 사용하므로, 리스트가 길면 스택 오버플로우가 발생할 수 있습니다.

* **재귀적 복제 코드 예시:**

```java
java코드 복사public class Entry {
    Object key;
    Object value;
    Entry next;

    Entry deepCopy() {
        return new Entry(key, value, next == null ? null : next.deepCopy());
    }
}
```

**4.2.2 반복문을 사용한 복제**

반복문을 사용하면 스택 오버플로우의 위험 없이 연결 리스트를 복제할 수 있습니다.

* **반복문을 사용한 복제 코드 예시:**

```java
java코드 복사public class Entry {
    Object key;
    Object value;
    Entry next;

    Entry deepCopy() {
        Entry result = new Entry(key, value, null);
        Entry p = result;
        Entry q = this.next;
        while (q != null) {
            p.next = new Entry(q.key, q.value, null);
            p = p.next;
            q = q.next;
        }
        return result;
    }
}
```

**4.3 코드 예시: `HashTable` 클래스의 `clone()` 메서드**

```java
java코드 복사public class HashTable implements Cloneable {
    private Entry[] buckets;

    @Override
    public HashTable clone() {
        try {
            HashTable result = (HashTable) super.clone();
            result.buckets = new Entry[buckets.length];
            for (int i = 0; i < buckets.length; i++) {
                if (buckets[i] != null)
                    result.buckets[i] = buckets[i].deepCopy();
            }
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError(); // 발생하지 않음
        }
    }
}
```

* 각 버킷의 연결 리스트를 복제하여 원본과 복제본이 독립적인 상태를 유지합니다.

***

#### 5. `clone()` 메서드에서 재정의 가능한 메서드 호출 금지

**5.1 재정의된 메서드 호출의 문제점**

`clone()` 메서드에서 하위 클래스에서 재정의된 메서드를 호출하면, 하위 클래스는 복제 과정에서 자신의 상태를 적절히 초기화할 수 없게 됩니다. 이는 원본과 복제본의 상태가 달라질 수 있다는 것을 의미합니다.

**5.2 해결책**

`clone()` 메서드 내에서 호출하는 메서드는 `final` 또는 `private`으로 선언하여 재정의되지 않도록 해야 합니다.

* **예시:**

```java
java코드 복사@Override
protected Object clone() throws CloneNotSupportedException {
    MyClass result = (MyClass) super.clone();
    result.init(); // init() 메서드는 final 또는 private이어야 함
    return result;
}
```

***

#### 6. `Cloneable` 인터페이스의 문제점과 대안

**6.1 `Cloneable`의 문제점 요약**

* **언어적인 모순과 위험성**: `Cloneable`과 `clone()` 메서드는 직관적이지 않고 구현하기 어렵습니다.
* **얕은 복사의 기본 동작**: 기본적으로 얕은 복사가 이루어져 가변 객체를 제대로 복제하지 못합니다.
* **`final` 필드와의 충돌**: `final` 필드는 복사 후 값을 변경할 수 없어 복제 시 문제가 발생합니다.
* **예외 처리의 복잡성**: `CloneNotSupportedException` 등 불필요한 예외 처리가 필요합니다.
* **재정의된 메서드 호출 문제**: `clone()` 메서드에서 재정의된 메서드를 호출하면 문제가 발생합니다.

**6.2 복사 생성자와 복사 팩터리 메서드 소개**

`Cloneable` 인터페이스와 `clone()` 메서드 대신 **복사 생성자**와 **복사 팩터리 메서드**를 사용하는 것이 좋습니다.

* **복사 생성자**: 같은 클래스의 인스턴스를 인수로 받아 새로운 인스턴스를 생성하는 생성자입니다.
* **복사 팩터리 메서드**: 복사 생성자와 유사하지만 정적 팩터리 메서드로 구현됩니다.

**6.3 코드 예시: 복사 생성자 및 복사 팩터리 메서드**

**6.3.1 복사 생성자**

```java
java코드 복사public class Yum {
    private int value;

    // 복사 생성자
    public Yum(Yum yum) {
        this.value = yum.value;
    }
}
```

**6.3.2 복사 팩터리 메서드**

```java
java코드 복사public class Yum {
    private int value;

    private Yum(int value) {
        this.value = value;
    }

    // 복사 팩터리 메서드
    public static Yum newInstance(Yum yum) {
        return new Yum(yum.value);
    }
}
```

**6.4 복사 생성자와 복사 팩터리의 장점**

* **직관적인 객체 생성**: 생성자나 정적 메서드를 사용하므로 객체 생성 방식이 명확합니다.
* **안전성**: `Cloneable`의 복잡한 규약에 의존하지 않으므로 구현 실수의 위험이 적습니다.
* **유연성**: 필요한 경우 복사 과정에서 필드를 변환하거나 수정할 수 있습니다.
* **예외 처리 간소화**: 불필요한 예외를 던지지 않으므로 사용하기 편리합니다.
* **형변환 불필요**: 반환 타입이 명확하므로 형변환할 필요가 없습니다.

## **✨ 결론**

> 객체 복사를 필요로 할 때는 `Cloneable`과 `clone()` 메서드보다는 **복사 생성자**나 **복사 팩터리 메서드**를 사용하는 것이 더 안전하고 권장되는 방법

`Cloneable`이 몰고 온 모든 문제를 되짚어봤을 때, <mark style="color:red;">새로운 인터페이스를 만들 때는 절대 Cloneable을 확장해서는 안 되며, 새로운 클래스도 이를 구현해서는 안 된다.</mark>&#x20;

* **새로운 클래스나 인터페이스를 만들 때 `Cloneable`을 구현하거나 확장하지 말아라**
  * `Cloneable`은 많은 문제점을 가지고 있어 사용을 권장하지 않는다.
* **복제 기능은 복사 생성자나 복사 팩터리 메서드를 사용해라**
  * 더 안전하고 유연하며, 이해하기 쉽다.
  * 이들은 코드가 명확하고 이해하기 쉬우며, `final` 필드와도 충돌하지 않고, 불필요한 예외 처리나 형변환이 필요하지 않는다.
* **배열은 예외**
  * 배열은 `clone()` 메서드를 사용하여 복제하는 것이 가장 깔끔하고 효율적

[^1]: AssertionError란 무엇인가요?

    `AssertionError`는 자바에서 에러(Error)의 한 종류로, 주로 프로그램의 논리적인 오류가 발생했을 때 사용됩니다. 이 에러는 일반적으로 개발자가 예측할 수 없는 상황이 발생했을 때 이를 알리기 위해 던집니다.

    **1. AssertionError의 목적**

    * **개발 중에만 사용**: `AssertionError`는 주로 개발 및 디버깅 단계에서 프로그램의 내부 상태를 검증하기 위해 사용됩니다.
    * **불가능한 상황을 나타냄**: 코드에서 절대로 발생해서는 안 되는 상황이 발생했을 때 이를 알리기 위해 사용합니다.
    * **예외 처리의 간소화**: 검사 예외(checked exception)를 처리해야 하는 번거로움을 피하기 위해 사용됩니다.
