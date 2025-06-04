# item 19 : 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라

## 1. 상속용 클래스가 지켜야할 것 들

{% hint style="info" %}
상속용 클래스는 재정의할 수 있는 메서드들을 내부적으로 어떻게 이용하는지(자기사용)  문서로 남겨야 한다.
{% endhint %}

1. 어떤 순서로 호출하는지, 호출 결과가 이어지는 처리에 어떤 영향을 주는지도 담아야 한다.
2. `재정의 가능한 메서드`란? public, protected 중 final이 아닌 모든 메서드를 말한다.
3. 재정의 가능한 메서드를 호출할 수 있는 모든 상황을 문서로 남기는 것이 좋다. 백그라운드 스레드나 정적 초기화 과정에서 호출될 수 있으므로 유의하자

### 1) Implentation Requirements :  remove()의 예

> API 문서의 메서드 설명 끝에서 종종 “Implementation Requirements”로 시작하는 절을 볼 수 있는데, 그 메서드의 내부 동작 방식을 설명하는 곳이다. 이 절은 메서드 주석에 `@implSpec` 태그를 붙여주면 자바독 도구가 생성해준다.

예를 들어, `AbstractCollection`의 `remove` 메서드를 살펴보자.

<figure><img src="../../../../.gitbook/assets/화면 캡처 2024-10-10 102802.png" alt=""><figcaption></figcaption></figure>

이렇게 문서화하면, 하위 클래스 작성자가 어떤 메서드를 재정의할 때 주의해야 할지를 알 수 있다.

* API 문서의 메서드 설명 끝에 종종 `Implementation Requirements`로 시작하는 절이 있다. 이 절은 그 메서드의 내부 동작 방식을 설명하는 곳이다. 이 절은 메서드 주석에 `@implSpec` 태그를 붙여주면 자바독 도구가 생성해준다.
* 해당 설명에 따르면 `iterator` 메서드를 재정의하면 `remove` 메서드에 영향을 준다는 점을 알 수 있다.

### **2) `@implSpec` 태그로 내부 구현 문서화하기**

자바 8부터는 `@implSpec` 태그를 사용해 메서드의 내부 동작을 문서화할 수 있다. 이를 통해 상속용 클래스의 메서드들이 어떻게 구현되었는지 명확하게 전달할 수 있다.

<figure><img src="../../../../.gitbook/assets/화면 캡처 2024-10-10 102223.png" alt=""><figcaption></figcaption></figure>

이렇게 하면 하위 클래스 작성자가 성능 개선을 위해 어떻게 해야 할지를 알 수 있다.

### **3) 하위 클래스에서 활용할 수 있는 `protected` 메서드 제공**

{% hint style="success" %}
효율적인 하위 클래스를 큰 어려움 없이 만들 수 있게 하려면 클래스의 내부 동작 과정 중간에 끼어들 수 있는 훅(hook)을 잘 선별하여 하위클래스에서  확장할 수  있는 protected 메서드 형태로 공개해야 할 수도 있다. 이를 통해 하위 클래스 작성자가 원하는 대로 기능을 변경하거나 성능을 향상시킬 수 있다.
{% endhint %}

* 다음은 `java.util.Abstract`의 `removeRange` 메서드이다. List의 구현체의 최종 사용자는 해당 메서드에 관심이 없다.&#x20;
* 그럼에도 이 메서드를 제공한 이유는 단지 하위 클래스에서 부분리스트의 clear 메서드를 고성능으로 만들기 쉽게 하기 위해서다.

