# item 23 : 태그 달린 클래스보다는 클래스 계층구조를 활용하라

## 1. 태그 달린 클래스

{% hint style="danger" %}
**태그 달린 클래스**는 **하나의 클래스에서 여러 가지 다른 타입의 객체를 처리하는 방식으로 설계**된다. 하지만 이렇게 하면 쓸모없는 데이터 필드와 코드가 생기며, 유지보수도 어렵고 실수로 인해 런타임 오류가 발생할 가능성도 커진다.
{% endhint %}

```java
class Figure {
    enum Shape { RECTANGLE, CIRCLE };
    
    final Shape shape;  // 태그 필드
    
    // 사각형(RECTANGLE)일 때 사용
    double length;
    double width;
    
    // 원(CIRCLE)일 때 사용
    double radius;
    
    // 원용 생성자
    Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius;
    }
    
    // 사각형용 생성자
    Figure(double length, double width) {
        shape = Shape.RECTANGLE;
        this.length = length;
        this.width = width;
    }
    
    // 면적 계산 (태그 값에 따라 분기 처리)
    double area() {
        switch (shape) {
            case RECTANGLE:
                return length * width;
            case CIRCLE:
                return Math.PI * (radius * radius);
            default:
                throw new AssertionError("알 수 없는 모양: " + shape);
        }
    }
}

```

* **불필요한 필드**: 각 도형에 필요하지 않은 필드도 클래스에 포함되어 있다. 예를 들어, 원에는 `length`, `width`가 필요하지 않고, 사각형에는 `radius`가 필요 없다.
* **조건문으로 분기 처리**: 태그 값에 따라 `area()` 메서드에서 조건문을 사용해 분기 처리하는 방식은 복잡하고 실수를 유발할 수 있다.
* **확장성 부족**: 새로운 도형을 추가하려면 `switch` 문에 새로운 `case`를 추가해야 하며, 태그 값을 처리할 수 있는 코드도 추가해야 한다.

### 1)  태그 달린 클래스란?

클래스 내부에 **특정 태그 필드**를 두고, 그 <mark style="color:red;">태그의 값에 따라 클래스의 동작이나 데이터를 다르게 처리하는 방식으로 설계된 클래스를 의미</mark>한다. `태그 필드`는 클래스의 상태나 모양을 나타내는 역할을 한다. 이 태그 필드에 따라 클래스가 어떤 방식으로 동작할지 결정하는데, 예를 들어 클래스가 여러 가지 다른 타입의 객체를 표현해야 하는 상황에서 태그 필드를 사용해 그 타입을 구분하게 되는 것임.

### 2) 태그 달린 클래스의 단점

* 열거 타입 선언, 태그 필드, switch 문 등 쓸데없는 코드가 많다. 여러 구현이 한 클래스에 혼합돼 있어서 가독성도 나쁘다.&#x20;
* 다른 의미를 위한 코드도 언제나 함께 하니 메모리도 많이 사용한다. 필드들을 `final`로 선언하려면 해당 의미에 <mark style="color:red;">쓰이지 않는 필드들까지 생성자에서 초기화</mark>해야 한다(쓰지 않는 필드를 초기화하는 불필요한 코드가 늘어난다).&#x20;
* 또 다른 의미를 추가하려면 코드 를 수정해야 한다. 예를 들어 새로운 의미를 추가할 때마다 모든 switch 문을 찾아 새 의미를 처리하는 코드를 추가해야 하는데 , 하나라도 빠뜨리면 역시 <mark style="color:blue;">런타임에 문제가 불거져 나올 것이다.</mark>
* 마지막으로, <mark style="color:red;">인스턴스의 타입만으로는 현재 나타내는 의미를 알 길이 전혀 없다.</mark>&#x20;

> 한마디로, 태그 달린 클래스는 장황하고, 오류를 내기 쉽고, 비효율적이다.

