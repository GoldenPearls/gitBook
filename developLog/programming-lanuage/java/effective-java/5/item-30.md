# item 30 : 이왕이면 제네릭 메서드로 만들라

클래스와 마찬가지로, 메서드도 제네릭으로 만들 수 있다.

> 매개변수화 타입을 받는 정적 유틸리티 메서드는 보통 제네릭이다. 예컨대 Collections의 **알고리즘 메서드(binarysearch, sort 등)는 모두 제네릭**이다.

## 1. 제네릭 메서드 작성법

### 1) **로 타입을 사용하는 문제점**

먼저, 로 타입(raw type)을 사용한 `union` 메서드는 컴파일 경고가 발생한다:

```java
public static Set union(Set s1, Set s2) {
    Set result = new HashSet(s1);
    result.addAll(s2);
    return result;
}
```

이 메서드는 경고 없이 컴파일되지만, 타입 안전성을 보장하지 않는다. `Set` 타입의 원소가 어떤 타입인지 알 수 없어, `ClassCastException`이 발생할 가능성이 높다.

### 2) 제네릭 타입으로 안전성 확보

로 타입 문제를 해결하려면 메서드를 제네릭 메서드로 작성해야 한다.

```java
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```

* `<E>`: 타입 매개변수를 선언
* `Set<E>`: 메서드의 반환 타입과 매개변수 타입에 타입 매개변수를 사용하여 타입 안전성을 보장한다.

> 이 메서드는 입력 집합과 반환 집합의 타입이 일치해야 하며, 타입 안전한 코드로 컴파일할 수 있다.

### 3) 제네릭 사용 틀

```java
public static <E/*타입 매개변수 목록*/> Set<E/*반환 타입*/> union(Set<E/*파라미터 타입*/> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```

* 순서대로 타입 매개변수 목록, 반환 타입, 파라미터 타입 3가지를 메서드 시그니처에 입력할 수 있다.
* 이 메서드는 경고 없이 컴파일 되며, 타입 안전하고, 쓰기도 쉽다.
* union 메서드는 집합 3개（입력 2개, 반환 1개）의 타입이 모두 같아야 한다.&#x20;

### 4) 활용 예제

```java
public static void main（String[] args） {
    Set<String> guys = Set.of("톰", "딕", "헤리");
    Set<String> stooges = Set.of("래리", "모에", "컬리");
    Set<String> aflCio = union(guys, stooges);
    System.out.println("aflCio = " + aflCio);
}
```

모에, 톰, 해리, 래리, 컬리, 딕 출력됨&#x20;

* 위 예제에서는 두 개의 `Set<String>`을 합쳐 새로운 집합을 생성한다.
* 이 메서드는 제네릭 메서드이기 때문에, 컴파일 시 타입 검사를 통해 안전한 코드임을 보장한다.

### 5) 한정적 와일드 카드를 이용한 union 메서드

한정적 와일드카드 타입을 사용하여 `union` 메서드를 더 유연하게 개선할 수 있다.&#x20;

> 와일드카드를 사용하면 **두 입력 집합의 타입이 정확히 일치하지 않아도, 공통의 상위 타입이 있는 경우 메서드를 호출할 수 있게 된다.** 이를 통해 다양한 타입의 집합을 안전하게 합칠 수 있다.

<pre class="language-java"><code class="lang-java">import java.util.HashSet;
import java.util.Set;

public class UnionExample {

    // 한정적 와일드카드 타입을 사용한 union 메서드
    public static &#x3C;E> Set&#x3C;E> union(Set&#x3C;? extends E> s1, Set&#x3C;? extends E> s2) {
        Set&#x3C;E> result = new HashSet&#x3C;>(s1);
        result.addAll(s2);
        return result;
    }

    public static void main(String[] args) {
        Set&#x3C;String> guys = Set.of("톰", "딕", "헤리");
        Set&#x3C;String> stooges = Set.of("래리", "모에", "컬리");
        
        // 한정적 와일드카드 타입을 사용한 union 메서드를 호출
        Set&#x3C;String> aflCio = union(guys, stooges);
        
        // 결과 출력
        System.out.println("aflCio = " + aflCio);
    }
}
    Set&#x3C;E> result = new HashSet&#x3C;>(s1);
    result.addAll(s2);
    return result;
<strong>}
</strong></code></pre>

`Set<? extends E>`는 `E`의 하위 타입을 허용하여 다양한 타입의 집합을 합칠 수 있다.

