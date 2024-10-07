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

## 6. 로그 추적을 위한 것

* ELK

<figure><img src="../../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://spring.io/projects/spring-cloud-sleuth" %}

<figure><img src="../../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

## 7. private로 했을 때.. 클라이언트가 값을 저장하고 받는 방법

> 음 나는 궁금한게 클라이언트가 변경하지 못하게 필드나 그런거에 private를 쓰래 근데 클라이언트가 값을 입력하면 받아서 또 저장하고 보여줘야 하잖아? 근데 그러면 private를 쓰면 그게 불가하지 않아?

좋은 질문입니다! **`private` 접근 제어자**를 사용하면 **클래스 내부의 필드를 외부에서 직접 접근하지 못하도록 막을 수 있습니다**. 하지만 이것이 **클라이언트가 값을 입력하고 저장하고 보여주는 기능을 불가능하게 만드는 것은 아닙니다**.

#### 어떻게 가능한가요?

1. **생성자나 메서드를 통해 값을 입력받습니다**:
   * **생성자**: 객체가 생성될 때 필요한 값을 받아서 `private` 필드에 저장합니다.
   * **메서드**: `public` 메서드를 통해 외부에서 값을 입력받아 `private` 필드에 저장할 수 있습니다.
2. **접근자(getter) 메서드를 통해 값을 반환합니다**:
   * `public` 접근자를 가진 메서드를 제공하여 `private` 필드의 값을 외부에 노출할 수 있습니다. 이렇게 하면 필드의 값을 읽을 수 있지만, 직접 수정할 수는 없습니다.

#### 예제 코드

아래는 `User` 클래스를 예로 들어 설명하겠습니다.

```java
public final class User {

    // 필드를 private으로 선언하여 외부에서 직접 접근을 막음
    private final String name;
    private int age;

    // 생성자를 통해 초기 값 설정
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // public 메서드를 통해 값을 설정 (필요한 경우)
    public void setAge(int age) {
        if (age > 0) {
            this.age = age;
        } else {
            System.out.println("유효한 나이를 입력하세요.");
        }
    }

    // 접근자 메서드로 필드 값 반환
    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    // 추가적인 메서드 작성 가능
    public void displayUserInfo() {
        System.out.println("이름: " + name + ", 나이: " + age);
    }
}
```

#### 설명

* **`private` 필드**:
  * `name`과 `age` 필드를 `private`으로 선언하여 **외부에서 직접 접근하거나 수정할 수 없도록** 했습니다.
* **생성자를 통한 초기화**:
  * `User` 객체를 생성할 때 생성자를 통해 `name`과 `age` 값을 입력받아 필드에 저장합니다.
* **`public` 메서드를 통한 값 설정**:
  * `setAge(int age)` 메서드를 제공하여 \*\*특정 조건(예: 나이가 0보다 큰지 확인)\*\*을 만족하는 경우에만 `age` 필드의 값을 변경할 수 있도록 합니다.
* **접근자 메서드**:
  * `getName()`과 `getAge()` 메서드를 통해 `private` 필드의 값을 읽을 수 있습니다.
* **정보 표시 메서드**:
  * `displayUserInfo()` 메서드를 통해 사용자 정보를 출력할 수 있습니다.

#### 사용 예시

```java
public class Main {

    public static void main(String[] args) {
        // 객체 생성
        User user = new User("홍길동", 25);

        // 정보 출력
        user.displayUserInfo(); // 출력: 이름: 홍길동, 나이: 25

        // 나이 변경
        user.setAge(30);

        // 변경된 정보 출력
        user.displayUserInfo(); // 출력: 이름: 홍길동, 나이: 30

        // 필드에 직접 접근하려고 하면 오류 발생
        // user.age = 35; // 컴파일 오류: age has private access in User
    }
}
```

#### 핵심 포인트

* **캡슐화(Encapsulation)**:
  * `private` 필드를 사용함으로써 **데이터를 보호**하고, **클래스 내부 구현을 숨길 수 있습니다**.
  * 외부에서는 **제공된 `public` 메서드를 통해서만 필드에 접근하거나 수정**할 수 있습니다.
* **데이터 무결성 유지**:
  * `setAge()` 메서드에서 나이가 0보다 큰지 확인하는 것처럼, **필드 값의 유효성을 검증**할 수 있습니다.
  * 이를 통해 **잘못된 데이터가 필드에 저장되는 것을 방지**합니다.
