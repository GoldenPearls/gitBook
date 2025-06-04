# item10 : equals는 일반 규약을 지켜 재정의하라

> equals 메서드는 재정의하기 쉬워 보이지만 곳곳에 함정이 도사리고 있다. **가장 쉬운 길은 아예 재정의하지 않는 것이다.**

## 1. equals를 <mark style="color:red;">재정의하지 않아도</mark> 되는 경우

> **값이 같은 인스턴스가 둘 이상 만들어지지 않는 인스턴스 통제 클래스**라면 `equals`를 재정의하지 않아도 됩니다. 이런 경우에는 객체의 식별성(Identity)과 논리적 동치성(Equality)이 동일하게 취급되기 때문입니다.

### 1) 각각의 객체(인스턴스)가 본질적으로 고유할 때

값을 표현하는 게 아니라 동작하는 개체를 표현하는 클래스가 여기 해당한다. 대표적인 예로 **Thread, Controller, Service** 등이 이 조건에 부합한다.

```java
public class MyService {
    // 서비스 로직 구현
    public void execute() {
        // 실행 코드
    }
}

// 사용 예시
MyService service1 = new MyService();
MyService service2 = new MyService();

// 서로 다른 서비스 인스턴스는 본질적으로 고유합니다.
System.out.println(service1.equals(service2)); // false
```

### 2) 인스턴스의 [논리적 동치성(logical equality)](#user-content-fn-1)[^1]을 검사할 일이 없을 때

Pattern의 인스턴스가 같은 정규 표현식을 나타내는지 검사하거나 Random 클래스의 equals 메서드가 큰 의미를 가지지 못하는 것처럼 클라이언트가 이 방식이 필요 없다고 판단되면 equals를 재정의 하지 않아도 된다.

```java
Pattern pattern1 = Pattern.compile("[a-z]*");
Pattern pattern2 = Pattern.compile("[a-z]*");

// Pattern 클래스는 equals를 재정의하지 않았으므로 참조 비교를 합니다.
System.out.println(pattern1.equals(pattern2)); // false

// 그러나 동일한 정규 표현식을 나타냅니다.
String testStr = "abc";
System.out.println(pattern1.matcher(testStr).matches()); // true
System.out.println(pattern2.matcher(testStr).matches()); // true
```

### 3) 상위 클래스에서 재정의한 equals가 하위 클래스에도 들어맞을 때

<mark style="color:red;">하위 클래스에서도 사용하기에 적합한 equlas라면</mark> 재정의할 필요 없이 상속받아 사용하면 된다

* Set 구현체는 Abstractset이 구현한 equals를 상속받아 쓰고, List 구현체들은 AbstractList로부터, Map 구현체들은 AbstractMap으로부터 상속받아 그대로 쓴다.

### 4) 클래스가 private 또는 package-private(default)이고 equals 메서드를 호출할 일이 없을 때

equals가 호출될 일이 없어 실수로라도 호출되는 걸 막고 싶다면 다음과 같이 구현하면 된다.

```java
class PackagePrivateClass {
    // 클래스 내용

    @Override
    public boolean equals(Object o) {
        throw new AssertionError("equals 메서드는 호출될 수 없습니다.");
    }
}

// 사용 예시
PackagePrivateClass obj1 = new PackagePrivateClass();
PackagePrivateClass obj2 = new PackagePrivateClass();

// equals 메서드를 호출하면 AssertionError가 발생합니다.
boolean isEqual = obj1.equals(obj2); // AssertionError 발생

```

* **AssertionError 사용 이유**: `equals` 메서드가 호출되지 않도록 보장하며, 만약 호출되면 프로그래머에게 즉각적인 피드백을 제공한다.
* **주의사항**: `AssertionError`는 일반적으로 예상치 못한 상황에서 사용되며, 런타임 환경에서 오류를 나타낸다. 따라서 이 방식을 사용할 때는 해당 클래스의 사용 범위와 목적을 명확히 해야 한한다.

## 2. equals를 <mark style="color:red;">재정의해야 하는</mark> 경우

> **객체의 식별성이 아닌 논리적 동치성을 확인**해야 하는데, 상위 클래스의 equals`가 논리적 동치성`을 비교하도록 재정의되지 않았을 때 해야한다.&#x20;

