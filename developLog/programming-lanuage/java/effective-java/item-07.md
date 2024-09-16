# item 07 : 다 쓴 객체 참조를 해제하라

> 자바는 자칫 메모리 관리에 더 이상 신경 쓰지 않아도 된다고 오해할 수 있는데, 절대 사실이 아니다.

## 메모리 누수

### 문제가 되는 코드

```java
import java.util.Arrays;
import java.util.EmptyStackException;

public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e; // size를 증가시키기 전에 요소를 추가
    }

    public Object pop() {
        if (size == 0) throw new EmptyStackException();
        return elements[--size]; // size를 감소시키고 요소를 반환
    }

    // 원소를 위한 공간을 적어도 하나 이상 확보한다.
    // 배열 크기를 늘려야 할 때마다 대략 두 배씩 늘린다.
    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}

```

> 메모리 누수가 발생하면 어떻게 될까?



#### 메모리 누수 위치

1. **다 쓴 참조(Obsolete References)**:
   * `elements` 배열의 크기를 늘릴 때, 기존 배열의 참조는 새로운 배열로 복사된다. 이때 기존 배열의 요소들은 더 이상 사용되지 않더라도 여전히 `elements` 배열에 남아 있다.
   * 스택의 `size`가 줄어들면 비활성 영역에 있는 객체들에 대한 참조가 계속 남아 있어 가비지 컬렉터가 이 객체들을 회수하지 못하게 된다.
   * 즉, 스택에서 꺼내진 객체들은 `elements` 배열에 여전히 참조로 남아 있어 가비지 컬렉터가 이 객체들을 회수하지 못하게 된다.
2. **활성 영역과 비활성 영역**:
   * 스택의 `size`가 줄어들면, `elements` 배열의 인덱스 `size` 이상의 요소들은 더 이상 활성 영역에 포함되지 않지만, 이 요소들은 여전히 배열에 남아 있다.
   * 이 비활성 영역의 객체들은 프로그램에서 더 이상 사용되지 않더라도, 배열에 대한 참조가 남아 있기 때문에 가비지 컬렉터가 이 객체들을 회수할 수 없다.
3. **가비지 수집에 대한 영향**:
   * 이러한 다 쓴 참조(obsolete reference)는 메모리 누수를 유발할 수 있다. 특히, 오래 지속되는 객체가 포함된 스택의 경우, 이 객체들이 메모리에 계속 남아 있어 메모리 사용량이 증가하게 된다.

> **정리하자면,**
>
> `Stack` 클래스에서 메모리 누수는 `elements` 배열의 비활성 영역에 있는 객체들에 대한 다 쓴 참조 때문에 발생한다. 이 객체들은 더 이상 사용되지 않지만, **스택이 이 객체들에 대한 참조를 계속 유지하기 때문에 가비지 컬렉터가 회수하지 못하게 된다.** 따라서, 이러한 참조를 적절히 관리하지 않으면 메모리 누수와 성능 저하를 초래할 수 있다.

스택의 `size`가 줄어들면 비활성 영역에 있는 객체들에 대한 참조가 계속 남아 있어 가비지 컬렉터가 이 객체들을 회수하지 못하게 된다.



## 메모리 누수에 대한 이해

> 즉, 가비지 컬렉터를 통해 객체를 자동으로 수거하는데, 여전히 메모리 누수를 걱정해야 하는 이유 중 하나는 다 쓴 객체에 대한 참조 해제를 못하기 때문이라는 것

`메모리 누수`로, 이 스택을 사용하는 프로그램을 오래 실행하다 보면 **점차 가비지 컬렉션 활동과 메모리 사용량이 늘어나 결국 성능이 저하될 것이다.** 상대적으로 드문 경우긴 하지만 심할 때는 디스크 페이징이나 OutOfMemoryError[^1]를 일으켜 프로그램이 예기치 않게 종료되기도 한다.



