# 스터디에서 알아가는 것

## 1. **false인 이유**

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



## 2. 컬렉션 인터페이스를 equals를 언제쓰냐

**컬렉션(Collection) 인터페이스에서 `equals`를 사용하는 경우**는 두 컬렉션이 같은지(동일한지)를 비교할 때입니다. Java 컬렉션 프레임워크의 다양한 구현체들은 `equals` 메서드를 오버라이드하여 **두 컬렉션의 요소들이 동일한지**를 확인하는 로직을 제공합니다.

#### 1. `equals` 사용 시점

**a) 두 컬렉션이 동일한지 비교할 때**

`equals` 메서드는 두 컬렉션의 **크기, 요소의 순서**, 그리고 각 요소가 동일한지 여부를 기준으로 두 컬렉션을 비교합니다. 특히 `List`, `Set`, `Map`과 같은 컬렉션 타입에서 각각의 `equals` 메서드는 해당 타입의 특성을 고려하여 구현되어 있습니다.

예를 들어:

```java
List<String> list1 = new ArrayList<>(Arrays.asList("a", "b", "c"));
List<String> list2 = new ArrayList<>(Arrays.asList("a", "b", "c"));

System.out.println(list1.equals(list2)); // true
```

* \*\*`List`\*\*에서는 요소의 **순서**가 중요합니다. `list1`과 `list2`의 요소들이 순서대로 모두 동일하기 때문에 `equals` 결과는 `true`입니다.

```java
Set<String> set1 = new HashSet<>(Arrays.asList("a", "b", "c"));
Set<String> set2 = new HashSet<>(Arrays.asList("c", "b", "a"));

System.out.println(set1.equals(set2)); // true
```

* \*\*`Set`\*\*은 순서를 고려하지 않고, **요소의 동일성**만을 비교합니다. `set1`과 `set2`는 순서는 다르지만 동일한 요소들을 가지고 있으므로 `equals` 결과는 `true`입니다.

#### 2. 컬렉션 구현체에서 `equals` 동작 방식

**a) `List`:**

* `List` 인터페이스를 구현하는 클래스 (`ArrayList`, `LinkedList` 등)에서 `equals`는 **요소의 순서**와 **내용**을 기준으로 두 리스트가 동일한지 비교합니다.
* 요소의 순서가 다르면 `equals`는 `false`를 반환합니다.

```java
List<String> list1 = Arrays.asList("a", "b", "c");
List<String> list2 = Arrays.asList("a", "c", "b");

System.out.println(list1.equals(list2)); // false (순서가 다름)
```

**b) `Set`:**

* `Set` 인터페이스를 구현하는 클래스 (`HashSet`, `TreeSet` 등)에서 `equals`는 **요소의 순서는 무시**하고 **내용**만을 기준으로 비교합니다.
* 같은 요소들이 있으면 `equals`는 `true`를 반환합니다.

```java
Set<String> set1 = new HashSet<>(Arrays.asList("a", "b", "c"));
Set<String> set2 = new HashSet<>(Arrays.asList("c", "b", "a"));

System.out.println(set1.equals(set2)); // true (순서가 무시됨)
```

**c) `Map`:**

* `Map` 인터페이스를 구현하는 클래스 (`HashMap`, `TreeMap` 등)에서 `equals`는 키(key)와 값(value)이 모두 동일할 때 두 맵이 같다고 판단합니다.
* 키와 값이 모두 같아야 `equals`는 `true`를 반환합니다.

```java
Map<String, String> map1 = new HashMap<>();
map1.put("key1", "value1");
map1.put("key2", "value2");

Map<String, String> map2 = new HashMap<>();
map2.put("key1", "value1");
map2.put("key2", "value2");

System.out.println(map1.equals(map2)); // true
```

#### 3. 언제 `equals`를 사용하는가?

1. **컬렉션의 내용 비교**: 두 개의 리스트, 세트, 맵이 같은지 비교할 때 사용합니다. 이때 단순히 객체의 참조값(주소)을 비교하는 것이 아니라 **컬렉션 내부의 요소**가 동일한지 확인합니다.
2. **테스트 및 검증**: 단위 테스트에서 두 컬렉션이 동일한지(동일한 요소를 가지고 있는지)를 검증할 때 자주 사용됩니다.

```java
List<String> expected = Arrays.asList("a", "b", "c");
List<String> actual = Arrays.asList("a", "b", "c");
assertTrue(expected.equals(actual)); // 같은지 테스트
```

3. **데이터의 무결성 확인**: 데이터 처리 중 두 컬렉션이 동일한지 확인하는 로직을 통해 데이터가 올바르게 처리되었는지 검증하는 데 사용됩니다.

#### 4. 결론

