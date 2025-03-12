# item 76 : 가능한 한 실패 원자적으로 만들라

## 1. 실패 원자성(Failure-Atomic) **정의 및 개념**

> 실패 원자성(Failure-Atomic)은 **메서드 호출이 실패하더라도 해당 객체가 이전 상태를 유지해야 하는 특성**을 말한다.

<figure><img src="../../../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 이를 통해, 호출자가 오류 상태를 복구할 수 있으며, 예외가 발생해도 객체가 불안정한 상태에 빠지지 않도록 보장한다.
* 실패 원자성을 지키는 것은 신뢰성 높은 소프트웨어 설계의 핵심 중 하나이다.

## **2. 실패 원자성을 구현하는 방법**

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### **1) 불변 객체(Immutable Object) 사용**

* **불변 객체**는 본질적으로 실패 원자적이다.
  * 불변 객체는 상태가 생성 시점에 고정되며 이후 절대 변경되지 않는다.
  * 메서드가 실패하더라도 객체의 상태는 안전하게 유지된다.
*   **예시**:

    ```java
    public final class Point {
        private final int x;
        private final int y;

        public Point(int x, int y) {
            this.x = x;
            this.y = y;
        }

        public Point translate(int dx, int dy) {
            return new Point(x + dx, y + dy); // 새로운 객체 반환
        }
    }
    ```

    * `translate` 메서드가 실패하더라도 기존 객체인  Point는 영향받지 않는다.

### **2) 매개변수 유효성 검사**

* 가변 객체에서 **매개변수의 유효성을 먼저 검증**하면, 내부 상태 변경 전에 예외를 처리할 수 있다.
* 유효성 검사는 **객체 상태가 예외로 인해 손상되지 않도록 방지**하는 중요한 단계다.
*   **예시: Stack.pop 메서드**

    ```java
    public class Stack {
        private Object[] elements;
        private int size = 0;

        public Object pop() {
            if (size == 0) {
                throw new EmptyStackException();
            }
            Object result = elements[--size];
            elements[size] = null; // 참조 해제
            return result;
        }
    }
    ```

    * `if (size == 0)` 조건으로 빈 스택에서의 호출을 사전에 방지함.
    * 만약 이 검사가 없었다면, `ArrayIndexOutOfBoundsException`이 발생하고 스택 상태가 손상될 가능성이 있음.

### **3) 실패 가능 코드의 위치 조정**

* **객체 상태를 변경하는 코드보다 실패 가능성이 있는 코드를 앞에 배치**한다.
* 예외가 발생할 가능성이 있는 작업을 미리 처리하여 내부 상태 변경 전에 예외가 발생하도록 보장.

**예시: TreeMap**

* TreeMap은 원소를 추가하기 전에 원소가 비교 가능한 타입인지 확인한다.
* 잘못된 타입의 원소를 추가하려 할 때는 트리가 수정되기 전에 `ClassCastException`이 발생한다.

```java
TreeMap<String, String> map = new TreeMap<>();
map.put("key1", "value1");
map.put("key2", 123); // ClassCastException 발생
```

* 원소를 추가하기 전에 비교 연산이 수행되어 **상태 변경 없이** 예외가 발생.

### **4) 임시 복사본 사용**

* **작업을 임시 복사본에서 수행**하고, 작업이 성공적으로 완료되었을 때만 원래 객체와 교체.
* 데이터 무결성을 유지하면서도, 작업 실패 시 기존 상태를 그대로 유지.
*   **예시: 정렬 메서드**

    * 정렬 알고리즘이 입력 데이터를 직접 수정하지 않고 임시 자료구조에 복사하여 작업.

    ```java
    public void sort(List<Integer> list) {
        List<Integer> temp = new ArrayList<>(list); // 입력 리스트 복사
        Collections.sort(temp); // 복사본에서 작업 수행
        // 성공적으로 완료되면 결과를 반환하거나 교체
    }
    ```
* 정렬 실패 시 원래 리스트는 영향을 받지 않음.

### **5) 실패 후 상태 복구**

* 작업 중 일부가 실패했더라도 **객체를 이전 상태로 되돌리는 로직**을 구현할 수 있다.
* 이는 복잡할 수 있지만, 실패 원자성을 제공하기 위한 효과적인 방법이다.
* 주로 내구성이 요구되는 시스템(예: 디스크 기반 데이터베이스)에 사용.
*   **예시**:

    ```java
    public class TransactionManager {
        private boolean transactionStarted;

        public void startTransaction() {
            transactionStarted = true;
        }

        public void commitTransaction() {
            if (!transactionStarted) {
                throw new IllegalStateException("No transaction started");
            }
            // 커밋 로직 수행
            transactionStarted = false;
        }

        public void rollbackTransaction() {
            if (!transactionStarted) {
                return; // 이미 롤백된 상태
            }
            // 복구 로직 수행
            transactionStarted = false; // 상태 복원
        }
    }
    ```

## **3. 추가 고려사항**

### **실패 원자성의 한계**

<figure><img src="../../../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

* 실패 원자성을 항상 달성할 수 있는 것은 아니다.
* **비용**이나 **복잡성**이 지나치게 큰 연산에서는 실패 원자성을 추구하지 않을 수도 있다.
* 예를 들어, 여러 스레드가 동기화 없이 동일 객체를 수정하면 **ConcurrentModificationException**이 발생하며, 이 경우 객체의 상태를 복구할 수 없을 수도 있다.
* `Error`와 같은 복구 불가능한 문제는 실패 원자성을 고려하지 않아도 된다.

### **메서드 명세와 API 문서화**

* 메서드가 실패하더라도 객체 상태가 이전 상태를 유지해야 한다는 것을 **명시적으로 문서화**해야 한다.
* 예외가 발생할 경우 객체 상태가 변경될 수밖에 없는 상황이라면, 이를 API 문서에 반드시 기술해야 한다.

## 📚 **정리**

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 실패 원자성을 지키면 예외가 발생하더라도 객체는 일관된 상태를 유지하며, 호출자가 오류를 복구하기 용이하다.
* 실패 원자성을 확보하기 위해 **불변 객체 사용**, **유효성 검사**, **임시 복사본 활용**, **상태 복구 로직** 등을 설계에 반영하자.
* 신뢰성 높은 소프트웨어를 만들기 위해 **메서드 명세와 문서화**를 통해 개발자에게 기대 동작을 명확히 전달하는 것도 중요하다.
