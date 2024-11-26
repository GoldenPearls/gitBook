# item 24 : 멤버 클래스는 되도록 static으로 만들라

## **1. 중첩 클래스**

{% hint style="success" %}
각각의 중첩 클래스를 언제 그리고 왜 사용해야 하는지를 알아야 함
{% endhint %}

### **1) 중첩 클래스란?(**&#x6E;ested class)

다른 클래스 안에 정의된 클래스를 말한다. 중첩 클래스는 **자신을 감싼 바깥 클래스에서만 쓰여야 하며**, 그 외의 쓰임새가 있다면 `톱레벨 클래스`로 만들어야 한다.

### 2) 중첩 클래스의 종류

1. 정적 멤버 클래스 : 외부 클래스
2. (비정적) 멤버 클래스 : 내부 클래스(inner class)
3. 익명 클래스 : 내부 클래스(inner class)
4. 지역 클래스 : 내부 클래스(inner class)

## 2. 정적 멤버 클래스

### 1) 정적 멤버 클래스&#x20;

* **정적 멤버 클래스**는 외부 클래스와 독립적으로 존재할 수 있다. 즉, **외부 클래스의 인스턴스 없이도** 정적 멤버 클래스의 객체를 만들 수 있다.
* 정적 멤버 클래스는 **바깥 클래스의 인스턴스에 접근하지 못한다**. 이는 외부 클래스의 상태와 상관없이 **독립적으로 동작할 수 있는 클래스**라는 것을 의미한다.
* 정적 멤버 클래스는 **다른 정적 멤버와 동일한 규칙을 적용**받는다. 예를 들어 `private`로 선언되면 외부 클래스에서만 접근할 수 있고, `public`으로 선언되면 어디서든 접근 가능히다.
* 주로 외부 클래스와 밀접한 관련이 있지만, 외부 클래스의 인스턴스와 상관없이 동작하는 `도우미 클래스(helper class)`로 사용된다.

```java
public class Calculator {

    // 정적 멤버 클래스
    public static class Operation {
        public static final Operation PLUS = new Operation("+");
        public static final Operation MINUS = new Operation("-");

        private String symbol;

        private Operation(String symbol) {
            this.symbol = symbol;
        }

        public String getSymbol() {
            return symbol;
        }
    }

    public static void main(String[] args) {
        System.out.println(Operation.PLUS.getSymbol()); // +
        System.out.println(Operation.MINUS.getSymbol()); // -
    }
}
```

* 위 예시에서 `Operation`은 `Calculator` 클래스의 **정적 멤버 클래스이**다. `Calculator`의 인스턴스 없이도 `Operation` 클래스의 멤버를 사용할 수 있다. 이를 통해 `Calculator.Operation.PLUS` 형태로 연산을 정의하고 사용할 수 있게 된다.

```java
public class OuterClass {
    private static String secret = "OuterClass Secret";

    // 정적 멤버 클래스
    public static class StaticInnerClass {
        public void revealSecret() {
            // 바깥 클래스의 private 멤버에 접근 가능
            System.out.println("Secret: " + secret);
        }
    }
}

public class Main {
    public static void main(String[] args) {
        OuterClass.StaticInnerClass inner = new OuterClass.StaticInnerClass();
        inner.revealSecret(); // "Secret: OuterClass Secret"
    }
}

```

> 정적 멤버 클래스는 바깥 클래스와 독립적으로 동작할 수 있으며, **외부 클래스의 인스턴스와는 상관없이** 인스턴스화가 가능하다. **바깥 클래스의 `private` 멤버에 접근할 수 있는** 특징을 제외하고는 일반 클래스와 다르지 않으며, 정적 멤버 클래스는 보통 바깥 클래스와 함께 사용된다.

### 2) 정적 멤버 클래스와 일반 클래스

> 정적 멤버 클래스는 **다른 클래스 안에 선언**되고, <mark style="color:red;">바깥 클래스의 private 멤버에도 접근할 수 있다는 점만 제외</mark>하고는 일반 클래스와 똑같다.

#### 1. **일반 클래스와 유사한 특징**

