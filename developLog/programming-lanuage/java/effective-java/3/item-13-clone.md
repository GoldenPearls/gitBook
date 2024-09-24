---
description: 이번 아이템은 clone()을 사용할 때 주의점을 다룬다.
---

# item 13 : clone 재정의는 주의해서 진행해라

## 1. Cloneable 인터페이스

### Cloneaable 인터페이스란?

* 일종의 `maker interface`로 'cloen에 의해 복제할 수 있다'를 표시하는 인터페이스이다.
* 복제해도 되는 클래스임을 명시하는 용도의 믹스인 인터페이스

Java에서는 인스턴스의 복제를 위해 `clone()` 메서드가 구현되어 있다.

신기하게도 이 메서드는 `Cloneable` 내부에 구현되어 있을 거란 예상을 깨고 `java.lang.Object` 클래스에 protected 접근 지정자로 구현되어 있다. 내부에는 구현해야 할 메서드가 하나도 없다!

### Cloneaable 인터페이스 하는 일

* Object의 <mark style="color:red;">protected 메서드인 clone의 동작 방식</mark>을 결정한다.
* Cloneable의 경우, 일반적으로 인터페이스를 구현한다는 것 즉, 해당 클래스가 그 인터페이스에서 정의한 기능을 제공한다고 선언하는 것과 달리 상위 클래스에 정의된 <mark style="color:red;">protected 메서드의 동작방식을 변경한 것</mark>

#### **사용법**

```java
public class Pokemon implements Cloneable {
    private List<String> skills;
    private int attack;
    private int defense;
    private int hp;

    public Pokemon(List<String> skills, int attack, int defense, int hp) {
        this.skills = skills;
        this.attack = attack;
        this.defense = defense;
        this.hp = hp;
    }

    @Override // clone() 구현..!!
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    // getter, seterr ...
}
```

복사할 객체에 `Cloneable`을 상속시켜 준 뒤 오버라이딩해 주면 된다.

`clone()`을 호출하면 인스턴스와 같은 크기의 메모리를 할당하고, <mark style="background-color:red;">인스턴스의 필드를 그대로 복사한 복사본을 리턴</mark>한다.

## 2. 재 정의시 문제점

> 🤔 Cloneable을 구현한 클래스는 clone 메서드를 public으로 제공하며, 사용자는 당연히 복제가 제대로 이뤄지겠지? 라고 실무에서는 기대

이 기대를 만족시키려면 그 클래스와 모든 상위 클래스는 복잡 하고, 강제할 수 없고, 허술하게 기술된 프로토콜을 지켜야만 하는데, 그 결과 로 <mark style="color:red;">깨지기 쉽고, 위험하고, 모순적인 메커니즘이 탄생</mark>한다. 생성자를 호출하지 않고도 객체를 생성할 수 있게 되는 것이다.





## **✨ 결론**

`Cloneable`이 몰고 온 모든 문제를 되짚어봤을 때, <mark style="color:red;">새로운 인터페이스를 만들 때는 절대 Cloneable을 확장해서는 안 되며, 새로운 클래스도 이를 구현해서는 안 된다.</mark> `final 클 래스`라면 Cloneable을 구현해도 위험이 크지 않지만, 성능 최적화 관점에서 검토한 후 별다른 문제가 없을 때만 드물게 허용해야 한다(아이템 67). 기본 원칙은 ‘복제 기능은 생성자와 팩터리를 이용하는 게 최고’라는 것이다. 단, `배열`만은 clone 메서드 방식이 가 장 깔끔한, 이 규칙의 합당한 예외라 할 수 있다.