`Collection` 인터페이스에서 `equals`는 컬렉션의 **내용**을 비교하기 위해 사용됩니다. 각 컬렉션 타입(`List`, `Set`, `Map`)은 자신만의 방식으로 `equals`를 구현하여 그 특성에 맞게 비교를 수행합니다. **`List`는 순서와 내용을** 비교하고, **`Set`은 순서를 무시하고 내용만을** 비교하며, **`Map`은 키와 값 모두를** 비교합니다.



## 3. 룸북에서도 equals를 재정의해서 사용하는 경우도 있어? 막.. final로 막아보자는게 뭘까?

네, **Lombok**을 사용하는 경우에도 `equals` 메서드를 재정의해서 사용할 수 있습니다. Lombok은 `@EqualsAndHashCode` 어노테이션을 통해 자동으로 `equals`와 `hashCode` 메서드를 생성해줍니다. 하지만, 때로는 기본으로 제공되는 `equals` 구현을 변경하거나 사용자 정의를 위해 직접 재정의할 수 있습니다.

#### 1. Lombok에서 `equals` 메서드 생성

Lombok의 `@EqualsAndHashCode` 어노테이션을 클래스에 추가하면, 해당 클래스의 모든 필드를 기반으로 `equals`와 `hashCode` 메서드를 자동으로 생성합니다.

```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode
public class MyClass {
    private String name;
    private int age;
}
```

위 코드에서 Lombok은 자동으로 `equals` 메서드를 생성하여 `name`과 `age` 필드를 기반으로 두 객체의 동일성을 비교하게 됩니다.

#### 2. Lombok에서 `equals`를 재정의해야 하는 경우

때때로 Lombok의 기본 동작을 수정하거나, 특정 필드만을 대상으로 `equals` 메서드를 정의하고 싶은 경우가 있습니다. Lombok의 `@EqualsAndHashCode` 어노테이션은 필드 선택 옵션을 제공하며, 이를 통해 특정 필드만 포함하거나 제외할 수 있습니다.

```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode(onlyExplicitlyIncluded = true)
public class MyClass {
    @EqualsAndHashCode.Include
    private String name;
    
    @EqualsAndHashCode.Exclude
    private int age;
}
```

위 코드에서는 `name` 필드만 `equals`와 `hashCode`에 포함되며, `age`는 제외됩니다.

#### 3. `final`로 막는다는 의미

`final`로 막는다는 것은 주로 클래스나 메서드가 **상속되지 않도록 방지**하거나, **재정의할 수 없도록** 제한하는 것을 의미합니다.

*   **메서드에서 `final`**: 메서드를 `final`로 선언하면 해당 메서드는 하위 클래스에서 재정의(overriding)할 수 없습니다. 즉, `equals` 메서드를 `final`로 선언하면, 해당 클래스를 상속한 다른 클래스가 이를 재정의할 수 없게 됩니다.

    ```java
    @EqualsAndHashCode
    public class MyClass {
        private String name;
        
        @Override
        public final boolean equals(Object obj) {
            // 커스텀 equals 구현
        }
    }
    ```
*   **클래스에서 `final`**: 클래스를 `final`로 선언하면, 그 클래스를 상속하는 다른 클래스를 만들 수 없습니다. 즉, 상속을 통한 `equals` 재정의를 막을 수 있습니다.

    ```java
    public final class MyClass {
        private String name;
        
        // Lombok의 @EqualsAndHashCode를 이용해 생성된 equals 메서드
    }
    ```

#### 4. `final`로 막아보자는 의미

주로 `equals`나 `hashCode`와 같은 메서드의 재정의를 막는 것은 **객체의 동일성 비교 로직이 변하지 않도록 강제**하려는 목적입니다. 만약 `equals` 메서드를 하위 클래스에서 재정의한다면, 동일 객체를 비교할 때 의도하지 않은 동작이 발생할 수 있습니다. 이를 방지하기 위해 `final` 키워드를 사용하여 메서드를 재정의하지 못하도록 막을 수 있습니다.

예를 들어, **데이터 무결성**이나 **비즈니스 로직**에서 객체의 동일성 비교가 중요한 경우, `equals` 메서드를 `final`로 지정함으로써 이러한 재정의를 제한할 수 있습니다.

#### 결론

* Lombok을 사용하면 `@EqualsAndHashCode`로 자동으로 `equals` 메서드를 생성할 수 있지만, 특정 요구 사항이 있을 경우 이를 재정의할 수 있습니다.
* `final` 키워드를 사용해 `equals`나 `hashCode`를 재정의할 수 없게 막음으로써 객체의 동일성 비교 로직이 일관되게 유지되도록 할 수 있습니다. 이 방법은 객체의 일관성과 무결성을 보장하려는 경우에 유용하게 사용됩니다.



## 4. @AutoValue는 lomback이 나온이후로 못한다..?