* **메서드 시그니처**:
  * `public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2)`:
    * `<E>`는 타입 매개변수를 정의한다.
    * `Set<? extends E>`는 `E`의 하위 타입을 나타내는 한정적 와일드카드 타입이다. 즉, `s1`과 `s2`는 `E`의 하위 타입인 집합을 받을 수 있다.
    * 반환 타입은 `Set<E>`로, 두 집합의 합집합을 반환한다.
* **유연한 타입 처리**:
  * `Set<? extends E>`를 사용함으로써, 입력 집합의 타입이 `E`의 하위 타입일 때에도 메서드를 호출할 수 있다.
* **출력 결과**:
  * `guys`와 `stooges` 두 집합의 합집합을 구한 결과가 출력된다.

## 2. 제네릭 싱글턴 팩터리

때로는 **불변 객체를 여러 타입으로 활용할 수 있게 만들어야 할 때가 있다.**&#x20;

> 제네릭 싱글턴 팩터리 패턴을 사용하면, 런타임에 하나의 객체를 어떤 타입으로든 매개변수화할 수 있다.

다음은 **제네릭 싱글턴 패턴을 사용하여 항등 함수(identity function)를 구현한 예제**로, 이 패턴을 통해 하나의 객체를 어떤 타입으로든 매개변수화할 수 있다.



### 1) 여기서 잠깐 런타임에 하나의 객체를 어떤 타입으로든 매개변수화할 수 있다는 무슨뜻일까?

`매개변수화(Parameterized)`란 어떤 요소를 특정 값이나 타입에 맞게 일반화하여 사용할 수 있도록 하는 것을 의미한다.



`런타임에 하나의 객체를 어떤 타입으로든 매개변수화할 수 있다`는 것은 **제네릭 타입의 객체를 생성할 때 사용하는 타입 매개변수가 런타임에 결정될 수 있다는 의미**이다.&#x20;

> 즉, 컴파일 시점에는 타입 매개변수가 소거되지만, 런타임에 제네릭 객체를 특정 타입으로 활용할 수 있는 방법을 제공할 수 있다는 뜻이다.

자바에서 제네릭은 컴파일 시점에 타입 검사를 수행하고, 런타임에는 타입 정보를 제거하는 "타입 소거(type erasure)" 방식을 사용한다. 그러나**, 제네릭 싱글턴 패턴을 통해 런타임에 타입을 변경하여 여러 타입에 대해 동일한 객체를 사용할 수 있다.**



### 2) **제네릭 싱글턴 팩토리 패턴 예제**

```java
// 항등 함수(identity function)를 구현한 제네릭 싱글턴 객체
// 입력 값을 그대로 반환하는 함수 객체로, 상태가 없기 때문에 여러 타입에 대해 재사용 가능
private static final UnaryOperator<Object> IDENTITY_FN = t -> t;

@SuppressWarnings("unchecked")
public static <T> UnaryOperator<T> identityFunction() {
    // 제네릭 타입 T로 형변환하여 반환 (타입 안전성이 보장되므로 비검사 경고를 억제)
    return (UnaryOperator<T>) IDENTITY_FN;
}
```

* `IDENTITY_FN`은 입력값을 수정 없이 그대로 반환하는 `UnaryOperator<Object>` 타입의 함수 객체이다.
* `identityFunction()` 메서드는 제네릭 메서드로, 호출 시 타입 매개변수 `T`에 맞게 항등 함수 객체를 반환한다.
* `@SuppressWarnings("unchecked")` 애너테이션은 컴파일러의 비검사 형변환 경고를 억제한다. 여기서는 타입 안전성이 보장되기 때문에 사용해도 문제가 없다.

### **3) 제네릭 싱글턴 사용 예제**

> UnaryOperator를 어떤 타입이든 계속해서 이용할 수 있다.

```java
public static void main(String[] args) {
    // 문자열 배열
    String[] strings = { "삼베", "대마", "나일론" };
    // 제네릭 싱글턴을 사용하여 UnaryOperator<String> 타입의 항등 함수 생성
    UnaryOperator<String> sameString = identityFunction();
    // 배열의 각 원소에 대해 항등 함수를 적용 (값이 그대로 출력됨)
    for (String s : strings) {
        System.out.println(sameString.apply(s));
    }

    // 숫자 배열
    Number[] numbers = { 1, 2.0, 3L };
    // 제네릭 싱글턴을 사용하여 UnaryOperator<Number> 타입의 항등 함수 생성
    UnaryOperator<Number> sameNumber = identityFunction();
    // 배열의 각 원소에 대해 항등 함수를 적용 (값이 그대로 출력됨)
    for (Number n : numbers) {
        System.out.println(sameNumber.apply(n));
    }
}
```

