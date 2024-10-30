# item 34 : int 상수 대신 열거 타입을 사용하라

## 1. int 상수 패턴,   문자열 상수  패턴 개념과 문제점(int enum pattern)&#x20;

열거 타입은 일정 개수의 상수 값을 정의한 다음, 그 외의 값은 허용하지 않는 타입이다.

### 1) int 상수 패턴의 문제점

아래의 코드는 경우의 수가 한정될 때 각 경우를 상수 값으로 치환하여 표현하는 것이다.

```java
public static final int APPLE_FUJI = 0;
public static final int APPLE_PIPPIN = 1;
public static final int APPLE_GRANNY_SMITH = 2;

public static final int ORANGE_NAVEL = 0;
public static final int ORANGE_TEMPLE = 1;
public static final int ORANGE_BLOOD = 2;
```

1. 타입 안전을 보장할 방법이 없으며 표현력도 좋지 않다.

오렌지를 건네야 할 메서드에 사과를 보내 고 동등 연산자(=)로 비교하더라도 컴파일러는 아무런 경고 메시지를 출력하지 않는다.

```java
// 향긋한 오렌지 향의 사과 소스!
int i = (APPLE_FUJI - ORANGE_TEMPLE) / APPLE_PIPPIN;
```

2. 정수 열거 패턴을 위한 `별도 이름공간(namespace)`을 지원 하지 않는다.

사과용 상수의 이름은 모두 APPLE\_로 시작하고, 오렌지용 상수는 ORANGE\_로 시작한다. 정수 열거 패턴을 위한 별도 `이름공간`을 지원하지 않기 때문에 어쩔 수 없이 접두어를 써서 이름 충돌을 하는 것이다.

3. 정수 열거 패턴을 사용한 프로그램은 깨지기 쉽다.

평범한 상수를 나열한것 뿐이라 <mark style="color:red;">컴파일하면 그 값이 클라이언트 파일에 그대로 새겨진다.</mark>&#x20;

따라서 상수의 값이 바뀌면 클라이언트도 반드시 다시 컴파일해야 한다.

4. 정수 상수는 문자열로 출력하기 까다롭다.

값을 출력하거나 디버거로 살펴보면 (의미가 아닌) 단지 숫자로만 보여서 썩 도움이 되지 않는다. 같은 정수 열거 그룹에 속한 모든 상수를 한 바퀴 순회하는 방법도 마땅치 않다. 심지어 그 안에 상수가 몇 개인지도 알 수 없다.&#x20;

### 2) 문자열 열거 패턴 (string enum pattern)

정수 대신 문자열 상수를 사용하는 변형 패턴도 있는데 더 안좋다. 수의 의미를 출력할 수 있다는 점은 좋지만, 경험이 부족한 프로그래머가 문자열 상수의 이름 대신 **문자열 값을 그대로 하드코딩하게 만들기 때문**이다.&#x20;

> 이렇게 하드코딩한 문자열 에 오타가 있어도 컴파일러는 확인할 길이 없으니 자연스럽게 런타임 버그가 생긴다. 문자열 비교에 따른 성능 저하 역시 당연한 결과다.

## 2. 자바 열거 타입(enum type)

> 열거 패턴들의 단점을 말끔히 씻어주는 동시에 여러 장점을 안겨주는 대안

### 1) 자바 열거 타입(enum type)

```java
public enum Apple { FUJI, PIPPIN, GRANNY_SMITH } 
public enum Orange { NAVEL, TEMPLE, BLOOD }
```

{% hint style="success" %}
자바의 열거 타입은 완전한 형태의 <mark style="color:red;">클래스</mark>라서 (단순한 정숫 값일 뿐인) 다른 언어의 열거 타입보다 훨씬 강력하다.
{% endhint %}

### 2)  열거 타입의 아이디어

**열거 타입 자체는 클래스이며, 상수 하나당 자신의 인스턴스를 하나씩 만들어 public static final 필드로 공개한다.**

