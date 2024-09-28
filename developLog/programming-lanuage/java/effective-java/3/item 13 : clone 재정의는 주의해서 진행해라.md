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

### 3. Clone() 메서드

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



## 3. Clone() 메서드의 구현

### 1) 가변 상태를 참조하지 않는 클래스용 clone() 메서드

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

### 2) 가변 객체를 참조하는 클래스의 `clone()` 메서드 구현

{% hint style="info" %}
가변객체란, instance 생성 이후에도 내부 상태 변경이 가능한 객체를 말한다.
{% endhint %}

* 이 클래스를 그대로 복제하면, primitive 타입의 값은 올바르게 복제되지만, 복제된 Stack의 elements는 복제 전의 Stack과 같은 배열을 가리키게 될 것이다.
* <mark style="color:red;">두 스택에 같은 elements가 들어있고, 하나를 바꾸면 다른 하나도 연동된다는 뜻이다.</mark> 이건 우리가 원한 clone()이 아니다.
* 예를 들어, `Stack` 클래스의 `elements` 배열이 이에 해당한다.
* `Stack` 클래스의 유일한 생성자를 이용하면 이런 문제는 없을 것이다. 그러나 값이 복사되지 않는 문제가 있다.

**얕은 복사의 문제점**

* 단순히 `super.clone()`을 호출하면 얕은 복사가 이루어져, 객체의 필드 값만 복사되고 그 필드들이 참조하는 객체들은 복사되지 않는다.
* 따라서 가변 객체를 참조하는 필드가 있을 경우, 원본 객체와 복제된 객체가 동일한 가변 객체를 공유하게 된다.
* **문제점**: 원본이나 복제본 중 하나에서 가변 객체를 변경하면 다른 하나에도 영향을 미쳐 불변식이 깨질 수 있다.

**깊은 복사의 필요성**

원본과 복제본이 독립적인 객체 상태를 유지하려면 <mark style="color:red;">가변 객체를 복사해야 한다.</mark> 이를 위해 해당 필드를 깊은 복사하여 새로운 객체를 생성해야 한다.

***

### 3) 깊은 복사를 위한 방법

**배열의 `clone()` 메서드 사용**

배열은 `clone()` 메서드를 호출하면 깊은 복사가 이루어집니다. 즉, 배열 자체가 복사되고 배열의 각 요소들도 복사됩니다. 따라서 배열을 복제할 때는 배열의 `clone()` 메서드를 사용하는 것이 좋습니다.

* **예시:**

```java
@Override
public Stack clone() {
    try {
        // 먼저 super.clone()을 호출하여 이 객체의 얕은 복사(shallow copy)를 수행합니다.
        Stack result = (Stack) super.clone();

        // elements 배열을 복제하여 원본과 복제본이 서로 다른 배열을 참조하도록 합니다.
        result.elements = elements.clone();

        // 복제된 Stack 객체를 반환합니다.
        return result;

    } catch (CloneNotSupportedException e) {
        // 이 예외는 발생하지 않으므로 AssertionError를 던집니다.
        // Cloneable을 구현했기 때문에 super.clone()은 성공해야 합니다.
        throw new AssertionError();
    }
}

```

🧐 **왜 `elements` 배열을 복제하나요?**

* `elements` 배열은 `Stack`의 내부 상태를 저장하는 중요한 가변 객체이다.
* 원본과 복제본이 같은 배열을 공유하면 한쪽에서 변경이 발생할 때 다른 쪽에도 영향을 미친다.
* 이를 방지하기 위해 `elements` <mark style="color:red;">배열을 복제하여 복제본이 독립적인 배열을 가지도록 한다.</mark>

**사용예제**

```java
public class Stack implements Cloneable {
    private Object[] elements;
    private int size = 0;

    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    // 생성자
    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    // push 메서드
    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    // pop 메서드
    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }

    // 내부 배열의 크기 조정
    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }

    // clone 메서드
    @Override
    public Stack clone() {
        try {
            Stack result = (Stack) super.clone();
            result.elements = elements.clone(); // 배열 복제
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
}
```

#### 사용 예시

