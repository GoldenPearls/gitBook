# item  78 : 공유 중인 가변 데이터는 동기화해 사용하라

## Synchronized 키워드와 동기화의 중요성

## **1. Synchronized의 기본 동작**

* **Synchronized** 키워드는 **메서드나 블록**을 **한 번에 한 스레드씩** 수행하도록 보장한다.
* **동기화의 주요 목적**:
  * **배타적 실행**: 한 스레드가 객체를 변경하는 동안 다른 스레드가 **일관되지 않은 상태**의 객체를 보지 못하도록 막는다.
  * **스레드 간 통신 보장**: 한 스레드가 만든 변화를 다른 스레드에서 **확인 가능**하게 만든다.

동기화는 자바 프로그램에서 매우 중요한 역할을 한다. **스레드 간의 데이터 일관성을 유지**하고, **스레드 안전성을 보장**하기 위해 필수적이다. 동기화가 없다면, 프로그램이 **의도하지 않은 동작**을 수행할 가능성이 커지고, 디버깅이 극도로 어려워질 수 있다. 스레드 간의 통신과 실행 순서가 불확실할 때, 동기화는 안정성과 예측 가능성을 제공한다.

<figure><img src="../../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

특히, 멀티스레드 환경에서는 데이터 불일치와 경쟁 상태가 빈번하게 발생할 수 있다. 이 문제는 동기화를 통해 해결할 수 있으며, 이를 통해 코드의 신뢰성과 안전성을 크게 향상시킬 수 있다.

## **2. 동기화와 자바 메모리 모델**

### **1) 원자적 동작**

<figure><img src="../../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

* **long**과 **double**을 제외한 변수의 읽기와 쓰기 동작은 **원자적**이다.
  * 이는 스레드가 값을 **정상적으로 저장**했을 때 항상 **온전한 값**을 읽는다는 뜻이다.

하지만 이러한 원자성 보장만으로는 충분하지 않다. **한 스레드의 변경 사항이 다른 스레드에서 관찰될 시점**은 보장되지 않는다. 이는 동기화를 통해 해결할 수 있다.

> **예시**: 단순히 데이터를 읽고 쓸 수 있다고 해서 스레드 간의 통신이 보장되지 않는다. 동기화는 스레드가 데이터를 **일관성 있게 공유**하도록 돕는다.

### **2) 동기화의 필요성**

<figure><img src="../../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

* 동기화 없이 원자적 데이터를 읽고 쓸 경우 **치명적 문제**가 발생할 수 있다.
* **Thread.stop() 메서드**의 사례를 통해 이를 이해할 수 있다.
  * Thread.stop() 메서드는 안전하지 않아 이미 **사용 자제(deprecated)** API로 지정되었다.
  * 이 메서드는 데이터를 강제로 수정하며 **스레드 간 일관성을 깨트릴 가능성**이 크다.

따라서, 스레드를 멈추는 작업조차도 안전하게 처리하기 위해 **동기화된 상태 관리**가 필요하다. 동기화를 통해 데이터를 보호하고, 스레드 간 통신의 신뢰성을 확보해야 한다.

## **3. 잘못된 코드 예시**

### **1) 문제 코드**

```java
public class StopThread {
    private static boolean stopRequested;

    public static void main(String[] args) throws InterruptedException {
        Thread backgroundThread = new Thread(() -> {
            int i = 0;
            while (!stopRequested) {
                i++;
            }
        });
        backgroundThread.start();

        TimeUnit.SECONDS.sleep(1);
        stopRequested = true;
    }
}
```

* **문제점**:
  * 메인 스레드에서 `stopRequested = true;`로 설정해도 **백그라운드 스레드가 이를 인지하지 못할 수 있다.**
  * 이는 **동기화가 없기 때문**이며, **가상 머신의 최적화**로 인해 문제가 발생할 수 있다.

> **예시 최적화**:

```java
// 원래 코드
while (!stopRequested) i++;

// 최적화된 코드
if (!stopRequested) {
    while (true) i++;
}
```

* 이와 같은 최적화가 발생하면 프로그램은 응답 불가 상태(liveness failure)가 되어 종료되지 않을 수 있다.

### **2) 수정된 코드**

```java
public class StopThread {
    private static boolean stopRequested;

    private static synchronized void requestStop() {
        stopRequested = true;
    }

    private static synchronized boolean stopRequested() {
        return stopRequested;
    }

    public static void main(String[] args) throws InterruptedException {
        Thread backgroundThread = new Thread(() -> {
            int i = 0;
            while (!stopRequested()) {
                i++;
            }
        });
        backgroundThread.start();

        TimeUnit.SECONDS.sleep(1);
        requestStop();
    }
}
```

* **해결 방법**:
  * `requestStop`과 `stopRequested`를 **모두 동기화**하여 스레드 간의 **통신을 보장**.
  * 이는 스레드가 서로의 변경 사항을 즉시 반영할 수 있도록 한다.

> 동기화를 올바르게 사용하면 스레드 간의 데이터 상태를 명확히 정의할 수 있으며, 동작 예측이 가능해진다. 코드의 유지보수성과 디버깅 편의성 또한 증가한다.

### **3) 개선된 코드 (volatile 사용)**

<figure><img src="../../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