가령, 우리가 다 쓴 객체를 필요 이상으로 참조하고 있는 경우, 해당 객체는 가비지 컬렉터의 대상이 되지 않으며, 그 결과 메모리가 불필요하게 사용되게 됨

{% embed url="https://stackoverflow.com/questions/6470651/how-can-i-create-a-memory-leak-in-java" %}

> 위의 내용을 간단히만 정리 해서 메모리 누수가 일어나는 원인을 보자면,

### **Java에서 메모리 누수의 이해와 예제**

&#x20;메모리 누수는 여전히 발생할 수 있으며, 이는 메모리를 소모하지만 더 이상 참조되지 않는 객체를 해제할 수 없는 상황을 말한다.

#### **1. ThreadLocal 사용**

* **설명**: `ThreadLocal` 객체는 스레드 전용 변수로, 각 스레드가 자신만의 변수를 유지하게 해준다. 그러나 스레드가 살아 있는 동안 이 변수에 대한 강한 참조가 유지되면 가비지 수집이 불가능해진다.
*   **예제**:

    ```java
    public class MemoryLeakExample {
        private static final ThreadLocal<Object> threadLocal = new ThreadLocal<>();
        
        public void createMemoryLeak() {
            threadLocal.set(new byte[1000000]); // 큰 배열 할당
        }
    }
    ```
* **결과**: 스레드가 종료되어도 `threadLocal`의 객체는 메모리에서 해제되지 않는다.&#x20;

#### **2. 정적 참조 객체**

* **설명**: 정적 필드는 클래스의 인스턴스화 없이 접근할 수 있으므로, 이 필드가 객체에 대한 참조를 계속 유지하면 메모리가 해제되지 않는다.
*   **예제**:

    ```java
    class MemorableClass {
        static final List<Object> list = new ArrayList<>();
        // 객체를 계속 추가하면 메모리 누수 발생
    }
    ```

#### **3. 침식된 서브스트링**

* **설명**: Java 6 이하에서는 `String.substring()` 함수가 원래 문자열의 내부 배열을 참조한다. 이로 인해 큰 문자열에서 작은 서브스트링을 생성하면 큰 문자열이 가비지 수집되지 않을 수 있다.
*   **예제**:

    ```java
    String largeString = "Some very large text...";
    String subString = largeString.substring(0, 1); // 큰 문자열이 메모리에 남아 있음
    ```
* **결과**: `subString`이 사용되는 동안 `largeString`이 메모리에서 제거되지 않는다.

### 추가 주제: 가비지 수집의 이해

가비지 수집(GC)은 자바에서 메모리 관리를 자동화하는 메커니즘으로, 사용되지 않는 객체를 식별하고 메모리를 회수하는 작업이다. GC는 다음과 같은 방식으로 작동한다:

* **Mark and Sweep**: 먼저 사용되지 않는 객체를 '표시'(mark)한 후, 그 객체들이 할당된 메모리를 회수(수집)
* **Generational GC**: 객체의 생애주기에 따라 메모리를 분리하여 짧은 시간 내에 소멸한 객체는 빠르게 처리합

## 메모리 누수 원인(책  버전)

1.  **비활성 객체에 대한 강한 참조**:

    * 스택과 같은 데이터 구조에서 비활성 영역의 객체들이 여전히 참조되고 있을 경우, 가비지 컬렉터가 이를 회수하지 못한다.

    ```java
    public class Stack {
        private Object[] elements;
        private int size = 0;
        private static final int DEFAULT_INITIAL_CAPACITY = 16;

        public Stack() {
            elements = new Object[DEFAULT_INITIAL_CAPACITY];
        }

        public void push(Object e) {
            ensureCapacity();
            elements[size++] = e;
        }

        public Object pop() {
            if (size == 0) throw new EmptyStackException();
            return elements[--size]; // 비활성 영역의 객체는 여전히 참조됨
        }

        private void ensureCapacity() {
            if (elements.length == size) {
                elements = Arrays.copyOf(elements, 2 * size + 1);
            }
        }
    }
    ```