* **유지보수성 향상**:
  * 내부 구현이 변경되더라도 외부에서 사용하는 인터페이스(`public` 메서드)는 그대로 유지될 수 있으므로, **코드의 변경이 외부에 미치는 영향을 최소화**합니다.

#### 결론

* **`private` 필드를 사용하면** 클라이언트가 **객체의 내부 상태를 직접 변경하지 못하게 막을 수 있지만**, **생성자나 메서드를 통해 필요한 값을 입력받고 저장할 수 있습니다**.
* **`public` 메서드를 통해 필드의 값을 제공하거나 수정할 수 있으므로**, 클라이언트는 필요한 기능을 사용할 수 있습니다.
* 이렇게 하면 **데이터의 무결성을 유지하면서도 필요한 동작을 제공**할 수 있습니다.

> 근데 setter도 안쓰는게 좋다고 했는데 user.setAge는 생성자라 괜찮은거야? 생성자는 불변으로 안 만들어도 돼?

좋은 질문입니다! \*\*불변 클래스(Immutable Class)\*\*를 만들 때 **setter 메서드**를 제공하지 않는 것이 좋다고 말씀드렸습니다. 이는 객체의 상태를 변경할 수 없도록 하기 위함입니다. 하지만 **생성자**는 객체를 생성할 때 필수적인 요소이며, 객체의 초기 상태를 설정하는 역할을 합니다.

#### Setter와 생성자의 차이점

* **Setter 메서드**:
  * 객체가 생성된 이후에 **필드의 값을 변경**할 수 있게 합니다.
  * 불변 클래스에서는 **제공하지 않는 것이 좋습니다**. 왜냐하면 객체의 상태가 변경되면 불변성이 깨지기 때문입니다.
* **생성자**:
  * 객체가 **생성될 때 한 번만 호출**되며, **필드의 초기값을 설정**합니다.
  * 불변 클래스에서도 **필수적으로 사용**됩니다. 생성자를 통해 초기 상태를 설정한 후에는 상태를 변경할 수 없도록 합니다.

#### 코드 예시로 설명

당신이 제공한 코드에서 `user.setAge(30);`은 **setter 메서드**를 호출하여 `age` 필드의 값을 변경하고 있습니다. 이는 객체의 상태를 변경하므로 **불변 클래스의 원칙에 위배됩니다**.

```java
public class User {

    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 이 메서드는 객체의 상태를 변경하므로 불변성을 깨뜨립니다.
    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void displayUserInfo() {
        System.out.println("이름: " + name + ", 나이: " + age);
    }
}
```

위 코드에서 `setAge` 메서드는 **객체의 상태를 변경**하므로 불변 클래스에서는 제거해야 합니다.

#### 불변 클래스로 수정하기

**불변 클래스**를 만들기 위해서는 다음과 같이 수정해야 합니다:

1. **필드를 `private final`로 선언**하여 객체 생성 후에는 값이 변경되지 않도록 합니다.
2. **Setter 메서드를 제거**하여 외부에서 객체의 상태를 변경할 수 없게 합니다.
3. **생성자를 통해 필드 초기화**를 진행합니다.

```java
public final class User {

    private final String name;
    private final int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // Getter 메서드만 제공
    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void displayUserInfo() {
        System.out.println("이름: " + name + ", 나이: " + age);
    }
}
```

이렇게 수정하면 `User` 클래스는 **불변 클래스**가 됩니다. 이제 객체를 생성한 후에는 상태를 변경할 수 없으며, **클라이언트가 필드에 직접 접근하거나 수정할 수 없습니다**.

#### 사용 예시

```java
public class Main {

    public static void main(String[] args) {
        User user = new User("홍길동", 25);

        user.displayUserInfo(); // 출력: 이름: 홍길동, 나이: 25

        // 아래 코드는 컴파일 오류를 발생시킴
        // user.setAge(30); // 오류: setAge(int)은 User 클래스에서 정의되지 않음

        // 새로운 객체를 생성하여 값 변경
        User updatedUser = new User("홍길동", 30);
        updatedUser.displayUserInfo(); // 출력: 이름: 홍길동, 나이: 30
    }
}
```

#### 생성자는 불변 클래스로 만들 수 있을까?