* 열거 타입은 밖에서 접근할 수 있는 생성자를 제공하지 않으므로 사실상 final이다.
* 인스턴스가 통제된다. 인스턴스들은 오직 하나만 존재한다.
* 원소가 하나이면 싱글턴으로 볼 수 있다. 싱글턴은 원서가 하나뿐인 열거 타입이라고 할 수 있고, 거꾸로 열거 타입은 싱글턴을 일반화한 형태라고 할 수 있다.

**열거 타입은 컴파일타임 타입 안전성을 제공한다.**

* 이전의 `int 상수 패턴`처럼 `ORANGE`가 갈 곳에 `APPLE`이 간다면, 명확히 타입 에러가 발생한다.
* 타입이 다른 열거 타입 변수에 할당하려 하거나 다른 열거 타입의 값끼리 == 연산자로 비교하려는 꼴이기 때문이다.

**네임스페이스를 제공하여, 이름이 같은 상수도 평화롭게 공존할 수 있다.**

* `APPLE.RED`와 `ORANGE.RED`는 구분된다.

**`toString()`이 출력하기에 적합한 문자열을 내어준다.**

**열거 타입에는 다양한 메서드나 필드도 추가 가능하다.**

* 추가로 임의의 인터페이스도 구현하게 할 수 있다.
* <mark style="color:red;">공개되는 것이 오직 필드의 이름</mark>뿐이라, 정수 열거 패턴과 달리 상수 값이 클라이언트로 컴파일되어 각인되지 않기 때문이다

### 3) 예시

* 태양계의 여덞 행성에 대한 열거 타입을 만드는 것도 그리 어렵지 않다.
* 각 행성에 는 질량과 반지름이 있고, 이 두 속성을 이용해 표면중력을 계산할 수 있다. 따 라서 어떤 객체의 질량이 주어지면 그 객체가 행성 표면에 있을 때의 무게도 계산할 수 있다.

```java
enum Planet {
    MERCURY(3.302e+23, 2.439e6),
    VENUS(4.869e+24, 6.052e6),
    EARTH(5.975e+24, 6.378e6),
    MARS(6.419e+23, 3.393e6),
    JUPITER(1.899e+27, 7.149e7),
    SATURN(5.685e+26, 6.027e7),
    URANUS(8.683e+25, 2.556e7),
    NEPTUNE(1.024e+26, 2.477e7);

    private final double mass; // 질량 (단위: 킬로그램)
    private final double radius; // 반지름 (단위: 미터)
    private final double surfaceGravity; // 표면중력 (단위: m / s^2)

    // 중력상수 (단위: m^3 / kg s^2)
    private static final double G = 6.677300E-11;

    // 생성자
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
        this.surfaceGravity = G * mass / (radius * radius);
    }

    public double mass() { return mass; }
    public double radius() { return radius; }
    public double surfaceGravity() { return surfaceGravity; }

    public double surfaceWeight(double mass) {
        return mass * surfaceGravity; // F = ma
    }
}
```

> 열거 타입 상수 각각을 특정 데이터와 연결지으려면 생성자에서 데이터를 받아 인스턴스 필드 에 저장하면 된다.&#x20;

* 열거 타입은 근본적으로 `불변`이라 모든 필드는 `final`이어야 한다.
* 필드를 public으로 선언해도 되지만, private으로 두고 별도의 public 접근자 메서드를 두는 게 낫다.
* **Planet의 생성자에서 표면중력을 계산해 저장한 이유**는 단순히 `최적화`를 위해서다.

{% hint style="info" %}
열거 타입(enum)을 정의할 때 **각 열거 상수에 특정한 데이터를 연결하는 방식**과 **불변성을 유지하는 방법 좀 더 자세히**
{% endhint %}

1\. 열거 타입 상수와 데이터 연결

열거 타입에서 각 상수(행성)에 데이터를 연결하려면, 해당 데이터를 **생성자를 통해 받아 필드에 저장**한다. `Planet` 예제에서 `mass`(질량)와 `radius`(반지름) 데이터를 각 행성에 연결하는 방식이 이에 해당한다.