```java
public class StopThread {
    private static volatile boolean stopRequested;

    public static void main(String[] args) throws InterruptedException {
        Thread backgroundThread = new Thread(() -> {
            int i = 0;
            while (!stopRequested) {
                i++;
            }
        });
        backgroundThread.start();

        TimeUnit.SECONDS.sleep(1);
        stopRequested = true;
    }
}
```

* **volatile 한정자**를 사용하면 동기화 없이도 **최신 값 보장**.
* volatile은 스레드 간 통신 보장만 수행하며, 배타적 실행은 제공하지 않는다.

> **추가 설명**: volatile은 적절한 사용 시 성능과 간결성을 모두 제공하지만, 모든 상황에서 사용할 수 있는 것은 아니다. 배타적 실행이 필요한 경우에는 반드시 동기화를 사용해야 한다.

## **4. 잘못된 동기화와 원자성 문제**

<figure><img src="../../../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

### **1) 증가 연산자(++)의 문제**

```java
private static volatile int nextSerialNumber = 0;

public static int generateSerialNumber() {
    return nextSerialNumber++;
}
```

* **문제점**:
  * 증가 연산자(++)는 원자적이지 않음.
  * 읽기와 쓰기 사이에 다른 스레드가 개입할 가능성.

> 이로 인해 동일한 값이 여러 번 반환되는 **데이터 불일치** 문제가 발생할 수 있다.

### **2) 올바른 동기화 코드**

```java
private static final AtomicLong nextSerialNum = new AtomicLong();

public static long generateSerialNumber() {
    return nextSerialNum.getAndIncrement();
}
```

* **java.util.concurrent.atomic** 패키지의 **AtomicLong**을 사용.
  * **락-프리(lock-free)** 방식으로 **스레드 안전** 구현.
  * 성능 면에서도 **우수**.
  * **AtomicLong의 장점**: 별도의 락(lock)을 사용하지 않으므로, 성능이 중요한 환경에서 더 나은 결과를 제공한다.

### **3) 동기화 방식 비교**

* **synchronized**와 **AtomicLong**:
  * `synchronized`는 **배타적 실행**과 **스레드 간 통신**을 모두 보장하지만, 성능이 상대적으로 낮다.
  * `AtomicLong`은 **가벼운 락-프리 방식**으로, 동시성 작업에서 더 높은 성능 제공.

> **부가 설명**: synchronized는 코드의 동작 안정성을 보장하지만, 많은 스레드가 동시에 접근하는 경우 성능 병목 현상을 유발할 수 있다. 반면 AtomicLong은 이러한 병목을 최소화하며 더 높은 처리량을 제공한다.

## **5. 가변 데이터와 동기화 정책**

<figure><img src="../../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>



* **가장 좋은 방법**: 공유 중인 가변 데이터를 **최소화**.
  * **불변 데이터**(immutable)만 공유하거나, 가변 데이터를 **단일 스레드에서만 사용.**

### **1) 안전 발행(Safe Publication)**

* 데이터를 공유할 때는 **안전 발행** 기법 사용.
  * **객체를 다른 스레드에 안전하게 전달**.
  * 안전 발행은 데이터 일관성을 보장하며, 스레드 간의 예상치 못한 오류를 방지한다.

### **2) 안전 발행 방법**

* 객체를 다음 위치에 저장:
  * **정적 필드**
  * **volatile 필드**
  * **final 필드**
  * **동시성 컬렉션**
* 각 저장 위치는 데이터의 가시성과 일관성을 보장하는 데 중요한 역할을 한다.

### **3) 동시성 컬렉션 사용**

* **java.util.concurrent** 패키지의 동시성 컬렉션 사용:
  * `ConcurrentHashMap`, `CopyOnWriteArrayList` 등.
  * 락 없이도 **스레드 안전성** 제공.

> **주의**: 외부 프레임워크와 라이브러리의 동작도 잘 이해해야 한다.

* 예기치 않은 스레드 간 충돌을 방지하려면, 라이브러리 문서를 꼼꼼히 확인해야 한다.
* **주의**: 동시성 컬렉션을 사용할 때에도 데이터의 논리적 일관성을 유지하기 위해 주의를 기울여야 한다. 예를 들어, 여러 컬렉션에 걸친 작업은 별도의 동기화가 필요할 수 있다.



## 🗂️ 핵심 정리

> 여러 스레드가 가변 데이터를 공유한다면 그 데이터를 읽고 쓰는 동작은 반드시 동기화 해야 한다.  **Synchronized**와 **volatile**은 자바의 동기화에서 필수적이다.

* 동기화하지 않으면 한 스레드가 수행한 변경을 다른 스레드가 보지 못할 수 도 있다. 공유되는 가변 데이터를 동기화하는 데 실패하면 응답 불가 상태에 빠지거나 안전 실패로 이어질 수 있다.&#x20;
* 이는 디버깅 난이도가 가장 높은 문제에 속한다. 간헐적이거 나 특정 타이밍에만 발생할 수도 있고, VM에 따라 현상이 달라지기도 한다. 배타적 실행은 필요 없고 스레드끼리의 통신만 필요하다면 **volatile 한정자만으로 동기화할** 수 있다. 다만 올바로 사용하기가 까다롭다.
* 각 도구의 역할과 사용법을 명확히 이해하면, 보다 안전하고 효율적인 프로그램을 설계할 수 있다.
