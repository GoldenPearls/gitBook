# item 25 : 톱레벨 클래스는 한 파일에 하나만 담으라

## 1. 톱레벨 클래스와 정적 멤버 클래스

{% hint style="info" %}
**톱레벨 클래스**란 소스 파일 안에서 다른 클래스에 속하지 않고 독립적으로 선언된 클래스를 말한다.&#x20;
{% endhint %}

일반적으로 자바에서는 소스 파일당 하나의 톱레벨 클래스만 두는 것이 권장된다. 그러나 <mark style="color:red;">자바 컴파일러는 소스 파일 안에 여러 개의 톱레벨 클래스를 선언하더라도 오류를 발생시키지 않는다.</mark> 하지만 이는 여러 가지 문제를 초래할 수 있다.

### 1) 톱레벨  클래스를 여러 개 선언 시 문제점

<mark style="color:red;">**컴파일러의 처리 순서**</mark>**에 따른 동작 차이**:

* 만약 소스 파일 하나에 두 개의 톱레벨 클래스를 정의한 후, 동일한 이름으로 다른 소스 파일에도 같은 톱레벨 클래스를 정의했다면, <mark style="color:red;">어느 파일을 먼저 컴파일하느냐에 따라 프로그램의 동작이 달라질 수 있다.</mark>
* 이는 예기치 못한 결과를 초래할 수 있으며, 코드가 어디서 어떻게 실행될지 예측하기 어렵게 만든다.

**예시**

```java
// Main.java
public class Main {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }
}
```

```java
// Utensil.java
class Utensil {
    static final String NAME = "pan";
}

class Dessert {
    static final String NAME = "cake";
}
```

```java
// Dessert.java
class Utensil {
    static final String NAME = "pot";
}

class Dessert {
    static final String NAME = "pie";
}
```

* 만약 `javac Main.java Utensil.java` 명령으로 컴파일하면 `pancake`이 출력된다.
* 반면에 `javac Dessert.java Main.java` 명령으로 컴파일하면 `potpie`가 출력된다.
* 같은 프로그램이라도 컴파일 순서에 따라 전혀 다른 결과를 낳게 된다.

### 2) 해결책

**톱레벨 클래스를 각각의 파일로 분리**하면 문제를 해결할 수 있다. 각 톱레벨 클래스는 고유한 소스 파일을 가져야 하며, 이렇게 하면 컴파일 순서에 관계없이 예상한 대로 프로그램이 동작한다.

하지만 여러 톱레벨 클래스를 한 파일에 두고 싶다면 **정적 멤버 클래스**를 사용할 수 있다.

**정적 멤버 클래스로 변경**

```java
// Test.java
public class Test {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }

    private static class Utensil {  // Utensil을 정적 멤버 클래스로 선언
        static final String NAME = "pan";
    }

    private static class Dessert {  // Dessert를 정적 멤버 클래스로 선언
        static final String NAME = "cake";
    }
}
```

#### **부가 설명: 정적 멤버 클래스의 장점**

* **독립성**: 정적 멤버 클래스는 외부 클래스의 인스턴스와 독립적으로 존재할 수 있으므로, 외부 클래스의 상태를 공유하지 않으면서도 바깥 클래스에 속한 클래스처럼 작동한다.
* **접근성**: `private`으로 선언하면 외부에서 접근할 수 없게 하여 불필요한 노출을 방지할 수 있습니다. 이렇게 하면 코드의 가독성이 좋아지고, 불필요한 참조를 줄여 메모리 누수를 예방할 수 있다.
* **구조적 정리**: 여러 관련된 클래스들을 하나의 파일에 두고 싶을 때, 정적 멤버 클래스를 사용하면 코드가 더 읽기 쉬워지고 유지보수도 용이해진다.

## **✨** 최종 정리

> 소스 파일 하나에는 반드시 톱레벨 클래스(혹은 톱레벨 인터페이스)를 하나만 담자.&#x20;

1. **톱레벨 클래스는** 보통 소스 파일당 하나만 선언하는 것이 좋다. 여러 톱레벨 클래스를 한 파일에 정의하면, 컴파일 순서에 따라 프로그램 동작이 달라질 수 있어 유지보수와 디버깅이 어려워진다.
2. **정적 멤버 클래스를 사용**하면, 외부 클래스와 함께 사용될 수 있으면서도 외부 클래스의 상태와 독립적으로 동작할 수 있어 코드의 안정성과 효율성이 높아진다.
3. **정적 멤버 클래스를 적절히 활용**하면 코드의 가독성과 유지보수성이 향상되며, 불필요한 외부 참조로 인한 메모리 누수를 방지할 수 있다
