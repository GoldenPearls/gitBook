# item 81 : wait와 notify보다는 동시성 유틸리티를 애용하라

## 들어가기

지금은 wait와 notify를 사용해야 할 이유가 많이 줄었&#xB2E4;**. 자바 5에서 도입된 고수준의 동시성 유틸리티가 이전이라면 wait와 notify로 하드코딩해야 했던 전형적인 일들을 대신 처리해주기 때문이다**. wait와 notifiy는 올바르게 사용하기가 아주 까다로우니 고수준 동시성 유틸리티를 사용하자.

## java.util.concurrent 패키지

`java.util.concurrent` 패키지는 고수준의 동시성 유틸리티를 제공한다. 이 패키지는 크게 **세 가지 주요 범주**로 나눌 수 있다:

1. **실행자 프레임워크 (Executor Framework)**
2. **동시성 컬렉션 (Concurrent Collections)**
3. **동기화 장치 (Synchronizers)**

각각의 기능과 동작 방식을 자세히 살펴보자.

***

### 1. 동시성 컬렉션 (Concurrent Collections)

**동시성 컬렉션**은 `List`, `Queue`, `Map`과 같은 표준 컬렉션 인터페이스에 **동시성 기능**을 추가한 컬렉션이다.

* 내부적으로 동기화를 수행하며, 외부에서 별도로 락을 걸어 동기화를 시도하면 오히려 성능이 저하된다. 즉, <mark style="color:red;">동시성 컬렉션에서 동시성을 무력화하는 건 불가능하며 , 외부에서 락을 추가로 사용하면 오히려 속도가 느려 진다</mark>
* 여러 메서드를 원자적으로 묶어 호출하는 일 역시 불가능하다.
* **동기화된 컬렉션** 대신 **동시성 컬렉션**을 사용하는 것이 더 효율적이다.

#### **상태 의존적 메서드**

동시성 컬렉션은 원자적으로 여러 메서드를 호출하는 것이 불가능하다. 이 문제를 해결하기 위해 여러 동작을 하나의 원자적 동작으로 묶는 **상태 의존적 메서드**가 추가되었다.

**`putIfAbsent` 메서드 예시**

> 이 메서드 덕에 스레드 안전한 정규화 맵 (canonicalizing map)을 쉽게 구현할 수 있다.

```java
private static final ConcurrentMap<String, String> map = new ConcurrentHashMap<>();

public static String intern(String s) {
    String previousValue = map.putIfAbsent(s, s);
    return previousValue == null ? s : previousValue;
}
```

**설명**

1. **`map.putIfAbsent(s, s)`**
   * `s`라는 키가 존재하지 않으면 `s`를 맵에 추가하고 `null`을 반환
   * `s`라는 키가 이미 존재하면 기존 값을 반환
2. **`return` 문**
   * `previousValue`가 `null`인 경우 → 입력된 문자열 `s`를 반환.
   * `previousValue`가 `null`이 아닌 경우 → 이미 존재하는 값을 반환.

> 아직 개선할 게 남았다. ConcurrentHashMap은 get 같은 검색 기능에 최적화되었다. 따라서 <mark style="color:red;">get을 먼저 호출하여 필요할 때만 putlfAbsent를 호출하면 더 빠르다.</mark>&#x20;



`putIfAbsent`는 `ConcurrentMap`에 추가된 메서드로, **키가 없을 때만 값을 추가**하는 동작을 수행한다. 기존 값이 있으면 그 값을 반환하고, 없으면 `null`을 반환한다.

아래는 `String.intern` 메서드를 `ConcurrentHashMap`으로 흉내 낸 예제 코드다:

```java
// ConcurrentHashMap을 사용해 문자열을 정규화하는 메서드
private static final ConcurrentMap<String, String> map = new ConcurrentHashMap<>();

public static String intern(String s) {
    // 기존 값을 가져옴
    String result = map.get(s);
    if (result == null) {
        // 키가 없으면 값을 추가
        result = map.putIfAbsent(s, s);
        if (result == null) {
            result = s;
        }
    }
    return result;
}
```

