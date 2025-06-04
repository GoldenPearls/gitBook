# item 41 : 정의하려는 것이 타입이라면 마커 인터페이스를 사용하라

## 1. **마커 인터페이스와 마커 애너테이션의 비교 및 활용**

### **1) 마커 인터페이스란?**

자바 프로그래밍에서 `마커 인터페이스(marker interface)`란 메서드를 하나도 담고 있지 않으면서, <mark style="color:red;">자신을 구현하는 클래스가 특정 속성을 가짐을 표시해주는 인터페이스를 말한다.</mark> 대표적인 예로 `Serializable` 인터페이스를 들 수 있다. 이 인터페이스를 구현한 클래스의 인스턴스는 `ObjectOutputStream`을 통해 직렬화할 수 있음을 나타낸다.

### 2) 마커 애너테이션

반면에 `마커 애너테이션(marker annotation)`은 자바 5부터 도입된 애너테이션 기능을 활용하여, <mark style="color:red;">클래스나 메서드 등에 특정 속성을 부여하는 데 사용</mark>된다. 예를 들어 `@Deprecated` 애너테이션은 해당 요소가 더 이상 사용되지 않음을 표시한다.

많은 개발자들이 마커 애너테이션이 등장하면서 마커 인터페이스는 구식이 되었다고 생각할 수 있지만, 실제로는 마커 인터페이스가 여전히 유용한 경우가 있다.&#x20;

## **2. 마커 인터페이스의 장점**

> 마커 어노테이션이 나왔는데 사용해야할 이유가 있을까?

마커 인터페이스는, 두 가지 면에서 애너테이션보다 장점을 가진다.

### 1) 마커 인터페이스는 **타입**이기 때문에 애너테이션을 사용했다면 런타임에나 발견될 오류를 **컴파일 타임에 잡을 수 있다.**&#x20;

> **즉, 타입으로 사용할 수 있다**

마커 인터페이스는 이를 구현한 클래스의 인스턴스들을 구분하는 **타입**으로 사용할 수 있다. 이는 마커 애너테이션으로는 불가능한 부분입니다. 타입으로 활용할 수 있다는 것은 컴파일 타임에 타입 체크를 통해 오류를 사전에 방지할 수 있다는 뜻이다.

예를 들어, 다음과 같이 `Serializable`을 구현한 클래스의 인스턴스를 받아들이는 메서드를 작성할 수 있다.

이렇게 하면 `Serializable`을 구현하지 않은 객체를 인수로 전달하려고 할 때 컴파일 오류가 발생한다. 즉, **런타임이 아닌 컴파일 타임에 오류를 발견할 수 있어 안정성을 높일 수 있다.**

```java
public void saveObject(Serializable obj) {
    // 객체를 저장하는 로직
}
```

{% hint style="info" %}
**마커 인터페이스**는 어엿한 타입이기 때문에, 마커 애너테 이션을 사용했다면 <mark style="color:red;">런타임에야 발견될 오류를 컴파일타임 에 잡을 수 있다</mark>
{% endhint %}

하지만 자바의 직렬화는 컴파일 타임 오류 검출의 이점을 살리지 못했다.

