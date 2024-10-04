# item 17 : 변경 가능성을 최소화하라

## 1. 불편 클래스

{% hint style="info" %}
불변 클래스는 가변 클래스보다 설계하고 구현하고 사용하기 쉬우며, 오류가 생길 여지도 적고 훨씬 안전하다.
{% endhint %}

### 1) 불변 클래스란?

인스턴스의 내부 값을 수정할 수 없는 클래스다. 불변 인스턴스에 간직된 정보는 고정되어 객체가 파괴되는 순간까지 절대 달라지지 않는다.

## 2. 클래스를 불변으로  만들기 위해  지켜야 하는 5가지 규칙

### 1) 객체의 상태를 변경하는 메서드(변경자) setter를 제공하지 않는다.&#x20;

> [🧐 ](https://ttl-blog.tistory.com/1205#%F0%9F%A7%90%20%EA%B0%9D%EC%B2%B4%EC%9D%98%20%EC%83%81%ED%83%9C%EB%A5%BC%20%EB%B3%80%EA%B2%BD%ED%95%98%EB%8A%94%20%EB%A9%94%EC%84%9C%EB%93%9C%EB%A5%BC%20%EC%A0%9C%EA%B3%B5%ED%95%98%EC%A7%80%20%EC%95%8A%EB%8A%94%EB%8B%A4-1)만약 객체의 상태를 변경하는 메서드를 제공한다면?

```java
public class Person {

    private String name;

    public Person(final String name) {
        this.name = name;
    }

    public String name() {
        return name;
    }
    
    /* 변경자 */
    public void setName(final String name) {
        this.name = name;
    }
}
```

바깥에서 객체의 상태를 변경할 수 있음

```java
public class Main {

    public static void main(String[] args) {
        Person boki = new Person("boki ");
        System.out.println(boki .name());
        boki.setName("lotto");
        System.out.println(boki.name()); // lotto
    }
}

```

클래스를 불변으로 만들 수 없음



### 2) 클래스를 확장할 수 없도록 한다.&#x20;

하위 클래스에서 부주의하게 혹은 나쁜 의도로 객체의 상태를 변하게 만드는 사태를 막아준다. **상속을 막는 대표적 인 방법은 클래스**를 `final`로 선언하는 것이다.



상속의 방법을 막는 방법 2가지

1. **클래스를 final로 설정하는 것**

> 클래스를 상속받을 수 있다면, 해당 클래스는 값이 변경될 수 있음을 의미하는데, 이게 어떻게 가능할까를 final로 하지 않았을 때를 보면!

```java
public class Animal {

    private String type;

    public Animal(final String type) {
        this.type = type;
    }

    public String type() {
        return type; // type() 메서드를 통해 type 필드의 값을 반환
    }
}
```

setter를 제거함으로써 객체의 상태를 변경하는 메서드 제공 하지 않도록 수정

{% hint style="success" %}
그렇다면 값을 바꾸려면? private로 내부 값 바꿀 수는 없으나 **바뀐 것처럼 사용 가능**
{% endhint %}

**`Dog` 클래스 (Animal을 상속):**

```java
public class Dog extends Animal {

    private String type;

    // type 필드를 private으로 선언하고, 생성자를 통해 초기화
    public Dog(final String type) {
        super(type);
        this.type = "Unknown Type " + type;  // 생성자에서 this.type에 "Unknown Type " 문자열을 붙여 초기화
    }

    @Override
    public String type() {
        return "Dog Type: " + this.type;
    }
}
```

**`Main` 클래스:**

```java
public class Main {

    // animal.type()을 호출하면 Dog 클래스에서 오버라이딩된 type() 메서드가 호출
    public static void main(String[] args) {
        Animal animal = new Dog("Bulldog");
        System.out.println(animal.type());
    }
}
```

#### 실행 결과:

```python
Dog Type: Unknown Type Bulldog
```

#### 이 코드가 보여주는 점:

* **상속을 통한 동작 변경**: `Dog` 클래스는 `Animal` 클래스를 상속받아 `type()` 메서드를 오버라이딩하고 있다. 이를 통해 부모 클래스의 동작을 자식 클래스에서 변경할 수 있음을 보여준다.
* **필드 숨김(Field Hiding)**: `Dog` 클래스에서 `type` 필드를 다시 선언함으로써, 부모 클래스의 `type` 필드를 가리고 있다. 이는 좋은 설계가 아니며, 혼란을 초래할 수 있다.
* **불변성 위반 가능성**: `Animal` 클래스는 자신의 필드를 `private`으로 선언하고 있지만, 상속을 통해 자식 클래스에서 동작이 변경될 수 있다. 따라서 **불변성을 보장하기 위해서는 클래스의 상속을 막거나(final 클래스), 필드를 `final`로 선언하는 등의 조치가 필요하**다.

#### 불변성을 유지하는 방법:

* **클래스를 `final`로 선언**하여 상속을 금지

```java
public final class Animal {
    // 클래스 내용
}
```

* **필드를 `private final`로 선언**하여 필드가 한 번 초기화된 후 변경되지 않도록 한다.

```java
private final String type;
```

* **메서드를 `final`로 선언**하여 오버라이딩을 방지한다.

```java
public final String type() {
    return type;
}
```

#### 수정된 `Animal` 클래스 (불변성을 유지하도록):

```java
public final class Animal {

    private final String type;

    public Animal(final String type) {
        this.type = type;
    }

    public String getType() {
        return type;
    }
}
```

이렇게 수정하면 `Animal` 클래스를 상속할 수 없으며, 필드도 변경할 수 없게 되어 **불변성이 유지**된다.

{% hint style="warning" %}
여기서 의문 : 이렇게하면클라이언트가 결국 수정도 못해서 불변 클래스는 오히려 안좋은게 아닌가?
{% endhint %}

답변 : 불변 클래스를 사용하면 클라이언트가 객체의 상태를 직접 수정할 수 없기 때문에 불편하다고 느낄 수 있습니다. 하지만 불변 클래스는 여러 가지 **장점**을 제공하며, 이러한 이점을 통해 코드의 안정성과 신뢰성을 높일 수 있습니다.

#### 클라이언트가 값을 변경하고 싶을 때

불변 클래스에서는 객체의 상태를 직접 변경할 수 없지만, **변경된 값을 가진 새로운 객체를 생성하여 반환**하는 방법을 사용할 수 있습니다. 이는 함수형 프로그래밍에서 흔히 사용하는 패턴으로, 객체의 불변성을 유지하면서 필요한 변경을 가능하게 합니다.

**예시: `Animal` 클래스에 변경 메서드 추가**

```java
public final class Animal {

    private final String type;

    public Animal(final String type) {
        this.type = type;
    }

    public String getType() {
        return type;
    }

    // 새로운 타입의 Animal 객체를 생성하여 반환하는 메서드
    public Animal withType(String newType) {
        return new Animal(newType);
    }
}
```

**사용 방법**

```java
public class Main {

    public static void main(String[] args) {
        Animal animal = new Animal("Cat");

        // 기존 객체를 수정하는 것이 아니라 새로운 객체를 생성
        Animal newAnimal = animal.withType("Dog");

        System.out.println(animal.getType());      // 출력: Cat
        System.out.println(newAnimal.getType());   // 출력: Dog
    }
}
```

위 예시에서 `animal` 객체는 여전히 `"Cat"` 타입을 유지하며, `newAnimal`은 `"Dog"` 타입을 갖는 새로운 객체입니다. 이처럼 불변 객체를 사용하면 원본 객체의 상태를 변경하지 않으면서도 원하는 값을 가진 객체를 생성할 수 있습니다.

불변 클래스는 다음과 같은 이유로 유용합니다:

* **안전하고 신뢰할 수 있는 코드 작성**: 상태 변경에 따른 예기치 않은 버그를 줄일 수 있습니다.
* **멀티스레드 환경에서의 안전성 확보**: 동기화 없이도 스레드 안전성을 보장합니다.
* **유지보수 용이성**: 객체의 상태가 변하지 않으므로 코드의 복잡성이 감소합니다.

{% hint style="danger" %}
결국 여러개의 객체를 생성하는 건 메모리를 많이 차지하는 것과도 연관이 있는거 아닌가?
{% endhint %}

답변 : 맞습니다. **불변 객체를 사용할 때 변경할 때마다 새로운 객체를 생성**하므로, **메모리 사용량이 증가**할 수 있다는 우려가 있습니다. 이는 불변 클래스의 단점 중 하나로 언급되기도 합니다. 하지만 실제로는 이러한 메모리 사용 증가가 **심각한 문제를 일으키는 경우는 드뭅니다**.&#x20;

* **불변 객체 사용으로 인한 메모리 사용 증가**는 존재하지만, **현대 JVM의 최적화와 프로그래밍 기법**을 통해 **그 영향을 최소화**할 수 있습니다.
* **불변 클래스의 장점**인 **안정성**, **스레드 안전성**, **유지보수성 향상**은 메모리 사용량 증가로 인한 단점을 상쇄하고도 남습니다.
* **실제 개발에서는 불변 객체를 우선적으로 사용**하고, **메모리 사용이 문제가 되는 부분에 한해 최적화 기법을 적용**하는 것이 좋습니다.



2. **public 정적 팩터리 메서드를 제공하는 방법**

> 모든 생성자를 private 혹은 package-private(default)로 만들 후, public 정적 팩터리 메서드를 제공하는 방법

```java
public final class Animal {

    private final String type;

    // private 생성자: 외부에서 직접 인스턴스 생성 불가
    private Animal(final String type) {
        this.type = type;
    }

    // public 정적 팩토리 메서드
    public static Animal of(String type) {
        return new Animal(type);
    }

    public String getType() {
        return type;
    }
}

```

package 외부에서는 사실상 final 클래스와 동일하게 동작할 뿐더러, 내부에서는 해당 클래스를 상속하여 여러 클래스를 생성할 수 있기 때문에 훨씬 유연한 방법

#### 사용법

```java
public class Main {

    public static void main(String[] args) {
        Animal cat = Animal.of("Cat");
        Animal dog = Animal.of("Dog");

        System.out.println(cat.getType()); // 출력: Cat
        System.out.println(dog.getType()); // 출력: Dog
    }
}

```

#### 🌱   인스턴스 캐싱을 통한 메모리 효율 개선

정적 팩토리 메서드를 사용하면 **캐싱**을 통해 동일한 값을 가진 객체의 중복 생성을 방지할 수 있다.&#x20;

```java
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

public final class Animal {

    private static final Map<String, Animal> CACHE = new ConcurrentHashMap<>();

    private final String type;

    private Animal(final String type) {
        this.type = type;
    }

    public static Animal of(String type) {
        // 이미 해당 타입의 객체가 존재하면 재사용
        return CACHE.computeIfAbsent(type, t -> new Animal(t));
    }

    public String getType() {
        return type;
    }
}
```

**설명**

* **`CACHE` 맵**:
  * `ConcurrentHashMap`을 사용하여 스레드 안전하게 캐싱을 구현한다.
  * 키는 `type` 문자열이고, 값은 해당 `Animal` 객체이다.
* **`computeIfAbsent` 메서드**:
  * `CACHE`에 해당 `type`의 객체가 없을 경우에만 새로운 객체를 생성하고, 이미 존재하면 그 객체를 반환한다.
* **메모리 사용량 감소**:
  * 동일한 `type`을 가진 객체를 재사용하므로 **불필요한 객체 생성을 줄여 메모리 효율을 높일 수 있다**.

#### 캐싱된 객체 사용 예시

```java
public class Main {

    public static void main(String[] args) {
        Animal animal1 = Animal.of("Dog");
        Animal animal2 = Animal.of("Dog");

        System.out.println(animal1 == animal2); // 출력: true (같은 인스턴스)
    }
}
```

* `animal1`과 `animal2`는 동일한 인스턴스를 참조하므로 `==` 연산 결과가 `true`이다.
* 이는 메모리 사용량을 줄이고 객체 비교 시 효율성을 높인다.

### 3) 모든 필드를 final로 선언한다. 시스템이 강제하는 수단을 이용해 설계자 의 의도를 명확히 드러내는 방법이다.&#x20;

자바 언어에서 **final**을 사용하면 값을 바꿀 수 없기 때문에, 명시적으로 값을 `불변`으로 만들 수 있다.

새로 생성된 인스턴스를 동기화 없이 다른 스레드에 전달하더라도 문제없이 동작하게끔 동작하는 데에도 필요하다.

&#x20;

### 4) 모든 필드를 private으로 선언한다.&#x20;

필드가 참조하는 가변 객체를 클라이언트에서 직접 접근해 수정하는 일을 막아준다. 기술적으로는 기본 타입 필드 나 불변 객체를 참조하는 필드를 public final로만 선언해도 불변 객체가 되지만, 이렇게 하면 다음 릴리스에서 내부 표현을 바꾸지 못하므로 권하지는 않는다.



### 5) 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다.

> 클래스에 가변 객체를 참조하는 필드가 하나라도 있다면 클라이언트에서 그 객체의 참조를 얻을 수 없도록 해야 한다.&#x20;

이런 필드는 절대 클라이언트가 제공한 객 체 참조를 가리키게 해서는 안 되며, 접근자 메서드가 그 필드를 그대로 반환해서도 안 된다. 생성자, 접근자, readObject 메서드 모두에서 방어 적 복사를 수행하라.



## 3. 이 모든 것을 지키는 클래스 예제

```java
import java.util.Date;

public final class Person {
    // 1) 모든 필드를 private final로 선언
    private final String name;
    private final Date birthDate; // Date는 가변 객체이므로 방어적 복사 필요

    // 2) 생성자를 통해 필드를 초기화하고, 가변 객체는 방어적 복사 수행
    public Person(String name, Date birthDate) {
        this.name = name;
        // 방어적 복사 (외부에서 전달된 birthDate의 참조를 직접 사용하지 않음)
        this.birthDate = new Date(birthDate.getTime());
    }

    // 3) getter 메서드도 가변 객체에 대해 방어적 복사를 수행
    public String getName() {
        return name; // String은 불변 객체라 방어적 복사 필요 없음
    }

    public Date getBirthDate() {
        // 방어적 복사 (내부의 Date 객체를 외부에 그대로 반환하지 않음)
        return new Date(birthDate.getTime());
    }

    // 4) setter 메서드 제공하지 않음 (객체 상태 변경 불가)
    // 이 클래스에는 상태를 변경하는 메서드가 없음
    
    // 5) 이 클래스는 상속할 수 없도록 final로 선언됨
}

```

**1) 모든 필드를 `private final`로 선언**