정적 멤버 클래스는 클래스 내부에 선언되지만, 그 외에는 **일반 클래스와 동일한 방식으로 동작한다.**

* **생성자**를 정의할 수 있다.
* **메서드와 필드**를 가질 수 있다.
* **상속**을 할 수 있고, **인터페이스**를 구현할 수 있다.
* **독립적으로 인스턴스를 생성**할 수 있다.

예를 들어, 정적 멤버 클래스도 일반 클래스처럼 인스턴스를 만들 수 있다.

```java
public class OuterClass {
    // 정적 멤버 클래스
    public static class StaticInnerClass {
        public void display() {
            System.out.println("Static Inner Class");
        }
    }
}

public class Main {
    public static void main(String[] args) {
        // 정적 멤버 클래스는 외부 클래스의 인스턴스 없이도 인스턴스를 생성할 수 있다.
        OuterClass.StaticInnerClass inner = new OuterClass.StaticInnerClass();
        inner.display(); // "Static Inner Class"
    }
}
```

위 코드에서 **`StaticInnerClass`는 외부 클래스 `OuterClass`와 별개로** 동작하며, 외부 클래스의 인스턴스 없이도 인스턴스를 생성할 수 있다. 이는 일반적인 독립 클래스처럼 사용된다는 점에서 **일반 클래스와 비슷**하다.

#### 2. **바깥 클래스의 private 멤버 접근 가능**

일반적인 독립 클래스는 **다른 클래스의 `private` 멤버**에 접근할 수 없다. 그러나 **정적 멤버 클래스**는 **외부 클래스 안에 선언되어 있기 때문에** <mark style="color:red;">외부 클래스의</mark> <mark style="color:red;"></mark><mark style="color:red;">`private`</mark> <mark style="color:red;"></mark><mark style="color:red;">멤버에도 접근할 수 있다.</mark>

예를 들어:

```java
public class OuterClass {
    private static String secret = "OuterClass Secret";

    // 정적 멤버 클래스
    public static class StaticInnerClass {
        public void revealSecret() {
            // 바깥 클래스의 private 멤버에 접근 가능
            System.out.println("Secret: " + secret);
        }
    }
}

public class Main {
    public static void main(String[] args) {
        OuterClass.StaticInnerClass inner = new OuterClass.StaticInnerClass();
        inner.revealSecret(); // "Secret: OuterClass Secret"
    }
}
```

위 코드에서 **`StaticInnerClass`는 외부 클래스 `OuterClass`의 `private` 필드인 `secret`에 접근할 수 있다**. 이는 **외부 클래스 내부에 선언된** 정적 멤버 클래스이기 때문에 가능한 동작이다.

일반적으로 **독립적인 클래스**는 다른 클래스의 `private` 멤버에 접근할 수 없지만, **정적 멤버 클래스**는 외부 클래스의 멤버이므로 외부 클래스의 `private` 멤버에도 접근할 수 있는 예외적인 경우이다.

* **정적 멤버 클래스**는 외부 클래스의 일부로 선언되며, **일반 클래스처럼** 독립적으로 동작할 수 있다.
* 그러나 **외부 클래스의 `private` 멤버**에 접근할 수 있다는 점에서 **일반 클래스와 차이**가 있다.

## 3. 비정적 멤버 클래스

### **1) 비정적 멤버 클래스** (Non-static Member Class)

{% hint style="danger" %}
비정적 멤버 클래스는 **외부 클래스의 인스턴스와 암묵적으로 연결**되어, 즉, **외부 클래스의 인스턴스 없이 생성할 수 없다**. 이를 통해 **바깥 클래스의 메서드나 필드에 접근할 수 있는** 강력한 기능을 제공한다. 비정적 멤버 클래스는 바깥 클래스의 인스턴스와 강하게 연결되며, 가비지 컬렉션에서 메모리 누수의 원인이 될 수도 있다.
{% endhint %}

