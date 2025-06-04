# item 38 : 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

열거 타입을 확장 가능하게 만들고자 할 때, 자바에서는 `Enum` 자체의 확장은 허용되지 않는다.

대신 **인터페이스**와 **열거 타입**을 함께 활용하여 열거 타입을 확장하는 것과 유사한 효과를 낼 수 있다. 이를 통해 열거 타입의 이점을 유지하면서도 필요한 경우 확장 가능한 열거 타입과 같은 기능을 구현할 수 있다.

{% hint style="info" %}
확장 가능 열거 타입을 위한 인터페이스 사용
{% endhint %}

먼저, **인터페이스를 이용해 열거 타입의 기본 동작을 정의**한다. 그리고 열거 타입이 이 인터페이스를 구현하여 기본 기능을 제공하도록 설계한다.

## 1. 연산 인터페이스 `Operation`

기본적으로 제공하는 연산은 `Operation`이라는 인터페이스를 통해 정의된다.

```java
public interface Operation {
    double apply(double x, double y);
}
```

* `Operation` 인터페이스는 연산을 나타내는 추상 메서드 `apply(double x, double y)`를 제공한다,
* 이를 통해 연산을 수행하는 열거 타입이 추가될 때마다 `apply` 메서드를 각기 다른 방식으로 구현할 수 있게 된다.

## 2. `BasicOperation` 열거 타입

기본 연산 기능을 제공하는 열거 타입 `BasicOperation`을 정의한다. 각 열거 상수는 `Operation` 인터페이스를 구현하여 연산을 수행한다.

```java
public enum BasicOperation implements Operation {
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
        public double apply(double x, double y) { return x / y; }
    };

    private final String symbol;

    BasicOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override public String toString() {
        return symbol;
    }
}
```

* `BasicOperation`은 `Operation` 인터페이스를 구현하며, `PLUS`, `MINUS`, `TIMES`, `DIVIDE`의 기본 연산을 정의한다.
* `apply` 메서드는 `Operation` 인터페이스의 메서드로서 열거 상수마다 다르게 정의된다.

## 3. `ExtendedOperation` 열거 타입

기본 연산에 더해 새로운 연산을 정의하고자 할 때는 **또 다른 열거 타입을 정의**하여 `Operation` 인터페이스를 구현할 수 있다.

```java
public enum ExtendedOperation implements Operation {
    EXP("^") {
        public double apply(double x, double y) { return Math.pow(x, y); }
    },
    REMAINDER("%") {
        public double apply(double x, double y) { return x % y; }
    };

    private final String symbol;

    ExtendedOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override public String toString() {
        return symbol;
    }
}
```

* `ExtendedOperation`은 기존 연산 외에 추가 연산(거듭제곱(EXP)와 나머지(REMAINDER))을 구현한다.
* 기본 열거 타입 `BasicOperation`을 확장한 것은 아니지만, `Operation` 인터페이스를 구현함으로써 기존 연산과 같은 방식으로 사용할 수 있다.

## 4. 확장 가능 열거 타입의 활용 예

**기존 연산과 새로운 연산을 모두 사용할 수 있도록** `Operation` 인터페이스를 기반으로 처리하는 메서드를 작성한한다.

```java
public static void main(String[] args) {
    double x = Double.parseDouble(args[0]);
    double y = Double.parseDouble(args[1]);

    // BasicOperation과 ExtendedOperation 모두를 테스트하는 예제
    test(Arrays.asList(BasicOperation.values()), x, y);
    test(Arrays.asList(ExtendedOperation.values()), x, y);
}

private static void test(Collection<? extends Operation> opSet, double x, double y) {
    for (Operation op : opSet)
        System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
}
```

* `test` 메서드는 `Collection<? extends Operation>` 타입을 받아, `Operation` 인터페이스를 구현한 열거 타입을 모두 처리할 수 있다.
* `test(Arrays.asList(BasicOperation.values()), x, y);`는 `BasicOperation`의 연산을 테스트하고, `test(Arrays.asList(ExtendedOperation.values()), x, y);`는 `ExtendedOperation`의 연산을 테스트한다.

## 5. `Class<T>`를 이용한 확장성 제공

연산 인터페이스와 열거 타입을 `Class<T>`로 받아 **확장된 열거 타입의 모든 원소를 순회하는 방식**으로도 사용 가능하하다.

```java
public static <T extends Enum<T> & Operation> void test(Class<T> opEnumType, double x, double y) {
    for (Operation op : opEnumType.getEnumConstants())
        System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
}
```

* `Class<T> opEnumType`에 `ExtendedOperation.class`를 넘기면 확장된 연산을 포함해 열거 타입의 모든 원소를 사용할 수 있습.
* `<T extends Enum<T> & Operation>`으로 제약을 설정하여 `Enum`이면서 `Operation`을 구현한 타입만 허용한다.

### 요약

이 패턴을 사용하면 **기존 열거 타입을 수정하지 않고도 확장된 연산을 추가**할 수 있다. `Operation` 인터페이스를 통해 **확장 가능성을 제공하면서도 열거 타입의 장점**을 유지하는 이점이 있다.

1. **타입 안전성**을 유지하며 새로운 연산을 추가할 수 있다.
2. **코드 중복**을 줄이고, 기본 열거 타입과 확장된 열거 타입의 사용 방식을 동일하게 유지한다.
3. **EnumMap**이나 **EnumSet**을 사용할 수는 없지만, `Operation` 인터페이스를 사용해 **기존 코드를 그대로 확장**할 수 있다.

이 패턴은 주로 `java.nio.file.LinkOption` 등이 적용된 방식으로, **인터페이스와 열거 타입을 결합**하여 확장 가능한 열거 타입을 구현하는 효과를 제공한다.

## 핵심 정리

> 열거 타입 자체는 확장할 수 없지만, 인터페이스와 그 인터페이스를 구현하는 기본 열거 타입을 함께 사용해 같은 `효과`를 낼 수 있다.&#x20;

* 이렇게 하면 클라이언트는 이 인터페이스 를 구현해 자신만의 열거 타입혹은 다른 타입을 만들 수 있다.&#x20;
* API가 기본 열거 타입을 직접 명시하지 않고  **인터페이스 기반으로 작성되었다면** 기본 열거 타입의 인스 턴스가 쓰이는 모든 곳을 **새로 확장한 열거 타입의 인스턴스로 대체해 사용할 수 있다.**