* `identityFunction()` 메서드를 통해 `UnaryOperator<String>`과 `UnaryOperator<Number>`로 각각의 타입에 대해 항등 함수를 사용한다.
* 문자열 배열과 숫자 배열의 각 요소에 대해 항등 함수를 적용하여 값을 그대로 출력한다. 컴파일 경고나 오류 없이 사용할 수 있다.

> 이 방식은 제네릭 객체를 생성할 때 사용하는 타입 매개변수를 런타임에 결정하여 동일한 객체를 다양한 타입으로 사용할 수 있게 해준다. 이는 메모리 사용을 줄이고, 코드 중복을 방지하는 데 유용한 패턴이다.

## 3.  재귀적 타입 한정

`재귀적 타입 한정`은 <mark style="color:red;">제네릭 타입 매개변수가 자기 자신을 사용하여 타입의 허용 범위를 한정하는 것</mark>을 말한다. 주로 `Comparable` 인터페이스와 함께 사용되어, **특정 타입이 자기 자신과 비교할 수 있도록 제한한다.**



재귀적 타입 한정은 정렬, 검색, 최댓값 또는 최솟값 계산과 같은 비교 연산이 필요한 메서드에서 자주 사용된다.

```java
// 컬렉션 내의 원소 중에서 가장 큰 값을 반환하는 메서드
// <E extends Comparable<E>>는 E 타입이 자기 자신과 비교 가능하다는 의미
public static <E extends Comparable<E>> E max(Collection<E> c) {
    // 컬렉션이 비어 있는 경우 예외 발생
    if (c.isEmpty()) {
        throw new IllegalArgumentException("컬렉션이 비어 있습니다.");
    }

    E result = null;
    // 컬렉션의 각 원소를 순회하며 최댓값 찾기
    for (E e : c) {
        // 처음 원소이거나 현재 최댓값보다 큰 경우 최댓값 갱신
        if (result == null || e.compareTo(result) > 0) {
            result = e;
        }
    }
    // 최댓값 반환
    return result;
}

```

> 위 메서드는 컬렉션의 최댓값을 구하며, `Comparable<E>`를 구현한 타입이어야만 사용 가능하다.

* `<E extends Comparable<E>> :` 타입 매개변수 `E`는 `Comparable<E>`를 구현해야 한다는 뜻입니다. 즉, `E` 타입의 객체끼리 비교할 수 있어야 한다.
* 이 메서드는 컬렉션에 있는 원소의 자연적 순서를 기준으로 최댓값을 찾는다.
* 컬렉션이 비어 있을 경우 `IllegalArgumentException`을 찾는다.
* 재귀적 타입 한정을 통해 `E` 타입의 객체가 서로 비교 가능함을 컴파일러가 보장해준다.

## JPA EnumConverter 예시

```java
public interface CodeValue {
	String getCode();
	String getValue();
}

public class EnumConverter<E extends Enum<E> & CodeValue> implements AttributeConverter<E, String> {

	private Class<E> clz;

	protected EnumConverter(Class<E> enumClass) {
		this.clz = enumClass;
	}

	@Override
	public String convertToDatabaseColumn(E attribute) {
		return attribute != null ? attribute.getCode() : null;
	}

	@Override
	public E convertToEntityAttribute(String dbData) {
		if (dbData == null)
			return null;
		return EnumSet.allOf(clz).stream()
			.filter(e -> e.getCode().equals(dbData))
			.findAny()
			.orElseThrow(() -> new BusinessException(ErrorCode.ENUM_NOT_FOUND));
	}
}

@Converter(autoApply = true)
public class ChargerTypeConverter extends EnumConverter<ChargerType> {
	public ChargerTypeConverter() {
		super(ChargerType.class);
	}
}

@Convert(converter = ChargerTypeConverter.class)
@Column(name = "charger_type", nullable = false)
private ChargerType chargerType;

public enum ChargerType implements CodeValue {
	SLOW("SLOW", "완속"),
	AC3("AC3", "AC3상"),
	DESTINATION("DESTINATION", "급속"),
	ETC("ETC", "기타"),
	;

	private final String code;
	private final String value;

	ChargerType(String code, String value) {
		this.code = code;
		this.value = value;
	}

	@Override
	public String getCode() {
		return code;
	}

	@Override
	public String getValue() {
		return value;
	}
}
```

