# Object 클래스에 대한 정리

**예, `equals` 메서드는 `java.lang.Object` 클래스에 정의되어 있는 메서드입니다.**

`Object` 클래스는 자바에서 모든 클래스의 최상위 부모 클래스이며, 모든 객체는 암시적으로 `Object`를 상속받습니다. `Object` 클래스는 자바 객체들이 공통적으로 가져야 할 기본 메서드들을 정의하고 있습니다.

***

**`Object` 클래스의 주요 메서드들:**

1. **`public boolean equals(Object obj)`**
   * **역할**: 두 객체의 동등성을 비교하는 메서드입니다.
   * **기본 구현**: 두 객체의 \*\*참조(메모리 주소)\*\*를 비교하여 동일한 객체인지 확인합니다.
   * **오버라이드 필요성**: 객체의 \*\*내용(속성 값)\*\*을 기준으로 비교하려면 이 메서드를 오버라이드해야 합니다.
2. **`public int hashCode()`**
   * **역할**: 객체의 해시 코드를 반환하는 메서드입니다.
   * **사용 이유**: 해시 기반 컬렉션(`HashMap`, `HashSet` 등)에서 객체를 효율적으로 관리하기 위해 사용됩니다.
   * **오버라이드 필요성**: `equals`를 오버라이드하면 `hashCode`도 함께 오버라이드해야 합니다.
3. **`public String toString()`**
   * **역할**: 객체의 문자열 표현을 반환하는 메서드입니다.
   * **기본 구현**: 클래스 이름과 해시 코드를 포함한 문자열을 반환합니다.
   * **오버라이드 필요성**: 객체의 유용한 정보를 포함하도록 오버라이드하는 것이 일반적입니다.
4. **`protected Object clone() throws CloneNotSupportedException`**
   * **역할**: 객체의 복제본을 만드는 메서드입니다.
   * **사용 조건**: `Cloneable` 인터페이스를 구현한 클래스에서 사용 가능합니다.
   * **주의사항**: 깊은 복사와 얕은 복사를 고려해야 합니다.
5. **`public final void wait()`, `wait(long timeout)`, `wait(long timeout, int nanos)`**
   * **역할**: 현재 스레드를 일시 정지하고 다른 스레드에서 `notify()`나 `notifyAll()`을 호출할 때까지 대기합니다.
   * **사용 조건**: 반드시 동기화된 컨텍스트(`synchronized` 블록 또는 메서드) 내에서 호출해야 합니다.
6. **`public final void notify()`**
   * **역할**: `wait()`에 의해 대기 중인 스레드 중 하나를 깨웁니다.
   * **사용 조건**: 동기화된 컨텍스트 내에서 호출해야 합니다.
7. **`public final void notifyAll()`**
   * **역할**: `wait()`에 의해 대기 중인 모든 스레드를 깨웁니다.
   * **사용 조건**: 동기화된 컨텍스트 내에서 호출해야 합니다.
8. **`protected void finalize() throws Throwable`**
   * **역할**: 객체가 가비지 컬렉션될 때 호출되는 메서드입니다.
   * **주의사항**: 자바 9부터는 더 이상 사용이 권장되지 않습니다(deprecated).

***

**`Object` 클래스의 구조와 역할:**

* **패키지**: `java.lang`
* **접근 제어자**: `public class Object`
* **상속 관계**: 자바에서 모든 클래스의 최상위 부모 클래스이며, 다른 클래스를 상속받지 않습니다.

**역할**:

* **기본 메서드 제공**: 모든 객체가 가져야 할 기본 메서드(`equals`, `hashCode`, `toString` 등)를 제공합니다.
* **동기화 메커니즘 지원**: 스레드 동기화를 위한 메서드(`wait`, `notify`, `notifyAll`)를 제공합니다.

***

**`equals` 메서드의 상세 설명:**

*   **메서드 시그니처**:

    ```java
    public boolean equals(Object obj)
    ```
* **기본 구현**:
  * `this == obj`와 같이 **참조 비교**를 수행합니다.
  * 즉, 두 객체가 동일한 메모리 주소를 가리킬 때만 `true`를 반환합니다.
* **오버라이드 이유**:
  * 객체의 **내용**을 비교하여 논리적으로 동등한지를 판단하려면 `equals` 메서드를 오버라이드해야 합니다.
* **오버라이드 시 지켜야 할 계약**:
  1. **반사성(reflexive)**: `x.equals(x)`는 항상 `true`여야 합니다.
  2. **대칭성(symmetric)**: `x.equals(y)`가 `true`이면 `y.equals(x)`도 `true`여야 합니다.
  3. **추이성(transitive)**: `x.equals(y)`와 `y.equals(z)`가 `true`이면 `x.equals(z)`도 `true`여야 합니다.
  4. **일관성(consistent)**: 객체의 상태가 변경되지 않았다면, 여러 번 호출해도 결과는 동일해야 합니다.
  5. **`null`과의 비교**: `x.equals(null)`은 항상 `false`여야 합니다.
