# item 33 : 타입 안전 이종 컨테이너를 고려하라

## 1. 타입  안전이중 컨테이너 방식 개념 및 사용 방법

### 1) 타입안전이중 컨테이너 방식이란?

기존의 제네릭 컨테이너(`Set<E>`, `Map<K, V>` 등)에서는 지정된 개수의 타입 매개변수만 사용할 수 있다.\
예를 들어, `Set<E>`에는 원소의 타입을 뜻하는 단 하나의 타입 매개변수만 있으면 되며, `Map<K,V>` 에는 키와 값을 뜻하는 2개만 필요하다.

{% hint style="warning" %}
하지만 **컨테이너 자체가 아닌 "키"를 매개변수화한 다음, 컨테이너에 값을 넣거나 뺄 때 매개변수화한 키를 함께 제공**한다면 더 유연하게 여러 타입을 받을 수 있지 않을까?
{% endhint %}

바로 이러한 설계 방식을, 우리는 **타입 안전 이종 컨테이너 패턴**이라고 한다.

```java
public class Favorites {
    public <T> void putFavorite(Class<T> type, T instance); // 키가 매개변수화 됨
    public <T> T getFavorite(Class<T> type); 
}
```

**컨테이너를 타입별로 나누지 않고, 키 자체에 타입을 매개변수화하는 방식**을 제안하고 있다. 즉, 특정 키에 원하는 타입을 지정하고, 값을 저장하거나 조회할 때 이 키와 함께 타입 정보를 제공하여 **더 유연하게 여러 타입의 값을 저장하고 관리**할 수 있는 구조를 생각하는 것이다.

{% hint style="info" %}
예제를 보기 전, 여기서 제네릭은 Set, Map\<K,V> 등의 컬렉션과 Threadl\_ocal, AtomicReference 등의 단일원소 컨테이너에도 흔히 쓰인다. 이런 모든 쓰임에서 매개변수화되는 대상은 (원소가 아닌) 컨테이너 자신이다.
{% endhint %}

여기서 "매개변수화되는 대상이 컨테이너 자신"이라는 것은, **제네릭 타입이 적용되는 범위가 컨테이너 타입**이라는 의미이다. 즉, `Set<E>`, `Map<K, V>`, `ThreadLocal<T>`, `AtomicReference<T>` 등에서 제네릭 타입은 해당 **컨테이너의 구조 자체**를 타입으로 매개변수화하고 있다는 뜻이다.

**원소(Element)와 컨테이너(Container) 구분**

* **원소 (Element)**: 컨테이너 안에 저장되는 실제 데이터, 예를 들어 `Set<Integer>`에서 원소는 `Integer` 타입의 데이터들이 된다.
* **컨테이너(Container)**: 원소들을 담는 그릇으로, 원소들이 담기는 구조 자체가 컨테이너이다. 예를 들어 `Set<Integer>`에서 `Set`이 컨테이너에 해당한다.

**매개변수화된 대상이 "컨테이너"라는 의미**

`Set<E>` 같은 제네릭 컬렉션을 예로 들어보면, `Set<Integer>`, `Set<String>`과 같이 특정 타입의 원소들을 담는 구조로 매개변수화될 뿐, **제네릭 타입 매개변수 `E`가 "원소"를 직접 매개변수화하는 것이 아니다.**&#x20;

> 즉, 제네릭 타입으로 정의된 것은 **원소의 타입이 아닌 "컨테이너"가 원소를 어떤 타입으로 관리하는가**를 결정하는 것이다.

**예시 1: `Set<E>`**

```java
Set<String> stringSet = new HashSet<>();
Set<Integer> intSet = new HashSet<>();
```

* `Set<String>`과 `Set<Integer>`는 둘 다 "Set"이라는 동일한 컨테이너이다.
* 다만, **제네릭 타입 매개변수 `E`를 통해 컨테이너가 어떤 타입의 원소(`String` 또는 `Integer`)를 가질지 정의한다.**

**예시 2: `AtomicReference<T>`**