* **필드 접근을 `private`로 제한**하여 외부에서 직접 접근하지 못하게 하고, `final`로 선언해 초기화 후 필드의 값을 변경할 수 없도록 했다.

**2) 생성자에서 방어적 복사 수행**

* **생성자**에서 외부에서 전달된 `Date` 객체가 원본 그대로 저장되지 않도록 **복사본**을 만들어 저장했다. 이렇게 하면 외부에서 전달한 `Date` 객체를 수정하더라도 `Person` 객체의 `birthDate` 필드에 영향을 줄 수 없다.

**3) `getter` 메서드에서 방어적 복사 수행**

* **`getBirthDate` 메서드**에서는 원본 `birthDate` 객체 대신 **복사본**을 반환한다. 이를 통해 외부 코드에서 반환된 `Date` 객체를 변경하더라도, `Person` 객체의 필드에는 영향을 주지 않는다.

**4) setter 메서드를 제공하지 않음**

* **변경자(setter) 메서드**를 제공하지 않아서, **객체가 생성된 후 필드 값을 변경할 수 없도록** 했다.

**5) 상속 불가 (`final` 클래스)**

* 이 클래스는 **`final`로 선언**되어 있어, **상속을 통해 확장할 수 없도록** 했다. 이를 통해 하위 클래스가 부모 클래스의 불변성을 해치거나 상태를 변경하는 것을 방지했다.



