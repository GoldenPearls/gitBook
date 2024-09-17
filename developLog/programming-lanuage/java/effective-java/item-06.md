# item 06 : 불필요한 객체 생성을 피하라

똑같은 기능의 객체를 매번 생성하기보다는 객체 하나를 `재사용`하는 편이 나을 때가 많다. 자바에서는 `가비지 컬렉션`이라는 것을 통해 더 이상 필요 없는 객체를 정리하지만, 너무 많은 객체가 생성 시 성능 저하가 될 수 있음

> 특히 불편 객체는 언제든 재사용할 수 있다.

## new을 이용한 객체 생성

### 하지 말아야 하는 극단적인 예

```java
String s = new String ("bikini"); // 따라 하지 말 것!
```

이 문장이 반복문이나 빈번히 호출되는 메서드 안 에 있다면 쓸데없는 String 인스턴스가 수백만 개 만들어질 수도 있다.



객체를 생성하는 과정은 메모리 할당, 초기화, 그리고 생성자 호출 등의 작업을 수반... 이러한 작업들이 단일 객체에 대해서는 큰 문제가 되지 않을 수 있지만.. 동일한 객체를 반복적으로 생성하거나 짧은 시간에 많은 객체 생성 시 성능 정하와 메모리 낭비로 이어짐.. 🤨

> 그렇다면.. 불필요한 객체 생성을 피하기 위해서는? 🤔

## 불필요한 객체 생성 피하기 방법

### 1. 객체의 재사용 예

```java
String s = "bikini"
```

새로운 인스턴스를 매번 만드는 대신 하나의 String 인스턴스를 사용한다. 같은 가상 머신 안에서 이와 똑같은 문자열 리터럴을 사용하는 모든 코드가 같은 객체를 `재사용함`이 보장된다.

생성자 대신 **정적 팩터리 메서드**를 제공하는 불편 클래스에서는 정적 팩터리 메서드를 사용해 불필요한 객체 생성을 피할 수 있음

**✔️ Boolean (String) 생성자** 대신 **Boolean. valueOf (String)** 팩터리 메서드를 사용 하면 호출될 때마다 새로운 객체가 생성되는 것을 방지할 수 있다.

> 그렇다면 자주 사용되는 객체, 매번 새로 만들어야 할까? 🤔

### 2. 객체의 반복 시 캐싱 사용

불변객체뿐 아니라 가변객체도 사용 중에 변경되지 않는다는 것을 알 수 있다면 재사용이 가능하다. 특히 생성 비용이 비싼 객체의 생성이 반복해서 필요할 경우 캐싱하여 사용 권장한다.&#x20;

```java
// before : 매번 패턴을 컴파일
Pattern pattern = Pattern.compile("regex");

// After : 패턴 객체를 캐싱하여 재사용
private static final Pattern PATTERN = Pattern.compile("regex");

Matcher matcher = PATTERN.matcher(input);
```

이렇게 하면 매번 객체 생성 시 비용을 줄이고, 성능을 크게 개선 가능하다.  반면 isRomanNumeral 방식의 클래스가 초기화된 후 이 메서드를 한 번도 호출하지 않으면 ROMAN필드는 쓸데없이 초기화된 것이다.

> 이 경우 isRomanNumeral 메서드가 처음 호출될 때 필드를 초기화하는 지연 초기화로 불필요한 초기화를 없앨 순 있지만 성능개선에 비해 코드복잡도가 올라가서 추천하지 않은 방식이다.

### 3. 불필요한 객체 생성(keySet)

객체가 불변이면 재사용하는 것이 항상 안전하지만, 불변이 명확하지 않은 경우도 있다.&#x20;

Map 인터페이스의 **keySet 메서드를 보면,** Map인터페이스의 KeySet메서드는 Map 객체 안의 키를 Set뷰로 반환한다. 반환된 Set을 통해 원본 Map의 키를 수정하거나 제거하면, 이 변경 사항이 Map에 그대로 반영되어 참조하는 모든 뷰에 영향을 미치기에 반환한 객체 중 하나를 수정하면 다른 모든 객체가 따라서 바뀌게 된다.



따라서 'keySet' 메서드를 여러 번 호출하여 여러 개의 `Set` 뷰를 생성해도 기능상 문제는 없지만, 실질적으로 볼 수 있는 효과가 없다. 모든 `Set`뷰는 <mark style="color:red;">원본 Map을 참조하기에 여러뷰를 생성하는 것보다 단일 뷰를 재사용하는 것이 효율적</mark>이다.

#### Map의 `keySet` 메서드에서 반환된 Set의 특징

1. **동기화**: 반환된 Set은 원본 Map의 뷰이다. 따라서 Set에서 키를 추가, 수정, 제거하면 원본 Map에도 동일한 변경 사항이 반영된다.
2. **가변성**: 반환된 Set은 가변적이다. 즉, Set에 키를 추가하거나 제거할 수 있지만, Set에 추가된 키는 원본 Map에 존재해야 한다.
3. **자동 업데이트**: 원본 Map의 변경 사항이 Set에 실시간으로 반영되므로, 원본 Map에서 키를 변경하면 Set 또한 그에 따라 업데이트된다.
4. **유일성**: Set의 특성상 중복된 키는 허용되지 않으며, 원본 Map의 모든 키는 고유
5. **효율성**: 여러 번 호출하여 다양한 Set 뷰를 생성할 수 있지만, 각 뷰는 동일한 Map을 참조하므로 단일 뷰를 재사용하는 것이 더 효율적이다.