```java
// 각 행성마다 질량(mass)과 반지름(radius)을 전달받아 연결합니다.
MERCURY(3.302e+23, 2.439e6),
VENUS(4.869e+24, 6.052e6),
EARTH(5.975e+24, 6.378e6),
```

위처럼 각 행성 상수에 `mass`와 `radius` 값을 전달하면, 이 값들이 생성자에 의해 인스턴스 필드에 저장된다. 이를 통해 각 상수는 **자신만의 고유한 데이터**를 가지게 된다.

#### 2. 불변성과 필드 선언

열거 타입은 불변성을 유지하는 것이 중요하다. 즉, 한 번 생성된 열거 타입 상수의 데이터는 수정할 수 없어야 한다. 이를 위해 **모든 필드는 `final`로 선언**된다.

```java
private final double mass; // 질량
private final double radius; // 반지름
private final double surfaceGravity; // 표면 중력
```

> 이렇게 필드를 `final`로 선언하면 **초기화된 후 변경이 불가능**하다.&#x20;

열거 타입인 `Planet`은 각 행성의 `mass`, `radius`, `surfaceGravity` 값을 생성 시점에만 설정하고 이후에는 변경할 수 없다.

#### 3. 접근자 메서드의 사용

비록 `final` 필드라 하더라도 직접 값을 읽기 위해서는 직접 필드를 공개(public으로 선언)하기보다는 **private으로 선언하고 public 접근자 메서드를 추가**하는 것이 좋다.

```java
public double mass() { return mass; }
public double radius() { return radius; }
public double surfaceGravity() { return surfaceGravity; }
```

위처럼 `mass`, `radius`, `surfaceGravity`를 반환하는 public 메서드를 제공하여 값을 읽도록 하는데, 이 방식이 선호되는 이유는 다음과 같다:

* **캡슐화**: 내부 구현에 대한 세부 정보를 숨기고 필요한 정보만 외부에 공개할 수 있다.
* **추후 변경 용이성**: 필드나 메서드를 수정하거나 추가할 때도, 외부 사용 방식에 영향을 주지 않고 유연하게 대응할 수 있다.

이렇게 `Planet` 예제에서 각 행성은 `final` 필드로 선언된 불변 데이터를 가진다. 데이터에 접근할 때는 `mass()`, `radius()`, `surfaceGravity()`와 같은 접근자 메서드를 통해서만 값을 얻을 수 있도록 하여 **안전하고 일관된 방식**으로 필드에 접근하게 된다.

### 4) enum.value()

> value()메서든느 정적 메서드로 자신 안에 정의된 상수들의 값을 배열에 담아 반환해준다. 값들은 선언된 순서로 저장된다.

```javascript
public class WeightTable{
    public static void main(String []args) {
        double earthWeight = Double.parseDouble("180");
        double mass = earthWeight / Planet.EARTH.surfaceGravity();

        // 모든 enum 요소를 탐색할 수 있다.       
        for(Planet p : Planet.values()) {
            System.out.printf("%s에서의 무게는 %f이다.%n", p, p.surfaceWeight(mass));
        }
     }
}
```

* toString 메서드 : 상수 이름을 문자열로 반환하므로 println과 printf로 출력하기 좋음

<figure><img src="../../../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

{% hint style="danger" %}
열거 타입에서 상수를 하나 제거하면 어떻게 되지?
{% endhint %}

제거한 상수를 참조하 지 않는 클라이언트에는 아무 영향이 없다. WeightTable 프로그램에서라면 단지 출력하는 줄 수가 하나 줄어들 뿐이다.

> 그렇다면 제거된 상수를 참조하는 클라이언트는 어떻게 될까?

클라이언트 프로그램을 다시 컴파일하면 제 거된 상수를 참조하는 줄에서 디버깅에 유용한 메시지를 담은 컴파일 오류가 발생할 것이다.

정수 열거 패턴에서는 기대할 수 없는 가장 바람직한 대응

## 3. 열거타입 상수마다 동작이 달라지는 메서드 구성

> 클라이언트 코드&#x20;