```java
public class Main {
    public static void main(String[] args) {
        Stack originalStack = new Stack();
        originalStack.push("A");
        originalStack.push("B");

        // 스택 복제
        Stack clonedStack = originalStack.clone();

        // 원본 스택과 복제본 스택이 별개의 객체인지 확인
        System.out.println(originalStack != clonedStack); // true

        // elements 배열이 별개의 객체인지 확인
        System.out.println(originalStack.elements != clonedStack.elements); // true

        // 원본과 복제본의 내용이 같은지 확인
        System.out.println(Arrays.equals(originalStack.elements, clonedStack.elements)); // true

        // 원본 스택에 추가
        originalStack.push("C");

        // 복제본에는 영향이 없는지 확인
        System.out.println(clonedStack.size); // 2
    }
}
```

* **설명:**
  * `originalStack`을 생성하고 "A", "B"를 추가
  * `originalStack`을 복제하여 `clonedStack`을 생성
  * 원본과 복제본이 서로 다른 객체이고, 내부의 `elements` 배열도 다른 객체임을 확인
  * 원본 스택에 "C"를 추가해도 복제본에는 영향이 없다.

**테스트 결과:**

```csharp
stack1 = [value1, value2, null, null, null, null, null, null, null, null, null, null, null, null, null, null]
stack2 = [value1, value2, value3, null, null, null, null, null, null, null, null, null, null, null, null, null]
```

* `stack1`과 `stack2`는 서로 다른 `elements` 배열을 가지고 있으며, 독립적으로 동작한다.

**요약**

* `clone()` 메서드를 재정의할 때는 `super.clone()`을 호출하여 객체의 얕은 복사를 수행한다.
* 가변 객체를 참조하는 필드는 반드시 복제하여 원본과 복제본이 독립적인 객체 상태를 유지하도록 한다.
* `CloneNotSupportedException`은 발생하지 않지만 예외 처리를 위해 `try-catch` 블록을 사용하고, 예외가 발생하면 `AssertionError`를 던진다.
* `Cloneable` 인터페이스를 구현해야 `super.clone()`을 호출할 수 있다.

{% hint style="info" %}
😀 위의 내용을 요약하자면
{% endhint %}

* `clone()` 메서드를 재정의할 때는 `super.clone()`을 호출하여 객체의 얕은 복사를 수행한다.
* <mark style="color:red;">가변 객체를 참조하는 필드</mark>는 반드시 복제하여 원본과 복제본이 독립적인 객체 상태를 유지하도록 힌다.
* `CloneNotSupportedException`은 발생하지 않지만 예외 처리를 위해 `try-catch` 블록을 사용하고, 예외가 발생하면 `AssertionError`를 던진다.
* `Cloneable` 인터페이스를 구현해야 `super.clone()`을 호출할 수 있다.

**연결 리스트의 복제 방법**

연결 리스트를 복제할 때는 각 노드를 개별적으로 복사해야 합니다.

**재귀적 복제의 문제점**

재귀적으로 연결 리스트를 복제하면 리스트의 길이만큼 스택 프레임을 사용하므로, 리스트가 길면 스택 오버플로우가 발생할 수 있다.

* **재귀적 복제 코드 예시:**

```java
public class Entry {
    Object key;
    Object value;
    Entry next;

    Entry deepCopy() {
        return new Entry(key, value, next == null ? null : next.deepCopy());
    }
}
```

**반복문을 사용한 복제**

반복문을 사용하면 스택 오버플로우의 위험 없이 연결 리스트를 복제할 수 있다.

* **반복문을 사용한 복제 코드 예시:**

