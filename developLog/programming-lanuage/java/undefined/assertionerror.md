# AssertionError

#### AssertionError란 무엇인가요?

\*\*`AssertionError`\*\*는 자바에서 \*\*에러(Error)\*\*의 한 종류로, 주로 프로그램의 논리적인 오류가 발생했을 때 사용됩니다. 이 에러는 일반적으로 개발자가 예측할 수 없는 상황이 발생했을 때 이를 알리기 위해 던집니다.

**1. AssertionError의 목적**

* **개발 중에만 사용**: `AssertionError`는 주로 개발 및 디버깅 단계에서 프로그램의 내부 상태를 검증하기 위해 사용됩니다.
* **불가능한 상황을 나타냄**: 코드에서 절대로 발생해서는 안 되는 상황이 발생했을 때 이를 알리기 위해 사용합니다.
* **예외 처리의 간소화**: 검사 예외(checked exception)를 처리해야 하는 번거로움을 피하기 위해 사용됩니다.

**2. 사용 예시**

**예시 1: 절대 발생하지 않는 예외 처리**

`Cloneable`을 구현한 클래스에서 `clone()` 메서드를 재정의할 때, `CloneNotSupportedException`이 발생할 수 없습니다. 하지만 `Object`의 `clone()` 메서드는 이 예외를 던질 수 있도록 선언되어 있으므로, 이를 처리해야 합니다.

```java
@Override
public Object clone() {
    try {
        return super.clone();
    } catch (CloneNotSupportedException e) {
        throw new AssertionError(); // 이 예외는 절대 발생하지 않음
    }
}
```

* **설명**: `CloneNotSupportedException`은 검사 예외지만, 이 코드에서는 절대 발생하지 않으므로, 이를 잡아서 `AssertionError`를 던집니다.
* **의미**: `AssertionError`를 던짐으로써 이 예외가 발생하면 논리적 오류가 있다는 것을 나타냅니다.

**예시 2: 불가능한 조건 검증**

```java
int result = calculateValue();
assert result >= 0 : "Result should not be negative";
```

* **설명**: `assert` 키워드를 사용하여 조건이 거짓일 경우 `AssertionError`를 던집니다.
* **주의**: `assert` 키워드는 기본적으로 비활성화되어 있으며, 실행 시 `-ea` 옵션을 사용해야 작동합니다.

**3. AssertionError와 예외 처리**

* **Error vs. Exception**:
  * `Error`는 심각한 시스템 오류로, 프로그램이 정상적으로 복구할 수 없는 상황을 나타냅니다.
  * `Exception`은 프로그램에서 처리할 수 있는 예외 상황을 나타냅니다.
* **AssertionError의 특징**:
  * **Unchecked Error**: `RuntimeException`처럼 `AssertionError`는 검사 예외가 아니므로 `throws` 절에 선언할 필요가 없습니다.
  * **프로그램 종료 유도**: 발생 시 프로그램을 종료시키는 것이 일반적입니다.

**4. AssertionError를 사용하는 이유**

* **불필요한 예외 전파 방지**: 검사 예외를 처리하기 위해 불필요한 코드가 추가되는 것을 방지합니다.
* **논리적 오류 표시**: 개발자가 예상하지 못한 상황이 발생했음을 명확하게 나타냅니다.
* **디버깅 용이성**: 어디에서 오류가 발생했는지 즉시 알 수 있어 디버깅이 쉬워집니다.

**5. 주의사항**

* **프로덕션 코드에서는 사용에 신중해야 합니다**: `AssertionError`는 주로 개발 및 테스트 단계에서 사용되며, 실제 운영 환경에서는 사용자에게 노출되지 않도록 해야 합니다.
* **예상 가능한 예외 상황에는 사용하지 않습니다**: 입력 값이 잘못되었거나 네트워크 오류 등 예측 가능한 상황에서는 적절한 예외(Exception)를 사용해야 합니다.

**6. 요약**

* `AssertionError`는 프로그램에서 **절대로 발생해서는 안 되는 논리적 오류**를 나타낼 때 사용합니다.
* `Cloneable`을 구현한 클래스에서 `clone()` 메서드를 재정의할 때, 발생할 수 없는 `CloneNotSupportedException`을 처리하기 위해 `AssertionError`를 던집니다.
* 이를 통해 코드의 가독성을 높이고, 불필요한 예외 처리 코드를 줄일 수 있습니다.

***

**추가 참고**:

* **`assert` 키워드**: 자바에서 `assert`를 사용하면 특정 조건이 참인지 검사할 수 있으며, 거짓인 경우 `AssertionError`가 던져집니다.
*   **활용 예시**:

    ```java
    public void setAge(int age) {
        assert age >= 0 : "Age cannot be negative";
        this.age = age;
    }
    ```
* **실행 방법**: JVM 실행 시 `-ea` 또는 `-enableassertions` 옵션을 사용하여 `assert` 문을 활성화해야 합니다.