```java
public class OperationTest {
    public static void main(String[] args) {
        double x = 2.0;
        double y = 4.0;

        // 각 Operation 상수의 apply 메서드를 호출하여 결과 출력
        for (Operation op : Operation.values()) {
            System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
        }
    }
}

```

### 1) `switch` 문을 사용하여 열거 상수별 동작 구현(bad)

> `apply` 메서드가 `switch` 문을 사용하여 `this`에 따른 연산을 수행한다.

```java
// 사칙 연산을 나타내는 Operation 열거 타입 (비권장 방식)
public enum Operation {
    PLUS, MINUS, TIMES, DIVIDE;

    // 각 상수에 따라 다른 동작을 수행하기 위해 switch 문을 사용
    public double apply(double x, double y) {
        switch (this) {
            case PLUS: 
                return x + y;
            case MINUS: 
                return x - y;
            case TIMES: 
                return x * y;
            case DIVIDE: 
                return x / y;
            default: 
                throw new AssertionError("알 수 없는 연산: " + this);
        }
    }
}

```

* 동작은 하지만  마지막의 throw 문은 실제로는 도달할 일 이 없지만 기술적으로는 도달할 수 있기 때문에 생략하면 컴파일조차 되지 않는다.&#x20;
* 더 나쁜 점은 깨지기 쉬운 코드라는 사실이다.
* <mark style="color:red;">새로운 상수를 추가하면 해당 case 문도 추가해야 한다.</mark> 혹시라도 깜빡한다면, 컴파일 은 되지만 새로 추가한 연산을 수행하려 할 때 `알 수 없는 연산`이라는 **런타임 오류를 내며 프로그램이 종료**된다.

### 2)  switch 단점  개선:  상수별 메서드 구현을 사용하여 동작 정의

> 열거 타입에서 상수별로 서로 다른 동작을 정의하고 싶을 때, 각 상수마다 메서드를 개별적으로 정의할 수 있다. 이를 통해 각 상수가 서로 다른 계산 방식이나 로직을 가질 수 있다.

```java
// 상수별 메서드 구현을 사용한 사칙 연산 Operation 열거 타입 (권장 방식)
public enum Operation {
    PLUS {
        @Override
        public double apply(double x, double y) { return x + y; }
    },
    MINUS {
        @Override
        public double apply(double x, double y) { return x - y; }
    },
    TIMES {
        @Override
        public double apply(double x, double y) { return x * y; }
    },
    DIVIDE {
        @Override
        public double apply(double x, double y) { return x / y; }
    };

    // 추상 메서드를 선언하여 각 상수별로 개별적으로 구현
    public abstract double apply(double x, double y);
}
```

1. **상수별 클래스 몸체**: 각 열거 상수(`PLUS`, `MINUS`, `TIMES`, `DIVIDE`)는 `apply` 메서드를 개별적으로 구현한다.
2. **장점**:
   * **추가 시 안전성**: 새 연산을 추가할 때 각 상수별로 `apply` 추상메서드를 재정의하지 않으면 컴파일러가 경고를 띄워, 구현 누락을 방지한다.
   * **간결하고 유지보수가 용이**: 각 연산의 동작이 해당 상수에 직접 정의되어 있어 코드가 더 직관적이며 유지보수에 용이하다.

### 3) 생성자 이용 및 공통 메서드 재정의

1. **열거 타입 상수 정의**: 각 연산(`PLUS`, `MINUS`, `TIMES`, `DIVIDE`) 상수는 개별 `apply` 메서드를 구현하여 상수별로 다른 연산을 수행한다.
2. **`symbol` 필드**: 각 상수에 대응되는 기호(`+`, `-`, `*`, `/`)를 저장하는 `symbol` 필드를 사용하여 `toString` 메서드가 해당 기호를 반환하도록 설정한다.
3. **`fromString` 메서드**: 연산 기호(`+`, `-`, `*`, `/`)를 이용해 적절한 `Operation` 상수를 찾을 수 있도록 `fromString` 메서드를 구현한다.
4. **`operation` 메서드**: 문자열 형태의 연산(`"3 * 5"`)을 받아 기호를 기준으로 `Operation`을 찾아 연산 결과를 반환하는 메서드이다.

