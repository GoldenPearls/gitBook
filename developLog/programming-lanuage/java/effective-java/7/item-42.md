# item 42 : 익명 클래스보다는 람다를 사용하라

## 1. 익명 클래스

예전에는 <mark style="color:red;">자바에서 함수 타입을 표현할 때</mark> 추상 메서드를 하나만 담은 인터페이스(드물게는 추상 클래스)를 사용했다.&#x20;

> 이런 인터페이스의 인스턴스를 `함수 객체(Rmction object)`라고 하여, 특정 함수나 동작을 나타내는 데 썼다.  JDK 1.1 이 등장하면서 함수 객체를 만드는 주요 수단은 익명 클래스(아이템 24)가 되었다

### 1) 익명클래스란?

익명 클래스(Anonymous Class) : 이름이 없는 클래스로, 주로 인터페이스의 구현체를 생성할 때 사용된다. 익명 클래스는 일회성으로 사용되며, <mark style="color:red;">클래스 정의와 동시에 인스턴스를 생성하여 사용할 수 있다.</mark>

### 2) 익명 클래스의 인스턴스를 함수 객체로 사용

익명 클래스는 특히 <mark style="color:blue;">특정 기능을 정의하는 함수 객체로 자주 사용된다.</mark>

예를 들어, 문자열 리스트를 길이 순으로 정렬하는 코드가 있다고 가정해 보자. 자바 8 이전에는 `Comparator` 인터페이스를 구현하는 익명 클래스를 다음과 같이 사용했다:

```java
import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("kim", "taeng", "mad", "play");

        Collections.sort(words, new Comparator<String>() {
            public int compare(String s1, String s2) {
                return Integer.compare(s1.length(), s2.length());
            }
        });
    }
}

```

비교 로직을 익명 클래스로 작성하여`Comparator`  구현했지만 익명 클래스 방식은 코드가 너무 길기 때문에 자바는 **함수형 프로그래밍(Functional Programming)**에 적합하지 않다.

## 2. 자바 8에서의 람다 표현식 도입

자바 8에 와서  `JDK 1.8` 버전 부터는 추상 메서드 하나 짜리 인터페이스, 즉 함수형 인터페이스를 말하는데 그 인터페이스의 인스턴스를 람다식(lambda expression, 짧게 람다)라고 사용해 만들 수 있게 되었다

자바 8부터는 람다 표현식(lambda expression)을 도입하여, 코드가 더욱 간결해졌다. **람다**는 익명 클래스처럼 구현할 필요 없이 <mark style="color:blue;">함수형 인터페이스(추상 메서드가 하나인 인터페이스)의 인스턴스를 간단히 정의</mark>할 수 있다.

```java
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("kim", "taeng", "mad", "play");

        Collections.sort(words,
                (s1, s2) -> Integer.compare(s1.length(), s2.length()));
    }
}
```

람다는 `Comparator<String>` 인터페이스의 인스턴스를 생성하며, 여기서 매개변수(`s1`과 `s2`) 타입인`String과` 반환값의 타입인  int는컴파일러가 **타입 추론**을 통해 자동으로 결정한다. 상황에 따라 컴파일러가 타입을 결정하지 못할 때만 프로그래머가 직접 타입을 명시하면 된다.

<mark style="color:red;">타입을 명시해야 코드가 더 명확할 때만 제외하고는, 람다의 모든 매개변수 타입은 생략하자.</mark> 그런 다음 컴파일러가 “타입을 알 수 없다”는 오류 를 낼 때만 해당 타입을 명시하면 된다.

### 1) 타입 추론 관련

아이템 26에서는 제네릭의 로 타입을 쓰지 말라 했고, 아이템 29에서는 제네릭을 쓰라 했고, 아이템 30에서는 제네릭 메서드를 쓰라고 했다.&#x20;

> 이 조언들은 람다와 함께 쓸 때는 두 배로 중요해진다.&#x20;