## 2. 서브타이핑 (subtyping) : 클래스 계층 구조를 이용한 해결 방법

다행히 자바와 같은 객체 지향 언어는 **타입 하나로 다양한 의미의 객체를 표현하는 훨씬 나은 수단을 제공**한다.

> 바로 클래스 계층구조를 활용하는 `서브타이핑 (subtyping)`이다.&#x20;

클래스를 **계층구조**로 설계하면 된다. 가장 먼저 **루트 클래스**로 <mark style="color:red;">추상 클래스를 정의</mark>하고, 태그에 따라 달라지는 메서드를 <mark style="color:red;">추상 메서드</mark>로 선언한다. 그런 다음 각 도형에 해당하는 하위 클래스를 만들어 각각의 필드를 가지게 하고, 루트 클래스의 추상 메서드를 구현한다.

### 1) 클래스 계층 구조로 한 코드

```java
// 루트 클래스: 모든 도형의 공통 조상
abstract class Figure {
    abstract double area();
}

// 하위 클래스: 원
class Circle extends Figure {
    final double radius;

    Circle(double radius) {
        this.radius = radius;
    }

    @Override
    double area() {
        return Math.PI * (radius * radius);
    }
}

// 하위 클래스: 사각형
class Rectangle extends Figure {
    final double length;
    final double width;

    Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    @Override
    double area() {
        return length * width;
    }
}

```

### 2) 클래스 계층구조의 장점

* **불필요한 필드 제거**: 각 도형에 맞는 필드만 남기고 불필요한 필드는 모두 제거했다.
* **분기 처리 제거**: 조건문을 사용한 분기 처리가 필요 없다. 각 도형 클래스에서 고유한 로직을 구현할 수 있어 코드를 간결하게 유지할 수 있다.
* **유연한 확장성**: 새로운 도형을 추가할 때마다 루트 클래스를 건드릴 필요 없이 독립적인 하위 클래스를 추가하면 된다.
* **컴파일 타임 검증**: 컴파일러가 각 클래스가 필요한 필드를 모두 초기화하고, 추상 메서드를 구현했는지 확인해준다. 따라서 실수로 인한 런타임 오류가 줄어든다.

### 3) 추가적인 확장 (정사각형 예시)

계층 구조로 설계했을 경우, 새로운 도형을 쉽게 확장할 수 있다. 예를 들어, 정사각형을 사각형의 특별한 형태로 정의할 수 있다.

```java
class Square extends Rectangle {
    Square(double side) {
        super(side, side);  // 정사각형은 길이와 너비가 동일
    }
}
```

## **3. 추상 클래스를 쓰는 때**

> 아이템 20에서는 추상클래스보다는 인터페이스를 고려하라고 했는데..? 왜 위에 계층 구조일 때는 추상클래스를 이용한 걸까? 🤔

**추상 클래스**를 사용하는 이유는 상황에 따라 다르다. 위에서 **인터페이스**가 더 유연하고 다중 상속이 가능하다고 했지만, **추상 클래스**가 필요한 경우도 있다. 추상 클래스를 선택하는 주요 이유를 몇 가지 설명해보자

### 1) **공통 상태나 구현이 필요할 때**

> 추상 클래스를 사용하는 가장 큰 이유는 `공통 상태(필드)`를 공유하거나 **기본 구현**을 제공하기 위해서이다.

예를 들어, 여러 서브 클래스들이 공통적으로 사용할 **인스턴스 변수**나 **메서드**가 있다면, 추상 클래스로 이 부분을 상속받아 사용하게 할 수 있다.

* 예시: 모든 도형 클래스에서 **색깔**이나 **위치** 등의 공통 필드를 유지하고 싶다면, 추상 클래스가 적합하다. 이렇게 하면 공통된 상태를 상속받아 사용할 수 있고, 코드 중복을 줄일 수 있다.