이 코드는 `JPA`(Java Persistence API)에서 사용되는 커스텀 `AttributeConverter`를 통해 데이터베이스와 엔티티 간의 변환을 처리하는 예제입니다. 여기서는 `Enum` 타입인 `ChargerType`을 데이터베이스 컬럼에 저장하고, 다시 엔티티 속성으로 변환하는 과정을 다룹니다.

#### 주요 구성 요소 설명

1.  **`CodeValue` 인터페이스**

    * 이 인터페이스는 각 열거형(Enum)이 `getCode()`와 `getValue()` 메서드를 구현해야 한다는 것을 보장합니다.
    * 이 코드에서는 `getCode()`가 데이터베이스에 저장할 값을 반환하고, `getValue()`가 표현할 값을 반환합니다.

    ```java
    public interface CodeValue {
        String getCode();
        String getValue();
    }
    ```
2.  **`EnumConverter` 클래스**

    * 이 클래스는 `AttributeConverter<E, String>`을 구현하여 제네릭 타입의 열거형(`E extends Enum<E> & CodeValue`)을 데이터베이스의 `String` 타입으로 변환하는 역할을 합니다.
    * `E`는 `Enum` 타입이면서 `CodeValue` 인터페이스를 구현한 열거형을 의미합니다.
    * `AttributeConverter<E, String>`: `E` 타입의 속성을 데이터베이스의 `String` 타입으로 변환하고, 반대로 변환하는 역할을 합니다.

    ```java
    public class EnumConverter<E extends Enum<E> & CodeValue> implements AttributeConverter<E, String> {
        private Class<E> clz;

        protected EnumConverter(Class<E> enumClass) {
            this.clz = enumClass;
        }

        @Override
        public String convertToDatabaseColumn(E attribute) {
            // 엔티티 속성(E)을 데이터베이스 컬럼(String)으로 변환
            return attribute != null ? attribute.getCode() : null;
        }

        @Override
        public E convertToEntityAttribute(String dbData) {
            // 데이터베이스의 String을 엔티티 속성으로 변환
            if (dbData == null)
                return null;
            return EnumSet.allOf(clz).stream()
                .filter(e -> e.getCode().equals(dbData)) // Enum의 코드와 일치하는 항목을 찾음
                .findAny()
                .orElseThrow(() -> new BusinessException(ErrorCode.ENUM_NOT_FOUND)); // 찾지 못하면 예외 발생
        }
    }
    ```

    * **`convertToDatabaseColumn` 메서드**:
      * 엔티티의 열거형 속성을 데이터베이스에 저장할 `String` 타입의 값으로 변환합니다.
      * 열거형 값이 `null`인 경우 `null`을 반환합니다. 그렇지 않으면 `getCode()` 메서드를 통해 변환된 코드를 반환합니다.
    * **`convertToEntityAttribute` 메서드**:
      * 데이터베이스에서 가져온 `String` 값을 엔티티의 열거형 타입으로 변환합니다.
      * `EnumSet.allOf(clz)`를 통해 해당 열거형 클래스의 모든 값을 순회하며, `getCode()` 메서드로 필터링하여 매칭되는 열거형 값을 찾습니다.
      * 매칭되는 값이 없으면 `BusinessException`을 발생시킵니다(`ENUM_NOT_FOUND`).
3.  **`ChargerTypeConverter` 클래스**

    * `EnumConverter`의 구체적인 구현체로, `ChargerType` 열거형 타입에 대한 변환기를 정의합니다.
    * `@Converter(autoApply = true)` 어노테이션을 사용하여 JPA가 자동으로 이 변환기를 적용하도록 설정합니다.

    ```java
    @Converter(autoApply = true)
    public class ChargerTypeConverter extends EnumConverter<ChargerType> {
        public ChargerTypeConverter() {
            super(ChargerType.class);
        }
    }
    ```
