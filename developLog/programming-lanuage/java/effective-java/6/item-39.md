# item 39 : 명명 패턴보다 애너테이션을 사용하라

## 1. 명명 패턴의 문제점

과거에는 테스트 프레임워크가 메서드의 이름을 특정 패턴으로 명명하여 특정 작업을 수행하는 메서드임을 구분하곤 했다. 예를 들어 **JUnit 3**는 테스트 메서드 이름이 `test`로 시작해야만 테스트 프레임워크에서 해당 메서드를 테스트 메서드로 인식했다.

> 그러나 이 방식에는 몇 가지 문제가 있다

1. **오타 문제**: `tsetSafetyOverride`처럼 오타가 있으면 JUnit은 테스트 메서드로 인식하지 않아 무시하며, 테스트가 실패하지 않았으므로 개발자는 이 테스트가 통과했다고 오해할 수 있다.
2. **올바른 요소에 대한 보증 없음**: 명명 패턴으로는 잘못된 위치(예: 클래스 이름)에 설정된 패턴을 검사할 수 없다. 예를 들어 메서드가 아닌 클래스 이름을 TestSafety로 지어 JUnit에게 줘도,  JUnit은 이 클래스 이름에 관심이 없으며,  테스트는 수행되지 않으며 Junit은 경고 메시지조차 출력하지 않는다.
3. **매개변수 전달의 어려움**: 특정 예외가 발생해야 성공하는 테스트를 구현하고자 할 때 예외 <mark style="color:red;">타입을 명시적으로 지정하는 방법이 없기 때문에,</mark> 테스트 이름에 **예외 이름을 포함시키는 방식**으로 우회하는 경우가 있다. 하지만 이러한 방식은 가독성이 떨어지고 깨지기 쉬우며, 컴파일러가 메서드 이름에 포함된 문자열이 예외와 관련이 있는지 확인할 방법이 없다.

**예시: JUnit 3의 테스트 메서드 명명 패턴**

```java
public class MyTests {

    // 테스트 메서드는 이름이 "test"로 시작해야 함
    public void testAddition() {
        assert (1 + 1 == 2);
    }

    public void testSubtraction() {
        assert (2 - 1 == 1);
    }

    public void utilityMethod() {
        // 일반적인 유틸리티 메서드로, 테스트가 아님
    }
}

```



애너테이션은, 이러한 명명 패턴의 문제들을 해결해주는 멋진 대안이다.

## 2. 명명패턴의  대안 애너테이션의 장점

```java
import org.junit.Test;

public class MyTests {

    @Test
    public void addition() {
        assert (1 + 1 == 2);
    }

    @Test
    public void subtraction() {
        assert (2 - 1 == 1);
    }

    public void utilityMethod() {
        // 일반 메서드
    }
}

```

애너테이션은 다음과 같은 장점을 제공한다.

* **명확한 역할 구분**: 애너테이션은 메서드 이름에 의존하지 않고도 명확하게 특정 기능을 수행하는 메서드를 구분할 수 있게 해준다.  <mark style="color:red;">메서드 이름이 아닌 애너테이션을 통해 역할을 표현할 수 있다.</mark>
* **유연성과 확장성**: 추가적인 정보를 애너테이션으로 저장할 수 있어 <mark style="color:red;">더 많은 기능을 쉽게 확장</mark>할 수 있다.
* **컴파일러와 도구의 지원**: 애너테이션을 통해 컴파일러가 추가 검증을 수행할 수 있으며, 리플렉션을 통해 동적으로 메서드를 제어할 수 있다.

예를 들어, `@Test`라는 애너테이션을 만들고 이를 특정 메서드에 붙이면 그 메서드가 테스트 메서드임을 명확하게 알 수 있다. 이를 통해 테스트 코드를 더욱 읽기 쉽게 만들고 유지 보수를 용이하게 할 수 있다.

## 3. 애너테이션(Annotation)