* **비정적 멤버 클래스**는 **외부 클래스의 인스턴스와 연관**되어 있다.&#x20;
* **비정적 멤버 클래스**는 **외부 클래스의 인스턴스**에 접근할 수 있다. 따라서 외부 클래스의 인스턴스 필드와 메서드에 자유롭게 접근할 수 있는 클래스이다.
* 주로 외부 클래스의 상태와 밀접하게 연관된 작업을 수행할 때 사용된다.

```java
public class OuterClass {
    private String message = "Hello from OuterClass!";

    // 비정적 멤버 클래스
    public class InnerClass {
        public void printMessage() {
            System.out.println(message); // 외부 클래스의 필드에 접근 가능
        }
    }

    public static void main(String[] args) {
        OuterClass outer = new OuterClass();
        OuterClass.InnerClass inner = outer.new InnerClass();
        inner.printMessage(); // "Hello from OuterClass!"
    }
}
```

* 위 예시에서 `InnerClass`는 **비정적 멤버 클래스**입니다. **외부 클래스인 `OuterClass`의 인스턴스가 있어야** `InnerClass`의 객체를 만들 수 있다. 또한 `InnerClass`는 `OuterClass`의 필드에 접근할 수 있다.

```java
public class OuterClass {
    private String secret = "OuterClass Secret";

    // 비정적 멤버 클래스
    public class NonStaticInnerClass {
        public void revealSecret() {
            // 바깥 클래스의 private 멤버에 접근 가능
            System.out.println("Secret: " + secret);
        }
    }

    public NonStaticInnerClass getInnerClass() {
        return new NonStaticInnerClass();
    }
}

public class Main {
    public static void main(String[] args) {
        OuterClass outer = new OuterClass();
        OuterClass.NonStaticInnerClass inner = outer.getInnerClass();
        inner.revealSecret(); // "Secret: OuterClass Secret"
    }
}

```

* 바깥 클래스의 인스턴스가 있어야만 인스턴스화 가능.
* 바깥 클래스의 인스턴스와 암묵적으로 연결되어 있다.
* 바깥 인스턴스의 `this` 참조를 통해 바깥 클래스의 필드와 메서드에 접근할 수 있다.

**정적 멤버 클래스와 비정적 멤버 클래스의 구문 차이**

구문상으로는 **정적 멤버 클래스는 `static` 키워드를 붙인다는 차이**만 있지만, 동작 측면에서는 바깥 클래스와의 **연결 여부**가 큰 차이를 만든다.

```java
public class OuterClass {
    public static class StaticInnerClass {
        // 정적 멤버 클래스
    }

    public class NonStaticInnerClass {
        // 비정적 멤버 클래스
    }
}
```



### **2) 정적 멤버 클래스와 비정적 멤버 클래스의 차이점 요약**

| **정적 멤버 클래스**           | **비정적 멤버 클래스**              |
| ----------------------- | --------------------------- |
| 외부 클래스의 인스턴스 없이 생성 가능   | 외부 클래스의 인스턴스가 있어야 생성 가능     |
| 외부 클래스의 인스턴스 멤버에 접근 불가  | 외부 클래스의 인스턴스 멤버에 접근 가능      |
| 주로 **독립적인 도우미 클래스**로 사용 | 외부 클래스와 **밀접하게 연관된 동작**을 수행 |
| `static` 키워드로 선언됨       | `static` 키워드가 없음            |

### **3) 언제 사용하나?**

* **정적 멤버 클래스**는 외부 클래스와 밀접하게 연관되어 있지만, **외부 클래스의 상태에 의존하지 않는 경우**에 주로 사용된다. 예를 들어, 연산자 정의, 상수 집합 등을 나타낼 때 적합하다.
* **비정적 멤버 클래스**는 **외부 클래스의 상태를 참조**하거나 **외부 클래스와 밀접한 상호작용**이 필요한 경우에 사용된다. 예를 들어, 외부 클래스의 필드나 메서드에 접근해야 하는 상황에서 사용된다.

{% hint style="success" %}
**정적 멤버 클래스**는 외부 클래스의 상태와 상관없이 독립적으로 동작할 수 있는 도우미 클래스로 주로 사용되고, **비정적 멤버 클래스**는 외부 클래스의 인스턴스와 밀접하게 연관된 작업을 수행하는 데 사용된다.
{% endhint %}