**코드 설명:**

* `get`: 먼저 키에 해당하는 값을 확인한다.
* `putIfAbsent`: 키가 없으면 값을 추가하며, 원자적으로 실행된다.
* **결과**: 스레드 안전한 정규화 맵을 구현할 수 있다.

> **Tip**: `ConcurrentHashMap`은 검색 기능에 최적화되어 있으므로, 먼저 `get`을 호출하면 성능이 더욱 향상된다.

**왜 동시성 컬렉션이 중요한가?**

* ConcurrentHashMap은 동시성이 뛰어나며 속도도 무척 빠르다.
  * 내 컴퓨터에서 이 메서드는 String, intern 보다 6배나 빠르다(하지만 String, intern 에는 오래 실행 되는 프로그램 에서 **메모리 누수를 방지하는 기술도 들어가 있음을 감안**하자)
* `Collections.synchronizedMap` 대신 `ConcurrentHashMap`을 사용하면 성능이 크게 개선된다.
  * 동기화된 맵을 **동시성 맵으로 교체하는 것**만으로 동시성 애플리케이션의 성능은 극적으로 개선된다.
* 내부 동기화를 통해 높은 동시성을 제공하며, 외부에서 추가적인 동기화가 필요하지 않다.

***

> `컬렉션 인터페이스` 중 일부는 **작업이 성공적으로 완료될 때까지 기다리도록** (즉, 차단되도록) 확장되었다.

#### **BlockingQueue와 CountDownLatch 정리 및 예제 코드**

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**1. BlockingQueue**

**정의**:\
`BlockingQueue`는 큐의 확장으로, 비어있거나 가득 찬 상태에 따라 작업이 차단되도록 지원한다.

* **생산자-소비자 패턴**에 매우 유용하며, 스레드 간 안전하게 데이터를 공유할 수 있도록 설계되었다.
* `take()` 메서드는 큐가 비어 있을 경우 원소가 추가될 때까지 기다린다.
* `put()` 메서드는 큐가 가득 찼을 경우 공간이 생길 때까지 기다린다.

#### **BlockingQueue 예제 코드: 생산자-소비자 패턴**

<pre class="language-java"><code class="lang-java">import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;

public class ProducerConsumerExample {
    private static final int CAPACITY = 5; // 큐 용량 설정
    private static BlockingQueue&#x3C;Integer> queue = new ArrayBlockingQueue&#x3C;>(CAPACITY);