> > * [애너테이션의 동작 원리](https://velog.io/@anak\_2/Java-annotations-%EC%9D%B4%EB%9E%80-%EC%84%A4%EB%AA%85-%ED%99%9C%EC%9A%A9)
>
> Test라는 자동으로 수행되는 간단한 테스트용 애너테이션으로, 예외가 발생하면 테스트를 실패로 처리한다.

### 1) 마커 애너테이션 타입 선언

`@Test` 애너테이션은 <mark style="color:red;">메서드가 테스트 메서드임을 표시하기 위한 마커 애너테이션</mark>이다. 이 애너테이션은 매개변수를 가지지 않으며, <mark style="color:blue;">메타 애너테이션을 통해 특정 조건에서만 사용할 수 있도록 제한</mark>한다.

```java
import java.lang.annotation.*;

/**
 * 테스트 메서드임을 선언하는 애너테이션
 * 매개변수 없는 정적 메서드 전용
 */
@Retention(RetentionPolicy.RUNTIME)  // 런타임까지 유지
@Target(ElementType.METHOD)          // 메서드에만 적용 가능
public @interface Test {
}
```

{% hint style="info" %}
`@Test` 에너테이션 타입 선언 자체에 두 가지의 다른 애너테이션이 달려 있는데, 이를 **메타 애너테이션(meta-annotation)**이라 한다.
{% endhint %}

* **`@Retention(RetentionPolicy.RUNTIME)`**: <mark style="color:red;">애너테이션을 런타임에도 유지하도록 설정하여 리플렉션을 통해 접근할 수 있게 한다.</mark> 메타 애너테이션은 `@Test`가 런타임에도 유지되어야 한다는 의미이며, 만약 이를 생략하면 테스트 도구는 `@Test`를 인식할 수 없게 된다.
* **`@Target(ElementType.METHOD)`**: 애너테이션을 메서드에만 사용할 수 있도록 제한하여 잘못된 위치에서 사용하지 못하도록 한다.( _클래스 선언, 필드 선언 등 다른 프로그램 요소에는 달 수 없음_ )

이와 같이 **매개변수가 없는 애너테이션**을  `@Test`와 같은 애너테이션을, <mark style="color:red;">"아무 매개변수 없이 단순한 대상에 마킹한다"</mark>라는 뜻에서마커 애너테이션이라 부르며, 메서드에만 붙일 수 있게 설정하여 사용자가 클래스나 필드에 잘못 적용했을 때 컴파일러가 오류를 반환하도록 할 수 있다.

> 즉, 프로그래머가 Test이름에 오타를 내거나 메서드 선언 외의 프로그램 요소에 달면 컴파일 오류를 내준다.

### 2) Sample 클래스에 @Test 애너테이션 사용 :마커 애너테이션을 사용한 프로그램 예시

`Sample` 클래스에서 `@Test` 애너테이션을 사용하여 메서드를 테스트 메서드로 지정할 수 있다.

```java
public class Sample {
    @Test
    public static void m1() { }         // 성공해야 하는 테스트 메서드
    public static void m2() { }         // 테스트 메서드가 아님
    @Test
    public static void m3() {           // 실패해야 하는 테스트 메서드
        throw new RuntimeException("실패");
    }
    public static void m4() { }         // 테스트 메서드가 아님
    @Test
    public void m5() { }                // 잘못된 사용 예: 정적 메서드가 아님
}
```

* `m1`, `m3`, `m5`는 `@Test` 애너테이션이 지정된 테스트 메서드
* `m5`는 정적 메서드가 아니기 때문에 `@Test`의 요구사항에 맞지 않는다.

`@Test` 애너테이션은 Sample <mark style="color:red;">클래스의 의미에 직접적인 영향을 주지 않고, 단지 관심 있는 프로그램에게 추가 정보를 제공</mark>한다. 즉 **대상 코드의 의미는 그대로 둔 채 그 애너테이션에 관심 있는 도구에서 특별한 처리를 할 기회를 주는 것**이다.&#x20;

### 3) 애너테이션을 활용한 테스트 도구 구현 : 마커 애너테이션을 처리하는 프로그램

**`@Test` 애너테이션이 붙은 메서드만을 실행하는 간단한 테스트 도구**

```java
import java.lang.reflect.*;

public class RunTests {
    public static void main(String[] args) throws Exception {
        int tests = 0;
        int passed = 0;
        Class<?> testClass = Class.forName(args[0]);
        
        for (Method m : testClass.getDeclaredMethods()) {
            if (m.isAnnotationPresent(Test.class)) {
                tests++;
                try {
                    m.invoke(null);
                    passed++;
                } catch (InvocationTargetException wrappedExc) {
                    Throwable exc = wrappedExc.getCause();
                    System.out.println(m + " 실패: " + exc);
                } catch (Exception exc) {
                    System.out.println("잘못 사용한 @Test: " + m);
                }
            }
        }
        System.out.printf("성공: %d, 실패: %d%n", passed, tests - passed);
    }
}
```

* **`m.isAnnotationPresent(Test.class)`**: `@Test` 애너테이션이 붙은 메서드를 찾아 실행한다.
* **예외 처리**: `InvocationTargetException`으로 감싸진 예외는 `getCause()`를 통해 원본 예외를 출력한다.
* **결과 출력**: 총 테스트 개수와 성공/실패 개수를 출력하여 테스트의 결과를 요약한다.

이 테스트 러너는 명령줄로부터 완전 정규화된 클래스 이름을 받아, 클래스에서 `@Test` 애너테이션이 달린 메서드를 찾아 차례로 호출한다. 그리고 애너테이션을 잘못 사용해 예외가 발생한다면 오류 메세지를 출력한다.

### 4) 특정 예외를 기대하는 애너테이션 : 매개변수를 받는 애너테이션 타입

> <mark style="color:red;">특정 예외를 발생해야만 성공하는 테스트가 필요한 경우</mark>, `@ExceptionTest` 애너테이션을 사용해 예외를 명시할 수 있다.

#### 매개변수를 하나를 받는 애너테이션 타입, `@ExceptionTest` 애너테이션 타입 정의

```java
import java.lang.annotation.*;

/**
 * 지정한 예외가 발생해야 성공하는 테스트 메서드를 위한 애너테이션.
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTest {
    Class<? extends Throwable> value();  // 매개변수로 예외 타입을 받음
}
```

`@ExceptionTest`는 특정 예외 타입을 매개변수로 받아, 해당 예외가 발생해야만 테스트가 성공하는지 검사한다.

예제: `@ExceptionTest` 애너테이션 사용

```java
public class Sample2 {
    @ExceptionTest(ArithmeticException.class)
    public static void m1() {  // 성공해야 하는 테스트
        int i = 0;
        i = i / i;
    }

    @ExceptionTest(ArithmeticException.class)
    public static void m2() {  // 실패: 다른 예외 발생
        int[] a = new int[0];
        int i = a[1];
    }

    @ExceptionTest(ArithmeticException.class)
    public static void m3() {  // 실패: 예외가 발생하지 않음
    }
}
```

### 5) 테스트 도구 수정

이제 `@ExceptionTest`를 활용해 **테스트 메서드가 올바른 예외를 던지는지** 확인하는 로직을 추가한다. 앞의 코드와 한 가지 차이라면, 이 코드는 애너테이션 매개변수의 값을 추출하여 테스트 메서드가 올바른 예외를 던지는지 확인하는데 사용한다.

**예제:** 마커 애너테이션과 매개변수 하나짜리 애너태이션을 처리하는 프로그램

```java
if (m.isAnnotationPresent(ExceptionTest.class)) {
    tests++;
    try {
        m.invoke(null);  // 메서드를 실행
        System.out.printf("테스트 %s 실패: 예외를 던지지 않음%n", m);
    } catch (InvocationTargetException wrappedEx) {
        Throwable exc = wrappedEx.getCause();
        Class<? extends Throwable> excType = m.getAnnotation(ExceptionTest.class).value();
        if (excType.isInstance(exc)) {  // 예외 타입 일치 여부 확인
            passed++;
        } else {
            System.out.printf("테스트 %s 실패: 기대한 예외 %s, 발생한 예외 %s%n", m, excType.getName(), exc);
        }
    } catch (Exception exc) {
        System.out.println("잘못 사용한 @ExceptionTest: " + m);
    }
}
```

* **`m.getAnnotation(ExceptionTest.class).value()`**: `@ExceptionTest` 애너테이션에서 지정한 예외 타입을 가져온다.
* **`excType.isInstance(exc)`**: 발생한 예외가 기대한 예외 타입과 일치하는지 확인한다.

### 6) 다수의 예외를 명시하는 애너테이션 (배열 매개변수)

`@ExceptionTest`에 배열 매개변수를 사용하여, **다수의 예외 중 하나만 발생해도 테스트가 성공**하도록 설정할 수 있다.&#x20;

> @ExceptionTest 애너테이션의 **매개변수 타입을 Class 객체의 배열로 수정**하면 된다.

**예제: 배열 매개변수를 받는 애너테이션 타입**

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTest {
    Class<? extends Throwable>[] value();  // 여러 예외 타입을 배열로 받음
}
```

**예제: 배열 매개변수를 사용하는 `@ExceptionTest` 애너테이션 사용 예**

```java
@ExceptionTest({ IndexOutOfBoundsException.class, NullPointerException.class })
public static void doublyBad() {
    List<String> list = new ArrayList<>();
    // 자바 API 명세에 따르면 다음 메서드는 IndexOutOfBoundsException이나
     // NullPointerException을 던질 수 있다.
    list.addAll(5, null);  
}
```

**예제: 배열 매개변수를 처리하는 테스트 도구 코드**

```java
jif (m.isAnnotationPresent(ExceptionTest.class)) {
    tests++;
    try {
        m.invoke(null);
        System.out.printf("테스트 %s 실패: 예외를 던지지 않음%n", m);
    } catch (Throwable wrappedExc) {
        Throwable exc = wrappedExc.getCause();
        int oldPassed = passed;
        Class<? extends Throwable>[] excTypes = m.getAnnotation(ExceptionTest.class).value();
        for (Class<? extends Throwable> excType : excTypes) {
            if (excType.isInstance(exc)) {  // 예외 타입 중 일치하는 것이 있으면 성공
                passed++;
                break;
            }
        }
        if (passed == oldPassed)
            System.out.printf("테스트 %s 실패: %s %n", m, exc);
    }
}
```

### 7) 다수의 예외를 명시하는 애너테이션 (<mark style="color:red;">@Repeatable 사용</mark>)

> 하지만 위의 코드를 더 간단하게 개선하고 싶다면, 자바 8에서는 여러개의 값을 받는 애너테이션을, 배열 매개변수를 사용하는 대신 `@Repeatable` **메타 애너테이션을 다는 방식을 선택하여 코드 가독성을 높일** 수 있다.

`@Repeatable`를 단 애너테이션은 하나의 프로그램 요소에 여러 번 달 수 있다.

**주의 사항**

1. `@Repeatable`을 사용하려면 **컨테이너 애너테이션**을 별도로 정의해야 한다.
2. `@Repeatable`에 컨테이너 애너테이션 클래스를 전달해야 한다.
3. 컨테이너 애너테이션은 **애너테이션 배열을 반환하는 `value` 메서드**를 포함해야 한다.
4. 컨테이너 애너테이션 타입에는 적절한 보존 정책(@Retention)과 적용 대상(@Target)을 명시해야 한다. 그렇지 않으면 컴파일이 되지 않는다.

**예제: 반복 가능한 애너테이션 타입과 컨테이너 애너테이션 정의**

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Repeatable(ExceptionTestContainer.class)  // 컨테이너 애너테이션 클래스
public @interface ExceptionTest {
    Class<? extends Throwable> value();
}

// 컨테이너 애너테이션 정의
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTestContainer {
    ExceptionTest[] value();
}
```

반복 가능 애터네이션은, **처리할 때도 주의 사항이 존재**한다.\


먼저, 애너테이션을 여러개 달면 하나만 달았을 때와 구분하기 위해 해당 '컨테이너' 애너테이션 타입이 적용되기 때문에 `m.isAnnotationPresent()` 에서 둘을 명확히 구분하고 있는 것을 볼 수 있다.

하지만, 해당 메서드로 반복 가능 애너테이션이 달렸는지 검사한다면 검사에 실패할 것이고(애너테이션을 여러 번 단 메서드들을 무시) 컨테이너 애너테이션이 달렸는지만 검사하여도 <mark style="color:red;">반복 가능 애너테이션을 한번만 단 메서드를 무시하고 지나치기 때문에 둘을 따로따로 확인해야 한다.</mark>



**예제: 반복 가능한 애너테이션 사용 예**

```java
@ExceptionTest(IndexOutOfBoundsException.class)
@ExceptionTest(NullPointerException.class)
public static void doublyBad() {
    List<String> list = new ArrayList<>();
    list.addAll(5, null);  // 예외 발생 가능
}
```

**예제: 반복 가능한 애너테이션을 처리하는 테스트 도구 코드**

```java
if (m.isAnnotationPresent(ExceptionTest.class) || m.isAnnotationPresent(ExceptionTestContainer.class)) {
    tests++;
    try {
        m.invoke(null);
        System.out.printf("테스트 %s 실패: 예외를 던지지 않음%n", m);
    } catch (Throwable wrappedExc) {
        Throwable exc = wrappedExc.getCause();
        int oldPassed = passed;
        ExceptionTest[] excTests = m.getAnnotationsByType(ExceptionTest.class);
        for (ExceptionTest excTest : excTests) {
            if (excTest.value().isInstance(exc)) {
                passed++;
                break;
            }
        }
        if (passed == oldPassed)
            System.out.printf("테스트 %s 실패: %s %n", m, exc);
    }
}
```

`@Repeatable`을 사용한 반복 가능한 애너테이션을 처리할 때는, `getAnnotationsByType` 메서드를 통해 **개별 애너테이션을 배열 형태로 가져올 수 있다.**

## 📚 **핵심 정리**

애너테이션으로 할 수 있는 일을 명명 패턴으로 처리할 이유는 없으며, 자바 프로그래머라면 예외 없이 자바가 제공하는 애너테이션 타입들은 사용해야 한다.(_아이템 40, 27_)

애너테이션을 통해 코드를 더욱 읽기 쉽고 유지 보수하기 용이하게 만들 수 있습니다.



> 참고
>
> * [https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-39-%EB%AA%85%EB%AA%85-%ED%8C%A8%ED%84%B4%EB%B3%B4%EB%8B%A4-%EC%95%A0%EB%84%88%ED%85%8C%EC%9D%B4%EC%85%98%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC](https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-39-%EB%AA%85%EB%AA%85-%ED%8C%A8%ED%84%B4%EB%B3%B4%EB%8B%A4-%EC%95%A0%EB%84%88%ED%85%8C%EC%9D%B4%EC%85%98%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)