## 4. **실제 사례**

### **1) 반복자를 구현하는 비정적 멤버 클래스**

`비정적 멤버 클래스`는 **컬렉션의 반복자를 구현**할 때 자주 사용된다. 여기서 **반복자**는 바깥 클래스의 인스턴스 상태를 참조할 수 있어야 하므로 비정적 멤버 클래스로 구현된다.

```java
import java.util.AbstractSet;
import java.util.Iterator;

public class MySet<E> extends AbstractSet<E> {
    private E[] elements;  // MySet 클래스가 관리하는 요소 배열
    private int size;      // MySet에 포함된 요소의 수

    // 생성자: 배열을 받아 MySet을 초기화
    public MySet(E[] elements) {
        this.elements = elements;
        this.size = elements.length;  // 요소 수를 배열의 길이로 설정
    }

    // iterator() 메서드: MyIterator 인스턴스를 반환
    @Override
    public Iterator<E> iterator() {
        return new MyIterator();  // 비정적 멤버 클래스인 MyIterator를 생성하고 반환
    }

    // 비정적 멤버 클래스: 반복자를 구현하는 MyIterator 클래스
    private class MyIterator implements Iterator<E> {
        private int index = 0;  // 반복자가 현재 가리키고 있는 요소의 인덱스

        // hasNext(): 다음 요소가 있는지 여부를 확인
        @Override
        public boolean hasNext() {
            return index < size;  // 현재 인덱스가 size보다 작으면 다음 요소가 존재
        }

        // next(): 다음 요소를 반환
        @Override
        public E next() {
            return elements[index++];  // 현재 인덱스의 요소를 반환하고, 인덱스를 증가
        }
    }

    // size(): MySet에 포함된 요소의 수를 반환
    @Override
    public int size() {
        return size;
    }

    // main 메서드: MySet을 생성하고, 반복자를 사용하여 요소를 출력
    public static void main(String[] args) {
        MySet<String> mySet = new MySet<>(new String[]{"A", "B", "C"});  // String 요소로 구성된 MySet 생성
        Iterator<String> iterator = mySet.iterator();  // MySet의 반복자 생성
        while (iterator.hasNext()) {
            System.out.println(iterator.next());  // 반복자를 사용하여 요소를 하나씩 출력
        }
    }
}

```

* **`MySet<E>` 클래스**: `AbstractSet<E>`를 상속받아 사용자 정의 집합을 구현한 클래스. 내부에서 `E[]` 타입 배열을 사용해 요소들을 관리하며, 크기 정보는 `size`로 저장된다.
* **비정적 멤버 클래스 `MyIterator`**: `MySet` 클래스의 반복자를 구현한 비정적 멤버 클래스. `MyIterator`는 `MySet` 클래스의 인스턴스에 포함된 요소 배열에 접근할 수 있다.
* **중요한 점**: 비정적 멤버 클래스인 `MyIterator`는 `MySet`의 인스턴스 상태(`elements`, `size`)에 직접 접근할 수 있기 때문에 유용하다. 이를 통해 바깥 클래스의 상태와 긴밀하게 연결되어 동작한다.

### **2) 정적 멤버 클래스를 사용하는 사례: Map Entry**

정적 멤버 클래스는 보통 바깥 클래스의 일부 역할을 수행하지만, 바깥 클래스의 상태와는 독립적으로 동작한다. 예를 들어 `Map.Entry`는 `Map`과 관련 있지만 독립적으로 동작해야 하므로 정적 멤버 클래스로 정의된다.