### 4. 오토박싱

> Long에 long을 n번 더하면 불필요한 Long 인스턴스가 n개 만들어짐

불필요한 객체를 만들어내는 또 다른 방식으로는 `오토박싱`이 있다. 자바에서 기본 타입과 박싱 된 기본타입을 섞어 쓸 때 자동으로 상호변환해 주는 기능이다. 다만 `오토박싱`은 <mark style="color:blue;">기본 타입과 박싱 된 타입의 구분을 흐리게 해 주지만 아얘 구분을 없애는 것은 아니다.</mark>

다음 예제는 모든 양의 정수의 총합을 구하는 메서드로 int는 충분히 크지 않아 long을 사용 중이다. 기능 상으로는 동일하지만 성능에서는 오토박싱을 사용함으로써 성능에서 큰 차이를 보이고 있다.

#### 1) 오토박싱 사용  <a href="#section-4" id="section-4"></a>

* 오토박싱이 발생하는 경우, `Long` 객체와 기본 타입 `long` 간의 변환이 자동으로 이루어진다.

```cpp
private static long sumLong() {
    Long sum = 0L; // Long 객체로 선언
    for (long i = 0; i <= Integer.MAX_VALUE; i++) {
        sum += i; // 오토박싱 발생: long -> Long
    }
    return sum; // Long -> long으로 변환
}
```

#### 2) 기본 타입사용 <a href="#section-5" id="section-5"></a>

* 기본 타입을 사용하는 경우, 성능이 더 좋고 오토박싱이 발생하지 않습니다.

```cpp
private static long sumLongPrimitive() {
    long sum = 0; // 기본 타입 long으로 선언
    for (long i = 0; i <= Integer.MAX_VALUE; i++) {
        sum += i; // 기본 타입 간의 연산
    }
    return sum; // 기본 타입 long 반환
}
```

오토박싱을 사용한 예제를 보면 sum 변수를 Long으로 선언하여 불필요한 Long인스턴스가 2^31개나 생성된다. 실제로 두 메서드의 실행 시간을 비교해 보면 아주 큰 차이가 남을 확인할 수 있다.

그래서 **박싱 된 기본 타입보다는 기본 타입을 사용하고**, 의도치 않은 오토박싱이 숨어들지 않도록 주의해야 한다.

> **✔️** 요약!&#x20;
>
> <mark style="color:red;">**무조건 적인 재사용이 효율적이지 않을 때도 있다.**</mark>** 방어적 복사가 필요한 상황**에서 객체를 재사용했을 때의 피해가, 필요 없는 객체를 반복 생성했을 때의 피해보다 훨씬 크다는 사실을 기억하자. `방어적 복사`에 **실패하면 언제 터져 나올지 모르는 버그와 보안 구멍으로 이어 지지만**, `불필요한 객체 생성`은 **그저 코드 형태와 성능에만 영향을 준다.**

**보안 설명**

**방어적 복사가 필요한 경우**

```java
public class Person {
    private Date birthDate;

    public Person(Date birthDate) {
        // 방어적 복사를 수행
        this.birthDate = new Date(birthDate.getTime());
    }

    public Date getBirthDate() {
        // 반환 시 방어적 복사를 수행
        return new Date(birthDate.getTime());
    }
}
```

위의 코드에서 `Person` 클래스는 `birthDate`라는 `Date` 객체를 인자로 받는다. 만약 방어적 복사를 하지 않고 `this.birthDate = birthDate;`로 할당했다면, 외부에서 전달된 `Date` 객체를 그대로 사용하게 된다. 이렇게 되면 외부에서 해당 `Date` 객체를 변경하면 `Person` 객체 내부의 `birthDate`도 함께 변경되는 문제가 발생할 수 있다.

**방어적 복사를 하지 않은 경우의 문제점**

```java
public class Person {
    private Date birthDate;

    public Person(Date birthDate) {
        this.birthDate = birthDate;  // 방어적 복사 없음
    }

    public Date getBirthDate() {
        return birthDate;  // 방어적 복사 없음
    }
}

public static void main(String[] args) {
    Date date = new Date();
    Person person = new Person(date);

    date.setTime(0);  // 외부에서 date를 변경하면 Person 객체의 birthDate도 변경됨
    System.out.println(person.getBirthDate());  // 예상치 못한 결과 출력
}
```

이 코드에서는 외부에서 `birthDate`를 변경할 수 있는 위험이 존재한다. 만약 외부에서 `date` 객체의 값을 변경하면, `Person` 객체의 `birthDate`도 영향을 받게 된다.



## 요약&#x20;

* 불변이 보장된 객체의 경우 재사용을 항상 고려
* 무거운 객체의 반복 생성 시 캐싱을 사용
* 불필요한 객체 생성을 주의
* 의도치 않은 오토박싱이 숨어들지 않도록 주의
* 데이터베이스 연결 같이 생성 비용이 아주 큰 경우를 제외하고는 객체 풀을 사용하여 객체를 재사용하는 것보다 새로 생성하는 것이 효율적



결국... 반복된 것은 안좋다는 것인가..



> 참고 글
>
> * [https://comdolidol-i.tistory.com/430](https://comdolidol-i.tistory.com/430)
> * [https://junhkang.tistory.com/77](https://junhkang.tistory.com/77)