```java
abstract class Shape {
    String color;  // 공통 상태 (필드)
    
    // 공통 구현을 위한 메서드
    void setColor(String color) {
        this.color = color;
    }

    abstract double area();  // 서브 클래스가 구현해야 하는 추상 메서드
}
```

이 경우 모든 도형은 색깔을 갖고 있고, 색깔을 변경할 수 있다. <mark style="color:blue;">이 기능을 모든 도형 서브 클래스에 일일이 구현하는 대신, 추상 클래스를 통해 공통으로 상속받는다.</mark>

### 2) **공통된 행동(메서드)을 정의할 때**

**인터페이스**는 `상태(필드)`를 가질 수 없고, 메서드의 기본 구현을 제공할 수 있는 디폴트 메서드도 일부 제한적이다. 하지만 **추상 클래스**는 상속받는 클래스에 **기본 메서드 구현**을 제공할 수 있다.

예를 들어, 도형 클래스에서 면적을 구하는 `area()` 메서드는 각 도형마다 다르게 구현되겠지만, `toString()` 같은 공통적인 동작은 추상 클래스에서 미리 정의해줄 수 있다.

```java
abstract class Shape {
    abstract double area();

    @Override
    public String toString() {
        return "This is a shape";
    }
}
```

모든 도형 서브 클래스는 `toString()` 메서드를 자동으로 상속받아 사용할 수 있게 된다. 만약 인터페이스였다면 이런 공통적인 구현을 제공하기가 어렵다.

### 3) **상속의 편리함**

> 추상 클래스는 상속받는 클래스들이 **자동으로 기본 구현**을 가지게 하고, 필요한 부분만 오버라이드할 수 있게 도와준다. 이는 **코드 중복을 줄이고**, 일관된 방식으로 기능을 제공하는 데 유리하다.

예를 들어, 자바의 `AbstractList`, `AbstractMap` 같은 추상 클래스는 모든 리스트나 맵 구현체가 공통적으로 사용하는 메서드를 미리 정의하고, 각 구현체는 그 메서드를 상속받아 사용할 수 있다. 이는 **구체적인 구현**을 간편하게 만들 수 있게 해준다.

### 4) **제한된 상속**

추상 클래스는 클래스 계층구조 내에서 **좀 더 엄격하게 상속을 제한**하고자 할 때 사용된다. 인터페이스는 아무 클래스나 구현할 수 있지만, 추상 클래스는 **하나의 부모 클래스**만 상속할 수 있기 때문에, **일관된 계층구조**를 유지하고 싶을 때 사용된다.

예를 들어, `Shape`라는 추상 클래스가 있다면, 이 추상 클래스를 상속받은 `Circle`, `Rectangle` 등은 모두 **같은 상속 구조**를 공유하게 된다. 이는 **코드 유지보수성과 일관성을** 높이는 데 기여할 수 있다.

### 5) **공통 인터페이스를 위한 골격 구현 제공**

추상 클래스는 인터페이스와 함께 **골격 구현**(Skeletal Implementation)을 제공하는 데 매우 유용하다.&#x20;

> <mark style="color:red;">즉,</mark> <mark style="color:red;"></mark><mark style="color:red;">**인터페이스**</mark><mark style="color:red;">로는 타입을 정의하고,</mark> <mark style="color:red;"></mark><mark style="color:red;">**추상 클래스**</mark><mark style="color:red;">로는 기본 동작을 정의해서, 서브 클래스는 필요한 부분만 구현하면 된다.</mark>