```java
public class MapExample {
    // 정적 멤버 클래스 Entry: key-value 쌍을 저장하는 클래스
    public static class Entry<K, V> {
        private K key;    // 키
        private V value;  // 값

        // 생성자: key와 value를 받아 Entry를 초기화
        public Entry(K key, V value) {
            this.key = key;
            this.value = value;
        }

        // getKey(): 키를 반환
        public K getKey() {
            return key;
        }

        // getValue(): 값을 반환
        public V getValue() {
            return value;
        }

        // toString(): "key=value" 형식으로 출력
        @Override
        public String toString() {
            return key + "=" + value;  // 키와 값을 "=" 기호로 연결하여 문자열로 반환
        }
    }

    // main 메서드: Entry 인스턴스를 생성하고 출력
    public static void main(String[] args) {
        MapExample.Entry<String, Integer> entry = new MapExample.Entry<>("Key", 100);  // "Key", 100 쌍으로 Entry 생성
        System.out.println(entry);  // "Key=100" 출력
    }
}

```

* **정적 멤버 클래스 `Entry`**: 이 클래스는 `MapExample` 클래스의 내부에 선언된 정적 멤버 클래스로, 바깥 클래스 `MapExample`의 상태와는 독립적으로 동작한다. `Entry` 클래스는 키-값 쌍을 관리하며, `Map.Entry`의 개념을 구현한다.
* **중요한 점**: 정적 멤버 클래스는 바깥 클래스의 인스턴스와 관계없이 독립적으로 동작하므로, 바깥 클래스의 상태에 의존하지 않는다. `Entry` 클래스는 `MapExample` 클래스의 상태를 참조하지 않기 때문에 정적 멤버 클래스로 구현된다.

### 3) 정리

* **정적 멤버 클래스**는 바깥 클래스와 독립적으로 동작하며, 메모리와 성능 면에서 더 효율적일 수 있습니다.
* **비정적 멤버 클래스**는 바깥 클래스의 인스턴스와 연결되어 있으며, 바깥 클래스의 상태를 참조해야 할 때 사용됩니다.
* 구문적으로 `static` 키워드가 붙느냐 안 붙느냐 차이만 있지만, 의미상 큰 차이가 있습니다.



## 5. 익명 클래스와 지역 클래스

### 1) **익명 클래스 (Anonymous Class)**

> 익명 클래스는 이름이 없는 클래스이다. 주로 **즉석에서 인스턴스를 생성할 때** 사용된다.

&#x20;이름이 없기 때문에 오직 **선언과 동시에 인스턴스를 생성**할 수 있다. 그리고 특정 인터페이스를 구현하거나, 클래스의 서브 클래스를 한 번에 정의할 수 있지만 **여러 인터페이스를 구현하거나 다른 클래스를 상속할 수는 없다.**

익명 클래스의 특징:

* **이름이 없다**: 인스턴스를 생성하는 동시에 선언되어 사용
* **단일 인터페이스만 구현하거나, 단일 클래스를 상속할 수 있다.**
* **정적 문맥에서 사용할 수 없고, 정적 멤버를 가질 수 없다.**
* 코드의 선언 지점에서만 사용할 수 있다. (재사용 불가능)
* **10줄 이하의 간단한 코드**로 사용하는 것이 좋다. 그렇지 않으면 가독성이 떨어진다.
* **바깥 클래스의 인스턴스를 참조할 수 있다.**

```java
public class AnonymousClassExample {

    interface Greeting {
        void sayHello();
    }

    public static void main(String[] args) {
        // 익명 클래스 사용 예
        Greeting greeting = new Greeting() { // 이름이 없는 클래스 생성
            @Override
            public void sayHello() {
                System.out.println("Hello from Anonymous Class!");
            }
        };
        greeting.sayHello(); // "Hello from Anonymous Class!" 출력
    }
}
```

**특징 설명:**

* `Greeting` 인터페이스를 구현하는 익명 클래스가 생성되고, 그 클래스의 인스턴스가 바로 만들어진다.
* 클래스의 이름이 없기 때문에 선언과 동시에 인스턴스를 사용할 수 있다.
* 코드의 간결성과 일회성 사용을 위해 **익명 클래스**를 사용했다.

### 2) **지역 클래스 (Local Class)**

> 지역 클래스는 메서드 내에 정의되는 클래스이며, **해당 메서드의 지역변수처럼 사용**된다. 지역 변수가 선언될 수 있는 곳이라면 어디에서든 정의할 수 있고, **해당 블록 내에서만 유효**하다.

