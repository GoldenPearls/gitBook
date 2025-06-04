# item 40 : @Override 애너테이션을 일관되게 사용하라

자바가 기본으로 제공하는 애너테이션 중, 프로그래머에게 가장 중요한 것은 `@Override` 애너테이션이다. `@Override`는 **메서드 선언**에만 달 수 있으며, 이 애너테이션이 달렸다는 것은 **상위 타입의 메서드를 재정의** 했음을 뜻한다.

**해당 애너테이션을 일관되게 사용**한다면 여러 가지 버그들을 예방해둔다.

## 1. @Override를 일관되게 사용해야 하는 이유 <a href="#override" id="override"></a>

### 1) 영어 알파벳 2개로 구성된 문자열(바이그램)을 표현하는 클래스

```java
public class Bigram {
    private final char first;
    private final char second;

    // 두 개의 문자로 Bigram 객체를 생성하는 생성자
    public Bigram(char first, char second) { 
        this.first = first;
        this.second = second;
    }

    // Bigram 객체와 비교하는 equals 메서드 (문제가 있는 부분)
    public boolean equals(Bigram b) {
        return b.first == first && b.second == second;
    }

    // 해시코드 생성 메서드
    public int hashCode() {
        return 31 * first + second; 
    }

    public static void main(String[] args) { 
        Set<Bigram> s = new HashSet<>();
        // 10번 반복
        for (int i = 0; i < 10; i++)
            // 'a'부터 'z'까지의 문자 쌍을 생성하여 집합에 추가
            for (char ch = 'a'; ch <= 'z'; ch++) 
                s.add(new Bigram(ch, ch));
        // 집합의 크기 출력
        System.out.println(s.size()); 
    } 
}

```

`main` 메서드를 보면, 동일한 소문자 두 개로 구성된 바이그램 26개를 10번 반복하여 집합에 추가한 후, 그 집합의 크기를 출력하고 있다. `Set`은 중복을 허용하지 않으므로, 예상대로라면 출력값은 `26`이어야 합니다. <mark style="color:red;">그러나 실제로 실행해보면</mark> <mark style="color:red;"></mark><mark style="color:red;">`260`</mark><mark style="color:red;">이 출력된다.</mark> 무엇이 문제일까?

### 2) 문제의 원인

이 문제의 핵심은 `equals` 메서드의 **오버로딩(Overloading)과 오버라이딩(Overriding)을 혼동**한 데 있다. 해당 equals 메소드를 재정의(`overriding`)한 게 아니라 **다중정의(`overloading`)** 해버린 것이다. `Bigram` 클래스의 `equals` 메서드는 다음과 같이 정의되어 있다.

```java
public boolean equals(Bigram b) {
    return b.first == first && b.second == second;
}
```

여기서 매개변수 타입이 `Bigram`이다.&#x20;

> 그러나 <mark style="color:red;">`Object`</mark><mark style="color:red;">의</mark> <mark style="color:red;"></mark><mark style="color:red;">`equals`</mark> <mark style="color:red;"></mark><mark style="color:red;">메서드를 재정의하려면 매개변수 타입이 반드시</mark> <mark style="color:red;"></mark><mark style="color:red;">`Object`</mark><mark style="color:red;">여야 한다.</mark>&#x20;

Object의 `equals()` 를 재정의하려면 매개변수 타입을 Object로 해야 하는데 그렇지 않아서 **상속이 아닌 별개의 equals 메서드를 정의한 꼴**이 되었다.