```java
public class Entry {
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

**코드 예시: `HashTable` 클래스의 `clone()` 메서드**

```java
public class HashTable implements Cloneable {
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

### 4) `clone()` 메서드에서 재정의 가능한 메서드 호출 금지

**재정의된 메서드 호출의 문제점**

`clone()` 메서드에서 하위 클래스에서 재정의된 메서드를 호출하면, 하위 클래스는 복제 과정에서 자신의 상태를 적절히 초기화할 수 없게 된다. <mark style="color:red;">이는 원본과 복제본의 상태가 달라질 수 있다는 것을 의미</mark>

**해결책**

`clone()` 메서드 내에서 호출하는 메서드는 `final` 또는 `private`으로 선언하여 재정의되지 않도록 해야 한다.

* **예시:**

```java
@Override
protected Object clone() throws CloneNotSupportedException {
    MyClass result = (MyClass) super.clone();
    result.init(); // init() 메서드는 final 또는 private이어야 함
    return result;
}
```

### **5) `final` 필드와의 충돌 문제**

* 만약 `elements` 필드가 `final`로 선언되어 있다면, `clone()` 메서드에서 새로운 배열을 할당할 수 없어 문제가 발생한다.
* 이는 **가변 객체를 참조하는 필드는 `final`로 선언하라**는 일반적인 규칙과 충돌한다.
* 이러한 경우, `clone()` 메서드를 사용하는 대신 **복사 생성자**를 사용하는 것이 더 바람직하다.

**복사 생성자 사용 예시**

```java
public Stack(Stack original) {
    this.elements = original.elements.clone();
    this.size = original.size;
}
```

* **장점**:
  * `clone()` 메서드보다 구현이 간단하고 명확하다.
  * 예외 처리가 필요 없다.
  * `final` 필드와의 충돌이 없다.

**사용 예시**

```java
java코드 복사Stack stack1 = new Stack();
stack1.push("value1");
stack1.push("value2");

Stack stack2 = new Stack(stack1); // 복사 생성자 사용
stack2.push("value3");
```

* `stack1`과 `stack2`는 독립적인 스택으로 동작한다.

### **6) `HashTable` 클래스의 예시와 문제점**

**잘못된 `clone()` 메서드:**

```java
static class HashTable implements Cloneable {
    private Entry[] buckets;

    private static class Entry {
        final Object key;
        Object value;
        Entry next;

        public Entry(Object key, Object value, Entry next) {
            this.key = key;
            this.value = value;
            this.next = next;
        }
    }

    @Override
    public HashTable clone() {
        try {
            HashTable result = (HashTable) super.clone();
            result.buckets = buckets.clone(); // buckets 배열만 복제
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
}
```

* `buckets` 배열만 복제하고, 배열 내부의 `Entry` 객체들은 복제되지 않았다.
* **문제점:** 복제된 `HashTable`과 원본 `HashTable`이 같은 `Entry` 객체들을 공유하게 된다.

### **7) `HashTable` 클래스의 올바른 `clone()` 메서드 구현**

**깊은 복사를 위한 `clone()` 메서드:**

```java
static class HashTable implements Cloneable {
    private Entry[] buckets;

    private static class Entry {
        final Object key;
        Object value;
        Entry next;

        public Entry(Object key, Object value, Entry next) {
            this.key = key;
            this.value = value;
            this.next = next;
        }

        // 연결 리스트를 깊은 복사하는 메서드
        Entry deepCopy() {
            Entry result = new Entry(key, value, next);
            Entry p = result;
            while (p.next != null) {
                p.next = new Entry(p.next.key, p.next.value, p.next.next);
                p = p.next;
            }
            return result;
        }
    }

    @Override
    public HashTable clone() {
        try {
            HashTable result = (HashTable) super.clone();
            result.buckets = new Entry[buckets.length];

            for (int i = 0; i < buckets.length; i++) {
                if (buckets[i] != null) {
                    result.buckets[i] = buckets[i].deepCopy(); // 각 버킷의 연결 리스트를 복제
                }
            }

            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
}
```

* `deepCopy()` 메서드를 통해 각 버킷의 연결 리스트를 복제한다.
* **결과:** 복제된 `HashTable`은 원본과 독립적인 `Entry` 객체들을 가지게된다.

### **8)  `deepCopy()` 메서드의 동작 원리**

* **반복문 사용:** 재귀 호출 대신 반복문을 사용하여 스택 오버플로우를 방지
* **새로운 `Entry` 객체 생성:** 연결된 각 `Entry`를 새로운 객체로 생성하여 복사한다.
* **원본과 복제본의 독립성 보장:** 복제된 객체들은 원본 객체들과 독립적으로 동작한다.

### **9) 생성자에서 재정의 가능한 메서드 호출 금지**

* <mark style="color:red;">생성자에서는 재정의될 수 있는 메서드를 호출하면 안 됩니다.</mark>
* 만약 `put()` 메서드를 이용하여 복제한다면, `put()` 메서드는 `final` 또는 `private`으로 선언되어야 합니다.
* 이는 하위 클래스에서 메서드를 재정의하여 예상치 못한 동작이 발생하는 것을 방지합니다.

***



## **4. `Cloneable`의 문제점 요약**

* **언어적인 모순과 위험성**: `Cloneable`과 `clone()` 메서드는 직관적이지 않고 구현하기 어렵다.
* **얕은 복사의 기본 동작**: 기본적으로 얕은 복사가 이루어져 가변 객체를 제대로 복제하지 못한다.
* **`final` 필드와의 충돌**: `final` 필드는 복사 후 값을 변경할 수 없어 복제 시 문제가 발생한다.
* **예외 처리의 복잡성**: `CloneNotSupportedException` 등 불필요한 예외 처리가 필요
* **재정의된 메서드 호출 문제**: `clone()` 메서드에서 재정의된 메서드를 호출하면 문제가 발생한다.



## 5. Clone() 메서드 주의사항

* `Object.clone()`은 동기화를 신경쓰지 않은 메서드이다.
  * 동시성 문제가 발생할 수 있다.
* 만일 `clone()`을 막고 싶다면 `clone()` 메서드를 재정의하여, `CloneNotSupportedException()`을 던지도록 하자.
* 기본 타입이나 불변 객체 참조만 가지면 아무것도 수정할 필요 없으나 `일련번호` 혹은 `고유 ID`와 같은 값을 가지고 있다면, 비록 불변일지라도 새롭게 수정해줘야 함

## 6. 복사 생성자 및 복사 팩터리 메서드

{% hint style="info" %}
복사 생성자란 단순히 자신과 같은 클래스의 인스턴스를 인수로 받는 생성자를 말한다.
{% endhint %}

`Cloneable` 인터페이스와 `clone()` 메서드 대신 **복사 생성자**와 **복사 팩터리 메서드**를 사용하는 것이 좋다.

* **복사 팩터리 메서드**: 복사 생성자와 유사하지만 정적 팩터리 메서드로 구현된다.
* Cloneable을 이미 구현한 클래스를 확장한다면 어쩔 수 없이 clone을 잘 작동하도록 구현해야 한다. 그렇지 않은 상황에서는 `복사 생성자와 복사 팩터리`라는 더 나은 객체 복사 방식을 제공할 수 있다.

### **복사 생성자**

```java
public class Yum {
    private int value;

    // 복사 생성자
    public Yum(Yum yum) {
        this.value = yum.value;
    }
}
```

### **복사 팩터리 메서드**

```java
public class Yum {
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

### **복사 생성자와 복사 팩터리의 장점**

* **직관적인 객체 생성**: 생성자나 정적 메서드를 사용하므로 객체 생성 방식이 명확
* **안전성**: `Cloneable`의 복잡한 규약에 의존하지 않으므로 구현 실수의 위험이 적다.
* **유연성**: 필요한 경우 복사 과정에서 필드를 변환하거나 수정할 수 있다.
* **예외 처리 간소화**: 불필요한 예외를 던지지 않으므로 사용하기 편리합
* **형변환 불필요**: 반환 타입이 명확하므로 형변환할 필요가 없다.

### 복사 생성자랑 복사 팩터리로 clone() 구현하기

```java
/**
 * Constructs a new {@code HashMap} with the same mappings as the
 * specified {@code Map}.  The {@code HashMap} is created with
 * default load factor (0.75) and an initial capacity sufficient to
 * hold the mappings in the specified {@code Map}.
 *
 * @param   m the map whose mappings are to be placed in this map
 * @throws  NullPointerException if the specified map is null
 */
public HashMap(Map<? extends K, ? extends V> m) {
    this.loadFactor = DEFAULT_LOAD_FACTOR;
    putMapEntries(m, false);
}
```

* 위는 `HashMap`에서 제공하는 복사 생성자로 볼 수 있다.

> 더 정확한 이름은 `변환 생성자(conversion constructor)`와 `변환 팩터리(conversion factory)`이다.

### ArrayList

#### 변환 생성자 예시: `ArrayList`

이 방식은 `clone()` 메서드보다 여러 면에서 더 낫습니다. `ArrayList`는 `clone()` 메서드를 사용하기보다, 다른 컬렉션의 요소를 받아들이는 생성자를 제공한다.

**주석이 포함된 코드 예제:**

```java
/**
 * 지정된 컬렉션의 요소를 순서대로 포함하는 리스트를 생성합니다.
 *
 * @param c 이 리스트에 추가될 요소를 포함하는 컬렉션
 * @throws NullPointerException 지정된 컬렉션이 null인 경우
 */
public ArrayList(Collection<? extends E> c) {
    // 컬렉션의 요소를 배열로 변환합니다.
    elementData = c.toArray();
    // 리스트의 크기를 설정합니다.
    size = elementData.length;
    // c.toArray()가 Object[]를 반환하지 않을 수 있으므로, 배열의 타입을 Object[]로 보장합니다.
    if (elementData.getClass() != Object[].class)
        elementData = Arrays.copyOf(elementData, size, Object[].class);
}
```

**설명:**

* **목적**: 이 생성자는 지정된 컬렉션 `c`의 모든 요소를 포함하는 새로운 `ArrayList` 인스턴스를 생성한다다. 요소들은 컬렉션의 이터레이터가 반환하는 순서를 따른다.
* **자세한 장점:**
  1. **클론 메커니즘의 문제 회피**:
     * `clone()` 메서드는 얕은 복사로 인해 가변 객체를 올바르게 복제하지 못하는 등의 문제가 있다.
     * 생성자를 사용하면 복제 과정에서 완전한 통제를 할 수 있다.
  2. **인터페이스 타입 수용**:
     * `Collection<? extends E>`를 인수로 받으므로, `List`, `Set` 등 다양한 컬렉션을 인수로 받을 수 있다.
     * 이를 통해 생성자의 활용 범위가 넓어짐
  3. **형변환 불필요**:
     * 내부적으로 타입 변환을 처리하므로, 호출 시에 별도의 형변환이 필요 없다.
     * 런타임 시 `ClassCastException`이 발생할 위험을 줄인다.
  4. **검사 예외 처리 간소화**:
     * `clone()` 메서드는 `CloneNotSupportedException`을 처리해야 하지만, 이 생성자는 그런 예외를 던지지 않아 사용이 간편
  5. **`final` 필드의 올바른 초기화**:
     * 생성자를 사용하면 `final` 필드를 적절히 초기화할 수 있지만, `clone()`은 생성자를 호출하지 않으므로 `final` 필드 초기화에 문제가 생길 수 있다.

**사용 예시**

```java
// 원본 컬렉션
Collection<String> originalCollection = Arrays.asList("사과", "바나나", "체리");

// 변환 생성자를 사용하여 새로운 ArrayList 생성
ArrayList<String> fruitList = new ArrayList<>(originalCollection);

// 새로운 리스트에 요소 추가
fruitList.add("데이트");

// 원본 컬렉션은 변경되지 않음
System.out.println("원본 컬렉션: " + originalCollection); // [사과, 바나나, 체리]
System.out.println("과일 리스트: " + fruitList); // [사과, 바나나, 체리, 데이트]
```

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



\++ native 메서드

native: 메서드는 C나 C++ 같은 네이티브 프로그래밍 언어로 작성한 메서드를 의미한다. Java Native Interface(JNI)라고 부른다. 즉, C나 C++의 코드를 자바에서 불러 사용하려면 native 메서드를 정의해서 메서드 바디를 갖지 않는 메서드를 구현한다. (메서드 바디가 dll (Unix에선 so) 파일로 되어 있어 런타임 시에 dll 파일을 System.loadLibrary 메서드가 수행하여 classpath 경로에서 파라미터의 파일을 메모리에 로딩)



> 참고 및 일부 가져오기 : [https://jake-seo-dev.tistory.com/31#%EB%B-%B-%EC%--%AC%--%EC%--%-D%EC%--%B-%EC%-E%--%EC%--%--%--%EB%B-%B-%EC%--%AC%--%ED%-C%A-%ED%--%B-%EB%A-%AC%EB%A-%-C%--clone--%--%EA%B-%AC%ED%--%--%ED%--%--%EA%B-%B-](https://jake-seo-dev.tistory.com/31#%EB%B-%B-%EC%--%AC%--%EC%--%-D%EC%--%B-%EC%-E%--%EC%--%--%--%EB%B-%B-%EC%--%AC%--%ED%-C%A-%ED%--%B-%EB%A-%AC%EB%A-%-C%--clone--%--%EA%B-%AC%ED%--%--%ED%--%--%EA%B-%B-)

[^1]: AssertionError란 무엇인가요?

    `AssertionError`는 자바에서 에러(Error)의 한 종류로, 주로 프로그램의 논리적인 오류가 발생했을 때 사용됩니다. 이 에러는 일반적으로 개발자가 예측할 수 없는 상황이 발생했을 때 이를 알리기 위해 던집니다.

    **1. AssertionError의 목적**

    * **개발 중에만 사용**: `AssertionError`는 주로 개발 및 디버깅 단계에서 프로그램의 내부 상태를 검증하기 위해 사용됩니다.
    * **불가능한 상황을 나타냄**: 코드에서 절대로 발생해서는 안 되는 상황이 발생했을 때 이를 알리기 위해 사용합니다.
    * **예외 처리의 간소화**: 검사 예외(checked exception)를 처리해야 하는 번거로움을 피하기 위해 사용됩니다.
