# item 36 : 비트 필드 대신 EnumSet을 사용하라

## 1. 비트 필드 열거 상수

> 열거한 값들이 주로 집합으로 사용될 경우, 예전에는 상수에 서로 다 른2의 거듭제곱 값을 할당한 정수 열거패턴을 사용해 왔다.

```java
public class StyleWithBitField {
    public static final int STYLE_BOLD = 1 << 0; // 1
    public static final int STYLE_ITALIC = 1 << 1; // 2
    public static final int STYLE_UNDERLINE = 1 << 2; // 4
    public static final int STYLE_STRIKETHROUGH = 1 << 3; // 8

    public void applyStyles(int styles) {
//        styleWithBitField.applyStyles(STYLE_BOLD | STYLE_UNDERLINE);
    }
}
```

### 1) 비트 필드란?

* 위와 같이 `비트별 OR`를 사용해 **여러 상수를하나의 집합으로 모을 수 있으며, 이 집합**을 비트 필드라한다.
* 비트 필드를 사용하면 비트별 연산을 사용해 합집합과 교집합 같은 **집합 연산을 효율적으로 수행할 수 있다.**

```java
text.applyStyles（STYLE_BOLD | STYLE_ITALIC）;
```

### 2) 비트 필드의 단점

> 하지만, 비트 필드 또한 **정수 열거 상수**이므로<mark style="color:red;">, 정수 열거 상수의 단점을 그대로 지니며, 추가로 다른 문제도 가진다.</mark>

```javascript
public class FilePermissions {
    // 비트 필드로 권한을 정의
    public static final int READ = 1 << 0;    // 0001
    public static final int WRITE = 1 << 1;   // 0010
    public static final int EXECUTE = 1 << 2; // 0100

    public static void main(String[] args) {
        int filePermissions = READ | EXECUTE; // READ와 EXECUTE 권한만 부여

        // 단점 1: 해석하기 어렵다
        System.out.println("Permissions: " + filePermissions);
        // 출력 결과는 5이지만, 5가 어떤 권한을 의미하는지 바로 해석하기 어렵다.

        // 단점 2: 모든 비트를 순회하기 어렵다
        if ((filePermissions & READ) != 0) {
            System.out.println("Read permission granted.");
        }
        if ((filePermissions & WRITE) != 0) {
            System.out.println("Write permission granted.");
        }
        if ((filePermissions & EXECUTE) != 0) {
            System.out.println("Execute permission granted.");
        }

        // 단점 3: 필요한 비트 수를 예측해야 한다
        // 현재 READ, WRITE, EXECUTE만 사용하지만, 새로운 권한이 추가될 경우 기존 타입을 확장해야 할 수 있다.
    }
}

```



1. 비트 필드 값이 그대로 출력되면, 단순한 정수 열거 상수를 **출력할 때보다 해석하기 더 어렵다**

*   `System.out.println("Permissions: " + filePermissions);`의 결과로 숫자 `5`가 출력된다. `5`는 `READ`와 `EXECUTE` 권한이 설정된 상태를 의미하지만, 숫자만 보고 어떤 권한인지 해석하기 어렵다.



2. 비트 필드 하나에 녹아 있는 모든 원소를 순회하기 어렵다.

* 특정 권한이 활성화되었는지 확인하기 위해 각 권한에 대해 개별적으로 검사해야 한다. 권한이 추가될 때마다 검사하는 코드가 증가할 수 있다.

3. 마지막으로, 최대 몇 비트가 필요한지를 API 작성 시 미리 예측하여 적절한 타입（보통은 int나 long）을 선택해야 한다.

* 처음에는 `READ`, `WRITE`, `EXECUTE`의 3가지 권한만 사용한다고 가정하고 코드를 작성했지만, 이후 새로운 권한이 추가되면 기존 비트 필드 타입으로는 처리하기 어렵다.
* 예를 들어 long 이더라도 64비트만 지원하기 때문에 경우의 수가 65가지 이상이면 감당 불가인 것이다.

{% hint style="info" %}
그럼 모든 비트를 순회하기 어렵다는 건 for문으로도 못 돌린다는 건가?
{% endhint %}

> 자바에서 비트 필드에 `for` 문을 적용하여 각 비트를 순회할 수 있다. 그러나 비트 필드는 개별적인 비트에 대한 상수 값으로 정의되므로, 일반 배열이나 리스트처럼 반복문을 쉽게 적용하기 어렵다.&#x20;

대신, 모든 권한 상수를 배열에 담아 `for`문을 사용해 확인할 수는 있다.