## 4. 불변 객체의 장단점

### **1) 불변 객체의 장점**

* **스레드 안전성**: 불변 객체는 스레드 간에 안전하게 공유될 수 있으므로 **동기화 작업 없이**도 **안전하게 사용**할 수 있다.
* **안정성**: 불변 객체는 상태가 변하지 않으므로 **안정적**이고 **예측 가능한 동작**을 합니다. **함수형 프로그래밍**에서 자주 사용하는 패턴이다.
* **캐싱 및 재사용 가능성**: 불변 객체는 **공유** 및 **재사용**이 가능하다. 예를 들어 `ZERO`, `ONE`, `I`와 같은 상수를 재사용하여 메모리 사용을 최적화할 수 있다.

### **2) 불변 객체의 단점**

* **값 변경 시 새 객체 생성**: 불변 객체는 값을 변경하려면 항상 **새로운 객체를 생성**해야 한다. 이로 인해 성능 저하나 메모리 사용 증가가 발생할 수 있다.
  * 예를 들어, `Complex` 객체에서 덧셈이나 뺄셈을 수행할 때마다 새로운 `Complex` 객체가 생성되며, 원래 객체는 그대로 남는다.
  * 큰 객체나 다단계 연산을 수행할 때는 **불변 객체의 성능 문제**가 두드러질 수 있다. 이런 경우 가변 동반 클래스를 사용하거나 다단계 연산을 위한 최적화를 적용할 수 있다.