```java
AtomicReference<String> atomicString = new AtomicReference<>("hello");
AtomicReference<Integer> atomicInt = new AtomicReference<>(123);
```

* `AtomicReference`는 단일 원소를 담는 컨테이너이며, 제네릭 타입 매개변수 `T`는 담길 원소의 타입을 지정할 뿐, 원소 자체를 매개변수화하는 것은 아닙니다.
* 즉, **컨테이너(여기서는 `AtomicReference`)가 `String` 또는 `Integer` 타입의 원소를 담는 방식을 매개변수화**하는 것이다.

```java
// Set 예시: 여러 원소를 관리
Set<String> stringSet = new HashSet<>();
stringSet.add("hello");
stringSet.add("world");

// AtomicReference 예시: 단일 원소를 원자적으로 관리
AtomicReference<String> atomicString = new AtomicReference<>("hello");
atomicString.set("world");
```

> 따라서 **제네릭 매개변수는 항상 "컨테이너가 원소를 어떤 타입으로 취급하는지"를 결정**하며, 원소가 직접 매개변수화되는 것이 아니라, **컨테이너가 원소의 타입에 따라 구조를 관리**하는 방식을 정의하는 것이다. ㄷ

다음과 같은 구조에서, `Key` 객체 자체가 특정 타입의 매개변수로 정의되며, 이 키와 함께 컨테이너에서 값을 넣고 빼는 방식의 예제이다.

```java
// Key 클래스에 타입 매개변수 정의
public class Key<T> {
    // 타입 안전한 키 클래스
}

// 컨테이너 클래스
public class Container {
    private final Map<Key<?>, Object> values = new HashMap<>();

    // 값을 저장할 때 타입에 맞는 키를 사용
    public <T> void put(Key<T> key, T value) {
        values.put(key, value);
    }

    // 값을 조회할 때 타입에 맞는 키를 사용하여 타입 안전성 보장
    public <T> T get(Key<T> key) {
        return (T) values.get(key);
    }
}
```

> 이 방식의 핵심은, 컨테이너가 아닌 **키 객체에 타입 매개변수를 부여**하여, <mark style="color:red;">여러 타입의 값을 하나의 컨테이너에 타입 안전하게 담고 관리할 수 있다는 점이다.</mark>&#x20;

> 컨테이너를 클래스로 이해하면 알기 편하다

### 2) 타입 안전 이종 컨테이너 개념

사실 `class` 의 타입이 `Class<T>` 제네릭이기 때문에, 각 타입의 `Class` 객체를 매개변수화한 **키 역할**로 사용하는 것이 가능해진다. ex) `String.class` 타입은 `Class<String>, Integer.class`의 타입은 `Class<Integer>`

한편, 컴파일타임 타입 정보와 런타임 타입 정보를 알아내기 위해 메서드들이 주고받는 class 리터 럴을 **타입 토큰**(type token)이라 한다.&#x20;

{% hint style="success" %}
가장 중요한 것은, 우리는 이를 통해 제네릭에서의 컴파일타임 타입 안전성이 아닌 **런타임 타입 안전성**을 얻을 수 있다는 것이다.
{% endhint %}

### 3) 타입 안전 이종 컨테이너 구현

```java
public class Favorites{
    private Map<Class<?>, Object> favorites = new HashMap<>();

    public <T> void putFavorite(Class<T> type, T instance) {
        favorites.put(Objects.requireNonNull(type), instance);
    }

    public <T> T getFavorite(Class<T> type) {
        return type.cast(favorites.get(type));
    }
}
```

1. `Map<Class<?>, Object>`\
   키를 **비한정적인 와일드카드 타입**으로 선언하였기 때문에, 이를 통해서 **다양한 매개변수화 타입의 키**를 허용할 수 있게 되었다. 만약 `Map<Class<T>, Object>` 였다면 오직 한가지 타입의 키만 담을 수 있었을 것이다.