컴파일러가 타입을 추론 하는 데 <mark style="color:red;">필요한 타입 정보 대부분을 제네릭에서 얻기 때문이다.</mark> 우리가 이 정보를 제공하지 않으면 컴파일러는 람다의 타입을 추론할 수 없게 되어, 결국 우리가 일일이 명시해야 한다. 좋은 예로, 코드 42-2에서 인수 <mark style="color:blue;">words가 매개변수화 타입인 List< String> 아니라 로 타입인 List였다면 컴파일 오류가 났을 것</mark>이다.

### 2) 더 간결한 표현 람다식 : 함수형 인터페이스와 비교자 생성 메서드

함수형 인터페이스는 람다 표현식에 맞게 설계된 인터페이스로, `Comparator`도 대표적인 함수형 인터페이스이다. `Comparator`를 람다와 함께 사용할 때는 `comparingInt` 같은 **비교자 생성 메서드**를 사용하여 코드를 더욱 간결하게 작성할 수 있다.

```java
import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("kim", "taeng", "mad", "play");
        Collections.sort(words, Comparator.comparingInt(String::length));
    }
}
```

자바 8에서 `List` 인터페이스에 `sort` 메서드가 추가되면서 더욱 짧은 코드로 정렬을 수행할 수 있게 되었다.

```java
import java.util.Arrays;
import java.util.Comparator;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("kim", "taeng", "mad", "play");
        words.sort(Comparator.comparingInt(String::length));
    }
}
```

### 3) 열거 타입(Enum)에서 람다 활용

자바에서 **열거 타입**의 인스턴스는 고유의 동작을 지정할 수 있다. 자바 8 이전에는 `Operation` 같은 열거 타입의 `apply` 메서드를 각 상수별로 재정의하기 위해 <mark style="color:red;">상수별 클래스 몸체를 사용해야 했다.</mark>

```java
enum Operation {
    PLUS("+") { 
        public double apply(double x, double y) { return x + y; }
    },
    MINUS("-") {
        public double apply(double x, double y) { return x - y; }
    },
    TIMES("*") {
        public double apply(double x, double y) { return x * y; }
    },
    DIVIDE("/") {
        public double apply(double x, double y) { return x * y; }
    };
    
    private final String symbol;
   
    Operation(String symbol) { this.symbol = symbol; }
    
    @Override public String toString() { return symbol; } 
    public abstract double apply(double x, double y);
}
```

하지만, 자바 8 이후에는 람다 표현식을 사용하여 **열거 타입의 인스턴스 필드에 함수 객체를 저장**하는 방식으로 코드가 간결해졌다. 단순히 각 열거 타입 상수의 동작을 람다로 구현해 <mark style="color:red;">생 성자에 넘기고, 생성자는 이 람다를 인스턴스 필드로 저장</mark>해둔다. 그런 다음 apply 메서드에서 필드에 저장된 람다를 호출하기만 하면 된다.

```java
import java.util.function.DoubleBinaryOperator;

enum Operation {
    PLUS("+", (x, y) -> x + y),
    MINUS("-", (x, y) -> x - y),
    TIMES("*", (x, y) -> x * y),
    DIVIDE("/", (x, y) -> x / y);

    private final String symbol;
    private final DoubleBinaryOperator op;

    Operation(String symbol, DoubleBinaryOperator op) {
        this.symbol = symbol;
        this.op = op;
    }

    @Override
    public String toString() { return symbol; }

    public double apply(double x, double y) {
        return op.applyAsDouble(x, y);
    }
}

public class Main {
    public static void main(String[] args) {
        // 사용은 아래와 같이
        Operation.PLUS.apply(2, 3);
    }
}
```

람다 표현식이 도입되면서 열거 타입의 각 인스턴스에 대한 동작을 간단하게 정의할 수 있게 되었고, 코드가 더욱 간결하고 유지보수가 쉬워졌다.

{% hint style="success" %}
열거 타입 상수의 동작을 표현한 람다를 DoubleBinaryOperator 인터페이스 변수에 할당했다. **DoubleBinaryOperator**는 java.util.function 패키지가 제공하 는 다양한 함수 인터페이스 중 하나로, double 타입 인수 2개를 받아 double 타입 결과를 돌려준다.
{% endhint %}