&#x20;익명 클래스와 달리 **이름이 있어 반복적으로 사용할 수 있지만**, 외부에서 접근할 수 없다.

**지역 클래스의 특징:**

* 메서드 내부에 선언되어 **해당 메서드의 지역변수처럼 사용**된다.
* **이름이 있다**: 반복적으로 사용할 수 있다.
* 바깥 클래스의 멤버에도 접근할 수 있으며, **final 지역 변수**에 접근 가능하다.
* **외부에서는 접근할 수 없다** (지역 클래스는 해당 블록 내에서만 유효).

```java
public class LocalClassExample {

    public void printNumbers() {
        final int factor = 2; // final 지역 변수

        // 지역 클래스
        class Multiplier {
            int multiply(int number) {
                return number * factor; // 바깥 메서드의 final 변수에 접근 가능
            }
        }

        Multiplier multiplier = new Multiplier();
        System.out.println(multiplier.multiply(5)); // 출력: 10
        System.out.println(multiplier.multiply(10)); // 출력: 20
    }

    public static void main(String[] args) {
        LocalClassExample example = new LocalClassExample();
        example.printNumbers();
    }
}
```

**특징 설명:**

* `Multiplier`라는 **지역 클래스**가 `printNumbers` 메서드 내에서 선언되었다.
* 이 클래스는 `factor`라는 **final 변수**에 접근할 수 있다.
* `Multiplier` 클래스는 메서드 내에서 여러 번 인스턴스화할 수 있지만, **해당 메서드 밖에서는 사용할 수 없다.**

### 3) **익명 클래스와 지역 클래스의 차이**

| **특징**        | **익명 클래스**                                 | **지역 클래스**                                 |
| ------------- | ------------------------------------------ | ------------------------------------------ |
| **이름**        | 없음                                         | 있음                                         |
| **사용 가능 범위**  | 선언된 지점에서만 사용 가능 (일회성)                      | 메서드나 블록 내에서 여러 번 인스턴스화 가능                  |
| **바깥 클래스 참조** | 가능                                         | 가능                                         |
| **지역 변수 접근**  | `final` 또는 **effectively final** 지역 변수만 가능 | `final` 또는 **effectively final** 지역 변수만 가능 |
| **재사용성**      | 재사용 불가능                                    | 메서드 내에서 재사용 가능                             |
| **사용 위치**     | 메서드나 표현식 내에서 주로 사용                         | 메서드나 블록 내에서 선언                             |

### 4) **익명 클래스의 제한 사항**

* 여러 인터페이스를 구현할 수 없고, **단 하나의 인터페이스나 클래스**만 상속할 수 있다.
* 익명 클래스는 인스턴스가 생성될 때만 선언되며, **재사용이 불가능**하다.
* 클래스의 이름이 없으므로 `instanceof` 검사나 클래스의 이름이 필요한 작업은 수행할 수 없다.

<mark style="color:red;">익명 클래스</mark>는 주로 **간단한 일회성 사용**에 유리하며, 코드 간결성을 중시할 때 사용된다. 반면, **지역 클래스**는 이름이 있어 여러 번 인스턴스화가 필요할 때 사용된다.

## **✨** 최종 정리

* `중첩 클래스`에는 네 가지가 있으며, 각각의 쓰임이 다르다. 메서드 밖에서도 사용해야 하거나 메서드 안에 정의하기엔 너무 길다면 **멤버 클래스**로 만든다.&#x20;
* 멤버 클래스의 인스턴스 각각이 **바깥 인스턴스를 참조한다면 비정적**으로, **그렇지 않으면 정적**으로 만들자.&#x20;
* **중첩 클래스**가 <mark style="color:red;">한 메서드 안에서만 쓰이면서 그 인스턴스를 생성하는 지점이 단 한 곳이고 해당 타입으로 쓰기에 적합한 클래스나 인터페이스가 이미 있다면</mark> `익명 클래스`로 만들고, 그렇지 않으면 `지역 클래스`로 만들자.
