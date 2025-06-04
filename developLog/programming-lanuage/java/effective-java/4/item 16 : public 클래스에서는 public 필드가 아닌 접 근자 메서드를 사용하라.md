# item 16 : public 클래스에서는 public 필드가 아닌 접 근자 메서드를 사용하라

## **1. 퇴보한 클래스의 문제점**

{% hint style="danger" %}
인스턴스 필드를 모아놓기만 하고 **캡슐화가 이루어지지 않은 클래스**는 객체 지향 설계에서 문제가 된다.
{% endhint %}

* **직접 필드에 접근하는 클래스**는 API를 수정하지 않고는 내부 표현을 바꿀 수 없고, 불변식(객체의 상태가 항상 유효함을 보장하는 규칙)을 보장할 수 없으며, 외부에서 필드에 접근할 때 부수적인 작업을 수행할 수 없다.
* 예시로 제시된 `Point` 클래스처럼, **public 필드를 노출**하는 클래스는 내부 구현에 쉽게 묶여버리기 때문에 **캡슐화의 장점**을 활용하지 못한다.

```java
class Point {
    public double x; // 필드가 public으로 노출됨
    public double y; // 필드가 public으로 노출됨
}

public class Main {
    public static void main(String[] args) {
        Point point = new Point();
        
        // 외부에서 x와 y 필드에 직접 접근하고 수정할 수 있음
        point.x = 5.0;
        point.y = 10.0;

        System.out.println("X: " + point.x + ", Y: " + point.y);

        // 외부에서 필드에 임의로 잘못된 값도 설정 가능
        point.x = -999.0; // 이런 잘못된 값이 들어갈 수 있음
        System.out.println("X: " + point.x + ", Y: " + point.y);
    }
}

```

위 코드에서는 `x`와 `y` 필드가 `public`으로 노출되어 있어 **캡슐화의 이점을 제공하지 못하며, 필드의 값을 제어할 수 없다.**

* `Point` 클래스에서 `x`와 `y` 필드가 `public`으로 선언되어 있다.
* 외부에서 `point.x = 5.0;`처럼 **직접 접근하여 값을 변경**할 수 있다.

## **2. 접근자와 변경자를 사용하는 캡슐화 방식**

* 이러한 문제를 해결하기 위해, **필드를 `private`으로 감추고 public 접근자(getter)와 변경자(setter) 메서드를 사용하는 방식**이 일반적으로 적용된다. 이를 통해 내부 표현 방식을 바꾸더라도 외부 API를 수정하지 않고 유지할 수 있다.

```java
class Point {
    private double x; // 필드를 private으로 숨김
    private double y; // 필드를 private으로 숨김

    public double getX() {
        return x;
    }

    public void setX(double x) {
        if (x >= 0) {  // 유효성 검사: x는 0 이상이어야 한다
            this.x = x;
        } else {
            System.out.println("잘못된 값입니다.");
        }
    }

    public double getY() {
        return y;
    }

    public void setY(double y) {
        if (y >= 0) {  // 유효성 검사: y는 0 이상이어야 한다
            this.y = y;
        } else {
            System.out.println("잘못된 값입니다.");
        }
    }
}

public class Main {
    public static void main(String[] args) {
        Point point = new Point();
        
        // 메서드를 통해 필드에 접근 (제어 로직을 통해 값이 설정됨)
        point.setX(5.0);
        point.setY(10.0);
        System.out.println("X: " + point.getX() + ", Y: " + point.getY());

        // 유효하지 않은 값을 넣으려 하면 제어 로직이 동작함
        point.setX(-999.0); // 잘못된 값이 들어갈 수 없음
        System.out.println("X: " + point.getX() + ", Y: " + point.getY());
    }
}

```

* 이런 방식을 사용하면 **필드에 직접 접근하는 대신** 메서드를 통해 필드에 접근하거나 변경할 수 있어, 필드의 값을 안전하게 관리할 수 있다.

## **3. 패키지 내부나 private 중첩 클래스에서는 필드를 노출해도 괜찮을 때가 있다**

1. **패키지-프라이빗 클래스**나 **private 중첩 클래스**의 경우에는 **데이터 필드를 노출하는 것이 큰 문제가 되지 않는다.** 그 이유는 이 클래스들이 **외부에서 직접 접근할 수 없는 제한된 영역**에서 사용되기 때문이다.
2. **패키지-프라이빗 클래스**는 **같은 패키지 내에서만 접근할 수 있는 클래스**이다. 즉, 이 클래스를 사용하는 코드도 같은 패키지 내에서만 동작하므로, 클래스의 필드가 외부로 드러나도 크게 문제되지 않는다. **외부 패키지에서는 이 클래스와 그 필드에 접근할 수 없기 때문**
3. **private 중첩 클래스**는 **클래스 내부에서만 사용할 수 있는 클래스**이다. **이 경우는 더 좁은 범위**에서만 접근 가능하기 때문에, 필드를 공개하더라도 그 필드에 접근할 수 있는 코드는 매우 제한적이다. 그래서 굳이 getter/setter를 사용해 캡슐화할 필요가 없다는 의미이다.