2. `Class.cast`\
   `value` 가 `Object` 타입이므로 맵에 넣을때 값이 키 타입의 인스턴스라는 것이 보장되지 않는다. 따라서 맵에서 가져올때는 `cast` 메서드를 사용해 이 객체 참조를 `class` 객체가 가리키는 `T` 타입으로 동적 변환하고 있다.

> 그런데 cast 메서드가 **단지 인수를 그대로 반환하기만 한다면** 굳이 왜 사용 하는 것일까?&#x20;

그 이유는 cast 메서드의 시그니처가 Class 클래스가 제네릭이라는 이점을 완벽히 활용하기 때문이다. 다음 코드에서 보듯 <mark style="color:red;">cast의 반환 타입은 Class 객체의 타입 매개변수와 같다.</mark>

> 🔖 **cast 메서드**\
> 형변환 연산자의 동적 버전으로, 주어진 인수가 Class 객체가 알려주는 타입의 인스턴스인지 검사한 다음, 맞다면 반환하고 아니면 `ClassCastException` 을 던진다. 이를 활용하면, `T` 로 비검사 형변환을 하지 않아도 된다. 타입을 더욱 안전하게 만들어준다.
>
> ```java
> public class Class<T>{
> 	T cast(Object obj);
> }
> ```

{% hint style="info" %}
좀더,  자세히 `value` 가 `Object` 타입이므로 맵에 넣을때 값이 키 타입의 인스턴스라는 것이 보장되지 않는다. 따라서 맵에서 가져올때는 `cast` 메서드를 사용해 이 객체 참조를 `class` 객체가 가리키는 `T` 타입으로 동적 변환하고 있다 부분에 대해 알아보자
{% endhint %}

이 설명은 `Class.cast()` 메서드를 이용해 `Favorites` 클래스에서 타입 안전성을 보장하는 방법

1. **값을 Object 타입으로 저장**:
   * `Favorites` 클래스의 `favorites` 맵에서는 **키**로 `Class<T>` 타입을, **값**으로 `Object` 타입을 사용하고 있다.
   * 여기서 `Object` 타입은 모든 타입의 객체를 저장할 수 있지만, 타입이 맞지 않는 값이 저장될 가능성이 있다.
2. **타입 안전성 문제**:
   * 맵의 값이 `Object`로 저장되므로, 특정 키와 일치하는 타입의 인스턴스만 저장되도록 보장할 방법이 없다.
   * 예를 들어, `Class<String>` 타입의 키를 사용해 `String`을 저장했어야 하지만, 다른 타입의 값을 실수로 저장할 수 있는 위험이 있다.
3. **Class.cast()의 역할**:
   * `Class.cast()` 메서드는 동적 형 변환을 제공하여, 맵에서 값을 가져올 때 `Class<T>` 키에 지정된 타입으로 안전하게 변환해준다.
   * 예를 들어, `Class<String>` 키를 사용하여 가져오는 경우, `Class.cast()`는 반환되는 값이 `String`인지 확인하고 맞으면 그대로 반환한다. 만약 `String`이 아니면 `ClassCastException`이 발생한다.
4. **Favorites 클래스의 동작 원리**:
   * `putFavorite` 메서드에서는 `Class<T>` 키와 `T` 타입의 인스턴스를 함께 전달받아 저장한다. 이때 맵은 `Object` 타입으로 저장하지만, `getFavorite` 메서드는 `Class.cast()`를 통해 `T`로 반환되므로 타입 안전성이 확보된다.

```java
public <T> T getFavorite(Class<T> type) {
    return type.cast(favorites.get(type));  // T 타입으로 변환해 안전하게 반환
}
```

* `type.cast(favorites.get(type))`는 `favorites.get(type)`으로 얻어온 `Object` 타입의 값을 `type`이 가리키는 타입으로 형변환한다.
* `type`이 `String.class`일 때 `cast()`는 `Object` 값을 `String`으로 변환하고, `Integer.class`일 때는 `Integer`로 변환한다.