> [https://sasca37.tistory.com/259](https://sasca37.tistory.com/259)

### **4) 어떤 메서드를 `protected`로 노출할지 결정하기**

어떤 메서드를 `protected`로 노출할지는 신중하게 결정해야 한다. 너무 많이 노출하면 캡슐화가 약해지고, 너무 적게 노출하면 상속의 이점이 사라질 수 있다. 실제로 하위 클래스를 만들어 보면서 필요한 메서드가 무엇인지 확인해야 한다.

{% hint style="warning" %}
그럼 어떤 규칙을 가지고 노출할지 결정해야 할까?
{% endhint %}

**1. 하위 클래스에서 재정의하거나 확장해야 하는 메서드인지 확인한다**

* **핵심 동작을 커스터마이즈하기 위해 필요한 메서드**를 `protected`로 노출한다.
* 예를 들어, 알고리즘의 일부를 변경하거나 특정 단계에서 동작을 추가해야 하는 경우 해당 메서드를 `protected`로 제공한다.

**2. 내부 구현에 강하게 의존하지 않는 메서드만 노출한다**

* **내부 구현 세부 사항에 의존하지 않는 메서드**를 선택한다.
* 내부 구현이 변경되더라도 하위 클래스에서 재정의한 메서드가 영향을 받지 않도록 설계해야 한다.

**3. 최소한의 메서드만 노출한다**

* **필요한 최소한의 메서드만 `protected`로 공개**하여 캡슐화를 유지한다.
* 불필요하게 많은 메서드를 노출하면 유지 보수가 어려워지고, 클래스의 안정성이 떨어질 수 있다.

**4. 하위 클래스 작성 시 필요한 메서드인지 실제로 확인한다**

* **하위 클래스를 직접 구현해 보면서** 어떤 메서드가 필요한지 판단한다.
* 필요하지 않은 메서드를 노출하는 것을 방지하고, 필요한 메서드를 놓치지 않을 수 있다.

**5. 메서드의 계약을 명확히 정의하고 문서화한다**

* **노출하는 메서드의 기능과 사용 방법을 명확히 문서화**하여 하위 클래스 작성자가 올바르게 활용할 수 있도록 한다.
* 메서드의 입력, 출력, 예외 상황, 호출 시점 등을 상세히 기술한다.

**6. 가능하면 템플릿 메서드 패턴을 활용한다**

* **템플릿 메서드 패턴**을 사용하여 알고리즘의 구조를 정의하고, 일부 단계를 하위 클래스에서 재정의할 수 있도록 한다.
* 이때 재정의 가능한 메서드를 `protected`로 노출한다.

```java
public abstract class AbstractProcessor {
    // 템플릿 메서드: 알고리즘의 구조를 정의
    public final void process() {
        loadData();
        processData();
        saveResults();
    }

    // 하위 클래스에서 재정의할 수 있는 메서드들을 protected로 노출
    protected abstract void loadData();
    protected abstract void processData();
    protected void saveResults() {
        // 기본 구현 제공
        System.out.println("결과를 저장합니다.");
    }
}
```

* 이 예시에서 `loadData()`, `processData()`, `saveResults()` 메서드는 하위 클래스에서 필요한 경우 재정의할 수 있도록 `protected`로 노출되었다.

**7. 불변성을 해치지 않는 메서드만 노출한다**

* **클래스의 불변식을 유지하면서 재정의할 수 있는 메서드**를 노출해야 한다.
* 하위 클래스에서 메서드를 재정의하더라도 상위 클래스의 상태나 동작에 문제가 발생하지 않아야 한다.

**8. `protected` 필드보다 메서드를 선호한다**

* 가능하면 **`protected` 필드보다는 메서드를 `protected`로 노출**한다.
* 직접 필드에 접근하는 것보다 메서드를 통해 접근하면 캡슐화가 유지되고, 변경에 유연하게 대응할 수 있다.

> 이러한 기준들을 종합하여 어떤 메서드를 `protected`로 노출할지 결정하자. 핵심은 **하위 클래스 작성자가 필요한 기능을 제공하면서도 상위 클래스의 안정성과 캡슐화를 최대한 유지하는 것**이다.



근데 이렇게 쓸 일이 많을까..? 흠... 모르겠네..



### **5) 생성자에서 재정의 가능한 메서드 호출하지 않기**

생성자에서 재정의 가능한 메서드를 호출하면 예상치 못한 문제가 발생할 수 있다. 하위 클래스의 필드가 초기화되기 전에 메서드가 호출될 수 있기 때문이다.

예를 들어:

```java
// 상위 클래스
public class Super {
    public Super() {
        overrideMe();
    }

    public void overrideMe() {
        // 기본 구현
    }
}

// 하위 클래스
public class Sub extends Super {
    private final Instant instant;

    public Sub() {
        instant = Instant.now();
    }

    @Override
    public void overrideMe() {
        System.out.println(instant);
    }
}
```

* `Super` 클래스의 생성자에서 `overrideMe()` 메서드를 호출한다.
* `Sub` 클래스에서 `overrideMe()` 메서드를 재정의하고, `instant` 필드를 출력한다.
* 그러나 `instant` 필드는 `Sub` 클래스의 생성자에서 초기화되므로, **`Super`의 생성자가 실행될 때는 `instant`가 아직 초기화되지 않았다.**

**실행 결과:**

```plaintext
null
2023-10-05T12:34:56.789Z
```

* 첫 번째 출력은 `null`이 출력된다. 이는 `instant` 필드가 초기화되기 전에 `overrideMe()`가 호출되었기 때문
* 두 번째 출력은 `instant`가 초기화된 후 호출된 `overrideMe()`의 결과이다.

{% hint style="success" %}
그렇다면 해결방법은 무엇일까?
{% endhint %}

* **생성자에서는 재정의 가능한 메서드를 호출하지 말아야 한다.**
* 만약 호출해야 한다면, 해당 메서드를 `final`, `private`, 또는 `static`으로 선언하여 재정의되지 않도록한다.

**수정된 코드 예시:**

```java
public class Super {
    public Super() {
        init();
    }

    private void init() {
        // 초기화 로직
    }
}
```

* `init()` 메서드를 `private`으로 선언하여 하위 클래스에서 재정의할 수 없도록 한다.

## **2. `Cloneable`과 `Serializable` 구현 시 주의사항**

{% hint style="danger" %}
`clone()`이나 `readObject()` 메서드에서도 재정의 가능한 메서드를 호출하면 안 된다.&#x20;
{% endhint %}

이 메서드들은 새로운 객체를 생성하는 역할을 하기 때문에, 생성자에서의 문제와 비슷한 상황이 발생할 수 있다.

**문제점 설명:**

* `clone()`이나 `readObject()` 메서드는 **새로운 객체를 생성하는 역할**을 한다.
* 이 메서드들에서 **재정의 가능한 메서드를 호출하면** 생성자에서 발생하는 문제와 유사한 문제가 발생할 수 있다.
  * **하위 클래스의 상태가 완전히 초기화되기 전에** 재정의된 메서드가 호출될 수 있다.

**예제 코드:**

```java
public class Super implements Cloneable {
    public Super() {
        // 생성자 로직
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        Super cloned = (Super) super.clone();
        cloned.overrideMe();
        return cloned;
    }

    public void overrideMe() {
        // 기본 구현
    }
}

public class Sub extends Super {
    private final Instant instant;

    public Sub() {
        instant = Instant.now();
    }

    @Override
    public void overrideMe() {
        System.out.println(instant);
    }
}
```

* `Super` 클래스의 `clone()` 메서드에서 `overrideMe()`를 호출하고 있다.
* `Sub` 클래스에서 `overrideMe()`를 재정의하고 `instant` 필드를 사용한다.
* 그러나 `clone()` 메서드에서 생성된 객체의 `instant` 필드는 아직 초기화되지 않았으므로 `null` 또는 예상치 못한 값이 출력될 수 있다.

**해결 방법:**

* **`clone()`이나 `readObject()` 메서드에서는 재정의 가능한 메서드를 호출하지 말아야 한다.**
* 객체 복제나 역직렬화 과정에서 필요한 초기화는 **직접 필드에 접근하여 처리하거나, 생성자를 통해 초기화**해야 한다.

**수정된 코드 예시:**

```java
@Override
protected Object clone() throws CloneNotSupportedException {
    Super cloned = (Super) super.clone();
    // 필요한 초기화 로직을 직접 수행
    return cloned;
}
```

## 3. 상속을 해야할 때, 금지해야 할 때

### **1) 상속을 금지해야 하는 경우**

상속을 고려하지 않고 설계된 클래스는 상속을 금지하는 것이 좋다. 방법은 두 가지가 있다:

1.  클래스를 `final`로 선언하기:

    ```java
    public final class MyClass {
        // 클래스 구현
    }
    ```
2.  생성자를 `private` 또는 `package-private`으로 만들고, 정적 팩토리 메서드를 제공하기:

    ```java
    public class MyClass {
        private MyClass() {
            // 생성자 구현
        }

        public static MyClass newInstance() {
            return new MyClass();
        }
    }
    ```

* 생성자를 `private`으로 만들면 <mark style="color:red;">외부에서 인스턴스를 생성할 수 없으므로</mark> 상속이 불가능하다.
* 대신 **정적 팩토리 메서드**를 제공하여 객체를 생성한다.

**왜 상속을 금지해야 할까?**

* 클래스의 내부 구현에 의존하는 하위 클래스가 생성되면, **상위 클래스의 수정이 하위 클래스의 동작에 영향을 줄 수 있다.**
* 이를 방지하기 위해 **상속을 금지하여 클래스의 안정성을 높인다.**

### **2) 상속을 허용해야 하는 경우**

상속이 명확히 필요한 경우에는 내부 구현을 문서화하고, 하위 클래스 작성자가 안전하게 상속할 수 있도록 해야 한다.

#### 예제 코드: 안전한 상속을 위한 설계

상위 클래스 `Shape`를 설계해 보자:

```java
public abstract class Shape {
    private Color color;

    public Shape(Color color) {
        this.color = color;
    }

    // 추상 메서드: 하위 클래스에서 반드시 구현해야 함
    public abstract double area();

    // 재정의 가능하지만, 내부 상태에 영향을 주지 않는 메서드
    public void draw(Graphics g) {
        g.setColor(color);
        g.fillShape(this);
    }

    // Getter와 Setter 제공
    public Color getColor() {
        return color;
    }

    public void setColor(Color color) {
        this.color = color;
    }
}

```

* 추상 메서드 `area()`를 통해 하위 클래스에서 구체적인 면적 계산 방법을 구현하도록 한다.
* 재정의 가능한 메서드 `draw()`는 내부 상태에 의존하지 않고, `color` 필드만 사용한다.
* **캡슐화를 유지하면서 하위 클래스에서 필요한 부분만 재정의할 수 있도록 설계되었다.**

하위 클래스 `Circle`은 이렇게 구현할 수 있다:

```java
public class Circle extends Shape {
    private final double radius;

    public Circle(Color color, double radius) {
        super(color);
        this.radius = radius;
    }

    @Override
    public double area() {
        return Math.PI * radius * radius;
    }

    // 추가적인 메서드 제공
    public double getRadius() {
        return radius;
    }
}

```

* `Circle` 클래스는 `Shape`를 상속받아 구체적인 원의 면적 계산을 구현한다.
* **생성자에서 상위 클래스의 생성자를 호출하여 `color`를 초기화한**다.
* **추상 메서드 `area()`를 구현**하여 원의 면적을 계산한다.

### 3) **안전한 상속을 위한 설계 원칙**

1. **상속이 필요한 명확한 이유가 있어야 합니다.**
   * 상위 클래스와 하위 클래스 사이에 **IS-A 관계**가 성립해야 합니다.
2. **상위 클래스의 내부 구현을 문서화합니다.**
   * 재정의 가능한 메서드가 어떻게 동작하는지, 어떤 순서로 호출되는지 명확히 설명합니다.
3. **생성자에서 재정의 가능한 메서드를 호출하지 않습니다.**
   * 이는 하위 클래스의 예기치 않은 동작을 방지합니다.
4. **재정의 가능한 메서드의 사용을 최소화합니다.**
   * 가능한 경우 메서드를 `final`로 선언하여 재정의를 방지합니다.
5. **하위 클래스 작성자를 위한 가이드라인을 제공합니다.**
   * 하위 클래스에서 어떤 메서드를 재정의해야 하고, 어떤 메서드는 재정의하면 안 되는지 명확히 합니다.

### 🙂 **상속을 허용하는 클래스의 예시**

* **컬렉션 프레임워크의 추상 클래스**들 (`AbstractList`, `AbstractSet` 등)은 상속을 위한 설계가 잘 되어 있다.
* 이 클래스들은 하위 클래스에서 필요한 메서드를 재정의하여 원하는 동작을 구현할 수 있도록 설계되었다.

***

## **핵심 정리**

> 상속용 클래스를 설계하려면 많은 노력과 신중함이 필요하다. 특별한 이유가 없다면 상속을 금지하는 것도 좋은 선택이다. 클래스의 목적과 사용 사례에 따라 상속을 허용할지 말지 결정하자.

* 상속용 클래스를 설계할 때는 내부 동작 방식을 명확히 문서화하고, 하위 클래스 작성자가 안전하게 상속할 수 있도록 해야 한다.&#x20;
* 생성자, `clone()`, `readObject()`와 같은 객체 생성 과정에서 재정의 가능한 메서드를 호출하면 안 된다.
* 다른 이가 효율 좋은 하위 클래스를 만들 수 있도록 일부 메서드를 `protected`로 제공해야 할 수도 있다.&#x20;
* 그러니 클래스를 확장해야 할 명확한 이유가 떠오르지 않으면 상속을 금지하는 편이 나을 것이다.&#x20;
* <mark style="color:red;">상속을 금지하려면 클래스를 final로 선언하거나 생성자 모두를 외부에서 접근할 수 없도록 만들면 된다.</mark>