* **생성자 자체는 객체를 생성할 때 한 번만 호출**되며, 필드의 초기값을 설정하는 역할을 합니다.
* **생성자 내부에서 필드를 변경하는 것은 객체의 초기 상태를 설정하는 것이므로 불변성에 위배되지 않습니다**.
* 따라서 **생성자는 불변 클래스에서도 필수적이며, 불변 클래스를 만드는 데 문제가 되지 않습니다**.

#### 불변 클래스에서의 값 변경은 어떻게 하나요?

불변 클래스에서는 객체의 상태를 변경할 수 없기 때문에, **새로운 객체를 생성하여 변경된 값을 갖도록 합니다**.

**예시: 나이를 변경하고 싶은 경우**

```java
User user = new User("홍길동", 25);

// 나이를 30으로 변경하고 싶을 때 새로운 객체 생성
User updatedUser = new User(user.getName(), 30);

user.displayUserInfo();         // 출력: 이름: 홍길동, 나이: 25
updatedUser.displayUserInfo();  // 출력: 이름: 홍길동, 나이: 30
```

#### 불변 클래스의 장점 다시 정리

* **안전성**: 객체의 상태가 변경되지 않으므로 예기치 않은 변경으로 인한 오류를 방지할 수 있습니다.
* **스레드 안전성**: 여러 스레드에서 동시에 객체를 사용해도 상태가 변경되지 않으므로 안전합니다.
* **단순성**: 객체의 상태 변화에 따른 복잡한 로직을 관리할 필요가 없습니다.

#### 결론

* **Setter 메서드는 불변 클래스에서 제공하지 않는 것이 좋습니다**. 객체의 상태를 변경할 수 없도록 하기 위해서입니다.
* **생성자는 객체의 초기 상태를 설정하는 역할**을 하며, 불변 클래스에서도 필요합니다.
* **필드를 `private final`로 선언하고, 생성자를 통해 초기화**하며, **Setter 메서드를 제거**하면 불변 클래스를 만들 수 있습니다.
* 값의 변경이 필요할 때는 **새로운 객체를 생성**하여 사용하면 됩니다.

## 8. 그렇다면 스프링에서의 불변 클래스

#### 스프링 프레임워크에서의 불변 클래스 사용

스프링(Spring) 프레임워크는 자바 개발에서 널리 사용되는 애플리케이션 프레임워크로, 주로 \*\*의존성 주입(Dependency Injection)\*\*과 \*\*제어의 역전(Inversion of Control)\*\*을 통해 객체의 생성과 관리를 담당합니다. 스프링에서 객체의 상태 관리와 불변 클래스의 사용에 대해 궁금하신 것 같습니다. 아래에서는 스프링에서 불변 클래스를 어떻게 활용하고, 어떤 상황에서 주의해야 하는지 설명해 드리겠습니다.

***

#### 1. **스프링과 자바빈 규약**

스프링은 전통적으로 **자바빈(JavaBean) 규약**을 따르는 것을 권장합니다. 자바빈은 다음과 같은 특징을 갖습니다:

* **기본 생성자 제공**: 파라미터가 없는 public 기본 생성자.
* **프라이빗 필드와 공개된 Getter/Setter**: 필드는 `private`으로 선언하고, `public` 접근자와 변경자를 제공합니다.

이러한 규약은 **스프링이 객체를 생성하고 관리하는 데 용이**하게 만들어줍니다. 특히, 스프링의 **데이터 바인딩**이나 **프로퍼티 설정** 기능을 사용할 때 유용합니다.

***

#### 2. **불변 클래스와 스프링**

불변 클래스를 사용하면 **객체의 상태가 변경되지 않아** 여러 가지 장점을 얻을 수 있지만, 스프링에서는 다음과 같은 고려사항이 있습니다:

* **의존성 주입과 생성자 사용**: 스프링은 의존성 주입 시 \*\*생성자 주입(Constructor Injection)\*\*을 지원합니다. 이를 통해 **불변 클래스의 필드를 설정**할 수 있습니다.
* **Setter를 사용하지 않는 방식**: 생성자 주입을 사용하면 Setter 메서드가 없어도 됩니다.

**예시: 불변 클래스를 스프링 빈으로 등록하기**

```java
@Component
public class UserService {

    private final UserRepository userRepository;

    // 생성자 주입을 통해 의존성 주입
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    // 비즈니스 로직 메서드
}
```