4.  **`ChargerType` 열거형**

    * `ChargerType`은 `CodeValue` 인터페이스를 구현한 열거형입니다.
    * 각 상수에는 `code`와 `value`라는 두 개의 필드를 가지며, 이는 생성자에서 설정됩니다.
    * 예를 들어, `SLOW("SLOW", "완속")`는 `code`가 `"SLOW"`이고, `value`가 `"완속"`인 열거형 상수를 나타냅니다.

    > <mark style="color:red;">오직 jpa만 있는 컨터버 기능으로 SLOW => 완속으로 DB에 들어가고 또 자바로  나올때 SLOW로 나오는 그런 느낌?</mark>



    ```java
    public enum ChargerType implements CodeValue {
        SLOW("SLOW", "완속"),
        AC3("AC3", "AC3상"),
        DESTINATION("DESTINATION", "급속"),
        ETC("ETC", "기타");

        private final String code;
        private final String value;

        ChargerType(String code, String value) {
            this.code = code;
            this.value = value;
        }

        @Override
        public String getCode() {
            return code;
        }

        @Override
        public String getValue() {
            return value;
        }
    }
    ```
5.  **엔티티에서의 사용 예시**

    ```java
    @Convert(converter = ChargerTypeConverter.class)
    @Column(name = "charger_type", nullable = false)
    private ChargerType chargerType;
    ```

    * 이 코드에서는 `chargerType` 필드가 데이터베이스의 `charger_type` 컬럼과 매핑됩니다.
    * `@Convert` 어노테이션을 통해 `ChargerTypeConverter`를 사용하여 `ChargerType` 값을 변환합니다.

#### 동작 흐름

1. **엔티티 -> 데이터베이스**:
   * `ChargerType` 열거형 값이 `convertToDatabaseColumn` 메서드를 통해 `String` 값으로 변환되어 데이터베이스에 저장됩니다. 예를 들어, `ChargerType.SLOW`의 경우 `"SLOW"`로 저장됩니다.
2. **데이터베이스 -> 엔티티**:
   * 데이터베이스에서 가져온 `String` 값이 `convertToEntityAttribute` 메서드를 통해 `ChargerType` 열거형 값으로 변환됩니다. 예를 들어, `"SLOW"`가 `ChargerType.SLOW`로 변환됩니다.
3. **예외 처리**:
   * 데이터베이스의 값이 매칭되는 `ChargerType` 상수를 찾지 못하면 `BusinessException`이 발생하여 처리됩니다.

#### 코드의 장점

* **일관성 유지**: 열거형 값을 데이터베이스에 코드로 저장하고, 코드 값으로부터 열거형 값을 매핑할 수 있는 일관된 방식을 제공합니다.
* **재사용성**: `EnumConverter`는 모든 `CodeValue` 인터페이스를 구현하는 열거형에 대해 범용적으로 사용할 수 있는 변환기를 제공합니다.
* **유연한 예외 처리**: 변환 실패 시 예외를 명확하게 정의하여 오류 상황을 쉽게 처리할 수 있습니다.

이 코드는 JPA에서 열거형 타입을 처리하는 데 유용하며, 커스텀 변환기를 통해 데이터베이스와 엔티티 간의 변환을 효과적으로 관리합니다.



> 장점은?

이 코드는 JPA에서 열거형(Enum) 타입을 데이터베이스와 연동할 때 몇 가지 중요한 이점을 제공합니다:

#### 1. **코드와 데이터베이스 간의 매핑을 일관되게 유지**

* `EnumConverter`를 사용하면 열거형 값(예: `ChargerType.SLOW`)을 데이터베이스에 저장할 때 문자열(`SLOW`)로 변환하고, 다시 데이터베이스에서 열거형 타입으로 변환할 수 있습니다.
* 이를 통해 데이터베이스와 코드 간의 데이터 일관성을 유지할 수 있으며, 열거형 값이 변경되더라도 코드의 변경만으로 쉽게 반영할 수 있습니다.

#### 2. **커스텀 변환 로직 구현의 유연성**

* `EnumConverter`를 통해 변환 로직을 커스터마이즈할 수 있습니다. 예를 들어, 데이터베이스에 저장하는 값의 형식이나 특정 조건에 따른 변환 방식을 쉽게 조정할 수 있습니다.
* JPA의 기본 변환기 대신 커스텀 변환기를 사용함으로써, 엔티티 필드에 대한 세부적인 변환 로직을 적용할 수 있습니다.

#### 3. **코드의 재사용성과 유지보수성 향상**

* `EnumConverter`는 제네릭 타입으로 구현되어 있어, `CodeValue` 인터페이스를 구현하는 모든 열거형에 재사용할 수 있습니다. 따라서 코드 중복을 줄이고 변환 로직을 일관되게 적용할 수 있습니다.
* 예를 들어, 다른 열거형 타입을 추가할 때도 새로운 변환기를 만들지 않고 기존 `EnumConverter`를 재사용할 수 있어 코드 유지보수가 용이합니다.