<pre class="language-java"><code class="lang-java">public enum Operation {
    PLUS ("+") { // PLUS 상수는 덧셈 연산을 수행
        @Override
        public double apply(double x, double y) { return x + y; }
    },
    MINUS ("-") { // MINUS 상수는 뺄셈 연산을 수행
        @Override
        public double apply(double x, double y) { return x - y; }
    },
    TIMES ("*") { // TIMES 상수는 곱셈 연산을 수행
        @Override
        public double apply(double x, double y) { return x * y; }
    },
    DIVIDE ("/") { // DIVIDE 상수는 나눗셈 연산을 수행
        @Override
        public double apply(double x, double y) { return x / y; }
    };

    private final String symbol; // 각 연산에 해당하는 기호

    Operation(String symbol) {
        this.symbol = symbol;
    }

    public abstract double apply(double x, double y); // 상수별로 구현하는 추상 메서드

    @Override
    public String toString() {
        return symbol;
    }

    // 기호를 통해 Operation 상수를 찾기 위해 String -> Operation 매핑을 위한 Map 생성
    public static final Map&#x3C;String, Operation> stringToEnum = Stream.of(values())
            .collect(Collectors.toMap(Object::toString, e -> e));

    // 기호로부터 Operation을 가져오는 메서드
    public static Optional&#x3C;Operation> fromString(String symbol) {
        return Optional.ofNullable(stringToEnum.get(symbol));
    }
    @Test
<strong>    public void operationApplyTest() {
</strong>        double x = 10;
        double y = 15;
    
        for (Operation value : Operation.values()) {
            System.out.printf("%f %s %f = %f%n", x, value, y, value.apply(x, y));
    }
}
</code></pre>

* **열거 상수별 `apply` 메서드 구현**: 각 상수가 고유한 동작을 갖도록 `apply`를 재정의한다. 예를 들어 `PLUS`는 `x + y`를 수행하고, `TIMES`는 `x * y`를 수행한다.
* **`symbol` 필드와 `toString` 재정의**: 기호를 담는 `symbol` 필드를 통해 `toString` 메서드를 재정의하여 기호가 출력되도록 한다.
* **`fromString` 메서드**: 문자열로 받은 기호를 통해 해당하는 `Operation` 상수를 찾아주는 메서드로, `Map`을 통해 기호와 상수를 빠르게 매핑하여 반환한다.

```
10.000000 + 15.000000 = 25.000000
10.000000 - 15.000000 = -5.000000
10.000000 * 15.000000 = 150.000000
10.000000 / 15.000000 = 0.666667
```

**toString 메서드 재정의 시 고려해줘야 할 점 : fromString 메서드도 함께 제공**

```java
private static final Map<String, Operation> stringToEnum = 
    Stream.of(values()).collect(Collectors.toMap(Object::toString, e -> e));

public static Optional<Operation> fromString(String symbol) {
    return Optional.ofNullable(stringToEnum.get(symbol));
}
```

* 문자열로부터 열거 타입 상수를 안전하게 찾기 위해 `fromString` 메서드를 추가하여 기호에 따라 열거 타입 상수를 매핑한다.
* `Map`을 사용하여 문자열과 열거 타입 상수를 매핑하고, 주어진 문자열을 통해 열거 상수를 찾는다.
* fromString()을 만들어두면 편리하게 다시 문자열을 enum으로 변경할 수 있다.

## 4. 열거타입 상수끼리 코드 공유해보기

각각 `switch`문을 사용한 방법과 전략 패턴을 활용한 방법으로, 열거 타입 상수마다 다른 로직을 제공하면서도 공통된 코드를 효과적으로 공유할 수 있다. 각 접근 방식의 장단점을 비교하여 어떤 상황에서 더 적합한지 알아보자

### 1) `switch`문을 이용한 열거 타입 상수 코드 공유

첫 번째 방식은 `switch` 문을 사용하여 주중과 주말을 구분하는 `PayrollDay` 열거 타입이다. `switch` 문을 통해 상수마다 다른 로직을 적용하면서도 기본적인 코드 흐름을 공유할 수 있다.