### 왜 이런 상황에서 문제가 없을까?

일반적으로 객체 지향 프로그래밍에서는 캡슐화가 매우 중요합니다. <mark style="color:red;">즉, 필드를 직접 노출하는 대신 메서드를 통해 접근해야 하는 것이 권장된다.</mark> 그런데 패키지-프라이빗 클래스나 private 중첩 클래스처럼 <mark style="color:red;">외부에서 접근할 수 없는 제한적인 클래스라면, 필드를 노출해도 문제가 되지 않는 경우가 있다.</mark>

* **캡슐화의 필요성 감소**: 이런 클래스들은 외부에서 접근이 제한되어 있기 때문에, 굳이 복잡한 캡슐화 구조를 만들 필요 없이 필드를 직접 노출하는 것이 더 간단하고 효율적일 수 있다.
* **내부 구현에 묶이는 것이 괜찮다**: 클라이언트 코드가 이 클래스에 **의존**하더라도, 어차피 **패키지 내부에서만 동작하는 코드**이므로 클래스의 내부 구현 방식이 외부 패키지 코드에 영향을 주지 않는다.

```java
class OuterClass {
    private static class InnerClass {
        public int value; // 필드를 공개해도 외부에서 접근할 수 없음
    }

    public void test() {
        InnerClass inner = new InnerClass();
        inner.value = 5; // 내부에서는 자유롭게 필드에 접근 가능
    }
}
```

* `InnerClass`는 `private` 중첩 클래스이므로, **`OuterClass` 내부에서만 접근**할 수 있다.
* 따라서 필드를 `public`으로 노출해도 **외부에서 접근할 수 없으므로 문제가 되지 않는다**.

## **4. `public` 클래스의 가변 필드 노출 문제**

* 자바 플랫폼 라이브러리에도 이 규칙을 어긴 예시들이 있는데, 대표적으로 **`java.awt` 패키지의 `Point`와 `Dimension` 클래스**가 있다. 이런 클래스들은 성능 문제 등으로 인해 나쁜 예로 취급되며, 이러한 접근 방식을 따라하지 말아야 한다.

> **왜 필드를 노출하면 문제가 되나요?**

* **필드를 `public`으로 노출**하면, 외부에서 필드에 직접 접근하고 값을 변경할 수 있다.
* 이는 `객체의 불변식(invariant)`을 깨뜨릴 수 있으며, 예기치 않은 동작을 유발할 수 있다.
* 또한, **내부 구현을 변경하기 어렵게 만들고**, **유효성 검사**나 **부가적인 로직**을 추가하기 어렵게 만든다.

## **5. 불변 필드의 경우에도 노출은 피해야 한다**

* 불변 필드라고 해서 `public`으로 노출하는 것이 항상 안전한 것은  아니다.&#x20;
* **불변 필드라도 노출을 피해야 하는 이유**
  * 불변 필드는 값이 변경되지 않으므로 **무결성 측면에서는 안전하**다.
  * 그러나 **내부 구현을 숨길 수 없으며**,  나중에  **API를 수정하지 않고 내부 표현 방식을 변경할 수 없다**.
  * 또한, **필드를 읽을 때 부수적인 작업**(예: 로그 출력, 접근 권한 확인)을 수행할 수 없다.

**예시:**

```java
public final class Time {
    public final int hour;
    public final int minute;

    public Time(int hour, int minute) {
        // 유효성 검사
        if (hour < 0 || hour >= 24)
            throw new IllegalArgumentException("시간: " + hour);
        if (minute < 0 || minute >= 60)
            throw new IllegalArgumentException("분: " + minute);
        this.hour = hour;
        this.minute = minute;
    }
}
```

* `hour`와 `minute` 필드를 `public final`로 선언하여 **불변 필드로 노출**하고 있다.
* **문제점:**
  * 내부 구현을 변경할 수 없다.
  * 필드 접근 시 **부가적인 로직**을 추가할 수 없다.

**개선된 코드**

```java
public final class Time {
    private final int hour;
    private final int minute;

    public Time(int hour, int minute) {
        // 유효성 검사
        if (hour < 0 || hour >= 24)
            throw new IllegalArgumentException("시간: " + hour);
        if (minute < 0 || minute >= 60)
            throw new IllegalArgumentException("분: " + minute);
        this.hour = hour;
        this.minute = minute;
    }

    public int getHour() {
        // 부가 로직 추가 가능
        return hour;
    }

    public int getMinute() {
        // 부가 로직 추가 가능
        return minute;
    }
}
```

* 필드를 `private final`로 변경하고 **getter 메서드**를 제공한다.
* **장점:**
  * 나중에 내부 구현을 변경하더라도 **외부 API를 수정할 필요가 없다**.
  * 필드에 접근할 때 **부가적인 로직**을 추가할 수 있다.

***

## 6. 리팩터링된 코드 및 설명

