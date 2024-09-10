# item 03 : private 생성자나 열거 타입으로 싱글턴임을 보증하라.

## 싱글턴임을 보증하라

### 싱글턴(singleton)이란?

* <mark style="color:red;">인스턴스를 오직 하나만 생성할 수 있는 클래스</mark>를 말한다.
* 전형적인 예로는 함수와 같은 무상태(stateless) 객체나 설계상 유일해야 하는 시스템 컴포넌트

> 클래스를 싱글턴으로 만들면 **이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있다.**

이유는 타입을 `인터페이스`**로 정의한 다음 그 인터페이스를 구현해서 만든 싱글턴이 아니라면** 싱글턴 인스턴스를 **가짜(MOCK) 구현으로 대체할 수 없기 때문**이다.

### **싱글턴을 만드는 방식**

> 두 방식 모두 **생성자는 private**으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 `public static 멤버`를 하나 마련

### 방식 1 :  publioc static 멤버가 final 필드인 방식

```java
public class Elvis {
    // 유일한 인스턴스를 생성
    public static final Elvis INSTANCE = new Elvis();

    // 생성자를 private으로 설정하여 외부에서 인스턴스를 생성하지 못하게 함
    private Elvis() {
        // 초기화 코드 (필요한 경우)
    }

    // Elvis가 떠나는 행동을 정의하는 메서드
    public void leaveTheBuilding() {
        // 구현 코드
    }
}
```

private 생성자는 public static final 필드인 **Elvis.INSTANCE를 초기화할 때 딱 한 번만 호출**된다. public이나 protected 생성자가 없으므로 Elvis 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.&#x20;

#### 클라이언트 생성자 호출 방법

* **리플렉션 API**: Java의 리플렉션 API는 런타임에 클래스, 메서드, 필드 등의 정보를 동적으로 조사하고 조작할 수 있는 기능을 제공
* **AccessibleObject.setAccessible**: 이 메서드는 `private` 또는 `protected`로 설정된 필드나 메서드에 접근할 수 있게 해준다. 즉, 일반적으로 접근이 제한된 요소에 접근할 수 있도록 허용한다.
* 권한이 있는 클라이언트(예: 특정 설정이나 보안 권한을 가진 코드)는 리플렉션을 사용하여 `private` 생성자를 호출할 수 있다. 이 경우, 싱글턴 패턴의 의도가 무시되고, 여러 개의 인스턴스가 생성될 수 있다.

> 이러한 공격을 방어하려면, 생성자를 수정하여 **두 번째 객체가 생성되려 할 때 예외**를 던지게 하면 됨

### 방식 2 : 정적 팩터리 메서드를 public static 멤버로 제공

```java
public class Elvis {
    // 유일한 인스턴스를 생성
    private static final Elvis INSTANCE = new Elvis();

    // private 생성자
    private Elvis() {
        // 초기화 코드 (필요한 경우)
    }

    // 인스턴스를 반환하는 메서드(새로)추가됨
    public static Elvis getInstance() {
        return INSTANCE;
    }

    // Elvis가 떠나는 행동을 정의하는 메서드
    public void leaveTheBuilding() {
        // 구현 코드
    }
}

```

`Elvis, getInstance` 는 항상 같은 객체의 참조를 반환하므로 제2의 Elvis 인스턴스란 결코 만들어지지 않다（역시 리플렉션을 통한 예외는 똑같이 적용된다）