*   **예시**:

    ```java
    public class Person {
        private String name;
        private int age;

        @Override
        public boolean equals(Object obj) {
            if (this == obj)
                return true;
            if (obj == null || getClass() != obj.getClass())
                return false;
            Person other = (Person) obj;
            return age == other.age && Objects.equals(name, other.name);
        }

        @Override
        public int hashCode() {
            return Objects.hash(name, age);
        }
    }
    ```

    * 위 예시에서 `name`과 `age`가 같으면 같은 객체로 판단하도록 `equals`를 오버라이드했습니다.
    * `hashCode`도 함께 오버라이드하여 `equals`와의 계약을 지켰습니다.

***

**`Object` 클래스의 다른 메서드들의 상세 설명:**

1. **`hashCode()`**
   * **역할**: 객체의 해시 코드를 반환하며, 해시 기반 컬렉션에서 객체를 저장하거나 검색할 때 사용됩니다.
   * **오버라이드 시 주의사항**: `equals`가 같은 두 객체는 동일한 `hashCode`를 반환해야 합니다.
2. **`toString()`**
   * **역할**: 객체의 문자열 표현을 반환합니다.
   * **오버라이드 이유**: 객체의 유용한 정보를 포함한 문자열을 반환하여 디버깅 및 로깅에 활용합니다.
   *   **예시**:

       ```java
       @Override
       public String toString() {
           return "Person{name='" + name + "', age=" + age + "}";
       }
       ```
3. **동기화 관련 메서드**
   * **`wait()`, `notify()`, `notifyAll()`**
     * **역할**: 스레드 간 통신을 위한 메서드입니다.
     *   **사용 방법**:

         ```java
         synchronized (lock) {
             while (조건) {
                 lock.wait();
             }
             // 작업 수행
         }
         ```
     * **주의사항**: 반드시 `synchronized` 블록 내에서 호출해야 하며, 그렇지 않으면 `IllegalMonitorStateException`이 발생합니다.

***

**중요한 개념들:**

* **`equals`와 `hashCode`의 관계**:
  * **계약**: 두 객체가 `equals`에 의해 같다면, 동일한 `hashCode`를 반환해야 합니다.
  * **이유**: 해시 기반 컬렉션(`HashMap`, `HashSet` 등)은 `hashCode`를 기반으로 객체를 관리하므로, 이 계약을 지키지 않으면 예상치 못한 동작이 발생할 수 있습니다.
* **`toString` 메서드의 활용**:
  * 객체의 현재 상태를 사람이 이해하기 쉬운 형태로 표현합니다.
  * 디버깅, 로깅, UI 출력 등 다양한 곳에서 활용됩니다.
* **스레드 동기화와 `Object` 클래스**:
  * 모든 객체는 고유한 모니터 락(monitor lock)을 가지고 있습니다.
  * `synchronized` 키워드를 통해 객체의 모니터 락을 획득하여 동기화된 코드를 작성할 수 있습니다.
  * `wait`, `notify`, `notifyAll` 메서드는 이러한 동기화된 컨텍스트에서 스레드 간의 통신을 가능하게 합니다.

***

**결론:**

`java.lang.Object` 클래스는 자바에서 모든 클래스의 최상위 부모로서, 객체가 기본적으로 가져야 할 메서드들을 제공합니다. `equals` 메서드는 `Object` 클래스에 정의되어 있으며, 기본적으로 참조 비교를 수행합니다. 그러나 대부분의 경우 객체의 내용 비교가 필요하므로, `equals` 메서드를 오버라이드하여 원하는 동작을 구현해야 합니다.

또한, `hashCode`, `toString`, 스레드 동기화를 위한 메서드들도 제공하여 객체의 동작을 커스터마이징하고 스레드 안전한 프로그램을 작성할 수 있게 합니다.

***

**추가 참고 자료:**

* **Effective Java** (Joshua Bloch 저): `equals`와 `hashCode` 오버라이드에 대한 모범 사례를 다루고 있습니다.
* **Java 공식 문서**:
  * [Object 클래스 문서](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html)
  * [Overriding Methods](https://docs.oracle.com/javase/tutorial/java/IandI/override.html)
* **Java Language Specification**:
  * [Chapter 17. Threads and Locks](https://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html)

***

**요약:**

* **`equals`는 `Object` 클래스에 정의된 메서드로, 객체의 동등성을 비교합니다.**
* **`Object` 클래스는 모든 클래스의 부모이며, 공통 메서드를 제공합니다.**
* **필요에 따라 `equals`, `hashCode`, `toString` 등을 오버라이드하여 객체의 원하는 동작을 정의할 수 있습니다.**
* **스레드 동기화와 관련된 메서드들도 제공하여 멀티스레드 환경에서의 안전한 프로그래밍을 지원합니다.**