#### 예제 코드: `for` 문을 사용하여 모든 비트를 순회하는 방법

```java
public class FilePermissions {
    // 비트 필드로 권한을 정의
    public static final int READ = 1 << 0;    // 0001
    public static final int WRITE = 1 << 1;   // 0010
    public static final int EXECUTE = 1 << 2; // 0100

    // 각 권한과 이름을 저장하는 배열
    private static final int[] PERMISSIONS = { READ, WRITE, EXECUTE };
    private static final String[] PERMISSION_NAMES = { "Read", "Write", "Execute" };

    public static void main(String[] args) {
        int filePermissions = READ | EXECUTE; // READ와 EXECUTE 권한만 부여

        // 모든 권한을 for문으로 확인
        System.out.println("Permissions:");
        for (int i = 0; i < PERMISSIONS.length; i++) {
            if ((filePermissions & PERMISSIONS[i]) != 0) {
                System.out.println(PERMISSION_NAMES[i] + " permission granted.");
            }
        }
    }
}
```

1. **`PERMISSIONS` 배열**: 각 권한 상수를 배열에 저장하여 반복문으로 접근할 수 있도록 한다.
2. **`for` 문을 사용한 권한 확인**: `for` 문을 통해 `PERMISSIONS` 배열을 순회하면서, 각 비트가 활성화되었는지 확인한다. `filePermissions`에 해당 비트가 설정되어 있으면 권한이 있는 것으로 간주한다.

#### 결과

`filePermissions`이 `READ | EXECUTE`로 설정되어 있으므로, 출력 결과는 다음과 같다.

```mathematica
mathematica코드 복사Permissions:
Read permission granted.
Execute permission granted.
```

#### 장점과 단점

* **장점**: 비트 필드를 `for` 문으로 순회하며 간단하게 확인할 수 있다.
* **단점**: 권한을 추가할 때마다 `PERMISSIONS` 배열과 `PERMISSION_NAMES` 배열을 업데이트해야 한다.

## **2. EnumSet** <a href="#span-stylecolord68365---enumsetspan" id="span-stylecolord68365---enumsetspan"></a>

> **비트 필드 방식의 단점**를 해결하기 위한 방안이 바로 `EnumSet`이다.

### 1) `EnumSet`의 장점과 예제 코드

1. **간결하고 명확한 코드**: `EnumSet`은 열거형 상수를 집합으로 관리할 때 비트 필드보다 코드가 간결하고 이해하기 쉬운 장점이 있다.
2. **안전성**: 열거형을 사용하므로, 타입 안전성과 컴파일타임 검사가 가능해져 실수를 방지할 수 있다.
3. **성능**: `EnumSet`은 내부적으로 비트 벡터를 사용해 비트 필드와 유사한 성능을 제공하며, 추가로 열거형의 모든 장점을 제공한한다.

위의 정수 열거 패턴의 예시를 EnumSet을 사용한 것으로 바꾸어보면 다음과 같다.

<pre class="language-java"><code class="lang-java"><strong>import java.util.EnumSet;
</strong>import java.util.Set;

public class Text {
    // 열거형 정의
    public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }

    // 스타일을 적용하는 메서드
    // 어떤 Set을 넘겨도 되나, EnumSet이 가장 좋다.
    public void applyStyles(Set&#x3C;Style> styles) {
        // 스타일 적용 로직
        for (Style style : styles) {
            System.out.println("Applying style: " + style);
        }
    }

    public static void main(String[] args) {
        Text text = new Text();
        // 클라이언트 코드에서 EnumSet.of를 사용하여 여러 스타일을 동시에 설정
        text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
    }
}
</code></pre>

1. **열거형 `Style`**: `Style` 열거형은 텍스트에 적용할 스타일(BOLD, ITALIC 등)을 정의
2. **메서드 `applyStyles`**: `applyStyles` 메서드는 `Set<Style>`을 매개변수로 받아, 여러 스타일을 동시에 적용
3. **클라이언트 코드**: `EnumSet.of(Style.BOLD, Style.ITALIC)`를 통해 `EnumSet` 인스턴스를 생성하여, `applyStyles` 메서드에 전달한다.

{% hint style="danger" %}
**왜 `EnumSet`을 사용하고 `Set<Style>`로 받는가?**
{% endhint %}

`applyStyles` 메서드는 `EnumSet<Style>`이 아닌 `Set<Style>` 타입으로 매개변수를 받는다. 그 이유는 다음과 같다:

1. **유연성**: `Set<Style>` 인터페이스로 받으면 `EnumSet`뿐만 아니라 다른 `Set` 구현체도 사용할 수 있다. 예를 들어 특수한 요구사항이 있는 클라이언트가 `HashSet`을 전달할 수 있다.
2. **일반적인 습관**: 구체적인 구현체(`EnumSet` 등)보다 인터페이스(`Set`)로 받는 것이 좋은 습관이다. 이는 코드의 재사용성과 유연성을 높이고, 나중에 다른 `Set` 구현체로 변경할 가능성을 열어둔다.
3. _**상위 개념인 인터페이스 형태로 받는게 더 안전하기 때문이다.**_

이처럼 `EnumSet`을 사용하면 비트 필드의 장점과 더불어 열거형 타입의 안전성과 코드의 간결함을 모두 누릴 수 있다. `EnumSet`은 비트 필드와 유사한 성능을 제공하며, 각 열거형 상수를 명확히 표현할 수 있어 유지보수성이 높은 코드를 작성할 수 있다.

**비트 필드 대신 열거형 집합을 사용하여 스타일을 적용하는 방법을 보여주며**, 간결하고 가독성이 높은 장점을 제공한다.



이렇게 보면 어째서 정수 열거 패턴을 과거에 사용해왔는지 모를 정도일 수 있다..

다만 `Enumerate`라는 자료형 자체가 프로그래밍 언어의 시작과 함께 해온 것이 아니라는걸 감안해보면 충분히 그럴만 하다고 하다고 함

{% hint style="info" %}
애초에 비트 연산 자체가 약간 전통적인 C 스타일인것 같기도 하다. = **비트 필드가 전통적인 방식**
{% endhint %}

* 열거형(`enum`)은 비교적 최근에 등장한 개념이다. 초기 프로그래밍 언어는 열거형을 지원하지 않아, 정수형 상수로 모든 상태를 관리했다.
* 비트 연산은 특히 C 언어와 같은 저수준 프로그래밍에서 많이 사용되었기 때문에, 과거에는 비트 필드를 사용하는 방식이 자연스러웠다.

추가적으로, applyStyles에서 파라미터가 Set

어차피 클라이언트 코드에서 EnumSet으로 넘길텐데 EnumSet





## 요약

* `EnumSet`은 비트 필드보다 안전하고 가독성이 좋으며, 성능도 비트 필드에 뒤지지 않는다.
* `applyStyles` 메서드는 `Set<Style>` 인터페이스로 매개변수를 받아, `EnumSet` 외에 다른 `Set` 구현체도 지원할 수 있다.
* `EnumSet`을 사용함으로써 열거형의 장점을 최대한 활용하면서도 비트 필드의 단점을 피할 수 있다.
* 비트 필드를 사용할 필요가 있는 경우라면, 대신 `EnumSet`을 사용하세요. `EnumSet`은 비트 필드와 유사한 성능과 명확성을 제공하고, 열거형이 가지는 안전성과 가독성을 함께 제공한다.
* EnumSet의 유일한 단점이라면 （자바 9까지는 아직） 불변 `EnumSet`을 만들 수 없다는 것이다.

**++ 찾아봤는데 자바 23까지도 제공 x**

따라서 `EnumSet`을 불변으로 만들려면 `Collections.unmodifiableSet()`으로 감싸서 읽기 전용으로 설정할 수 있다. 이렇게 하면 `EnumSet`을 수정하려고 할 때`UnsupportedOperationException` 예외가 발생한다.

```java
import java.util.Collections;
import java.util.EnumSet;
import java.util.Set;

public class Example {
    public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }

    public static void main(String[] args) {
        Set<Style> modifiableSet = EnumSet.of(Style.BOLD, Style.ITALIC);
        Set<Style> immutableSet = Collections.unmodifiableSet(modifiableSet);

        // 불변으로 설정된 집합을 수정하려고 하면 UnsupportedOperationException 발생
        immutableSet.add(Style.UNDERLINE); // 예외 발생
    }
}
```

#### 주의 사항

이 예제에서는 `modifiableSet`을 `Collections.unmodifiableSet()`으로 감싸 `immutableSet`을 만들었다. 하지만 이 경우, `modifiableSet` 자체가 변경되면 `immutableSet`에도 반영된다. 따라서 완전한 불변성을 유지하려면 `modifiableSet`도 수정하지 않도록 주의해야 다.



> 참고
>
> * [https://j-d-i.tistory.com/330](https://j-d-i.tistory.com/330)