## 정리

1. Getter가 있다고 해서 무조건 Setter를 만들지 말자. 클래스가 꼭 필요한 경우가 아니라면 불변임을 보장하는 것이 설계적 관점에서 바람직하다.
2. 모든 클래스를 불변으로 만들 수 없다. 하지만 가변 클래스더라도 변경할 수 있는 부분은 최소한으로 줄이자. (변경이 없게 만든 후 모두 `final` 선언을 하고, 특별한 이유가 없다면 모두 private final 선언을 하자.)
3. 생성자는 불변식 설정이 모두 완료된 상태의 객체를 생성해야 한다. **즉, 확실한 이유가 없다면 생성자와 정적 팩터리 외에는 그 어떤 초기화 메서드도 public으로 제공해서는 안된다**. 객체를 재사용할 목적으로 상태를 다시 초기화하는 메서드도 올바르지 않다.



> 참고 글 : [https://ttl-blog.tistory.com/1205#%F0%9F%A7%90%20%EB%B6%88%EB%B3%80%20%EA%B0%9D%EC%B2%B4%20%EC%A0%95%EB%A6%AC%ED%95%98%EA%B8%B0-1](https://ttl-blog.tistory.com/1205#%F0%9F%A7%90%20%EB%B6%88%EB%B3%80%20%EA%B0%9D%EC%B2%B4%20%EC%A0%95%EB%A6%AC%ED%95%98%EA%B8%B0-1)