2.  **캐시 사용**:

    * 객체를 캐시에 저장한 후 해당 객체를 더 이상 사용하지 않더라도, 캐시가 이 객체에 대한 참조를 유지하면 메모리 누수가 발생할 수 있다.
    * **해결 방법**: [`WeakHashMap`](#user-content-fn-2)[^2]을 사용하여 캐시를 구현하면, 객체가 더 이상 사용되지 않을 때 자동으로 제거된다.

    ```java
    import java.util.WeakHashMap;

    public class Cache {
        private WeakHashMap<Key, Value> cache = new WeakHashMap<>();
        
        public void put(Key key, Value value) {
            cache.put(key, value);
        }

        public Value get(Key key) {
            return cache.get(key);
        }
    }
    ```
3.  **리스너와 콜백**:

    * 리스너[^3]나 콜백을 등록한 후 해지하지 않으면, 해당 객체에 대한 참조가 계속 유지되어 메모리 누수가 발생할 수 있습니다.
    * **해결 방법**: 약한 참조(weak reference)를 사용하여 리스너를 저장하면, 객체가 더 이상 사용되지 않을 때 가비지 컬렉터가 이를 회수할 수 있습니다.

    ```java
    import java.lang.ref.WeakReference;
    import java.util.ArrayList;
    import java.util.List;

    public class EventSource {
        private List<WeakReference<Listener>> listeners = new ArrayList<>();

        public void addListener(Listener listener) {
            listeners.add(new WeakReference<>(listener));
        }

        public void notifyListeners() {
            for (WeakReference<Listener> ref : listeners) {
                Listener listener = ref.get();
                if (listener != null) {
                    listener.onEvent();
                }
            }
        }
    }
    ```

#### 예방 방법

* **변수의 범위 최소화**: 다 쓴 참조를 유효 범위 밖으로 밀어내어 자연스럽게 메모리 누수를 방지합니다.
* **명시적 null 처리**: 객체 사용이 끝난 후 참조를 null로 설정하여 가비지 컬렉터가 회수할 수 있도록 합니다.
* **정기적인 코드 리뷰**: 메모리 누수를 방지하기 위해 코드 리뷰를 통해 문제를 조기에 발견할 수 있습니다.
* **힙 프로파일러 사용**: 메모리 사용량을 모니터링하고 누수를 추적할 수 있는 도구를 사용합니다.

#### 핵심 정리

메모리 누수는 시스템의 성능에 악영향을 미치며, 겉으로 드러나지 않아 발견하기 어려운 경우가 많습니다. 철저한 코드 리뷰와 적절한 메모리 관리 기법을 통해 이러한 문제를 예방하는 것이 중요합니다.

이런 자료를 참고했어요. \[1] 티스토리 - 아이템 7 다 쓴 객체 참조를 해제하라 - 개발일지 (https://soft-dino.tistory.com/49) \[2] 티스토리 - \[Java] 메모리 누수의 원인, 다 쓴 객체는 참조를 해제하라 - olrlobt (https://olrlobt.tistory.com/74) \[3] velog - 아이템 7. 다 쓴 객체 참조를 해제하라 (https://velog.io/@banjjoknim/%EC%95%84%EC%9D%B4%ED%85%9C-7.-%EB%8B%A4-%EC%93%B4-%EA%B0%9D%EC%B2%B4-%EC%B0%B8%EC%A1%B0%EB%A5%BC-%ED%95%B4%EC%A0%9C%ED%95%98%EB%9D%BC) \[4] 티스토리 - 메모리 정리하는 방법(null, Weak/SoftReference, Background ... (https://oneny.tistory.com/49)

뤼튼 사용하러 가기 > https://agent.wrtn.ai/5xb91l



## 메모리 누수 해결 방법

> 가장 간단한 해결 방법은 다 쓴 객체의 참조를 null로 **명시적 해제** 하는 것이다.

```java
public Object pop() {
    if (size = 0)
        throw new EmptyStackException();
    Object result = elements[——size];
    elements [size] = null; // 다 쓴 참조 해제 return result;
}
```

만약 null 처리한 참조를 실수로 사용하려 하면 프로그램은 즉시 `NullPointerException`을 던지며 종료하면 된다. 프로그램 오류는 가능한 조기에 발견하는 것이 좋다.

> 여기서 중요한 점은 모든 객체 참조를 null로 설정하는 것은 좋은 습관이 아니며, 필요한 경우에만 사용해야 한다는 것이다.

예를 들어, **사용된 객체가 더 이상 필요하지 않다는 것이 명확한 경우에만 참조를 null로 설정하는 것이 좋다.**

> 그러면, 객체를 언제 null로 참조 해제해야 할까?

1. 직접 관리하는 메모리 구조에서, 예를 들어 배열과 같은 자료 구조를 사용 시 필요
2. 오랜 시간동안 유지되면서 많은 객체 참조하는 경우, 참조 해제를 통해 메모리 사용을 줄일 수 있음

이 두 가지에 해당하지 않는 경우, 가비지 컬렉션이 대부분의 메모리 관리를 알아서 잘해주기 때문에 굳이 null 참조 설정하지 않아도 된다고 하며, 자바 21버전에서는 ZGC를 통해 메모리 자원을 효율적으로 관리하고 있다고 함

{% embed url="https://inside.java/2023/11/28/gen-zgc-explainer/" %}

{% embed url="https://huisam.tistory.com/entry/jvmgc" %}

### 메모리 누수 방지 방법 요약

* 객체가 더 이상 필요하지 않을 때 명시적으로 참조를 제거하
* 사용 후에는 항상 `ThreadLocal` 및 스트림을 닫도록 함
* 정적 필드 대신 순간적인 객체를 생성하여 덜 유지되도록 설계한다.



> 결론적으로 웬만한 경우, 아닌 이상 자바에서 GC는 안 건들이는 게 좋다고 함... 특히 자바 버전이 높을 수록 성능은 더더욱 좋아지기 때문





[^1]: `OutOfMemoryError`는 Java 프로그램이 더 이상 사용할 수 있는 메모리가 없을 때 발생하는 오류입니다. 이 오류는 JVM(Java Virtual Machine)이 요청된 메모리를 할당할 수 없을 때 발생하며, 이는 여러 가지 원인으로 인해 발생할 수 있습니다.

[^2]: `WeakHashMap`는 Java의 컬렉션 프레임워크에 포함된 맵 구현 중 하나로, 키에 대한 약한 참조(weak reference)를 사용하여 메모리 관리를 효율적으로 할 수 있게 해줍니다.&#x20;

    * 일반적인 `HashMap`은 키에 대한 강한 참조를 유지하므로, 해당 키가 더 이상 필요하지 않더라도 메모리에서 제거되지 않습니다. 반면, `WeakHashMap`은 키가 더 이상 사용되지 않을 때 자동으로 제거됩니다.

[^3]: 리스너(Listener)는 주로 이벤트 기반 프로그래밍에서 사용되는 디자인 패턴으로, 특정 이벤트가 발생했을 때 이를 감지하고 반응하는 객체를 의미합니다. 리스너는 이벤트 소스와 함께 작동하여, 이벤트가 발생할 때 호출되는 메서드를 정의합니다.

    #### 주요 개념

    1. **이벤트**:
       * 사용자 입력(예: 버튼 클릭, 키 입력)이나 시스템 변화(예: 데이터 변경) 등의 특정 상황을 말합니다.
    2. **이벤트 소스**:
       * 이벤트를 발생시키는 주체로, 리스너가 감지할 이벤트를 발생시키는 객체입니다. 예를 들어, 버튼, 창, 또는 데이터 모델 등이 될 수 있습니다.
    3. **리스너 인터페이스**:
       * 특정 이벤트를 처리하기 위해 구현해야 하는 메서드를 정의하는 인터페이스입니다. 일반적으로 `onEvent()`, `handleEvent()`와 같은 메서드가 포함됩니다.