이로 인해 `Object`의 기본 `equals` 메서드가 그대로 남아 있게 되고, `==` 연산자와 똑같이 객체의 식별성, 참조 동일성((identity)만 확인하고 있기 때문에 문제가 생김

{% hint style="danger" %}
따라서 **내용이 같은 바이그램 객체라도 서로 다른 객체로 인식**되어 소문자를 소유한 바이그램 10개 각각이 다른 객체로 인식되고 모두 집합에 추가되어 결국 260이 출력된 것이다.&#x20;
{% endhint %}

### 3) 해결 방법

이러한 문제는 **컴파일러가 잡아낼 수 있다**. `equals` 메서드에 `@Override` 애너테이션을 추가하면, 컴파일러는 해당 메서드가 실제로 상위 클래스의 메서드를 재정의하는지 확인한다.

```java
사@Override
public boolean equals(Bigram b) {
    return b.first == first && b.second == second;
}
```

위 코드로 컴파일하면 다음과 같은 오류가 발생

```sql
Bigram.java:10: error: method does not override or implement a method from a supertype
@Override
```

컴파일러는 우리가 `equals` 메서드를 재정의하려 했지만, 실제로는 잘못된 시그니처로 인해 **새로운 메서드를 정의했다**는 것을 알려준다. 이를 올바르게 수정하면 다음과 같다.

```java
@Override
public boolean equals(Object o) {
    // 객체가 Bigram의 인스턴스인지 확인
    if (!(o instanceof Bigram))
        return false;
    // 안전하게 캐스팅
    Bigram b = (Bigram) o;
    // 필드 값 비교
    return b.first == first && b.second == second;
}
```

이렇게 수정하면 `equals` 메서드는 상위 클래스인 `Object`의 `equals` 메서드를 정확히 재정의하게 되고, `Set`의 동작도 예상대로 이루어진다.

equals에 문제가 있음에도 컴파일에는 성공하기 때문에, `@Override` 를 달지 않게 되면 위의 잘못한 점을 컴파일러가 알려주지 못한다.

**따라서, 상위 클래스의 메서드를 재정의하려는 모든 메서드에는 @Override 애너테이션을 달자.**

## 2. 예외 사항

**구체 클래스에서 상위 클래스의 메서드를 재정의**할 때는, 굳이 `@Override`를 달지 않아도 된다. 구체 클래스인데 구현하지 않은 추상 메서드가 남아 있다면 컴파일러가 자동으로 사실을 알려주기 때문이다.

## 3. IDE의 도움

현대의 IDE는 `@Override` 애너테이션의 사용을 돕는다.

* **자동 완성**: 메서드를 재정의할 때 IDE는 `@Override`를 자동으로 추가해준다.
* **경고 제공**: `@Override`가 없는 재정의 메서드에 대해 경고를 표시하여 실수를 방지한다.
* **리팩토링 지원**: 메서드 시그니처 변경 시 관련된 부분을 자동으로 업데이트한다.

## 4. 인터페이스와 @Override

자바 6부터는 **인터페이스의 메서드를 구현할 때도 `@Override`를 사용할 수 있다**. 디폴트 메서드가 도입되면서, 인터페이스의 메서드를 재정의할 때 `@Override`를 사용하는 것은 더욱 중요해졌다. 이를 통해 시그니처가 정확한지 확인하고, 실수를 방지할 수 있다.

## 5. 추가적인 설명과 고민

### **1) 오버로딩과 오버라이딩의 차이**

* **오버로딩(Overloading)**: 동일한 이름의 메서드를 여러 개 정의하되, **매개변수의 타입이나 개수를 다르게** 하는 것.
* **오버라이딩(Overriding)**: 상위 클래스의 메서드를 **동일한 시그니처**로 재정의하여 새로운 동작을 구현하는 것.

위 예제에서 `equals(Bigram b)`는 **오버로딩된 메서드**로, `Object`의 `equals` 메서드를 재정의한 것이 아니다. 이로 인해 **객체의 동등성 비교가 의도대로 이루어지지 않았다**.

### **2) 왜 매개변수 타입이 Object여야 하는가**

`equals` 메서드는 `Object` 클래스에서 정의된 메서드로, **모든 객체의 최상위 타입인 `Object`를 매개변수로 받는다**. <mark style="color:red;">이를 재정의하려면 시그니처를 동일하게 유지해야 한다.</mark> 만약 구체적인 타입으로 변경하면, 이는 **새로운 메서드를 정의하는 것**이 된다.

### **3) `hashCode` 메서드의 중요성**

`equals`를 재정의할 때는 반드시 `hashCode`도 함께 재정의해야 한다. **해시 기반 컬렉션(`HashSet`, `HashMap` 등)은 객체의 해시코드를 사용하여 저장 위치를 결정**하므로, `equals`와 `hashCode`의 규약을 지키지 않으면 의도한 대로 동작하지 않는다.

```java
@Override
public int hashCode() {
    return 31 * first + second;
}
```

### **4) `instanceof`와 타입 비교**

`equals` 메서드에서 매개변수의 타입을 확인할 때 `instanceof`를 사용한다. 이는 **전달된 객체가 해당 클래스의 인스턴스인지 확인하는 안전한 방법**이다. 타입이 다르면 `false`를 반환하여 동등성 비교를 진행하지 않는다.

### **5) 안전한 캐스팅**

타입이 확인된 후에는 **안전하게 캐스팅**을 할 수 있다.

```java
Bigram b = (Bigram) o;
```

### **6) 대칭성, 추이성 등 `equals` 메서드의 규약**

`equals` 메서드를 올바르게 구현하려면 **대칭성, 추이성, 일관성 등의 규약**을 지켜야 한다. 이를 통해 객체의 동등성 비교가 정확하게 이루어진다.

## **핵심 정리**

{% hint style="info" %}
재정의한 모든 메서드에 **@Override 애너테이션을 의식적으로 달면, 실수했을때 컴파일러가 바로 알려줄 것이다.**&#x20;
{% endhint %}

* 예외는 한 가지뿐이다. 구체 클래스에서 상위 클래스의 추상 메서드를 재정의한 경우에는 이 애너테이션을 달지 않아도 된다.
* 개인적으로 코딩을 할 때 `@Override` 애너테이션을 항상 사용하는 습관을 들이는게   좋다.
* 이는 내가 의도한 대로 메서드가 재정의되었는지 확인할 수 있는 가장 간단하고 효과적인 방법이다.
*   또한, 팀 프로젝트에서 코드의 일관성을 유지하고, 다른 개발자들과의 협업 시에 의도를 명확히 전달할 수 있다.

    프로그래밍은 작은 실수가 큰 버그로 이어질 수 있기 때문에, 이러한 작은 습관들이 모여 안정적이고 유지보수하기 쉬운 코드를 만들 수 있다.

> 참고
>
> * [https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-40-Override-%EC%95%A0%EB%84%88%ED%85%8C%EC%9D%B4%EC%85%98%EC%9D%84-%EC%9D%BC%EA%B4%80%EB%90%98%EA%B2%8C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC](https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-40-Override-%EC%95%A0%EB%84%88%ED%85%8C%EC%9D%B4%EC%85%98%EC%9D%84-%EC%9D%BC%EA%B4%80%EB%90%98%EA%B2%8C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)