    public static void main(String[] args) {
        // 생산자 스레드
        Thread producer = new Thread(() -> {
            try {
                for (int i = 1; i &#x3C;= 10; i++) {
                    System.out.println("생산자: " + i + " 추가");
                    queue.put(i); // 큐에 원소를 추가 (가득 차면 대기)
                }
<strong>            } catch (InterruptedException e) {
</strong>                Thread.currentThread().interrupt();
            }
        });

        // 소비자 스레드
        Thread consumer = new Thread(() -> {
            try {
                while (true) {
                    Integer value = queue.take(); // 큐에서 원소를 꺼냄 (비어있으면 대기)
                    System.out.println("소비자: " + value + " 처리");
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        producer.start();
        consumer.start();
    }
}
</code></pre>

**2. CountDownLatch**

**정의**:\
`CountDownLatch`는 일회성 동기화 장치로, 지정된 작업이 완료될 때까지 다른 스레드의 실행을 차단한다.

* **`countDown()`**: 호출될 때마다 카운트가 감소한다.
* **`await()`**: 카운트가 `0`이 될 때까지 현재 스레드를 차단한다.

CountDownLatch는 일회성 장벽으로, 하나 이상의 스레드가 또 다른 하나 이상의 스레드 작업이 끝날 때까지 기다리게 하는 역할을 한다.

생성자로 받는 int 값을 받으며, 이 값이 countDown 메서드를 몇 번 호출해야 대기 중인 스레드를 깨우는지를 결정한다.

<figure><img src="https://blog.kakaocdn.net/dn/nAnjp/btrj87soXbB/Ct4GPgMjGO9kb8gkGWUT71/img.png" alt=""><figcaption></figcaption></figure>

#### **CountDownLatch 예제 코드**

```java
import java.util.concurrent.CountDownLatch;

public class CountDownLatchExample {
    public static void main(String[] args) {
        int numberOfWorkers = 3;
        CountDownLatch latch = new CountDownLatch(numberOfWorkers);

        // 작업자 스레드
        for (int i = 1; i <= numberOfWorkers; i++) {
            new Thread(new Worker(latch, i)).start();
        }

        try {
            latch.await(); // 모든 작업자 스레드의 작업이 끝날 때까지 대기
            System.out.println("모든 작업 완료. 메인 스레드 진행.");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }

    static class Worker implements Runnable {
        private final CountDownLatch latch;
        private final int workerId;

        Worker(CountDownLatch latch, int workerId) {
            this.latch = latch;
            this.workerId = workerId;
        }

        @Override
        public void run() {
            System.out.println("작업자 " + workerId + " 작업 시작");
            try {
                Thread.sleep(1000 * workerId); // 각 스레드가 다른 시간에 종료되도록 설정
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            System.out.println("작업자 " + workerId + " 작업 완료");
            latch.countDown(); // 카운트 다운
        }
    }
}
```

📚 **정리**

#### **BlockingQueue**

* **특징**: 큐가 비어있거나 가득 차면 작업이 차단됨.
* **사용 사례**:
  * **생산자-소비자 패턴**
  * 작업 큐 (ThreadPoolExecutor 내부에서 사용됨)

#### **CountDownLatch**

* **특징**: 지정된 개수만큼 `countDown()`이 호출될 때까지 다른 스레드의 작업을 차단함.
* **사용 사례**:
  * 다수의 작업을 병렬로 처리한 후 하나의 작업을 시작할 때
  * 특정 스레드들이 모두 완료될 때까지 대기할 때

***

### 2. 동기화 장치 (Synchronizers)

**동기화 장치**는 여러 스레드 간의 작업을 조율하거나 협력할 수 있도록 도와주는 도구이다. 대표적인 동기화 장치는 다음과 같다:

1. **CountDownLatch**: 여러 스레드의 작업이 끝날 때까지 대기.
2. **Semaphore**: 지정된 수의 스레드만 동시에 접근할 수 있도록 제한.
3. **CyclicBarrier**: 여러 스레드가 동시에 만나도록 조율.
4. **Exchanger**: 두 스레드 간 데이터를 교환.
5. **Phaser**: 다중 단계 작업 조율에 사용되는 강력한 동기화 장치.

#### **CountDownLatch 사용법**

**CountDownLatch**는 **일회성 동기화 장치**로, 정해진 횟수만큼 `countDown()`이 호출될 때까지 스레드가 대기한다. 즉, 하나 이상의 스레드가 또 다른 하나 이상의 스레드 작업이 끝날 때까지 기다리게 한다.

**동시 실행 시간 측정 예제**

이 프레임워크는 메서드 하나로 구성되며, 이 메서드는 동작들을 실행할 실행자와 동작을 몇 개나 동시에 수행 할 수 있는지를 뜻하는 <mark style="color:red;">동시성 수준(concurrency)을 매개변수로 받는다.</mark>&#x20;

> 타이머 스레드가 시계를 시작하기 전에 모든 작업자 스레드는 동작을 수행할 준비 를 마친다. 마지막 작업자 스레드가 준비를 마치면 타이머 스레드가 ‘시작 방아쇠’를 당겨 작업자 스레드들이 일을 시작하게 한다. 마지막 작업자 스레드가 동작을 마치자마자 타이머 스레드는 시계를 멈춘다. 이상의 기능을 wait 와 notify만으로 구현하려면 아주 난해하고 지저분한 코드가 탄생하지만, `CountDownLatch`를 쓰면 놀랍도록 직관적으로 구현할 수 있다.

아래는 여러 작업자의 준비 및 동시 실행 시간을 측정하는 코드이다:

```java
import java.util.concurrent.*;

public class CountDownLatchTest {
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(5);
        try {
            long result = time(executorService, 3, () -> System.out.println("Hello"));
            System.out.println("Execution time: " + result + " nanoseconds");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            executorService.shutdown();
        }
    }

    public static long time(Executor executor, int concurrency, Runnable action) throws InterruptedException {
        CountDownLatch ready = new CountDownLatch(concurrency);
        CountDownLatch start = new CountDownLatch(1);
        CountDownLatch done = new CountDownLatch(concurrency);

        for (int i = 0; i < concurrency; i++) {
            executor.execute(() -> {
                ready.countDown();
                try {
                    start.await();
                    action.run();
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                } finally {
                    done.countDown();
                }
            });
        }

        ready.await(); // 모든 작업자가 준비될 때까지 대기
        long startNanos = System.nanoTime();
        start.countDown(); // 작업 시작 신호
        done.await(); // 모든 작업이 완료될 때까지 대기
        return System.nanoTime() - startNanos;
    }
}
```

**코드 설명:**

* **`ready`**: 작업자들이 준비되었음을 알림.
* **`start`**: 모든 작업자에게 작업 시작 신호를 보냄.
* **`done`**: 작업 완료 알림.
* **결과**: 여러 스레드를 동시에 시작하고 완료 시간을 정확히 측정한다.

> 시간 간격을 잴 때는 항상 System.currentTiune1Vlillis가 아닌 System.nanoTime 을 사용하자. System.nanoTime은 더 정확하고 정밀하며 시스템의 실시간 시계 의 시간 보정에 영향받지 않는다.

**부가 설명**

`CountDownLatch`를 3개 사용하여 **타이머 스레드**와 **작업자 스레드** 간의 동기화를 관리한다.

1. **ready 래치**: 작업자 스레드가 준비가 완료되었음을 타이머 스레드에 알림.
2. **start 래치**: 타이머 스레드가 모든 작업자 스레드의 준비를 확인한 후, 작업 시작을 알림.
3. **done 래치**: 작업자 스레드가 작업을 완료했음을 타이머 스레드에 알림.

> **스레드의 준비 → 작업 시작 → 작업 완료** 과정을 **정확한 시점**에 맞추어 동기화

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

***

### 3. wait와 notify 사용 시 주의사항

<figure><img src="../../../../.gitbook/assets/image (97).png" alt=""><figcaption></figcaption></figure>

**1. 대기 반복문(wait loop) 관용구를 사용하라**

* **반복문 안에서 `wait()`를 호출해야 한다.**
* **대기 전**: 조건이 이미 충족되었다면 `wait()`를 건너뛴다 → **응답 불가 상태 예방**
* **대기 후**: 조건이 충족되지 않았으면 다시 `wait()`로 돌아간다 → **안전 실패 방지**

> **반복문 밖에서 절대 `wait()`를 호출하지 말라.**

새로운 코드에서는 **`wait`와 `notify` 대신 동시성 유틸리티를 사용**하는 것이 좋다. 하지만 레거시 코드에서 사용할 수밖에 없다면 다음과 같이 사용해야 한다:&#x20;

```java
synchronized (obj) {
    while (조건이 충족되지 않았다) {
        obj.wait(); // 현재 스레드를 대기 상태로 전환
    }
    // 조건이 충족되었을 때 수행할 동작
}
```

**2. 조건이 충족되지 않아도 깨어날 수 있는 상황들**

1. **다른 스레드의 상태 변경**
   * `notify()` 호출 후 다른 스레드가 락을 얻어 상태를 변경할 수 있다.
2. **실수 또는 악의적 `notify` 호출**
   * 외부에 노출된 객체를 락으로 사용하면 다른 스레드가 `notify`를 실수로 호출할 수 있다.
3. **허위 각성(Spurious Wakeup)**
   * `notify`가 호출되지 않았는데도 스레드가 깨어나는 현상. 이는 드물지만 발생할 수 있다. 스레드가 `notify` 없이도 깨어나는 현상을 **허위 각성**이라고 한다. 이를 방지하려면 `wait`를 반드시 **반복문 내부**에서 호출해야 한다.
4. **과도한 `notifyAll` 호출**
   * 조건이 충족되지 않은 스레드까지 모두 깨어날 수 있다.
   * 하지만 깨어난 스레드가 조건을 검사하고 충족되지 않으면 다시 대기하게 되므로 프로그램의 정확성은 유지된다.

**3. notify와 notifyAll 중 무엇을 사용해야 하는가?**

#### **일반 원칙**

* **항상 `notifyAll()`을 사용하라.**
  * 모든 대기 중인 스레드가 깨어나 조건을 확인하게 되므로 **정확한 결과**를 보장한다.
  * 일부 스레드가 불필요하게 깨어나더라도 이는 성능 문제일 뿐 프로그램의 정확성에 영향을 미치지 않는다.

#### **notify 사용 시 주의사항**

* **단 하나의 스레드만 조건을 충족할 때** 최적화를 위해 `notify()`를 사용할 수 있다.
* 하지만, 다음과 같은 위험이 있다:
  * 다른 스레드가 실수로 **중요한 `notify`를 삼켜버리면** 필수적으로 깨어나야 할 스레드가 **영원히 대기 상태에 빠진다.**
  * 이는 외부에 공개된 객체에 대해 악의적으로 `wait`를 호출하는 경우 더욱 치명적이다.

#### **`4. notify`와 `notifyAll`의 차이점**

* **`notify`**: 대기 중인 스레드 중 하나만 깨운다.
* **`notifyAll`**: 모든 대기 중인 스레드를 깨운다.

> **안전한 선택**: 일반적으로 `notifyAll`을 사용하는 것이 더 안전하다. 조건이 만족되지 않은 스레드들은 다시 대기 상태로 돌아가므로 문제가 없다.

#### **5. 핵심정리**

* `wait`와 `notify`는 **동시성의 어셈블리 언어**에 비유된다.
  * `java.util.concurrent`는 고수준 언어로, 더 안전하고 강력한 동시성 유틸리티를 제공한다.
* **새로운 코드**에서는 `wait`와 `notify`를 **사용할 필요가 거의 없다.**
* 레거시 코드 유지보수 시:
  * `wait()`는 **반드시 while 문 안에서** 호출하라.
  * `notifyAll()`을 사용하라. `notify`는 극히 조심해서 사용해야 한다.



***

### 📚 결론

`java.util.concurrent` 패키지는 복잡한 동시성 문제를 해결하기 위한 고수준 유틸리티를 제공한다.

1. **동시성 컬렉션**: `ConcurrentHashMap`과 같은 컬렉션은 높은 동시성을 제공하며 외부 동기화가 필요 없다.
2. **동기화 장치**: `CountDownLatch`, `Semaphore`, `Phaser` 등을 통해 스레드 간 협력 및 작업 조율을 할 수 있다.
3. **레거시 코드**: `wait`와 `notify`를 사용해야 한다면 반드시 반복문과 함께 사용하며 `notifyAll`을 권장한다.

새로운 코드에서는 동시성 유틸리티를 적극 활용하여 **안전하고 효율적인** 동시성 프로그래밍을 구현하자.



> 참고 \
> \- [https://jjingho.tistory.com/131](https://jjingho.tistory.com/131)
