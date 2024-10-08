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

<pre class="language-java"><code class="lang-java">public boolean remove(Object o)

<strong>// 주어진 객체와 동일한 요소를 컬렉션에서 제거한다. 내부적으로는 컬렉션을 순회하며, `equals`를 사용해 일치하는 요소를 찾는다.
</strong>
Implementation Requirements:
이 메서드는 반복자를 사용하여 구현되었다. 따라서, 하위 클래스에서 `iterator` 메서드를 재정의하면 `remove` 메서드의 동작에도 영향을 줄 수 있다.
</code></pre>

이렇게 문서화하면, 하위 클래스 작성자가 어떤 메서드를 재정의할 때 주의해야 할지를 알 수 있다.

### **2) `@implSpec` 태그로 내부 구현 문서화하기**

자바 8부터는 `@implSpec` 태그를 사용해 메서드의 내부 동작을 문서화할 수 있다. 이를 통해 상속용 클래스의 메서드들이 어떻게 구현되었는지 명확하게 전달할 수 있다.

예를 들어, `AbstractList`의 `removeRange` 메서드를 보자.

```java
protected void removeRange(int fromIndex, int toIndex)

// fromIndex(포함)부터 toIndex(비포함)까지의 요소를 제거한다.

@implSpec
이 메서드는 리스트 반복자를 사용하여 요소를 하나씩 제거하도록 구현되었다. 성능을 향상시키기 위해 하위 클래스에서 이 메서드를 재정의할 수 있다.
```

이렇게 하면 하위 클래스 작성자가 성능 개선을 위해 어떻게 해야 할지를 알 수 있다.

### **3) 하위 클래스에서 활용할 수 있는 `protected` 메서드 제공**

{% hint style="success" %}
효율적인 하위 클래스를 큰 어려움 없이 만들 수 있게 하려면 클래스의 내부 동작 과정 중간에 끼어들 수 있는 훅(hook)을 잘 선별하여 하위클래스에서   확장할 수  있는 protected 메서드 형태로 공개해야 할 수도 있다. 이를 통해 하위 클래스 작성자가 원하는 대로 기능을 변경하거나 성능을 향상시킬 수 있다.
{% endhint %}

예를 들어, `removeRange` 메서드를 `protected`로 제공하면 하위 클래스에서 이 메서드를 재정의하여 부분 리스트의 `clear` 메서드 성능을 개선할 수 있다.

### **4) 어떤 메서드를 `protected`로 노출할지 결정하기**

어떤 메서드를 `protected`로 노출할지는 신중하게 결정해야 한다. 너무 많이 노출하면 캡슐화가 약해지고, 너무 적게 노출하면 상속의 이점이 사라질 수 있다. 실제로 하위 클래스를 만들어 보면서 필요한 메서드가 무엇인지 확인해야 한다.

### **5) 하위 클래스 작성 및 검증**

직접 하위 클래스를 만들어 보면서 설계가 적절한지 검증하는 것이 좋다. 이렇게 하면 놓친 부분이나 불필요한 부분을 발견할 수 있다. 세 개 정도의 하위 클래스를 만들어 보면 충분하다.

### **6) 생성자에서 재정의 가능한 메서드 호출하지 않기**

생성자에서 재정의 가능한 메서드를 호출하면 예상치 못한 문제가 발생할 수 있다. 하위 클래스의 필드가 초기화되기 전에 메서드가 호출될 수 있기 때문이다.

예를 들어:

```java
public class Super {
    public Super() {
        overrideMe();
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

이 경우, `Sub` 클래스의 `instant` 필드가 초기화되기 전에 `overrideMe()` 메서드가 호출되어 `null`이 출력될 수 있다.



## **2. `Cloneable`과 `Serializable` 구현 시 주의사항**

{% hint style="danger" %}
`clone()`이나 `readObject()` 메서드에서도 재정의 가능한 메서드를 호출하면 안 된다.&#x20;
{% endhint %}

이 메서드들은 새로운 객체를 생성하는 역할을 하기 때문에, 생성자에서의 문제와 비슷한 상황이 발생할 수 있다.



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

    public abstract double area();

    public void draw(Graphics g) {
        g.setColor(color);
        g.fillShape(this);
    }

    public Color getColor() {
        return color;
    }

    public void setColor(Color color) {
        this.color = color;
    }
}
```

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

    public double getRadius() {
        return radius;
    }
}
```

이렇게 하면 하위 클래스에서 안전하게 상속할 수 있다.

***

## **핵심 정리**

> 상속용 클래스를 설계하려면 많은 노력과 신중함이 필요하다. 특별한 이유가 없다면 상속을 금지하는 것도 좋은 선택이다. 클래스의 목적과 사용 사례에 따라 상속을 허용할지 말지 결정하자.

* 상속용 클래스를 설계할 때는 내부 동작 방식을 명확히 문서화하고, 하위 클래스 작성자가 안전하게 상속할 수 있도록 해야 한다.&#x20;
* 다른 이가 효율 좋은 하위 클래스를 만들 수 있도록 일부 메서드를 `protected`로 제공해야 할 수도 있다.&#x20;
* 그러니 클래스를 확장해야 할 명확한 이유가 떠오르지 않으면 상속을 금지하는 편이 나을 것이다.&#x20;
* <mark style="color:red;">상속을 금지하려면 클래스를 final로 선언하거나 생성자 모두를 외부에서 접근할 수 없도록 만들면 된다.</mark>
