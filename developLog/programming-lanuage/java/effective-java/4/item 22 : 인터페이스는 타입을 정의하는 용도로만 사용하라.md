# item 22 : 인터페이스는 타입을 정의하는 용도로만 사용하라

`인터페이스`는 **자신을 구현한 클래스의 인스턴스를 참조할 수 있는 타입 역할**을 한다. 달리 말해, 클래스가 어떤 인터페이스를 구현한다는 것은 <mark style="color:red;">자신의 인스턴스로 무엇을 할 수 있는지를 클라이언트에 얘기해주는 것</mark>이다. 인터페이스는 이와 같은 용도로만 사용해야 한다.



## 1. 안티 패턴 : 상수 인터페이스

> 상수 인터페이스란 메서드 없이, 상수를 뜻하는 static final 필드로만 가득 찬 인터페이스를 말한다.

```java
public interface PhysicalConstants {
    // 아보가드로 수 (1/몰)
    static final double AVOGADROS_NUMBER = 6.022_140_857e23;
    // 볼츠만 상수 (JAO
    static final double BOLTZMW_CONSTANT = 1.380_648_52e-23;
    // 전자 질량 (kg)
    static final double ELECTRON_MASS = 9.109_383_56e-31;
}
```

상수 인터페이스 안티패턴은 인터페이스를 **잘못 사용한 예**다. 클래스 내부에서 사용하는 상수는 외부 인터페이스가 아니라 **내부 구현에 해당**한다. 따라서 상수 인터페이스를 구현하는 것은 <mark style="color:red;">이 내부 구현을 클래스의 API로 노출하는 행위</mark>다.

* 사용자에게는 아무런 의미가 없다. 오히려 사용자에게 혼란을 주기도 하며, 더 심하게는 클라이언트 코 드가 내부 구현에 해당하는 이 상수들에 종속되게 한다.
* **final이 아닌 클래스가 상수 인터페이스를 구현한다면** 모든 하위 클래스의 이름공간이 그 인터페이스가 정의한 상수들로 오염되어 버린다.

## 2. 상수를 공개할 때의 올바른 방법

### 1) **상수의 위치**

상수를 공개할 때는 그 상수가 **어디에 위치해야 할지** 신중히 결정해야 한다. 상수가 특정 클래스나 인터페이스와 강하게 연관되어 있다면 그 **클래스나 인터페이스 내부**에 포함하는 것이 적절히다. 예를 들어, 자바의 기본 타입 박싱 클래스인 `Integer`와 `Double`에는 각각 최소값과 최대값을 나타내는 상수 `MIN_VALUE`, `MAX_VALUE`가 포함되어 있다.

### 2) **열거 타입 사용**

상수가 **몇 가지 한정된 값** 중 하나일 경우, `열거형(enum)`을 사용하여 상수를 나타내는 것이 좋다. 예를 들어, 요일이나 방향을 나타낼 때 열거 타입으로 나타내는 것이 적절하다. 열거 타입은 값의 유효성을 보장하고, 타입 안전성을 제공한다(아이템 34에서 다룸)

### 3) **인스턴스화할 수 없는 유틸리티 클래스 사용**

어떤 클래스나 인터페이스와 직접적으로 연관되지 않은 상수는 **인스턴스화할 수 없는 유틸리티 클래스**에 담아 공개하는 것이 좋다.&#x20;

`유틸리티 클래스`는 상수나 정적 메서드를 제공하는 클래스인데, 이 클래스는 **인스턴스화하지 않도록** 설계되어야 한다. 이를 위해 **private 생성자**를 추가하여 외부에서 객체를 생성하지 못하게 만든다.

#### 코드 예시: 상수를 포함한 유틸리티 클래스

```java
package effectivejava.chapter4.item22.constantutilityclass;

public class PhysicalConstants {
    private PhysicalConstants() { } // 인스턴스화 방지

    // 아보가드로 수 (1/몰)
    public static final double AVOGADROS_NUMBER = 6.022_140_857e23;
    
    // 볼츠만 상수 (J/K)
    public static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;
    
    // 전자 질량 (kg)
    public static final double ELECTRON_MASS = 9.109_383_56e-31;
}
```

이 코드는 **물리 상수**를 담은 유틸리티 클래스를 정의한 예시이다. 각 상수는 `public static final`로 선언되어 있으며, 외부에서 객체를 생성할 수 없도록 **private 생성자**가 추가되어 있다.

### **4) 정적 임포트 (static import) 사용**

유틸리티 클래스에 정의된 상수를 빈번히 사용하는 경우 **정적 임포트**를 활용해 클래스 이름을 생략하고 상수 이름만 사용할 수 있다. <mark style="color:red;">정적 임포트를 사용하면 코드가 간결해지지만, 남용하면 코드의 가독성을 떨어뜨릴 수 있으므로 필요한 경우에만 사용하는 것이 좋다.</mark>

```java
import static effectivejava.chapter4.item22.constantutilityclass.PhysicalConstants.*;

public class Test {
    double atoms(double mols) {
        return AVOGADROS_NUMBER * mols;
    }
}
```

이 예시는 `PhysicalConstants` 클래스에서 상수를 가져와 사용하는 예이다. **정적 임포트**를 사용했기 때문에 `AVOGADROS_NUMBER`를 클래스 이름 없이 직접 사용할 수 있다.



## **✨ 최종 정리**

1. 상수를 공개할 때는 **연관된 클래스나 인터페이스 내부**에 포함하거나, 그렇지 않다면 **인스턴스화할 수 없는 유틸리티 클래스**에 담아야 한다.
2. 상수가 **한정된 값의 집합**이라면 열거 타입(enum)으로 정의하는 것이 적절하다.
3. 상수가 자주 사용된다면 정적 임포트(static import)를 통해 간편하게 사용할 수 있지만, 과도하게 사용하지 않도록 주의해야 한다.
4. 상수는 `public static final`로 선언해 불변성을 보장하고, 유틸리티 클래스는 **private 생성자**로 인스턴스화를 방지한다.

{% hint style="success" %}
인터페이스는 타입을 정의하는 용도로만 사용해야 한다. 상수 공개용 수단으로 사용하지 말자.
{% endhint %}