![](https://velog.velcdn.com/images/semi-cloud/post/dc4c393f-681a-4dd1-a0a6-dc78da03d3ce/image.png)

`Serializable` 이 아닌 상위 타입인 `Object` 를 객체로 받도록 설계되어 런타임에야 문제가 발생했다는 것을 알 수 있기 때문이다.

### **2) 적용 대상을 더 정밀하게 지정할 수 있다**

> 마커 인터페이스는 특정 클래스 계층에만 적용되도록 제한할 수 있다.&#x20;

마크 애너테이션은 적용 대상( `@Target` )을 `Element.Type` 으로 선언한 애너테이션은 모든 타입(클래스, 인터페이스, 열거 타입, 애너테이션)에 달 수 있지만, 부착할 수 있는 타입을 더 세밀하게 제한할 수는 없다.

반면, **마크 인터페이스**는 그냥 <mark style="color:red;">마킹하고 싶은 클래스에서만 그 인터페이스를 구현하면 마킹된 타입은 자동으로 그 인터페이스의 하위 타입임이 보장</mark>된다.

```java
public interface SpecialFeature extends BaseFeature {
    // 마커 인터페이스이므로 메서드 없음
}
```

이렇게 하면 `BaseFeature`를 구현한 클래스들 중에서 `SpecialFeature`를 구현한 클래스만을 대상으로 특정 로직을 적용할 수 있다.

**Set 인터페이스에 대한 생각**

> `Set 인터페이스`도 일종의 제약이 있는 마커 인터페이스로 볼 수 있다.&#x20;

Set은 **Collection의 하위 타입에만 적용할 수 있으며**, Collection이 정의한 메서드 외에는 새로 추가한 것이 없다. <mark style="color:blue;">보통은 Set 을 마커 인터페이스로 생각하지 않는데, add, equals, hashCode 등 Collection 의 메서드 몇 개의 규약을 살짝 수정했기 때문</mark>이다. `add`, `equals`, `hashCode` 등의 메서드에 대한 동작 규약을 변경하여 **집합(set)의 특성**을 구현했다. 하지만 다시 말하지만 추가적인 메서드는 정의하지 않았다.

하지만 특정 인터페이스 의 하위 타입에만 적용할 수 있으며, 아무 규약에도 손대지 않은 마커 인터페이스는 충분히 있음직하다. 이런 마커 인터페이스는 객체의 특정 부분을 불 변식(invariant)으로 규정하거나, 그 타입의 인스턴스는 다른 클래스의 특정 메서드가 처리할 수 있다는 사실을 명시하는 용도로 사용할 수 있을 것이다

## **3. 마커 애너테이션의 장점**

### **1) 애너테이션 프로세싱의 지원**

마커 애너테이션은 자바의 강력한 애너테이션 프로세싱 기능을 활용할 수 있다. 이는 리플렉션(reflection)을 통해 런타임에 애너테이션이 달린 요소를 쉽게 찾고 처리할 수 있게 해준다.

또한, 애너테이션은 **클래스뿐만 아니라 메서드, 필드, 모듈, 패키지 등 다양한 프로그램 요소에 적용**할 수 있다. 따라서 클래스와 인터페이스 외의 요소에 마킹해야 할 때는 마커 애너테이션을 사용할 수밖에 없다.

## **4. 마커 인터페이스 vs 마커 애너테이션: 언제 사용해야 할까?**

* **타입으로 활용해야 하는 경우**: <mark style="color:red;">마커 인터페이스</mark>를 사용한다. 이를 통해 컴파일 타임에 타입 체크를 할 수 있고, 해당 마커 인터페이스를 매개변수나 반환 타입으로 사용하여 코드의 안정성을 높일 수 있다.
* **클래스와 인터페이스 외의 요소에 적용해야 하는 경우**: <mark style="color:red;">마커 애너테이션</mark>을 사용한다. 메서드나 필드, 모듈 등에 마킹해야 할 때는 애너테이션이 유일한 선택지이다.
* **애너테이션을 적극 활용하는 프레임워크와 통합해야 하는 경우**: <mark style="color:red;">마커 애너테이션</mark>을 사용하는 것이 일관성을 유지하는 데 도움이 된다. 예를 들어, 스프링 프레임워크에서는 다양한 애너테이션을 통해 설정과 의존성 주입을 관리하므로, 마커 애너테이션을 사용하는 것이 적절하다.

## **5. 사용 예제**

### **1) 마커 인터페이스 사용 예제**

```java
// 마커 인터페이스 정의
public interface Archivable {
    // 메서드 없음
}

// Archivable을 구현한 클래스
public class Document implements Archivable {
    private String content;
    // 기타 멤버
}

// Archivable을 구현하지 않은 클래스
public class Image {
    private byte[] data;
    // 기타 멤버
}

// Archivable 타입의 객체만을 처리하는 메서드
public void archive(Archivable item) {
    // 아카이브 로직
}
```

위 코드에서 `archive` 메서드는 `Archivable` 인터페이스를 구현한 객체만을 인수로 받는다. 따라서 `Document` 객체는 전달할 수 있지만, `Image` 객체는 컴파일 타임에 오류가 발생한다.

### **2) 마커 애너테이션 사용 예제**

```java
// 마커 애너테이션 정의
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Test {
}

// 애너테이션을 적용한 메서드
public class Calculator {
    @Test
    public void testAddition() {
        // 테스트 코드
    }
}

// 애너테이션 프로세서를 통해 @Test 애너테이션이 달린 메서드를 실행
public class TestRunner {
    public static void main(String[] args) {
        for (Method m : Calculator.class.getDeclaredMethods()) {
            if (m.isAnnotationPresent(Test.class)) {
                // 테스트 메서드 실행 로직
            }
        }
    }
}
```

위 예제에서 `@Test` 마커 애너테이션은 메서드에 적용되며, 리플렉션을 통해 해당 애너테이션이 달린 메서드를 찾아 실행할 수 있다.

> 마커 인터페이스와 마커 애너테이션은 각각의 장단점이 있으므로 상황에 맞게 선택하는 것이 중요

1. **컴파일 타임 안전성**: <mark style="color:blue;">마커 인터페이스</mark>는 타입 시스템을 활용하므로, 컴파일 타임에 오류를 발견할 수 있다. 이는 대규모 프로젝트에서 매우 중요하며, 버그를 사전에 방지할 수 있다.
2. **리플렉션 및 메타데이터 활용**: <mark style="color:blue;">마커 애너테이션</mark>은 런타임에 메타데이터로 활용할 수 있어, 동적인 기능 구현에 유리하다. 특히 테스트 프레임워크나 DI(Dependency Injection) 프레임워크에서 유용하다.
3. **적용 범위**: <mark style="color:blue;">마커 인터페이스</mark>는 클래스와 인터페이스에만 적용할 수 있지만, <mark style="color:blue;">마커 애너테이션</mark>은 더 다양한 프로그램 요소에 적용할 수 있다.
4. **의도와 명확성**: <mark style="color:blue;">마커 인터페이스</mark>를 구현한다는 것은 해당 클래스가 **특정 타입의 일부임**을 명시적으로 나타낸다. 반면에 <mark style="color:blue;">마커 애너테이션</mark>은 해당 요소가 **특정 속성을 가짐**을 나타내지만, 타입으로서의 의미는 없다..

> **애너테이션 기반의 프레임워크를 많이 사용하는 현대의 개발 환경에서는 마커 애너테이션의 활용도가 높아지고 있다**. 하지만 타입 안정성과 명확한 의도를 전달하기 위해서는 마커 인터페이스를 사용하는 것이 좋을 때도 있다.

**언어의 특징과 개발 도구의 발전에 따라 코딩 스타일과 패턴이 변화하지만, 기본 원칙은 변하지 않는다는 것이다**. 마커 인터페이스와 마커 애너테이션은 각각의 목적과 장점이 있으므로, **프로젝트의 요구 사항과 팀의 코딩 컨벤션에 맞게 적절히 활용하는 것이 중요**하다.

또한, **타입 시스템을 적극적으로 활용하여 안정적인 코드를 작성하는 것의 중요성하다**. 특히 대규모 시스템에서는 작은 오류가 큰 문제로 이어질 수 있으므로, 컴파일 타임에 잡을 수 있는 오류는 최대한 그 단계에서 해결하는 것이 좋다.

**추가적인 고민**

* **자바 8 이후의 기능과의 조합**: 람다 표현식이나 스트림 API와 마커 인터페이스 또는 마커 애너테이션을 어떻게 조합하여 활용할 수 있을지 고민해볼 수 있다.
* **다른 언어에서의 적용 사례**: 코틀린이나 스칼라 같은 JVM 언어에서 마커 인터페이스와 마커 애너테이션은 어떻게 활용되는지 비교해보는 것도 흥미로울 것 같다.
* **미래의 방향성**: 애너테이션 프로세싱과 리플렉션의 성능 이슈, 그리고 모듈 시스템의 발전에 따라 마커 인터페이스와 마커 애너테이션의 사용 패턴이 어떻게 변화할지 예측해보는 것도 의미 있을 것이다.

## 6. 스프링에서의 사용

### **1) 마커 애너테이션과 마커 인터페이스의 차이점**

* **마커 애너테이션**: 클래스, 메서드, 필드 등에 애너테이션을 부여하여 특정 속성을 나타낸다. 스프링에서는 빈 스캐닝이나 AOP 등을 활용할 때 주로 사용한다.
* **마커 인터페이스**: 메서드 없이 인터페이스를 정의하고, 이를 구현함으로써 클래스에 특정 타입을 부여한다.

### **2) 스프링에서 마커 인터페이스의 사용**

스프링에서는 마커 인터페이스를 사용하는 경우가 상대적으로 적지만, 필요에 따라 활용할 수 있다. 예를 들어, 특정 타입의 빈만을 처리하고자 할 때 마커 인터페이스를 사용할 수 있다.

**예시: 마커 인터페이스 사용**

```java
// 마커 인터페이스 정의
public interface SpecialService {
    // 메서드 없음
}

// 마커 인터페이스를 구현한 클래스
@Service
public class MySpecialService implements SpecialService {
    // 구현 내용
}

// 마커 인터페이스를 구현하지 않은 클래스
@Service
public class RegularService {
    // 구현 내용
}
```

**특정 타입의 빈만 가져오기**

```java
@Autowired
private ApplicationContext applicationContext;

public void processSpecialServices() {
    // SpecialService 타입의 빈만 가져옴
    Map<String, SpecialService> beans = applicationContext.getBeansOfType(SpecialService.class);
    for (SpecialService service : beans.values()) {
        // SpecialService를 구현한 빈에 대한 처리 로직
    }
}
```

이렇게 하면 `SpecialService` 인터페이스를 구현한 빈들만을 대상으로 로직을 수행할 수 있다.

#### **스프링에서 마커 애너테이션의 사용**

하지만 스프링에서는 마커 인터페이스보다 **마커 애너테이션을 더 많이 사용**한다. 이는 애너테이션이 더 유연하고, 다양한 프로그램 요소에 적용할 수 있기 때문이다.

**예시: 마커 애너테이션 사용**

```java
// 마커 애너테이션 정의
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface SpecialComponent {
}

// 마커 애너테이션을 적용한 클래스
@SpecialComponent
@Service
public class MySpecialService {
    // 구현 내용
}

// 마커 애너테이션을 적용하지 않은 클래스
@Service
public class RegularService {
    // 구현 내용
}
```

**특정 애너테이션이 붙은 빈만 가져오기**

```java
@Autowired
private ApplicationContext applicationContext;

public void processSpecialComponents() {
    // 모든 빈 이름 가져오기
    String[] beanNames = applicationContext.getBeanDefinitionNames();
    for (String beanName : beanNames) {
        Object bean = applicationContext.getBean(beanName);
        // 클래스에 @SpecialComponent 애너테이션이 붙어 있는지 확인
        if (bean.getClass().isAnnotationPresent(SpecialComponent.class)) {
            // @SpecialComponent가 붙은 빈에 대한 처리 로직
        }
    }
}
```

또는 스프링 설정에서 **빈 스캐닝 시 필터링**을 통해 특정 애너테이션이 붙은 클래스만 빈으로 등록할 수 있다.

```java
@Configuration
@ComponentScan(
    basePackages = "com.example",
    includeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = SpecialComponent.class)
)
public class AppConfig {
}
```

### **3) 마커 인터페이스 vs 마커 애너테이션: 스프링에서의 활용**

* **마커 인터페이스**
  * 컴파일 타임에 타입 체크 가능
  * 특정 타입의 빈만 주입받을 수 있음
  * 인터페이스를 구현해야 하므로, 클래스가 이미 다른 클래스를 상속하고 있다면 다중 상속이 불가능한 자바의 특성상 제한이 있음
* **마커 애너테이션**
  * 클래스 외에도 메서드, 필드 등에 적용 가능
  * 리플렉션을 통해 런타임에 다양한 처리가 가능
  * 스프링의 빈 스캐닝, AOP 등과의 연동이 용이

스프링에서 **특정 빈을 식별하거나 처리하기 위해서는 마커 애너테이션을 사용하는 것이 더 일반적이며 편리하다**. 마커 인터페이스는 컴파일 타임 타입 체크 등의 장점이 있지만, 스프링의 기능과 결합하여 사용하기에는 마커 애너테이션이 더 적합하다.

따라서 제가 이전에 제시한 예시는 마커 인터페이스보다는 **마커 애너테이션을 활용한 예시**이며, 스프링에서는 이러한 방식을 통해 특정 클래스를 식별하고 처리한다.

#### **추가적인 설명**

* **마커 인터페이스의 사용 제한**
  * 마커 인터페이스는 클래스에만 적용 가능하며, 인터페이스를 구현해야 하므로 다중 상속의 제약이 있다.
  * 스프링에서 빈으로 등록할 때는 클래스패스 스캐닝을 통해 애너테이션 기반으로 처리하는 경우가 많다.
* **마커 애너테이션의 장점**
  * 다양한 프로그램 요소에 적용 가능
  * AOP 등의 기능과 결합하여 부가적인 처리를 쉽게 추가할 수 있음
  * 메타데이터로 활용되어 프레임워크와의 통합에 용이

### 4) 스프링의 서비스, 서비스impl과 마커 인터페이스 차이

| 항목        | 마커 인터페이스                    | 스프링의 Service/ServiceImpl         |
| --------- | --------------------------- | -------------------------------- |
| 목적        | 클래스에 **특정 속성** 부여           | **비즈니스 로직**의 정의와 구현 분리           |
| 메서드 존재 여부 | 메서드 없음                      | 인터페이스에 메서드 시그니처 정의               |
| 사용 방식     | 인터페이스 구현으로 **타입 지정**        | 인터페이스를 구현하여 **로직 구현**            |
| 예시        | `Serializable`, `Cloneable` | `UserService`, `UserServiceImpl` |

> 출처 및 참고
>
> * [https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-41-%EC%A0%95%EC%9D%98%ED%95%98%EB%A0%A4%EB%8A%94-%EA%B2%83%EC%9D%B4-%ED%83%80%EC%9E%85%EC%9D%B4%EB%9D%BC%EB%A9%B4-%EB%A7%88%EC%BB%A4-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC](https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-41-%EC%A0%95%EC%9D%98%ED%95%98%EB%A0%A4%EB%8A%94-%EA%B2%83%EC%9D%B4-%ED%83%80%EC%9E%85%EC%9D%B4%EB%9D%BC%EB%A9%B4-%EB%A7%88%EC%BB%A4-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)
