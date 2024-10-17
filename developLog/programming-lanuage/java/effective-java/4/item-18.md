# item 18 : 상속보다는 컴포지션을 사용하라

## 1. 구현 상속

<mark style="color:red;">일반적인 구체 클래스를 패키지 경계를 넘어, 즉 다른 패키지의 구체 클래스를 상속하는 일은 위험하다.</mark> 상기하자면, 이 책에서의 ‘상속’은 클래스가 다른 클래스를 확장하는  **구현 상속**을 말한다. 이번 아이템 에서 논하는 문제는 클래스가 인터페이스를 구현하거나 인터페이스가 다른 인터페이스를 확장하는 **인터 페이스 상속**과는 무관하다.

### 1)  상속이란

* 한 클래스가 다른 클래스의 속성과 메서드를 확장 혹은 재정의할 수 있도록 해주는 매커니즘

### 2) 상속의 문제점

{% hint style="danger" %}
[상속은 코드를 재사용하는 강력한 수단이지만, 항상 최선은 아니다](https://inpa.tistory.com/entry/OOP-%F0%9F%92%A0-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5%EC%9D%98-%EC%83%81%EC%86%8D-%EB%AC%B8%EC%A0%9C%EC%A0%90%EA%B3%BC-%ED%95%A9%EC%84%B1Composition-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
{% endhint %}

* 강한 결합 : 부모 클래스의 내부 변경이 자식 클래스에 영향을 줄 수 있어 유연성이 저하된다.
* 캡슐화 위반 : 자식 클래스가 부모 클래스의 구현 세부 사항에 의존하게되면, 캡슐화가 약화된다.
* 재사용성 저하 : 특정 구현에 강하게 결합된 상속 구조는 새로운 상황에 재사용하기 어렵다.

### 3) 메서드 호출과 달리 상속은 캡슐화를 깨뜨린다.

> 다르게 말하면, 상위 클래스가 어떻게 구현되느냐에 따라 **하위 클래스의 동작에 이상이 생길 수 있다.**

잘못된 상속의 예

```java
public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0;

    public InstrumentedHashSet() { }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }
}

```

```java
InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
s.addAll(Arrays.asList("틱", "탁탁", "펑"));

System.out.println(s.getAddCount()); // 예상: 3, 실제: 6
```

일반적으로 위 코드 실행 후 `addCount`가 3이 될 것이라 예상할 것이다. 하지만 실제로는 6이다. 이유는 부모 클래스인 `HashSet`의 `addAll` 메서드 안에서 `add`메서드를 호출하기 때문이다.

> `addAll`을 호출하면 내부에서 `add`를 호출하는데 `add`가 상위 클래스의 add를 호출할줄 알았지만 `InstrumentedHashSet`의 `add`를 호출했다.



위에서의  문제는 메서드 재정의 시 당장은 해결할 수 있으나,   HashSet의 `addAll`이 <mark style="color:red;">add 메서드를 이용해 구현했음을 가정한 해법이라는 한계를 지닌다.</mark> 이처럼 자신의 다른 부분을 사용하는 ‘자기사용(self-use)[^1]’ 여부는 해당 클래스의 내부 구현 방식 에 해당하며, 자바 플랫폼 전반적인 정책인지, 그래서 다음 릴리스에서도 유지될지는 알 수 없다. 따라서 이런 가정에 기댄 InstrumentedHashSet[^2]도 깨지기 쉽다.

`addAll` 메서드를 다른 식으로 재정의할 수도 있다. 주어진 컬렉션을 순회하며 원소 하나당 `add` 메서드를 하나만 호출하는 것이다. 조금 나은 방법이지만 상위 클래스의 메서드 동작을 다시 구현하는 것은 어렵고, 비용이 든다. 또한 하위 클래스에서 접근할 수 없는 private 필드를 써야 한다면 이 방식으로는 구현 자체가 불가능하다.

* **상위 클래스의 구현에 의존**: `HashSet`의 내부 구현이 `addAll()`에서 `add()`를 호출한다는 사실에 의존하고 있다.
* **메서드 오버라이딩의 위험성**: 상위 클래스의 메서드를 재정의하면, 상위 클래스의 내부 구현이 변경될 때 하위 클래스의 동작이 예기치 않게 변할 수 있다.

{% hint style="info" %}
그렇다면 위에서의 문제는 다 메서드 재정의 시니까 새로운 메서드를 추가한다면?
{% endhint %}

이 방식이 훨씬 안전한 것은 맞지만, 위험이 전혀 없는 것은 아니다. 다음 릴리스에서 상위 클래스에 새 메서드가 추가됐는데, 하필 하위 클래스에 추가한 메서드와 시그니처가 같고 반환 타입은 다르 다면 여러분의 클래스는 컴파일조차 되지 않을 수 있다.

## 2. 컴포지션(composition)

{% hint style="warning" %}
하나의 클래스가 다른 클래스의 인스턴스를 포함하여, 그 인스턴스의 메서드를 활용하는 방식
{% endhint %}

기존 클래스를 확장하는 대신, **새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 하자.** 상속의 문제의 대안법인 컴포지션은  기존 클래스가 새로운 클래스의 구성요소로 쓰인다는 뜻이다.

새 클래스의 인스턴스 메 서드들은 (private 필드로 참조하는) 기존 클래스의 대응하는 메서드를 호출 해 그 결과를 반환한다. 이 방식을 **전달(forwarding)**이라 하며, 새 클래스의 메서드들을 **전달 메서드(forwarding method)**라 부른다.&#x20;

<mark style="color:red;">그 결과 새로운 클래스는 기존 클래스의 내부 구현 방식의 영향에서 벗어나며, 심지어 기존 클래스에 새로운 메서드가 추가되더라도 전혀 영향받지 않는다.</mark>&#x20;

상속 대신 컴포지션(Composition)을 사용하여 기존 `Set` 인스턴스를 감싸는 래퍼 클래스(Wrapper Class)를 만들어기&#x20;

```java
public class InstrumentedSet<E> implements Set<E> {
    private final Set<E> set;
    private int addCount = 0;

    public InstrumentedSet(Set<E> set) {
        this.set = set;
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return set.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return set.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }

    // 나머지 Set 인터페이스 메서드들은 set 인스턴스에 위임
    @Override
    public int size() {
        return set.size();
    }

    @Override
    public boolean isEmpty() {
        return set.isEmpty();
    }

    // ... 기타 메서드들도 동일하게 위임
}
```

**설명**:

* `InstrumentedSet`은 `Set` 인터페이스를 구현하고, 내부에 실제 작업을 수행할 `Set` 인스턴스를 가진다.
* 모든 메서드는 내부 `set` 객체에 작업을 위임한다.
* `add()`와 `addAll()` 메서드에서 `addCount`를 정확하게 증가시킬 수 있다.
* 상속이 아닌 컴포지션을 사용함으로써 상위 클래스의 내부 구현에 의존하지 않는다.

**사용 예시**:

```java
Set<String> s = new InstrumentedSet<>(new HashSet<>());
s.addAll(Arrays.asList("틱", "탁탁", "펑"));

System.out.println(((InstrumentedSet<String>) s).getAddCount()); // 출력: 3
```

* **안전성**: 상위 클래스의 내부 구현 변화에 영향을 받지 않는다.
* **유연성**: 기존 클래스의 기능을 확장하거나 변경할 때 더 안전하게 구현할 수 있다.
* **재사용성**: 다양한 클래스와 함께 사용할 수 있으며, 기능을 추가하거나 변경하기 쉽다.

> 위임 메서드를 일일이 작성하는 대신, 재사용 가능한 포워딩 클래스를 만들어서 중복 코드를 줄일 수 있다.

```java
public class ForwardingSet<E> implements Set<E> {
    protected final Set<E> s;

    public ForwardingSet(Set<E> s) {
        this.s = s;
    }

    @Override
    public boolean add(E e) { return s.add(e); }

    @Override
    public boolean addAll(Collection<? extends E> c) { return s.addAll(c); }

    // 나머지 메서드들도 동일하게 s에 위임
    @Override
    public int size() { return s.size(); }

    @Override
    public boolean isEmpty() { return s.isEmpty(); }

    // ... 기타 메서드들도 동일하게 위임
}
```

이제 `InstrumentedSet`은 `ForwardingSet`을 상속받아 필요한 기능만 추가하면 됨

```java
public class InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;

    public InstrumentedSet(Set<E> s) {
        super(s);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }
}
```

`InstrumentedSet`은 `HashSet`의 모든 기능을 정의한 `Set` 인터페이스를 활용해 설계되어 견고하고 아주 유연하다. 구체적으로는 Set 인터페이스를 구현했고, Set의 인스턴스를 인수로 받는 생성자를 하나 제공한다. 임의의 Set에 계측 기능을 덧씌어 새로운 Set으로 만드는 것이 이 클래스의 핵심이다. 이 컴포지션 방식은 한 번만 구현해두면 어떠한 Set 구현체라도 계측할 수 있으며, 기존 생성자들도 함께 사용할 수 있다.

```java
Set<Instant> times = new InstrumentedSet<>(new TreeSet<>(cmp));
Set<E> s = new InstrumentedSet<>(new HashSet<>(INIT_CAPACITY));
```

다른 Set 인스턴스를 감싸고 있다는 뜻에서 `InstrumentedSet` 같은 클래스를 래퍼 클래스라 하며, 다른 Set에 계측 기능을 덧씌운다는 뜻에서 데코레이터 패턴이라고 한다. 컴포지션과 전달의 조합은 넓은 의미로 **위임**(delegation)이라고 부른다.

{% hint style="success" %}
상속은 반드시 하위 클래스가 상위 클래스의 '진짜' 하위 타입인 상황에서만 쓰여야 한다. 다르게 말하면, 클래스 B가 클래스 A와 `is-a` 관계일 때만 클래스 A를 상속해야 한다.
{% endhint %}

컴포지션을 써야 할 상황에서 상속을 사용하는 건 내부 구현을 불필요하게 노출하는 꼴이다. 그 결과 API가 내부 구현에 묶이고 그 클래스의 성능도 영원히 제한된다. 더 심각한 문제는 클라이언트가 노출된 내부에 직접 접근할 수 있다는 점이다.

{% hint style="info" %}
래퍼 클래스는 단점이 거의 없다. 한 가지, 래퍼 클래스가 콜백(callback) 프레임워크와는 어울리지 않는다는 점만 주의하면 된다.
{% endhint %}

콜백 프레임워크에서 는 자기 자신의 참조를 다른 객체에 넘겨서 다음 호출(콜백) 때 사용하도록 한다. 내부 객체는 자신을 감싸고 있는 래퍼의 존재를 모르니 대신 자신(this)의 참조를 넘기고, 콜백 때는 래퍼가 아닌 내부 객체를 호출하게 된다. 이를 `SELF 문제`라고 한다.&#x20;

전달 메서드가 성능에 주는 영향이나 래퍼 객체가 **메모리 사용량에 주는 영향**을 걱정하는 사람도 있지만, 실전에서는 둘 다 별다른 영향이 없다고 밝혀졌다. 전달 메서드들을 작성하는 게 재사용할 수 있는 전달 클래스를 인터 페이스당 하나씩만 만들어두 면 원하는 기능을 덧씌우는 전달 클래스들을 아주 손쉽게 구현할 수 있다.

## 3. 상속과 컴포지션의 비교

### 1) 상속(Inheritance)의 예시

**예제: 전자기기(ElectronicDevice)와 스마트폰(Smartphone)**

#### 부모 클래스: `ElectronicDevice`

```java
class ElectronicDevice {
    public void powerOn() {
        System.out.println("전자기기가 켜졌습니다.");
    }

    public void powerOff() {
        System.out.println("전자기기가 꺼졌습니다.");
    }
}
```

* **설명**: `ElectronicDevice` 클래스는 전자기기의 일반적인 동작인 `powerOn()`과 `powerOff()` 메서드를 가지고 있다.

#### 자식 클래스: `Smartphone`

```java
class Smartphone extends ElectronicDevice {
    public void makeCall(String number) {
        System.out.println(number + "로 전화를 겁니다.");
    }

    public void browseInternet() {
        System.out.println("인터넷을 검색합니다.");
    }
}
```

* **설명**: `Smartphone` 클래스는 `ElectronicDevice`를 **상속**받아 전자기기의 기능을 확장한다.
* **관계**: **`Smartphone`은 `ElectronicDevice`이다**라는 **Is-a 관계**를 형성한다.

#### 사용 예시

```java
public class Main {
    public static void main(String[] args) {
        Smartphone myPhone = new Smartphone();
        myPhone.powerOn();            // 전자기기가 켜졌습니다.
        myPhone.makeCall("010-1234-5678"); // 010-1234-5678로 전화를 겁니다.
        myPhone.browseInternet();     // 인터넷을 검색합니다.
        myPhone.powerOff();           // 전자기기가 꺼졌습니다.
    }
}
```

* **결과**: `Smartphone` 객체는 `ElectronicDevice`의 메서드인 `powerOn()`과 `powerOff()`를 그대로 사용할 수 있다.

***

### 2) 컴포지션(Composition)의 예시

**예제: `Battery` 클래스와 `Laptop` 클래스**

#### 독립된 기능을 가진 클래스: `Battery`

```java
class Battery {
    private int capacity; // 배터리 용량 (단위: mAh)

    public Battery(int capacity) {
        this.capacity = capacity;
    }

    public void supplyPower() {
        System.out.println("배터리에서 전원을 공급합니다. 용량: " + capacity + "mAh");
    }

    public void charge() {
        System.out.println("배터리를 충전합니다.");
    }
}
```

* **설명**: `Battery` 클래스는 배터리의 동작을 정의한다.

#### 컴포지션을 사용하는 클래스: `Laptop`

```java
class Laptop {
    private Battery battery;

    public Laptop(int batteryCapacity) {
        this.battery = new Battery(batteryCapacity);
    }

    public void powerOn() {
        battery.supplyPower(); // 위임: Laptop은 Battery의 기능을 사용합니다.
        System.out.println("노트북이 켜졌습니다.");
    }

    public void powerOff() {
        System.out.println("노트북이 꺼졌습니다.");
    }

    public void chargeLaptop() {
        battery.charge(); // Battery의 메서드를 호출
    }
}
```

* **설명**: `Laptop` 클래스는 `Battery` 객체를 **구성요소로 포함**하고 있다.
* **관계**: **`Laptop`은 `Battery`를 가진다**라는 **Has-a 관계**를 형성한다.

#### 사용 예시

```java
public class Main {
    public static void main(String[] args) {
        Laptop myLaptop = new Laptop(5000);
        myLaptop.powerOn();      // 배터리에서 전원을 공급합니다. 용량: 5000mAh
                                  // 노트북이 켜졌습니다.
        myLaptop.chargeLaptop(); // 배터리를 충전합니다.
        myLaptop.powerOff();     // 노트북이 꺼졌습니다.
    }
}
```

* **결과**: `Laptop` 객체는 `Battery` 객체의 기능을 사용하여 동작을 수행한3다.

***

### 3) 상속과 컴포지션의 비교

#### 1. 관계의 차이

* **상속(Inheritance)**: **Is-a 관계**를 나타낸다
* **컴포지션(Composition)**:
  * **Has-a 관계**를 나타냅니다.
  * 한 클래스가 다른 클래스의 객체를 **구성요소로 포함**하고, 그 기능을 사용합니다.
  * 예시: `Laptop`은 `Battery`를 가진다.

#### 2. 코드 재사용 측면

* **상속**:
  * 부모 클래스의 모든 public 및 protected 멤버를 자동으로 상속받습니다.
  * 코드 재사용이 쉽지만, 부모 클래스와 강한 결합이 발생합니다.
* **컴포지션**:
  * 필요한 기능만 선택적으로 사용 가능합니다.
  * 구성요소 클래스의 인터페이스를 통해서만 접근하므로 결합도가 낮습니다.

#### 3. 유연성과 유지보수성

* **상속**:
  * 부모 클래스의 변경이 자식 클래스에 영향을 미칩니다.
  * 부모 클래스의 내부 구현에 자식 클래스가 의존하게 되면, 예기치 않은 버그가 발생할 수 있습니다.
* **컴포지션**:
  * 구성요소의 변경이 상대적으로 덜 영향을 미칩니다.
  * 클래스 간의 결합도가 낮아 유지보수가 용이합니다.

#### 4. 다형성 활용

* **상속**:
  * 자식 클래스는 부모 클래스 타입으로 취급될 수 있어 다형성을 활용할 수 있습니다.
  *   예시:

      ```java
      ElectronicDevice device = new Smartphone();
      device.powerOn(); // 전자기기가 켜졌습니다.
      ```
* **컴포지션**:
  * 다형성보다는 기능 확장이나 조합에 중점을 둡니다.

***

### 4) 🤔 언제 상속과 컴포지션을 사용해야 할까?

#### 상속을 사용해야 하는 경우

* 클래스 간에 **Is-a 관계**가 명확할 때.
* 부모 클래스의 동작을 그대로 물려받아 사용하거나, 동작을 확장해야 할 때.
* 다형성을 적극적으로 활용하여 코드의 유연성을 높이고자 할 때.

#### 컴포지션을 사용해야 하는 경우

* 클래스 간에 **Has-a 관계**가 있을 때.
* 기능을 재사용하고 싶지만, 부모 클래스와 강한 결합을 피하고자 할 때.
* 기존 클래스의 일부 기능만 활용하거나, 내부 구현에 의존하지 않고 안정적인 코드를 작성하고자 할 때.

***

### 5) 상속과 컴포지션의 장단점

#### 상속의 장점

* 코드 재사용이 쉽습니다.
* 다형성을 활용하여 유연한 코드를 작성할 수 있습니다.
* 부모 클래스의 기능을 그대로 사용하거나, 필요한 경우 재정의하여 확장할 수 있습니다.

#### 상속의 단점

* 부모 클래스와 강한 결합이 발생하여, 부모 클래스의 변경이 자식 클래스에 영향을 미칩니다.
* 잘못된 상속 구조는 유지보수성을 떨어뜨리고, 코드의 안정성을 해칠 수 있습니다.
* 자식 클래스가 부모 클래스의 불필요한 기능까지 상속받을 수 있습니다.

#### 컴포지션의 장점

* 클래스 간의 결합도가 낮아 유지보수가 용이합니다.
* 필요한 기능만 선택적으로 사용 가능하며, 코드의 재사용성이 높습니다.
* 내부 구현에 의존하지 않으므로, 구성요소 클래스의 변경에도 안정적입니다.

#### 컴포지션의 단점

* 상속에 비해 구현해야 할 코드가 많아질 수 있습니다.
* 다형성을 활용하기 어려울 수 있습니다.
* 메서드 위임이 필요하여 코드가 장황해질 수 있습니다.

***

#### 추가 예시: 도형(Drawing) 프로그램에서의 상속과 컴포지션

**상속의 예시**: `Shape` 클래스와 `Circle` 클래스

```java
// 부모 클래스: 도형
abstract class Shape {
    public abstract void draw();
}

// 자식 클래스: 원
class Circle extends Shape {
    @Override
    public void draw() {
        System.out.println("원을 그립니다.");
    }
}
```

* **설명**: `Circle`은 `Shape`의 일종이므로 상속을 사용합니다.

**컴포지션의 예시**: `Canvas` 클래스와 `Pen` 클래스

```java
// 독립된 기능을 가진 클래스: 펜
class Pen {
    public void setColor(String color) {
        System.out.println(color + " 색으로 설정합니다.");
    }

    public void drawLine() {
        System.out.println("선을 그립니다.");
    }
}

// 컴포지션을 사용하는 클래스: 캔버스
class Canvas {
    private Pen pen;

    public Canvas() {
        this.pen = new Pen();
    }

    public void drawShape(Shape shape) {
        pen.setColor("검은색");
        shape.draw();
        pen.drawLine();
    }
}
```

* **설명**: `Canvas` 클래스는 `Pen` 객체를 사용하여 도형을 그립니다.
* **관계**: `Canvas`는 `Pen`을 가지고 있으며, `Shape` 객체를 사용하여 그림을 그립니다.

## **✨** 정리&#x20;

* **상속은 is-a 관계일 때만 사용**해야 한다. 클래스 간의 계층 구조를 형성할 때  말이다.  즉, 하위 클래스가 상위 클래스의 진짜 하위 타입인 경우에만 상속을 사용해야 한다. 내부 구현에 의존하거나 강한 결합이 발생할 수 있으므로 주의해야 한다.
* **컴포지션**은 **Has-a 관계**를 나타내며, 클래스의 기능을 유연하게 확장하고 재사용할 수 있다.  **컴포지션과 위임**을 사용하면 상속의 단점을 피하면서 유연하고 안전하게 기능을 확장할 수 있다. 하지만결합도가 낮아 유지보수가 용이하지만, 다형성 활용이 제한적일 수 있다.
* **래퍼 클래스**를 사용하면 기존 클래스의 내부 구현에 의존하지 않고도 기능을 추가하거나 변경할 수 있다.
* 특히, 상위 클래스에 새로운 메서드가 추가되더라도 하위 클래스의 동작에 영향을 주지 않는다.

> 참고 글
>
> * [https://stdio-han.tistory.com/144](https://stdio-han.tistory.com/144)
> * [https://velog.io/@injoon2019/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C-18.-%EC%83%81%EC%86%8D%EB%B3%B4%EB%8B%A4%EB%8A%94-%EC%BB%B4%ED%8F%AC%EC%A7%80%EC%85%98%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC](https://velog.io/@injoon2019/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C-18.-%EC%83%81%EC%86%8D%EB%B3%B4%EB%8B%A4%EB%8A%94-%EC%BB%B4%ED%8F%AC%EC%A7%80%EC%85%98%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)

[^1]: 자기사용(self-use)이란 무엇인가요?

    자기사용(self-use)은 객체 지향 프로그래밍에서 **클래스 내의 메서드가 같은 클래스의 다른 메서드를 호출하여 기능을 구현하는 것**을 의미합니다. 즉, 클래스의 메서드들이 서로를 호출하여 동작을 수행하는 것을 말합니다.

    **예시로 이해하기**

    예를 들어, `HashSet` 클래스에서 `addAll()` 메서드가 `add()` 메서드를 사용하여 구현되어 있다면, 이는 `addAll()` 메서드가 `add()` 메서드를 **자기사용**하고 있다고 말할 수 있습니다.

    ```java
    java코드 복사public class HashSet<E> implements Set<E> {
        // ...

        public boolean addAll(Collection<? extends E> c) {
            boolean modified = false;
            for (E e : c) {
                if (add(e)) { // 여기서 add() 메서드를 호출합니다.
                    modified = true;
                }
            }
            return modified;
        }

        public boolean add(E e) {
            // 원소를 추가하는 로직
        }

        // ...
    }
    ```

    위 코드에서 보듯이, `addAll()` 메서드는 내부적으로 `add()` 메서드를 호출하여 컬렉션의 각 원소를 추가합니다. 이는 `addAll()`이 `add()`를 **자기사용**하고 있는 것입니다.

    #### 왜 자기사용이 문제가 될까요?

    자기사용은 클래스의 내부 구현 방식에 해당하며, 외부에 공개되지 않은 **구현 세부사항**입니다. 따라서 자식 클래스가 부모 클래스의 **자기사용 여부에 의존하여 동작을 구현하면** 다음과 같은 문제가 발생할 수 있습니다.

    1. **내부 구현 변경 시 문제 발생**:
       * 부모 클래스의 내부 구현이 변경되면 자식 클래스의 동작이 의도치 않게 변할 수 있습니다.
       * 예를 들어, 다음 버전에서 `HashSet`의 `addAll()`이 더 이상 `add()`를 사용하지 않고 직접 원소를 추가하도록 구현이 변경된다면, 자식 클래스에서 `add()`를 재정의하여 추가적인 기능을 구현한 부분이 더 이상 동작하지 않을 수 있습니다.
    2. **상위 클래스의 사양에 없는 동작에 의존**:
       * `HashSet`의 공식 문서나 API 사양에는 `addAll()`이 `add()`를 사용하여 구현된다는 내용이 없습니다.
       * 즉, 이는 **구현 세부사항**이며, 외부에서 알 수 없고 의존해서도 안 되는 부분입니다.
    3. **코드의 유지보수성 및 안정성 저하**:
       * 상위 클래스의 내부 구현에 의존하면, 상위 클래스의 업데이트나 수정에 취약해집니다.
       * 이는 코드의 안정성을 저해하고, 예기치 않은 버그를 유발할 수 있습니다.

[^2]: **InstrumentedHashSet**은 자바에서 `HashSet` 클래스를 상속하여 **추가된 원소의 수를 세기 위한 클래스**입니다. 이 클래스는 원소를 추가하는 동작을 계측(instrument)하기 위해 만들어졌습니다.

    #### 왜 InstrumentedHashSet을 만들었을까?

    `HashSet`은 집합(Set) 자료구조를 구현한 클래스이며, 원소의 추가, 삭제 등을 수행할 수 있습니다. 그러나 `HashSet` 자체는 몇 개의 원소가 추가되었는지 추적하지 않습니다.

    어떤 상황에서는 **집합에 원소가 몇 번 추가되었는지 알고 싶을 때**가 있습니다. 예를 들어, 중복된 원소를 추가하려고 시도한 횟수까지 포함하여 총 몇 번의 추가 연산이 있었는지 알고 싶을 수 있습니다.

    이를 위해 `HashSet`을 상속하여 `InstrumentedHashSet`을 만들고, `add()`와 `addAll()` 메서드를 재정의하여 원소가 추가될 때마다 카운터를 증가시키는 방식을 생각했습니다.