<figure><img src="../../../../.gitbook/assets/화면 캡처 2024-11-11 111644.png" alt=""><figcaption></figcaption></figure>

### 4)  람다 표현식의 제한 사항

람다 기반 Operation 열거 타입을 보면 상수별 클래스 몸체는 더 이상 사용할 이유가 없다고 느낄지 모르지만, 꼭 그렇지는 않다. 메서드나 클래스와 달리, 람다는 **이름이 없고 문서화도 못 한다.**&#x20;

* **this 참조**: 람다 표현식에서 `this` 키워드는 바깥 인스턴스를 가리킨다. 익명 클래스에서는 `this`가 익명 클래스 자체를 참조하므로, 람다에서는 이를 활용할 수 없다.&#x20;

```java
import java.util.Arrays;
import java.util.List;


class Anonymous {
    public void say() {}
}

public class Main {
    public void someMethod() {
        List<Anonymous> list = Arrays.asList(new Anonymous());

        Anonymous anonymous = new Anonymous() {
            @Override
            public void say() {
                System.out.println("this instanceof Anonymous : " + (this instanceof Anonymous));
            }
        };
        
        // this instanceof Anonymous : true
        anonymous.say();

        // this instanceof Main : true
        list.forEach(o -> System.out.println("this instanceof Main : " + (this instanceof Main)));
    }

    public static void main(String[] args) {
        new Main().someMethod();
    }
}
```



* **추상 클래스 인스턴스**: 추상 메서드가 하나인 함수형 인터페이스가 아닌 경우, 익명 클래스를 대신 사용할 수 없다.
* **자신을 참조해야 할 때**: 람다에서 자신을 참조할 수 없으므로, 함수 객체가 자신을 참조해야 하는 경우에는 익명 클래스를 사용해야 한다. 즉, 추상 클래스의 인스턴스를 만들 때 람다를 사용할 수 없다.&#x20;

```java
abstract class Hello {
    public void sayHello() {
        System.out.println("Hello!");
    }
}

public class Main {
    public static void main(String[] args) {
        // 이건 원래 안됨~
        // Hello hello = new Hello();

        Hello instance1 = new Hello() {
            private String msg = "Hi";
            @Override public void sayHello() {
                System.out.println(msg);
            }
        };

        Hello instance2 = new Hello() {
            private String msg = "Hola";
            @Override public void sayHello() {
                System.out.println(msg);
            }
        };

        // Hi!
        instance1.sayHello();

        // Hola!
        instance2.sayHello();

        // false
        System.out.println(instance1 == instance2);
    }
}
```



### 5) 람다와 직렬화의 문제

람다 표현식과 익명 클래스는 모두 **직렬화**에 주의가 필요하다. 람다 표현식의 직렬화는 구현에 따라 다르며, **VM별로 직렬화 형태가 다를 수 있다**. 따라서 람다를 직렬화하는 일은 극히 삼가야 한다. 익명 클래스 의 인스턴스도 마찬가지. <mark style="color:red;">직렬화해야만 하는 함수 객체가 있다면 가령 Comparator처럼  private 정적 중첩 클래스(아이템 24）의 인스턴스를 사용하자</mark>

> 따라서 코드 자체로 동작이 명확히 설명되지 않거나 코드 줄 수가 많아지면 람다를 쓰지 말아야 한다.

## &#x20;핵심 정리

* 자바 8의 람다 표현식은 함수형 인터페이스 인스턴스를 더 간결하게 만들며, 함수형 프로그래밍의 문법적 장벽을 크게 낮췄다.
* 익명 클래스는 함수형 인터페이스가 아닌 타입의 인스턴스를 만들 때 사용하며, 람다가 제공하지 않는 기능을 활용할 때 여전히 필요하다.
* 람다와 제네릭을 함께 사용할 때 타입을 명시하여 컴파일 오류를 방지하는 것이 중요하다.



> 참고 및 출처
>
> * [https://madplay.github.io/post/prefer-lambdas-to-anonymous-classes](https://madplay.github.io/post/prefer-lambdas-to-anonymous-classes)
