# item 84 : 프로그램의 동작을 스레드 스케줄러 에 기대지 말라

## **이식성이 좋은 멀티스레드 프로그램 작성법**

### **1. 이식성이 좋은 프로그램이란?**

* **정확성**이나 **성능**이 특정 플랫폼(OS) 또는 JVM의 **스레드 스케줄러**에 의존하지 않는 프로그램이다.
* 스레드 스케줄링 정책이 다르더라도 일관된 동작과 성능을 제공할 수 있어야 **이식성**이 높다.

### **2. 실행 가능한 스레드 수와 생명 주기**

* **실행 가능한 스레드 수** = **전체 스레드 수** - **대기 중인 스레드 수**
* 실행 가능한 스레드 수를 **프로세서 수보다 지나치게 많게 만들지 말아야 한다.**
  * 스레드 스케줄러의 부담이 줄어들어 일관된 성능을 보장한다.

### **3. 실행 가능한 스레드의 관리 방법**

#### **1) 실행 준비된 스레드는 완료까지 실행되게 하기**

* 스레드가 작업을 맡았으면 **작업이 끝날 때까지 계속 실행**되게 한다.
* 이런 구조를 지키면 스레드 스케줄링 정책이 다른 플랫폼에서도 크게 영향을 받지 않는다.

#### **2) 스레드 수를 적게 유지하기**

* **유용한 작업**이 없다면 스레드는 **대기 상태**에 들어가야 한다.
* 스레드가 불필요하게 실행 상태에 있다면 다른 작업이 실행될 기회를 빼앗게 된다.
* 실행자 프레임워크(ExecutorService)를 사용하면 다음을 조절할 수 있다:
  * **스레드 풀 크기**: 적절하게 설정하여 스레드 수를 제한한다.
  * **작업 단위 크기**: 작업을 짧게 유지하되 너무 짧으면 분배 오버헤드가 발생할 수 있다.

### **4. 바쁜 대기(Busy Waiting)를 피해야 하는 이유**

**바쁜 대기란?**

* **공유 객체의 상태**를 계속 반복해서 검사하며 쉬지 않고 **CPU를 소모**하는 상태이다.
* 예제: 아래 `SlowCountDownLatch` 클래스는 나쁜 예시이다.

```java
public class SlowCountDownLatch {
    private int count;

    public SlowCountDownLatch(int count) {
        if (count < 0) throw new IllegalArgumentException(count + " < 0");
        this.count = count;
    }

    public void await() {
        while (true) {  // 상태를 계속 검사 (바쁜 대기)
            synchronized (this) {
                if (count == 0) return;
            }
        }
    }

    public synchronized void countDown() {
        if (count != 0) count--;
    }
}
```

**바쁜 대기의 문제점**

1. **CPU 자원 낭비**: 프로세서에 부담을 줘서 다른 작업이 실행될 기회를 박탈한다.
2. **스레드 스케줄러에 취약**: 스케줄링 정책이 변덕스러우면 성능이 예측 불가능해진다.
3. **성능과 이식성 저하**: 다른 플랫폼이나 JVM에서는 더욱 나쁜 성능을 보일 수 있다.

### **5. Thread.yield와 스레드 우선순위 사용 금지**

#### **1) Thread.yield를 사용하지 말자**

* `Thread.yield()`는 **스레드의 실행을 양보**하는 힌트를 스케줄러에 제공하지만:
  * **JVM 구현에 따라 동작이 다르다**: 일부 JVM에서는 효과적이지만, 다른 JVM에서는 무효하거나 오히려 성능이 저하될 수 있다.
  * **테스트하기 어렵다**: 성능 개선이 일관되게 나타나지 않는다.

#### **2) 스레드 우선순위에 의존하지 말자**

* **우선순위**를 조정해 특정 스레드에 CPU 시간을 더 많이 할당하려는 시도는 위험하다.
  * **이식성이 가장 나쁜 특성**이다: 운영체제와 JVM마다 우선순위 구현이 다르다.
  * **근본적인 문제 해결이 아님**: 성능 문제의 진짜 원인을 수정하지 않으면 같은 문제가 반복된다.

## **핵심 정리**

1. **스레드 스케줄러에 기대지 말라**
   * 플랫폼이나 JVM의 스케줄링 정책에 따라 성능이 달라지지 않도록 구조를 설계해야 한다.
2. **바쁜 대기 상태를 피하라**
   * 실행 준비된 스레드가 없으면 반드시 **대기 상태**로 만들어라.
3. **Thread.yield와 스레드 우선순위를 사용하지 말라**
   * 구조를 개선해 실행 가능한 스레드 수를 적절히 유지하는 것이 더 중요하다.

## **부가 설명: 권장되는 해결 방법**

* **ExecutorService와 스레드 풀**: 실행자 프레임워크를 사용해 스레드 수를 적절히 제한한다.
* **BlockingQueue**: 작업이 준비될 때까지 스레드를 효율적으로 대기 상태로 유지한다.
* **Lock과 Condition**: 객체의 상태 변경 시 알림을 통해 스레드를 깨우는 구조를 사용한다.

이와 같은 구조를 사용하면 **이식성이 높고 견고한 멀티스레드 프로그램**을 작성할 수 있다.