예를 들어, 자바의 `AbstractList`는 `List` 인터페이스를 구현한 추상 클래스로, `List`의 많은 메서드를 기본적으로 구현하고 있다. 이렇게 하면 새로운 `List` 구현체를 만들 때, `AbstractList`를 상속받아서 편리하게 구현할 수 있다.

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
    
    // 더 많은 메서드를 기본 구현으로 제공
}
```

이 방식은 새로운 `List` 구현체를 만들 때, 핵심 메서드만 구현하고 나머지는 자동으로 상속받을 수 있게 해준니다.

### 결론: 왜 추상 클래스를 사용하나?

인터페이스가 더 유연하고 다중 상속이 가능하더라도, **공통 상태(필드)를 공유**하거나 **기본 동작을 제공**해야 하는 상황에서는 추상 클래스가 더 적합하다. 특히 **공통된 데이터나 구현을 상속**하고, 여러 하위 클래스에서 공통적으로 사용하는 **공통 동작을 유지**해야 할 때는 추상 클래스가 더 적합한 선택이 된다.

결론적으로, **상황에 따라** 추상 클래스가 더 적합할 때가 있고, 이런 경우라면 추상 클래스를 선택하는 것이 좋다.

## 4. 인터페이스를 통한 서브타이핑

{% hint style="info" %}
**인터페이스를 사용한 계층 구조**도 서브타이핑(subtyping)이라고 할 수 있다.&#x20;
{% endhint %}

서브타이핑은 객체 지향 프로그래밍에서 **하위 타입(subtype)이 상위 타입(supertype)의 역할을 할 수 있는** 관계를 의미한다.&#x20;

> 즉, **상위 타입의 메서드를 하위 타입이 모두 구현**해서 **하위 타입 객체가 상위 타입으로 동작할 수 있는 상황**을 말합니다.

### 1) 서브타이핑과 인터페이스

서브타이핑의 핵심은 "하위 타입이 상위 타입의 모든 동작을 지원해야 한다"는 점이다. **인터페이스**는 이 서브타이핑의 규칙을 따르는 대표적인 수단 중 하나이다. **인터페이스**는 클래스가 반드시 구현해야 할 메서드들의 집합을 정의하므로, 그 인터페이스를 구현한 클래스들은 인터페이스 타입으로 참조할 수 있다.

즉, **인터페이스를 기반으로 계층 구조를 만들면**, 상위 인터페이스 타입을 구현한 하위 클래스가 상위 인터페이스로 동작할 수 있기 때문에, **이것도 서브타이핑에 해당한다**.

#### 예시

```java
interface Shape {
    double area(); // 모든 도형이 구현해야 하는 메서드
}

class Circle implements Shape {
    private double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
}

class Rectangle implements Shape {
    private double length;
    private double width;

    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    @Override
    public double area() {
        return length * width;
    }
}