주로 **String이나 Integer와 같은 값 클래스**가 이에 해당한다.

값 클래스의 equals를 재정의 할 때 논리적 동치성을 확인하도록 재정의해두면, 값을 비교하는 것 뿐만아니라 Map의 키나 Set의 원소로 사용할 수 있게 된다.

```java
// 값을 비교
@Override
public boolean equals(Object obj) {
    if (obj instanceof Integer) {
        return value == ((Integer)obj).intValue();
    }
    return false;
}
```

### 값 클래스여도 equals 재정의가 필요 없는 경우

* <mark style="color:red;">값이 같은 인스턴스가 둘 이상 만들어지지 않는 인스턴스 통제 클래스</mark>라면 equals를 재정의하지 않아도 된다.
* 대표적인 예로 **`Enum` 타입**이 있으며, 이러한 클래스에서는 `equals` 메서드를 재정의하지 않아도 된다.
* 논리적으로 같은 인스턴스가 2개 이상 만들어지지 않으니 논리적 동치성과 객체 식별성이 똑같은 의미라고 볼 수 있다.

#### 부가 설명 Enum

#### **대표적인 예: `Enum`**

* **`Enum` 타입**은 자바에서 [인스턴스 통제 클래스](#user-content-fn-2)[^2]의 대표적인 예
* 각 열거 상수는 **유일한 인스턴스**이며, 동일한 열거 상수에 대해 여러 인스턴스가 생성되지 않는다.
* 따라서 `Enum` 타입에서는 **`equals` 메서드를 재정의하지 않아도** 된다.
* **객체의 식별성**과 **논리적 동치성**이 동일하므로, **`==` 연산자**로 비교해도 충분다.

**예시 코드:**

```java
public enum Color {
    RED, GREEN, BLUE;
}

// 사용 예시
Color color1 = Color.RED;
Color color2 = Color.RED;

// 동일한 인스턴스이므로 참조 비교가 가능
System.out.println(color1 == color2);     // true
System.out.println(color1.equals(color2)); // true

// 다른 인스턴스와 비교
Color color3 = Color.GREEN;
System.out.println(color1 == color3);     // false
System.out.println(color1.equals(color3)); // false
```

* `Color.RED`는 유일한 인스턴스이므로 `color1`과 `color2`는 동일한 객체를 참조한다.
* 따라서 `==` 연산자와 `equals` 메서드 모두 `true`를 반환한한다.
* `equals` 메서드를 재정의하지 않았지만, `Object` 클래스의 기본 구현으로도 논리적 동치성을 올바르게 판단할 수 있다.

## 3. equals 메서드 재정의 시 지켜야  할  일반 규&#xC57D;**.**

* **반사성(reflexive)**: `x.equals(x)`는 항상 `true`여야 한다.
* **대칭성(symmetric)**: `x.equals(y)`가 `true`이면 `y.equals(x)`도 `true`여야 한다.
* **추이성(transitive)**: `x.equals(y)`이고 `y.equals(z)`이면 `x.equals(z)`도 `true`여야 한다.
* **일관성(consistent)**: 객체의 상태가 변경되지 않았다면 여러 번 호출해도 항상 같은 결과를 반환해야 한다.
* **`null`-아님(non-nullity)**: `x.equals(null)`은 항상 `false`여야 다.



## 4. 양질의 equals 메서드 구현 방법

1. \==연산자를 사용해 입력이 [자기 자신의 참조](#user-content-fn-3)[^3]인지 확인한다.

```java
@Override
public boolean equals(Object obj) {
    if (this == obj) {
        return true; // 같은 객체이므로 true 반환(불필요한 계산 피할 수 있음)
    }
    // 나머지 비교 로직...
}
```

2. `instanceof 연산자`로 입력이 올바른 타입인지 확인한다.

* 입력 객체(`obj`)가 **올바른 타입인지** 확인.
* 객체의 클래스 타입이 다르다면, 두 객체는 논리적으로 동등할 수 없으므로 `false`를 반환
* 이때 비교하는 타입은 일반적으로 해당 클래스 또는 특정 **인터페이스이다.**

```java
if (!(obj instanceof MyClass)) {
    return false; // 타입이 다르므로 false 반환
}
```

```java
if (!(obj instanceof MyInterface)) {
    return false; // 인터페이스를 구현하지 않으면 false 반환
}
```

3. 입력을 올바른 타입으로 형변환한다.

* 2단계에서 타입 검사를 통과했다면, 이제 입력 객체를 해당 타입으로 `형변환(casting)`할 수 있다.
* 형변환을 통해 입력 객체의 필드나 메서드에 접근할 수 있다.

**코드 예시:**

*   **클래스로 형변환하는 경우:**

    ```java
    MyClass other = (MyClass) obj;
    ```
*   **인터페이스로 형변환하는 경우:**

    ```java
    MyInterface other = (MyInterface) obj;
    ```

4. **핵심 필드들을 하나씩 비교하여 논리적 동등성을 판단**
   * 필드의 타입에 따라 적절한 비교 방법을 사용
5. 모든 필드가 일치하면 `true`를 반환하고, 하나라도 다르면 `false`를 반환합니다.

equals는  jvm내에 있는 객체만 가지고 판단해야 하는데 네트워크를 타게 되면.. 매번 달라지게 된다? ip에 따라 달라지게 된다고 한다..

{% embed url="https://stackoverflow.com/questions/3771081/proper-way-to-check-for-url-equality" %}

## 5. equals 구현 시 주의사항

### 1) 기본타입 별 비교 방법 <a href="#h-tag-22" id="h-tag-22"></a>

* float, doble을 <mark style="color:red;">제외한</mark> 기본 타입 : == 연산자로 비교하기
* **`float`와 `double` 타입**: `Float.compare(float, float)`와 `Double.compare(double, double)` 정적 메서드를 사용한다.
  * 이유: `NaN`, `-0.0f` 등 특수한 부동소수점 값을 정확하게 처리하기 위해서
  * **주의**: `Float.equals(Object)`와 `Double.equals(Object)` 메서드는 오토박싱을 유발하여 성능에 영향을 줄 수 있으므로 사용을 지양

### 2) 참조 타입 필드 비교

* **참조 타입 필드**: 해당 객체의 `equals` 메서드를 사용하여 비교
* **`null` 허용 필드**: 비교 시 `null` 체크를 수행하여 `NullPointerException`을 방지

### 3) 배열 필드 비교

* **배열 필드**: 배열의 각 요소를 앞서 언급한 방식대로 비교한다.
* 모든 요소가 핵심 필드라면 `Arrays.equals` 메서드를 활용할 수 있다.

### 4) null 정상 값으로 취급하는 참조 타입 필드일 경우 <a href="#h-tag-23" id="h-tag-23"></a>

Object.equals(Object, Object)로 비교해 NPE 발생을 방지한다.&#x20;

### 5) 필드의 표준형을 저장 <a href="#h-tag-24" id="h-tag-24"></a>

비교하기 복잡한 필드는 필드의 표준형을 저장한 후 비교한다. 불변 클래스에 제격이다.

### 6) 비용이 싼 필드를 먼저 비교하라(필드  비교 순서)

* **비교 가능성이 높은 필드** 또는 **비교 비용이 적은 필드**부터 비교하여 성능을 최적화한다.
* 객체의 논리적 상태와 관련 없는 필드(예: 동기화용 락 필드)는 비교하지 않는다.
* 핵심 필드로부터 계산 가능한 **파생 필드**는 일반적으로 비교할 필요가 없지만, 경우에 따라 비교가 효율적일 수 있다.

### 7) equals를 재정의할 때는 hashCode도 반드시 재정의하자

```java
public final class PhoneNumber {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(int areaCode, int prefix, int lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "지역 코드");
        this.prefix = rangeCheck(prefix, 999, "프리픽스");
        this.lineNum = rangeCheck(lineNum, 9999, "가입자 번호");
    }

    private static short rangeCheck(int val, int max, String argName) {
        if (val < 0 || val > max)
            throw new IllegalArgumentException(argName + ": " + val);
        return (short) val;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) // 참조 비교
            return true;
        if (!(o instanceof PhoneNumber)) // 타입 체크
            return false;
        PhoneNumber pn = (PhoneNumber) o;
        // 핵심 필드 비교
        return pn.lineNum == lineNum && pn.prefix == prefix && pn.areaCode == areaCode;
    }

    @Override
    public int hashCode() {
        return Objects.hash(areaCode, prefix, lineNum);
    }

    // 기타 메서드 생략
}

```

## 스터디에서 알아가는 것것

**false인 이유**

```java
public class Test {
    private String name;

    public Test(String name) {
        this.name = name;
    }

    @Override
        public boolean equals(Object obj) {
            if (obj instanceof Test) {
                return this.name.equals(((Test) obj).name);
            }
            return false;
        }

    public static void main(String[] args) throws Exception {
        Set<Test> set = new HashSet<>();
        Test test = new Test("java");
        set.add(test);
        Test test2 = new Test("java");
        System.out.println(set.contains(test2)); //false
    }
}

```

위 코드에서 `Set<Test>`에 동일한 `name` 값을 가진 객체(`test`와 `test2`)를 추가하고, 그 후 `set.contains(test2)`가 `false`로 출력되는 이유는 `HashSet`이 내부적으로 **`equals`와 `hashCode`** 메서드를 모두 사용해 객체의 동일성을 비교하기 때문이다.

#### 이유

`HashSet`은 내부적으로 해시 기반 자료구조이다. **`HashSet`이 객체를 저장하거나 조회할 때는 `equals`뿐만 아니라 `hashCode`** 메서드도 사용한다.

* **`equals`**: 두 객체가 동일한지 비교하는 메서이다. 현재 `equals` 메서드를 오버라이드하여 <mark style="color:red;">`name`</mark> <mark style="color:red;"></mark><mark style="color:red;">값만을 기준으로 비교하도록 구현되어 있다.</mark> 따라서 `test`와 `test2`는 `equals` 메서드 상으로는 동일한 객체로 판단
* **`hashCode`**: 객체를 해시 테이블에서 빠르게 찾기 위해 사용되는 정수 값이다. `HashSet`이나 `HashMap`과 같은 해시 기반 자료구조는 객체를 비교할 때 먼저 `hashCode`를 비교하고, `hashCode`가 동일한 경우에만 `equals` 메서드를 호출하여 최종적으로 동일성을 판단한다.

문제는 현재 `Test` 클래스에서 **`hashCode` 메서드가 오버라이드되어 있지 않기 때문이**다. 기본적으로 `Object` 클래스의 `hashCode`는 객체의 메모리 주소를 기반으로 해시 값을 생성하므로, `test`와 `test2`는 서로 다른 해시 코드를 가진다.

따라서, `HashSet`은 `test`와 `test2`를 서로 다른 객체로 인식하게 되며, `set.contains(test2)`는 `false`를 반환하게 된다.

#### 해결 방법

**`hashCode` 메서드를 `equals`와 일관되게 오버라이드**해야 한다. `name` 필드를 기준으로 `hashCode`를 생성하면 `test`와 `test2`가 같은 해시 코드를 가지게 되어 `HashSet`이 동일 객체로 인식하게 된다.

수정된 코드는 다음과 같다:

```java
public class Test {
    private String name;

    public Test(String name) {
        this.name = name;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj instanceof Test) {
            return this.name.equals(((Test) obj).name);
        }
        return false;
    }

    @Override
    public int hashCode() {
        return name.hashCode(); // name 필드를 기준으로 hashCode 생성
    }

    public static void main(String[] args) throws Exception {
        Set<Test> set = new HashSet<>();
        Test test = new Test("java");
        set.add(test);
        Test test2 = new Test("java");
        System.out.println(set.contains(test2)); // true
    }
}
```

#### 설명:

* **`hashCode` 오버라이드**: `hashCode` 메서드를 `name` 필드를 기준으로 오버라이드하여, `name`이 동일한 객체는 같은 해시 코드를 가지도록 했습니다.
* 이제 `test`와 `test2`는 같은 해시 코드를 가지며, `HashSet`이 이 두 객체를 동일한 객체로 인식하게 됩니다.

이제 `set.contains(test2)`는 `true`를 반환하게 됩니다.

> **✨  정리**&#x20;
>
> **꼭 필요한 경우가 아니면 equals를 재정의하지 말자.** 많은 경우에 Object의 equals가 원하는 비교를 정확히 수행해준다. 재정의해야 할 때는 그 클래스의 핵심 필드 모두를 빠짐없이, 다섯 가지 규약을 확실히 지켜가며 비교해야 한다.

[^1]: 논리적 동치성은 두 객체의 실제 메모리 주소(참조)가 동일한지 여부가 아닌, **객체가 가진 속성이나 상태가 동일한지**를 비교하는 것을 의미합니다. 즉, 서로 다른 인스턴스이지만 내부 데이터나 상태가 동일하다면 논리적으로 동등하다고 판단합니다.

    자바에서 **`equals` 메서드**를 오버라이드하여 객체의 논리적 동치성을 정의할 수 있습니다. 기본적으로 `Object` 클래스의 `equals` 메서드는 참조 비교(물리적 동치성)를 수행하여 두 객체가 동일한 메모리 주소를 가지는지 확인합니다. 그러나 이를 오버라이드하여 객체의 속성 값을 기반으로 비교하도록 구현하면 논리적 동치성을 판단할 수 있습니다.

[^2]: **인스턴스 통제 클래스란?**

    * 인스턴스 통제 클래스(Instance-Controlled Class)는 동일한 값을 갖는 객체가 단 하나만 존재하도록 설계된 클래스입니다.
    * 이러한 클래스는 **생성자를 `private`으로 제한**하고, 객체 생성을 통제하여 동일한 값에 대해 하나의 인스턴스만을 제공합니다.
    * 이로 인해 **객체의 식별성**(`==` 연산자 사용)과 **논리적 동치성**(`equals` 메서드 사용)이 동일해집니다.



    #### **인스턴스 통제 클래스의 구현 방법**

    * **생성자를 `private`으로 선언**하여 외부에서 객체 생성을 막습니다.
    * **정적 팩토리 메서드**를 제공하여 객체 생성을 통제합니다.
    * 동일한 값을 가지는 요청에 대해 **캐싱된 인스턴스를 반환**합니다.

    **예시 코드:**

    ```java
    java코드 복사public class Currency {
        private static final Map<String, Currency> instances = new HashMap<>();

        private final String code;

        // 생성자를 private으로 선언
        private Currency(String code) {
            this.code = code;
        }

        // 정적 팩토리 메서드로 객체 생성 통제
        public static Currency getInstance(String code) {
            synchronized (instances) {
                // 이미 존재하면 캐싱된 인스턴스 반환
                if (instances.containsKey(code)) {
                    return instances.get(code);
                }
                // 존재하지 않으면 새 인스턴스 생성 후 캐싱
                Currency currency = new Currency(code);
                instances.put(code, currency);
                return currency;
            }
        }

        public String getCode() {
            return code;
        }

        // equals를 재정의하지 않음
    }

    // 사용 예시
    Currency usd1 = Currency.getInstance("USD");
    Currency usd2 = Currency.getInstance("USD");

    System.out.println(usd1 == usd2);     // true
    System.out.println(usd1.equals(usd2)); // true
    ```

    * `Currency` 클래스는 특정 통화 코드를 가진 인스턴스가 하나만 생성되도록 합니다.
    * `usd1`과 `usd2`는 모두 `"USD"` 코드를 가지며, 동일한 인스턴스를 참조합니다.
    * 따라서 `==` 연산자와 `equals` 메서드 모두 `true`를 반환합니다.
    * `equals` 메서드를 재정의하지 않아도 객체의 동등성을 올바르게 판단할 수 있습니다.

    #### **객체 식별성과 논리적 동치성의 일치**

    * **객체 식별성(Identity)**: 객체의 메모리 주소를 비교하여 동일한 객체인지 판단합니다. `==` 연산자를 사용합니다.
    * **논리적 동치성(Equality)**: 객체의 속성이나 상태를 비교하여 논리적으로 동등한지를 판단합니다. `equals` 메서드를 사용합니다.
    * **인스턴스 통제 클래스**에서는 동일한 값을 가지는 객체가 하나만 존재하므로, **식별성과 동치성이 일치**합니다.



[^3]: 참조 비교(Reference Comparison)를 통해 두 객체가 **동일한 인스턴스**인지 확인합니다.