**예제 코드**

```java
enum PayrollDay {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;

    private static final int MINS_PER_SHIFT = 8 * 60;

    int pay(int minutesWorked, int payRate) {
        int basePay = minutesWorked * payRate;
        int overtimePay;

        switch (this) {
            case SATURDAY:
            case SUNDAY: // 주말
                overtimePay = basePay / 2;
                break;
            default: // 주중
                overtimePay = minutesWorked <= MINS_PER_SHIFT ? 0 : (minutesWorked - MINS_PER_SHIFT) * payRate / 2;
        }

        return basePay + overtimePay;
    }
}
```

**출력 결과**

```java
pay1 = 96000  // 주중 기본 시간의 임금
pay2 = 114000 // 주중 초과 근무 임금 포함
pay3 = 144000 // 주말 임금
```

**장점과 단점**

* **장점**: `switch` 문을 통해 코드를 간결하게 유지하며, 주중과 주말에 따라 다른 임금을 계산하는 코드를 쉽게 구현할 수 있다.
* **단점**: 새로운 상수를 추가할 때 `switch` 문에서 새로운 `case` 절을 추가하는 것을 잊을 위험이 있다. 이러한 코드 유지보수의 리스크가 있으며, 실수로 누락된 경우에는 런타임 오류가 발생할 수 있다.

***

### 2) 전략 상수 패턴을 사용한 열거 타입 상수 코드 공유

두 번째 방식은 전략 패턴을 활용하여 `PayrollDay` 열거 타입에 주중과 주말의 임금 계산 로직을 별도로 정의하는 방법이다. `PayType`이라는 중첩 열거 타입을 통해 `WEEKDAY`와 `WEEKEND`를 정의하고, 각각 다른 초과 근무 임금 계산 방식을 제공한다.

```java
enum PayrollDay {
    MONDAY(PayType.WEEKDAY),
    TUESDAY(PayType.WEEKDAY),
    WEDNESDAY(PayType.WEEKDAY),
    THURSDAY(PayType.WEEKDAY),
    FRIDAY(PayType.WEEKDAY),
    SATURDAY(PayType.WEEKEND),
    SUNDAY(PayType.WEEKEND);

    private final PayType payType;

    PayrollDay(PayType payType) {
        this.payType = payType;
    }

    public int pay(int minutesWorked, int payRate) {
        return payType.pay(minutesWorked, payRate);
    }

    enum PayType {
        WEEKDAY {
            @Override
            int overtimePay(int minsWorked, int payRate) {
                return minsWorked <= MINS_PER_SHIFT ? 0 : (minsWorked - MINS_PER_SHIFT) * payRate / 2;
            }
        },
        WEEKEND {
            @Override
            int overtimePay(int minsWorked, int payRate) {
                return minsWorked * payRate / 2;
            }
        };

        abstract int overtimePay(int minsWorked, int payRate);
        private static final int MINS_PER_SHIFT = 8 * 60;

        int pay(int minsWorked, int payRate) {
            int basePay = minsWorked * payRate;
            return basePay + overtimePay(minsWorked, payRate);
        }
    }
}
```

**동작 방식**

* **주중과 주말의 임금 계산 로직을 분리**: 주중 근무(`WEEKDAY`)와 주말 근무(`WEEKEND`)의 초과 근무 계산 방식이 서로 다르며, 각 상수에 맞는 `PayType`을 할당하여 각각 다른 동작을 수행한다.
* **코드 실행**: `PayrollDay`의 각 상수는 주어진 `PayType`을 통해 적절한 초과 근무 임금을 계산한다.

**장점과 단점**

* **장점**: 새로운 상수가 추가될 때 각 상수가 `PayType`을 필수로 지정해야 하므로 실수로 빠뜨릴 위험이 적다. 더불어 각 상수에 따른 로직을 전략 패턴으로 분리함으로써 코드를 좀 더 유연하게 관리할 수 있다.
* **단점**: `switch` 문에 비해 다소 복잡하지만, 코드의 안전성과 확장성을 높이는 데 유리

***