public class Main {
    public static void main(String[] args) {
        Shape circle = new Circle(5);
        Shape rectangle = new Rectangle(4, 6);

        System.out.println("Circle Area: " + circle.area());
        System.out.println("Rectangle Area: " + rectangle.area());
    }
}
```

위 코드에서 `Shape`는 인터페이스이고, `Circle`과 `Rectangle`은 `Shape` 인터페이스를 구현한 클래스들이다. `Circle`과 `Rectangle` 객체는 모두 `Shape` 타입으로 참조될 수 있고, `Shape` 인터페이스에 정의된 `area()` 메서드를 사용할 수 있다. 이것이 바로 **서브타이핑**

### 2) 추상 클래스 vs 인터페이스: 서브타이핑 관점

* **추상 클래스**는 공통된 상태(필드)와 구현을 공유하는 데 강점이 있지만, **단일 상속**만 가능하다. 즉, 한 클래스가 하나의 부모 추상 클래스만 가질 수 있다.
* **인터페이스**는 상태를 가질 수 없지만, 다양한 구현체가 동일한 동작을 보장하도록 **다중 상속**이 가능하다. 즉, 하나의 클래스가 여러 개의 인터페이스를 구현할 수 있습니다.

둘 다 서브타이핑을 지원하지만, 인터페이스는 더 **유연한 계층 구조**를 설계할 수 있게 도와준다.

{% hint style="success" %}
#### 서브타이핑은 상위 타입(인터페이스나 추상 클래스)의 동작을 하위 타입이 구현해서 **상위 타입처럼 동작할 수 있는 관계**를 말하며, 인터페이스는 서브타이핑을 구현하는 중요한 방법 중 하나이다.
{% endhint %}

## **5. 스터디에서 추가 설명 :** API SERVER APPLICATION 에서 Entity를 클래스 계층구조로 만드려면&#x20;

> 출처 : [https://antique-banon-928.notion.site/Effective-Java-20-25-10-17-11d82aee42598045ab1ded57b536635b](https://antique-banon-928.notion.site/Effective-Java-20-25-10-17-11d82aee42598045ab1ded57b536635b)

### 1) 퍼시스턴스 레이어 활용

> **퍼시스턴스 레이어**에서 단순히 DB에 저장되어 있는 값을 꺼내오는 역할만 하지 않고, **계층구조의 타입으로 변환해주는 역할**을 해준다.

**레이어드 아키텍쳐**

퍼시스턴스 레이어로 Repository, Dao Layer 총 두계층으로 나눈다.Dao는 DB에서 값을 꺼내오는 역할을, Repository는 계층구조의 타입으로 전환해주는 역할을 맡는다.

![](https://antique-banon-928.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fe2ba546f-5712-4557-b5d2-b776f0a2f06a%2F8a3e786a-5a82-440e-a0d0-a4ad2d860d21%2F%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA\_2024-10-13\_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE\_12.41.35.png?table=block\&id=11e82aee-4259-8037-8a35-e650f22aaf98\&spaceId=e2ba546f-5712-4557-b5d2-b776f0a2f06a\&width=1420\&userId=\&cache=v2)

#### 예시

![](https://antique-banon-928.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fe2ba546f-5712-4557-b5d2-b776f0a2f06a%2F6375739f-470f-4034-bc62-552ae34ed1d7%2F%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA\_2024-10-13\_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE\_12.58.30.png?table=block\&id=11e82aee-4259-8054-93b3-ce5f91eadd06\&spaceId=e2ba546f-5712-4557-b5d2-b776f0a2f06a\&width=1420\&userId=\&cache=v2)

이미지 퀴즈, 텍스트 퀴즈 총 2 종류의 퀴즈가 존재한다.

**Dao**

* QuizEntity 타입값을 가지게 되기 때문에 객체지향적으로 좋다는 것

```java
@Repository
@RequiredArgsConstructor
public class QuizDao {

    private final JPAQueryFactory queryFactory;
    
    public List<QuizEntity> findQuiz(QuizType quizType) {
        return queryFactory
                .select(quiz)
                .from(quiz)
                .where(quiz.type.eq(quizType))
                .fetch();
    }
}
```



**Repository**

```java
@Repository
@RequiredArgsConstructor
public class QuizRepository {

    private final QuizDao quizDao;

    public List<TextQuiz> findTextQuiz() {
        return quizDao.findQuiz(QuizType.TEXT)
                .map(TextQuiz::from).collect(Collectors.toList());
    }

    public List<ImageQuiz> findImageQuiz() {
        return quizDao.findQuiz(QuizType.IMAGE)
                .map(ImageQuiz::from).collect(Collectors.toList());
    }
}
```



## **✨** 최종 정리

* 태그 달린 클래스를 써야 하는 상황은 거의 없다. 새로운 클래스를 작성하는 데 태그필드가 등장한다면 **태그를 없애고 계층구조로 대체하는 방법**을 생각해보자.&#x20;
* 클래스 계층구조로 변환함으로써 코드가 더 간결해지고, 각 클래스의 역할이 명확해졌다. 태그 달린 클래스는 유지보수와 확장성이 떨어질 수 있지만, 클래스 계층구조로 변환하면 이러한 문제들을 해결할 수 있다.

> 기존 클래스가 태그 필드를 사용하고 있다면 계층구조로 리팩터링하는 걸 고민해보자.