* `UserService`는 `userRepository`를 생성자를 통해 주입받으며, 필드는 `private final`로 선언되어 있습니다.
* 스프링은 생성자를 통해 의존성을 주입할 수 있으므로 Setter가 필요 없습니다.

***

#### 3. **스프링 데이터와 엔티티 불변성**

스프링 데이터 JPA를 사용할 때는 **엔티티 클래스**를 정의해야 합니다. 여기서는 몇 가지 주의할 점이 있습니다:

* **JPA 엔티티는 기본 생성자가 필요**합니다. 이는 프록시를 생성하거나 조회 시 객체를 생성하기 위해 필요합니다.
* **필드는 프라이빗으로 선언하고 Getter/Setter를 제공**하는 것이 일반적입니다.
* **불변성을 유지하려면**:
  * 필드를 `final`로 선언하지 못하는 경우가 많습니다.
  * Setter를 제공하지 않고, 필요할 경우 **접근 수준을 `protected`로 낮추어 사용**할 수 있습니다.

**예시: 불변 엔티티 클래스**

```java
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    private String name;

    // 기본 생성자: JPA 구현체가 사용 (접근 제한자를 protected로 설정)
    protected User() {
    }

    // 생성자를 통해 필드 초기화
    public User(String name) {
        this.name = name;
    }

    // Getter만 제공
    public Long getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    // Setter를 제공하지 않음으로써 불변성 유지
}
```

* **주의점**: JPA 엔티티의 경우 프록시 생성을 위해 **기본 생성자**가 필요하며, 이는 `protected`로 선언하여 외부에서 사용되지 않도록 합니다.
* **Setter를 제공하지 않음**으로써 엔티티의 불변성을 어느 정도 유지할 수 있습니다.
* 하지만 필드를 `final`로 선언할 수는 없으므로 완전한 불변성을 달성하기는 어렵습니다.

***

#### 4. **DTO(Data Transfer Object)와 불변성**

스프링 애플리케이션에서 **DTO**는 계층 간 데이터 전달을 위해 사용됩니다. DTO를 불변 클래스로 만들면 다음과 같은 이점이 있습니다:

* **안전한 데이터 전달**: 객체의 상태가 변경되지 않아 예기치 않은 수정으로부터 보호됩니다.
* **직렬화와 역직렬화**: JSON 등으로 데이터를 주고받을 때 불변성을 유지할 수 있습니다.

**예시: 불변 DTO 클래스**

```java
public final class UserDto {

    private final String name;
    private final int age;

    @JsonCreator
    public UserDto(@JsonProperty("name") String name, @JsonProperty("age") int age) {
        this.name = name;
        this.age = age;
    }

    // Getter만 제공
    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

* **Lombok 라이브러리**를 사용하면 불변 DTO를 더 쉽게 생성할 수 있습니다.

**Lombok을 사용한 불변 DTO**

```java
import lombok.Value;

@Value
public class UserDto {
    String name;
    int age;
}
```

* `@Value` 어노테이션은 모든 필드를 `private final`로 선언하고, Getter를 생성하며, 생성자를 만들어 줍니다.

***

#### 5. **ConfigurationProperties와 불변성**

스프링에서는 애플리케이션 설정 값을 바인딩하기 위해 `@ConfigurationProperties`를 사용합니다. 이때도 불변 클래스를 사용할 수 있습니다.

**예시: 불변 설정 클래스**

```java
@Configuration
@EnableConfigurationProperties(AppProperties.class)
public class AppConfig {
}

@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private final String name;
    private final String version;

    public AppProperties(String name, String version) {
        this.name = name;
        this.version = version;
    }

    // Getter만 제공
    public String getName() {
        return name;
    }

    public String getVersion() {
        return version;
    }
}
```

* 그러나 `@ConfigurationProperties`는 기본적으로 Setter를 통해 프로퍼티를 주입하기 때문에, 불변 클래스를 사용하려면 **Constructor Binding**을 활성화해야 합니다.
* **스프링 부트 2.2 이상**에서는 `@ConstructorBinding`을 사용하여 생성자 바인딩을 할 수 있습니다.

**생성자 바인딩 예시**

```java
@ConfigurationProperties(prefix = "app")
@ConstructorBinding
public class AppProperties {

    private final String name;
    private final String version;

    public AppProperties(String name, String version) {
        this.name = name;
        this.version = version;
    }