위의 `Time` 클래스는 불변성을 보장하지만, 여전히 **필드를 직접 노출**하고 있습니다. **필드 접근을 제한**하면서도 내부 상태를 안전하게 유지할 수 있는 더 나은 방식

**리팩터링된 `Time` 클래스:**

```java
public final class Time {
    private static final int HOURS_PER_DAY = 24;
    private static final int MINUTES_PER_HOUR = 60;
    
    private final int hour;
    private final int minute;

    public Time(int hour, int minute) {
        if (hour < 0 || hour >= HOURS_PER_DAY)
            throw new IllegalArgumentException("시간: " + hour);
        if (minute < 0 || minute >= MINUTES_PER_HOUR)
            throw new IllegalArgumentException("분: " + minute);
        this.hour = hour;
        this.minute = minute;
    }

    public int getHour() {
        return hour;
    }

    public int getMinute() {
        return minute;
    }

    // 추가적인 로직 필요 시 이곳에 부가 메서드를 추가할 수 있음
}
```

**리팩터링 설명:**

1. **필드를 `private`으로 변경**: `hour`와 `minute` 필드를 `private`으로 감추고, **getter 메서드**를 통해 값을 제공하도록 수정했습니다. 이렇게 하면 **직접 필드에 접근할 수 없게 되어** 내부 상태가 보호된다.
2. **불변성을 유지**: 클래스의 불변성은 유지되며, **필드에 직접 접근할 수 없는 대신 메서드를 통해 값에 접근**할 수 있다.
3. **캡슐화와 유연성**: 이러한 방식은 이후 **내부 표현을 변경해야 할 때, 외부 API를 수정할 필요 없이 내부 로직만 수정**하면 되도록 유연성을 제공한다.

이제 **클라이언트 코드**가 `Time` 객체의 필드를 직접 수정하지 못하며, 객체 내부 상태를 안전하게 보호할 수 있다. **불변식**도 유지되며, 필드 접근 시 필요에 따라 **부수 작업**을 추가할 수 있는 구조를 갖추게 된다.

## 7. 부가적인 예제

> 가변 필드와 불변 필드란 무엇인가요?

**가변 필드(Mutable Field)**

* **가변 필드**는 **값을 변경할 수 있는 필드**를 말한다.
* 보통 필드에 대한 **setter 메서드**를 제공하거나, 필드가 `public`으로 선언되어 외부에서 직접 값을 변경할 수 있을 때 가변 필드가 된다.
* 예를 들어, `int age;` 필드가 있고, 외부에서 `person.age = 30;`으로 값을 변경할 수 있다면, `age`는 가변 필드이다.

**불변 필드(Immutable Field)**

* **불변 필드**는 **값을 변경할 수 없는 필드**를 말합니다.
* 필드를 `private final`로 선언하고, **setter 메서드를 제공하지 않으면** 불변 필드가 됩니다.
* 초기화 이후에 값이 변경되지 않으므로 객체의 상태가 안정적입니다.
* 예를 들어, `private final String id;` 필드는 불변 필드입니다.



**필드 노출의 문제점 예시**

```java
public class BankAccount {
    public double balance; // 계좌 잔액을 public으로 노출

    public BankAccount(double initialBalance) {
        this.balance = initialBalance;
    }
}
```

* 외부에서 `account.balance = -1000;`처럼 **계좌 잔액을 음수로 설정**할 수 있다.
* 이는 **논리적으로 말이 안 되며**, **은행 시스템의 무결성**을 해친다.

**개선된 BankAccount 클래스**

```java
public class BankAccount {
    private double balance; // 필드를 private으로 숨김

    public BankAccount(double initialBalance) {
        if (initialBalance < 0)
            throw new IllegalArgumentException("초기 잔액은 음수일 수 없습니다.");
        this.balance = initialBalance;
    }

    public double getBalance() {
        return balance;
    }

    public void deposit(double amount) {
        if (amount <= 0)
            throw new IllegalArgumentException("입금 금액은 양수여야 합니다.");
        balance += amount;
    }

    public void withdraw(double amount) {
        if (amount <= 0)
            throw new IllegalArgumentException("출금 금액은 양수여야 합니다.");
        if (balance < amount)
            throw new IllegalArgumentException("잔액이 부족합니다.");
        balance -= amount;
    }
}
```

* **메서드를 통해서만 잔액을 변경**할 수 있다.
* **유효성 검사**를 통해 잘못된 금액이 입력되지 않도록 한다.

## **✨** 정리&#x20;

* **`public` 클래스는 가변 필드를 직접 노출하지 말아야** 하며, 불변 필드라 하더라도 API 유연성을 위해 노출을 피하는 것이 좋다.
* **패키지-프라이빗 클래스**나 **private 중첩 클래스**에서는 때때로 필드를 직접 노출하는 것이 더 간결하고 유리할 수 있다.
* **캡슐화**를 통해 클래스 내부의 필드를 보호하고, 필드의 값을 관리하기 위해 **접근자(getter)와 변경자(setter) 메서드**를 사용하는 것이 좋다.