> `Class.cast()` 메서드를 활용함으로써 `Favorites` 클래스는 여러 타입의 객체를 `Map<Class<?>, Object>`에 안전하게 저장하고 꺼낼 수 있다. 이를 통해 동적 형 변환 과정에서 타입 안정성을 유지하고, 잘못된 형변환 시 `ClassCastException`을 발생시켜 오류를 방지할 수 있다.

**클라이언트 활용**

```java
 public static void main(String[] args) {
     Favorites f = new Favorites();
        
     f.putFavorite(String.class, "Java");
     f.putFavorite(Integer.class, 0xcafebabe);
     f.putFavorite(Class.class, Favorites.class);
       
     String favoriteString = f.getFavorite(String.class);
     int favoriteInteger = f.getFavorite(Integer.class);
     Class<?> favoriteClass = f.getFavorite(Class.class);
        
     // Java cafebabe Favorites 출력
     System.out.printf("%s %x %s%n", favoriteString,
                favoriteInteger, favoriteClass.getName()); 
 }
```

이렇게 Favorites 인스턴스는 **타입 안전**하며 일반적인 맵과 달리 **여러 가지 타입의 원소를 담을 수 있기 때문에, 타입 안전 이종 컨테이너**라고 할 수 있다.

<figure><img src="../../../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

### 4) 일반 제네릭과 타입 안정 이중 컨테이너 차이점 한 번 더 정리

{% hint style="info" %}
제네릭 타입을 사용한 일반적인 컨테이너(`Set<E>`, `Map<K,V>`)와 **타입 안전 이종 컨테이너 패턴**의 차이점은 **매개변수화되는 대상이 컨테이너 자신인지, 키인지**에서 나온다.
{% endhint %}

#### 일반적인 제네릭 컨테이너 (`Set<E>`, `Map<K,V>`)의 매개변수화

제네릭 컨테이너인 `Set<E>`, `Map<K,V>`는 컨테이너 자체의 구조를 매개변수화한다.&#x20;

> 즉, 특정 타입의 원소를 담는 구조로 컨테이너를 설정하고, 각 컨테이너에 정의된 **타입 매개변수에 따라 담을 수 있는 원소 타입이 고정**된다.

*   **예시**:

    ```java
    Set<String> stringSet = new HashSet<>();  // String 타입 원소만 저장 가능
    Set<Integer> intSet = new HashSet<>();    // Integer 타입 원소만 저장 가능

    Map<String, Integer> map = new HashMap<>(); // 키는 String, 값은 Integer 타입으로 고정
    ```

    * `Set<E>`에서 `E`는 해당 `Set`이 담을 **원소의 타입**을 나타내며, 이 컨테이너에 다른 타입의 값을 넣을 수 없다.
    * `Map<K,V>`에서 `K`와 `V`는 각각 **키와 값의 타입**을 나타내며, 이 `Map`은 특정 타입의 키와 값만 사용할 수 있다.

**따라서, 일반적인 제네릭 컨테이너에서 매개변수화되는 대상은 컨테이너 자체이다.** `Set`이든 `Map`이든, 특정 타입으로 제한된 하나의 타입을 담기 위한 설정을 해주는 것이다.

***

#### 타입 안전 이종 컨테이너 패턴

**타입 안전 이종 컨테이너 패턴**은 컨테이너에 다양한 타입의 객체를 담을 수 있도록 설계된 패턴이다.

> 이 패턴에서는 제네릭 키(`Class<T>`)를 사용해 컨테이너 안에 서로 다른 타입의 원소를 안전하게 저장하고 조회할 수 있다.

* **특징**:
  * **컨테이너 자체**가 아닌, **키를 매개변수화**하여 컨테이너에 여러 타입을 담을 수 있게 한다.
  * 각 키에 대해 해당 키와 일치하는 타입의 값이 저장되므로 **타입 안전성을 유지**한다.