    // Getter만 제공
}
```

* 이렇게 하면 프로퍼티 값이 생성자를 통해 주입되며, 불변성을 유지할 수 있습니다.

***

#### 6. **결론 및 권장 사항**

* **스프링에서 불변 클래스를 사용할 수 있으며**, 생성자 주입과 Getter를 활용하여 불변성을 유지할 수 있습니다.
* **JPA 엔티티나 일부 스프링 기능에서는 불변성을 완벽히 유지하기 어려울 수 있지만**, 가능한 범위 내에서 불변성을 지키는 것이 좋습니다.
* **Setter 메서드를 제공하지 않고**, 필요하다면 생성자나 정적 팩토리 메서드를 통해 객체를 생성합니다.
* **Lombok**이나 **Immutable**과 같은 라이브러리를 활용하면 불변 클래스를 더 쉽게 만들 수 있습니다.
* **불변성을 유지하면** 멀티스레드 환경에서의 안전성, 예측 가능한 코드 작성, 유지보수성 향상 등의 이점을 얻을 수 있습니다.

***

## 9. 레이스 컨디션

**레이스 컨디션(Race Condition)이란 무엇인가요?**

레이스 컨디션은 멀티스레드 또는 멀티프로세스 환경에서 두 개 이상의 스레드나 프로세스가 **동시에 공유 자원에 접근하거나 조작하려고 할 때 발생하는 문제**를 말합니다. 이로 인해 프로그램의 실행 결과가 의도치 않게 달라지거나 예기치 않은 버그가 발생할 수 있습니다.

#### 왜 발생하나요?

* **동기화 부족**: 공유 자원에 대한 접근이 적절히 동기화되지 않으면, 여러 스레드가 동시에 자원에 접근하여 상태를 변경할 수 있습니다.
* **비결정적 실행 순서**: 스레드의 실행 순서는 운영체제의 스케줄링에 따라 결정되며, 예측할 수 없습니다. 이로 인해 실행 결과가 매번 달라질 수 있습니다.

#### 예시

간단한 예로, 두 개의 스레드가 하나의 변수 `counter`를 증가시키는 작업을 수행한다고 가정해보겠습니다.

```java
public class Counter {
    private int counter = 0;

    public void increment() {
        counter++;
    }

    public int getCounter() {
        return counter;
    }
}
```

두 스레드가 동시에 `increment()` 메서드를 호출하면 다음과 같은 문제가 발생할 수 있습니다:

1. 스레드 A가 `counter`의 값을 읽음 (`counter = 0`)
2. 스레드 B가 `counter`의 값을 읽음 (`counter = 0`)
3. 스레드 A가 `counter`를 1 증가시킴 (`counter = 1`)
4. 스레드 B가 `counter`를 1 증가시킴 (`counter = 1`)

이 경우, 두 번 증가시켰지만 `counter`의 값은 1이 됩니다. 의도한 결과는 2여야 하지만, 레이스 컨디션으로 인해 올바르지 않은 결과가 나왔습니다.

#### 해결 방법

*   **동기화 사용**: `synchronized` 키워드나 `Lock` 객체를 사용하여 공유 자원에 대한 접근을 직렬화합니다.

    ```java
    public class Counter {
        private int counter = 0;

        public synchronized void increment() {
            counter++;
        }

        public synchronized int getCounter() {
            return counter;
        }
    }
    ```
*   **원자적 연산 사용**: `AtomicInteger`와 같은 원자적 클래스를 사용하여 동기화 없이도 안전하게 연산할 수 있습니다.

    ```java
    import java.util.concurrent.atomic.AtomicInteger;

    public class Counter {
        private AtomicInteger counter = new AtomicInteger(0);

        public void increment() {
            counter.incrementAndGet();
        }

        public int getCounter() {
            return counter.get();
        }
    }
    ```
* **불변 객체 사용**: 객체의 상태를 변경하지 않고 새로운 객체를 생성하여 반환하는 불변 객체 패턴을 사용하면 레이스 컨디션을 방지할 수 있습니다.

#### 결론

레이스 컨디션은 멀티스레드 환경에서 매우 흔하게 발생하는 문제로, 프로그램의 신뢰성과 안정성을 해칠 수 있습니다. 따라서 공유 자원에 대한 접근은 반드시 적절한 동기화 메커니즘을 사용하여 제어해야 합니다.