#### 4. **안전한 타입 변환과 예외 처리**

* 변환 시 매칭되는 열거형이 없으면 `BusinessException`을 통해 예외를 명확하게 처리할 수 있습니다. 이를 통해 잘못된 데이터나 예상치 못한 값이 들어오는 상황에 대비할 수 있습니다.
* `CodeValue` 인터페이스를 통해 각 열거형이 `getCode()`와 `getValue()`를 반드시 구현하도록 강제함으로써, 타입 안정성을 보장합니다.

#### 5. **자동 매핑 기능 지원**

* `@Converter(autoApply = true)`를 사용하면 해당 변환기를 자동으로 적용하여, 엔티티에서 매번 `@Convert`를 명시하지 않아도 됩니다. 이는 코드의 간결함과 가독성을 높여줍니다.

#### 6. **데이터베이스 스키마 변경 시 유연성 제공**

* 예를 들어, 데이터베이스에 저장되는 문자열 값이 `"SLOW"`에서 `"S"`로 변경되는 상황이 발생할 때, `EnumConverter`의 변환 로직만 수정하면 됩니다. 이를 통해 데이터베이스 스키마 변경에 유연하게 대처할 수 있습니다.

#### 정리

이 코드의 주요 장점은 **일관된 변환 로직**과 **재사용성**, **유연한 예외 처리**를 통해 데이터베이스와 애플리케이션 간의 매핑을 안전하고 효율적으로 관리할 수 있다는 점입니다. `EnumConverter`를 통해 데이터베이스의 값과 코드의 열거형 간의 매핑을 명확히 정의함으로써, 유지보수성이 높고 오류 발생 가능성이 낮아집니다.



**MYBATIS**

타입 핸들러라는 클래스 정의, XML에 넣어두면 열거형 변환해준다는 것

<figure><img src="../../../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

> 출처 : [https://velog.io/@ghk4889/mybatis%EC%9D%98-custom-typehandler-%EB%A7%8C%EB%93%A4%EA%B8%B0JAVA-Enum-%ED%83%80%EC%9E%85](https://velog.io/@ghk4889/mybatis%EC%9D%98-custom-typehandler-%EB%A7%8C%EB%93%A4%EA%B8%B0JAVA-Enum-%ED%83%80%EC%9E%85)



**재귀적 타입 한정의 의미**

`<E extends Comparable<E>>`는 "`E` 타입의 객체가 자기 자신(`E`)과 비교 가능하다"는 의미이다. 예를 들어, `String`은 `Comparable<String>`을 구현하고, `Integer`는 `Comparable<Integer>`를 구현하는 식이다. 이를 통해 컬렉션 내의 원소가 서로 비교 가능함을 보장할 수 있다.

## **✨ 최종** 정리 : 제네릭 메서드의 장점과 활용

1. **안전성과 편리성**:

* 제네릭 메서드는 `타입 안전성`을 제공하여, 메서드 호출 시 컴파일러가 타입 검사를 해준다.
* 형변환을 명시적으로 할 필요가 없어서 사용하기 쉽고, 코드의 가독성도 향상된다.

2. **형변환 없는 사용**:

* 제네릭 메서드를 사용하면, 입력 매개변수와 반환값의 타입을 명시적으로 형변환할 필요가 없다.
* 메서드의 타입 매개변수가 타입을 결정하므로, 클라이언트가 불필요한 형변환 없이 안전하게 메서드를 사용할 수 있다.

3. **기존 메서드의 개선**:

* 기존의 형변환을 필요로 하는 메서드는 제네릭 메서드로 변경하여 더 안전하게 만들 수 있다.
* 기존 클라이언트 코드에 영향을 주지 않으면서도, 새로운 사용자에게는 더 편리한 메서드를 제공할 수 있다.

4. **코드 재사용성**:

* 제네릭 메서드를 사용하면 다양한 타입에 대해 하나의 메서드를 재사용할 수 있어 코드 중복이 줄어든다.
* 제네릭 메서드는 타입 안전성과 코드 재사용성을 높인다.

> 제네릭을 사용하면 컴파일 시점에 타입 안전성을 보장받을 수 있으며, 기존 코드와의 호환성을 유지하면서 더 유연하고 안전한 코드를 작성할 수 있다.



> 출처&#x20;
>
> * [https://jake-seo-dev.tistory.com/50](https://jake-seo-dev.tistory.com/50)