*   **예시 코드**:

    ```java
    public class Favorites {
        private Map<Class<?>, Object> favorites = new HashMap<>();

        public <T> void putFavorite(Class<T> type, T instance) {
            favorites.put(type, instance);
        }

        public <T> T getFavorite(Class<T> type) {
            return type.cast(favorites.get(type));
        }
    }

    // 사용 예시
    Favorites favorites = new Favorites();
    favorites.putFavorite(String.class, "Hello, world!");  // String 타입 저장
    favorites.putFavorite(Integer.class, 123);             // Integer 타입 저장

    String favoriteString = favorites.getFavorite(String.class); // 안전하게 String 타입으로 꺼냄
    Integer favoriteInteger = favorites.getFavorite(Integer.class); // 안전하게 Integer 타입으로 꺼냄
    ```

    * `Favorites` 클래스는 여러 타입의 값을 담을 수 있지만, `Class<T>` 타입 키를 통해 **각 값의 타입을 식별**하고 **타입에 따라 안전하게 조회**할 수 있다.
    * `putFavorite`과 `getFavorite` 메서드는 제네릭 키를 통해 값의 타입을 보장합니다. 이 방식으로 하나의 컨테이너에 여러 타입의 객체를 저장할 수 있다.

**비교**:

* 일반 제네릭 컨테이너는 하나의 타입만을 매개변수화하여 **동일 타입 원소들만 담는 용도**로 사용한다.
* 타입 안전 이종 컨테이너는 키(`Class<T>`)를 통해 서로 다른 타입의 객체를 담고 관리할 수 있도록 **하나의 컨테이너로 여러 타입을 안전하게 관리**한다.

> 결론적으로, **제네릭 컨테이너는 컨테이너 자체의 타입을 매개변수화하고, 타입 안전 이종 컨테이너는 키를 매개변수화하여 다양한 타입을 안전하게 관리할 수 있도록 한다.**

## 2. 타입 안전 이중 컨테이너의 한계

### 1) 악의적인 클라이언트가 Class 객체를 제네릭이 아닌 로 타입으로 넘기면 Favorites 인스턴스의 타입 안전성이 쉽게 깨진다.

아래 코드에서는 `Class` 로 타입으로 다시 캐스팅하여 전달했으니, 컴파일 타임에서는 문제 없이 `Map` 에 저장이 된다. 이후 꺼내올때 `String` 객체를 `Integer` 로 캐스팅 하려 하니 런타임에서 예외가 발생한다.

```java
f.putFavorite((Class)Integer.class, "문자열");
int result = f.getFavorite(Integer.class);  // ClassCastException
```

* 위와 같이 악의적으로 `(Class)`와 같은 캐스팅을 통해 로타입을 사용하는 것에 대한 취약점이 있다.
* 그냥 실행하면, 런타임에 `ClassCastException`을 만나게 될 것이다.

첨고로 실제 개발할때는 컴파일 과정에서 비검사 경고가 발생하기 때문에, 지켜만 진다면 런타임에 타입 안전성이 보장될 것이다.\


<figure><img src="https://velog.velcdn.com/images/semi-cloud/post/51dedfcc-281b-4fe5-9ad1-7a77d63a2e72/image.png" alt=""><figcaption></figcaption></figure>

이처럼 인스턴스가 타입 불변식을 어기는 일이 없도록 보장하려면, 다음과 같이 **동적 형변환(cast)**&#xC744; 통해 인수로 주어진 `instance` 의 타입이 `type` 으로 명시한 타입과 같은지 확인하면 된다.

```java
 public <T> void putFavorite(Class<T> type, T instance) {
      favorites.put(Objects.requireNonNull(type), type.cast(instance));
 }
```

**활용**

`java.util.Collections` 에 `checkedSet()`, `checkedList()`, `checkedMap()` 메서드들도 해당 방식을 적용한 컬렉션 래퍼들이다. 해당 클래스들은 모두 `CheckedCollection` 을 상속 받았고, `typeCheck` 메서드를 통해 추가 연산시에 타입을 체크하여 안전성을 보장한다.