### 3) 전략 패턴과 `switch` 문 비교

| 항목         | `switch`문을 이용한 방법       | 전략 패턴을 사용한 방법              |
| ---------- | ----------------------- | -------------------------- |
| **코드 간결성** | 상대적으로 간결함               | 코드가 다소 복잡해짐                |
| **안전성**    | 새로운 상수 추가 시 누락할 가능성이 있음 | 새로운 상수 추가 시 필수 입력값이 있어 안전함 |
| **확장성**    | 제한적이며, 변경에 취약함          | 전략 패턴을 활용하여 유연하게 확장 가능     |
| **유지보수**   | 로직 중복이 발생할 가능성이 있음      | 각 상수의 로직을 한 곳에서 관리할 수 있음   |

* **`switch` 문**: 간단하게 작성할 수 있지만, 열거 타입 상수별 동작을 일괄적으로 수정할 때 불편하며, 유지보수가 어렵고 실수가 발생할 여지가 있다.
* **전략 상수 패턴**: 열거 타입 내부에서 전략 패턴을 활용하여 상수별로 다른 로직을 안전하게 관리할 수 있다. 이 방법은 특히 코드의 확장성과 안전성을 중시할 때 유용하다.



## 5. 열거 타입을 언제 쓰는데?

> 필요한 원소를 **컴파일타임에 다 알 수 있는 상수 집합이라면 열거 타입**을 사용하자.

* ex) 태양계 행성, 한 주의 요일, 체스 말
* ex) 메뉴 아이템, 연산 코드, 명령줄 플래그

## 정리하자면 ,

열거 타입은 확실히 정수 상수보다 효율이다. 읽기도 쉽고 강력하다. 물론 메서드도 쓸 수 있다. 필요한 원소를 **컴파일 타임에 모두 다 알 수 있는 상수의 집합**이라면 열거 타입을 강력히 추천한다. 바이너리 수준에서 호환되도록 설계되었기 때문에 열거 타입에 정의된 상수 개수가 영원히 고정 불변일 필요도 없다.

* 대다수 열거 타입은 명시적 생성자나 메서드 없이 쓰이지만, 각 상수를 특정 데이터와 연결짓거나 상수마다 다르게 동작할 때는 필요하다.
  * 이 경우, 보통은 추상 메서드를 선언한 뒤, `switch`문 대신 상수별 메서드 구현이 낫다.
  * 열거 타입 상수가 같은 코드를 공유한다면, 전략 열거 타입 패턴을 사용하자.



> 출처
>
> * [https://madplay.github.io/post/use-enums-instead-of-int-constants](https://madplay.github.io/post/use-enums-instead-of-int-constants)
> * [https://jake-seo-dev.tistory.com/54#int%--%EC%--%--%EC%--%--%--%ED%-C%A-%ED%--%B-%EC%-D%--%--%EB%-B%A-%EC%A-%--](https://jake-seo-dev.tistory.com/54#int%--%EC%--%--%EC%--%--%--%ED%-C%A-%ED%--%B-%EC%-D%--%--%EB%-B%A-%EC%A-%--)
> * 이펙티브 자바 책
> * [enum은 왜 쓰는 걸까?](#user-content-fn-1)[^1]

[^1]: [**https://velog.io/@red-sprout/Java-enum%EC%9D%80-%EC%99%9C-%EC%93%B0%EB%8A%94%EA%B1%B8%EA%B9%8C-feat.-%EC%9A%B0%EC%95%84%ED%95%9C%ED%98%95%EC%A0%9C%EB%93%A4-%EA%B8%B0%EC%88%A0%EB%B8%94%EB%A1%9C%EA%B7%B8**](https://velog.io/@red-sprout/Java-enum%EC%9D%80-%EC%99%9C-%EC%93%B0%EB%8A%94%EA%B1%B8%EA%B9%8C-feat.-%EC%9A%B0%EC%95%84%ED%95%9C%ED%98%95%EC%A0%9C%EB%93%A4-%EA%B8%B0%EC%88%A0%EB%B8%94%EB%A1%9C%EA%B7%B8)