`@AutoValue`와 Lombok은 서로 다른 라이브러리이며, 둘 다 **Java에서 보일러플레이트 코드**(getter, setter, equals, hashCode 등)를 줄여주는 역할을 합니다. 하지만 Lombok이 등장한 이후에 `@AutoValue`를 사용할 수 없거나 못하는 것은 아닙니다. 두 라이브러리 모두 각각의 장단점이 있으며, **어느 것을 사용할지는 프로젝트 요구 사항과 개발자의 선호에 따라 달라집니다**.

#### 1. `@AutoValue`와 Lombok 비교

**`@AutoValue`**

* **구글**에서 제공하는 라이브러리로, 주로 **불변 객체(Immutable Object)** 생성을 지원합니다.
* 주로 데이터 클래스(값 객체)에서 사용되며, 컴파일 타임에 필요한 코드를 자동으로 생성합니다.
*   **불변성**을 보장하고, `equals`, `hashCode`, `toString` 등의 메서드를 자동으로 생성합니다.

    예:

    ```java
    @AutoValue
    public abstract class Person {
        public abstract String name();
        public abstract int age();

        public static Person create(String name, int age) {
            return new AutoValue_Person(name, age);
        }
    }
    ```

    * `@AutoValue`는 생성자가 자동으로 만들어지며, 클래스의 불변성을 유지하는 데 유리합니다.
    * 불변성(Immutable)을 보장하는 데이터 객체에 주로 사용됩니다.

**Lombok**

* **Lombok**은 더 많은 기능을 제공하며, `@Getter`, `@Setter`, `@EqualsAndHashCode`, `@ToString` 등 다양한 어노테이션을 통해 다양한 종류의 메서드들을 자동 생성할 수 있습니다.
*   Lombok은 불변 객체를 만들기 위한 `@Value` 어노테이션도 제공하며, `@Builder`, `@AllArgsConstructor`, `@NoArgsConstructor` 등의 어노테이션으로 더 많은 유연성을 제공합니다.

    예:

    ```java
    @Data
    public class Person {
        private final String name;
        private final int age;
    }
    ```

    * `@Data`는 `@Getter`, `@Setter`, `@EqualsAndHashCode`, `@ToString` 등을 한 번에 적용합니다.
    * Lombok은 매우 **유연**하며, 불변성과 가변성 둘 다 처리할 수 있습니다.

#### 2. 왜 `@AutoValue`가 Lombok보다 덜 사용되는가?

Lombok이 더 많이 사용되는 이유는 그 유연성과 광범위한 기능 덕분입니다. `@AutoValue`는 주로 **불변 객체** 생성에 특화되어 있고, Lombok은 다양한 목적에 더 적합하며 더 많은 기능을 제공합니다. 그럼에도 불구하고 `@AutoValue`는 여전히 불변 객체를 만들 때 강력한 도구입니다.

**주요 차이점:**

1. **Lombok의 유연성**: Lombok은 불변 객체뿐만 아니라 가변 객체, 빌더 패턴, 리플렉션 기반 기능까지 지원하여 더 다양한 상황에 적용할 수 있습니다.
2. **프로젝트 요구 사항**: 불변성을 엄격하게 요구하는 경우 `@AutoValue`가 더 적합할 수 있으며, 가변 객체가 필요하거나 더 많은 유연성이 필요하다면 Lombok이 적합합니다.

#### 3. "Lombok 이후에 AutoValue를 못한다"는 의미?

이 말은 **Lombok이 더 유연하고 다양한 기능을 제공하기 때문에** 많은 프로젝트에서 Lombok이 더 선호된다는 의미일 수 있습니다. Lombok을 도입한 프로젝트에서는 `@AutoValue`를 사용할 필요가 없을 수 있으며, 팀 내에서 하나의 라이브러리로 통일성을 유지하는 것이 일반적입니다. 그러나 이것이 `@AutoValue`를 사용할 수 없다는 의미는 아닙니다. **Lombok이 나온 이후에도 `@AutoValue`는 여전히 사용 가능**하며, 특정한 요구사항이나 상황에서는 적합할 수 있습니다.

#### 결론

* `@AutoValue`는 여전히 사용할 수 있으며, 특히 **불변 객체를 생성**하는 데 강력한 도구입니다.
* Lombok은 **더 많은 기능**과 **유연성**을 제공하여 더 다양한 상황에서 사용할 수 있으며, 그래서 많은 프로젝트에서 Lombok이 선호됩니다.
* 어느 것을 사용할지 선택하는 것은 **프로젝트의 요구 사항**과 **개발자의 선호**에 따라 달라집니다.



## 5. equals는  jvm내에 있는 객체만 가지고 판단해야 하는데 네트워크를 타게 되면.. 매번 달라지게 된다? ip에 따라 달라지게 된다고 한다..

{% embed url="https://stackoverflow.com/questions/3771081/proper-way-to-check-for-url-equality" %}

## 5. equals 구현 시 주의사항