![](https://velog.velcdn.com/images/semi-cloud/post/34c762d6-b715-4936-a5dc-d5410f230182/image.png)

![](https://velog.velcdn.com/images/semi-cloud/post/60ce2e10-2b32-4aec-8f4c-ee8cb18bbff0/image.png)

![](https://velog.velcdn.com/images/semi-cloud/post/e7030655-4193-4469-8069-ba04238ce22f/image.png)

> 타입 검증을 통해 타입 안전성을 지키는 방식은 제네릭 타입과 동적 형변환(`cast`)을 함께 사용하여 **컬렉션이 허용된 타입 외의 값으로 오염되지 않도록** 방지할 수 있다. `Collections.checkedList()` 등도 같은 원리로 동작하여, 다양한 타입을 다루는 Java 프로그램에서 예기치 못한 타입 오류를 막아주는 중요한 기능을 한다.

### 2) 실체화 불가 타입에는 사용 할 수 없다는 것이다.

> 다시 말해, 그러니까, `String`이나 `String[]`은 저장할 수 있지만, `List<String>`은 저장할 수 없다. <mark style="color:red;">List을 저장하려는 코드는 컴파일되지 않을 것이다.</mark>

{% hint style="info" %}
List\<E>는 실체화 불가 타입이라는 말의 뜻
{% endhint %}

자바에서 `제네릭 타입의 인스턴스화(instantiation)`에 관한 것이다. 이것은 제네릭 타입이 **런타임 시에 실제 타입 정보를 유지하지 않는다는 것을 의미**한다.

제네릭은 컴파일 시에만 유효하고 런타임에는 소거(erasure)된다. 따라서 컴파일러는 제네릭 타입을 사용하여 코드를 검사하지만, **런타임에는 제네릭 타입의 실제 타입 정보를 제거하여 모든 제네릭 타입을 원시 타입(raw type)으로 변환한다.** 이는 호환성 및 역호환성을 유지하기 위한 것이다.

> 그 결과로 제네릭 타입에 대한 실체화된 타입 정보는 런타임에 사용할 수 없다. 따라서 List\<String>과 같은 제네릭 타입은 런타임에 List로만 인식된다. 이것이 "List\<String>이 실체화 불가 타입"이라는 말의 의미다.

실체화 불가 타입은 `리플렉션(reflection)`을 사용하여 제네릭 타입의 실제 타입 정보를 동적으로 추출하는 것이 불가능하다는 것을 의미한다. 따라서 런타임에서는 제네릭 타입의 파라미터화된 타입 정보를 사용하는 것은 어려워진다. 하지만 이것이 컴파일 시에는 제네릭 타입의 안전성을 보장하는 이유 중 하나이다.

{% hint style="info" %}
스프링에서는 `ParameterizedTypeReference`라는 클래스
{% endhint %}

**우회하기 위한 방법으로는 슈퍼 타입 토큰을 사용할 수 있다.** 슈퍼 타입을 토큰으로 사용한다는 뜻이다.스프링에서는 `ParameterizedTypeReference`라는 클래스로 미리 구현해놓았다.

```java
Favirotes f = new Favorites();

List<String> pets = Arrays.asList("강아지", "고양이");
f.putFavorite(new TypeRef<List<String>>(){}, pets);
List<String> list = f.getFavorite(new TypeRef<List<String>>(){});
```

위처럼 이용하여 해결한다.



> 출처 : [https://soft-dino.tistory.com/62](https://soft-dino.tistory.com/62)

스프링의 RestTemplate을 사용하여 HTTP 요청을 보내고, 응답을 받을 때 제네릭 타입을 유지하고 싶은 경우에 사용할 수 있다.

```java
import org.springframework.core.ParameterizedTypeReference;
import org.springframework.http.ResponseEntity;
import org.springframework.web.client.RestTemplate;

public class ParameterizedTypeReferenceExample {
    public static void main(String[] args) {
        RestTemplate restTemplate = new RestTemplate();

        ResponseEntity<String> response = restTemplate.getForEntity("https://api.example.com/data", String.class);
        String body = response.getBody();
        System.out.println("Response body: " + body);

        // 제네릭 타입을 유지하기 위해 ParameterizedTypeReference 사용
        ResponseEntity<List<MyObject>> listResponse = restTemplate.exchange(
            "https://api.example.com/data",
            HttpMethod.GET,
            null,
            new ParameterizedTypeReference<List<MyObject>>() {}
        );
        List<MyObject> dataList = listResponse.getBody();
        System.out.println("Response body with ParameterizedTypeReference: " + dataList);
    }
}
```

위의 예시에서는 RestTemplate을 사용하여 GET 요청을 보내고 응답을 받는다. 첫 번째 요청은 단순한 문자열을 받아온다.

하지만 두 번째 요청은 List\<MyObject>와 같은 제네릭 타입을 받아온다. 이 때 ParameterizedTypeReference를 사용하여 제네릭 타입 정보를 유지한다.

&#x20;

주의사항

{% hint style="danger" %}
ParameterizedTypeReference를 사용할 때 주의해야 할 점은 익명 클래스를 생성한다는 점이다.
{% endhint %}

이는 익명 클래스를 생성하는 것이므로 해당 클래스의 인스턴스는 하나만 사용할 수 있다. **따라서 동일한 제네릭 타입에 대해 여러 번 사용하려면 매번 새로운 ParameterizedTypeReference 인스턴스를 생성해야 한다.**

## 3. 한정적 타입 토큰을 이용한 타입 제한

> **타입 토근이란?**
>
> **타입 토큰**은 제네릭 타입 정보를 런타임에 안전하게 활용하기 위해 사용하는 객체이다. 주로 `Class<T>`를 타입 토큰으로 사용하여, 특정 타입의 정보를 담은 `Class` 객체를 키로 활용한다. 이를 통해 컴파일 시점에서 특정 타입을 지정하고, 런타임에 해당 타입의 인스턴스를 안전하게 다룰 수 있게 된다. 예를 들어 `Map<Class<?>, Object>` 형태로 여러 타입을 안전하게 담는 컨테이너를 구현할 때 유용하다.

```java
import java.util.HashMap;
import java.util.Map;
import java.util.Objects;

public class Favorites {
    // Class<?> 타입을 키로 하고 Object 타입을 값으로 가지는 맵
    private Map<Class<?>, Object> favorites = new HashMap<>();

    // 타입 토큰을 사용해 객체 저장 (T 타입 인스턴스)
    public <T> void putFavorite(Class<T> type, T instance) {
        favorites.put(Objects.requireNonNull(type), instance);
    }

    // 타입 토큰을 사용해 객체 반환, 저장된 타입과 매핑하여 반환
    public <T> T getFavorite(Class<T> type) {
        return type.cast(favorites.get(type));
    }

    public static void main(String[] args) {
        Favorites f = new Favorites();

        // 각 타입별로 Class를 타입 토큰으로 사용해 저장
        f.putFavorite(String.class, "Java");
        f.putFavorite(Integer.class, 42);
        f.putFavorite(Class.class, Favorites.class);

        // 타입 토큰으로 안전하게 객체를 꺼냄
        String favoriteString = f.getFavorite(String.class);
        int favoriteInteger = f.getFavorite(Integer.class);
        Class<?> favoriteClass = f.getFavorite(Class.class);

        // 출력
        System.out.printf("%s %d %s%n", favoriteString, favoriteInteger, favoriteClass.getName());
    }
}

```

> 🔖 **한정적 타입 토큰이란?**\
> 한정적 타입 매개변수( `E extends Delayed` )나 한정적 와일드카드( `? extends Delayed` )을 사용하여 표현 가능한 타입을 제한하는 토큰

Favorites가 어떤 Class 객체든 받아들이므로 `비한정적 타입 토큰`이라 할 수 있다. **이 메서드들이 허용하는 타입을 제한**하고 싶다면, 한정적 타입 토큰을 활용하자.

<pre class="language-java"><code class="lang-java">// 대상 요소에 달려있는 애너테이션을 런타임에 읽어 오는 기능
public &#x3C;T extends Annotation> T getAnnotation(Class&#x3C;T> <a data-footnote-ref href="#user-content-fn-1">annotationType</a>);
</code></pre>

이 메서드는 토큰으로 명시한 타입의 애너테이션이 대상 요소에 달려 있다면 그 애너테이션을 반환하고, 없다면 Null을 반환한다. 이를 통해 요소가 타입 안전 이종 컨테이너처럼 동작하게 한다.

## 4. 한정적 타입 토큰을 받는 메서드에 Class\<?> 타입의 객체를 넘기는 법

### 1) 애너테이션 API에서 한정적 타입 토큰의 사용

애너테이션 API의 `AnnotatedElement` 인터페이스에서 런타임에 특정 애너테이션을 가져오는 메서드의 예

```java
public <T extends Annotation> T getAnnotation(Class<T> annotationType);
```

* 여기서 `annotationType`은 **한정적 타입 토큰**으로 `Annotation`을 상속받는 타입만 받을 수 있도록 제한한다.
* 이 메서드는 명시된 타입의 애너테이션이 요소에 달려 있으면 해당 애너테이션을 반환하고, 없으면 `null`을 반환한다. 이를 통해 요소가 타입 안전 이종 컨테이너처럼 동작하게 한다.

***

### 2) 한정적 타입 토큰을 사용한 안전한 형변환

일반적으로 `Class<?>` 타입 객체를 `Class<? extends Annotation>`로 형변환하면 비검사 경고가 발생한다. 하지만 `Class` 클래스의 `asSubclass` 메서드를 사용하면 **동적 타입 검증**을 통해 이러한 형변환을 안전하게 수행할 수 있다.

```java
static Annotation getAnnotation(AnnotatedElement element, String annotationTypeName) {
    Class<?> annotationType = null; // 비한정적 타입 토큰

    try {
        annotationType = Class.forName(annotationTypeName);
    } catch (Exception ex) {
        throw new IllegalArgumentException(ex);
    }

    // `asSubclass`를 통해 안전하게 형변환하여 한정적 타입 토큰 적용
    return element.getAnnotation(annotationType.asSubclass(Annotation.class));
}
```

**`asSubclass` 메서드의 역할**

* `asSubclass` 메서드는 호출된 `Class<?>` 객체를 안전하게 지정된 `Class`의 서브클래스로 형변환한다.
* 이때 **형변환에 성공하면 인수로 받은 클래스 객체를 반환**하고, 실패 시 `ClassCastException`을 던진다.



## **✨ 최종** 정리

> 컨테이너 자체가 아닌 키를 타입 매개변수로 바꾸면 타입 안전 이종 컨테이너를 만들 수 있다.

컬렉션 API로 대표되는 일반적인 제네릭 형태에서는 한 컨테이너가 다룰 수 있는 타입 매개변수의 수가 고정되어 있다. 하지만 컨테이너 자체가 아닌 키를 타입 매개변수로 바꾸면 이런 제약이 없는 `타입 안전 이종 컨테이너`를 만들 수 있다.&#x20;

* `Class`를 키로 쓰며, 이렇게 쓰이는 `Class` 객체를 타입 토큰이라 한다.
  * 직접 구현한 키 타입도 사용 가능하다.
* 데이터베이스의 행을 표현한 `DatabaseRow` 타입에는 제네릭 타입인 `Column<T>`를 키로 사용할 수 있다.



> 출처 및 참고
>
> * [https://jake-seo-dev.tistory.com/53](https://jake-seo-dev.tistory.com/53)
> * [https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-33-%ED%83%80%EC%9E%85-%EC%95%88%EC%A0%84-%EC%9D%B4%EC%A2%85-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EB%A5%BC-%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC](https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-33-%ED%83%80%EC%9E%85-%EC%95%88%EC%A0%84-%EC%9D%B4%EC%A2%85-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EB%A5%BC-%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC)
> * [https://soft-dino.tistory.com/62](https://soft-dino.tistory.com/62)

[^1]: `annotationType` 인수는 애너테이션 타입을 뜻하는 한정적 타입 토큰이다.
