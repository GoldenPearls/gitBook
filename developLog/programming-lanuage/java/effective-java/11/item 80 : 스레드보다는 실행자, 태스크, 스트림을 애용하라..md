# item 80 : 스레드보다는 실행자, 태스크, 스트림을 애용하라.

## 실행자 프레임워크 (Executor Framework)

<figure><img src="../../../../.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

`java.util.concurrent` 패키지에는 **인터페이스 기반의 유연한 태스크 실행 기능**을 제공하는 **실행자 프레임워크**(Executor Framework)가 있다. 이 프레임워크는 스레드와 작업 큐를 직접 다룰 필요 없이 효율적으로 태스크를 관리하고 실행할 수 있게 도와준다.

과거에는 단순한 작업 큐를 만들기 위해 수많은 코드를 작성해야 했지만, 실행자 프레임워크를 사용하면 아래와 같이 간단하게 작업 큐를 생성하고 사용할 수 있다.

> 이전에 단순한 작업 큐(Work queue)의 경우,

그 클래스는 클라이언트가 요청한 작업을 백그라운드 스레드에 위임해 비동기적으로 처리해줬&#xB2E4;**. 작업 큐가 필요 없어지면 클라이언트는 큐에 중단을 요청할 수 있고, 그러면 큐는 남아 있는 작업을 마저 완료한 후 스스로 종료**한다. 예시용 의 간단한 코드였지만 책 한 페이지를 가득 메웠는데, <mark style="color:red;">안전 실패나 웅답 불가가 될 여지를 없애는 코드를 추가해야 했기 때문</mark>이다.&#x20;

```java
// 큐를 생성한다.
ExecutorService exec = Executors.newSingleThreadExecutor();

// 태스크 실행
exec.execute(runnable);

// 실행자 종료
exec.shutdown();
```

이 단순한 코드 한 줄로도 안정적이고 신뢰할 수 있는 작업 큐를 생성할 수 있으며, 작업의 종료 시점까지의 관리도 가능하다.

## 실행자 프레임워크의 주요 기능

<figure><img src="../../../../.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>

### 1. **특정 태스크가 완료되기를 기다리기**

`submit()` 메서드와 `get()`을 사용하면 특정 태스크의 완료를 기다릴 수 있다. 이 메서드는 결과를 반환하며, 태스크가 끝날 때까지 호출자는 대기 상태에 놓이게 된다.

```java
ExecutorService exec = Executors.newSingleThreadExecutor();
exec.submit(() -> System.out.println("Task Done")).get(); // 끝날 때까지 기다린다.
```

이 방법은 주로 결과를 필요로 하는 작업이나 반드시 완료되어야 하는 선행 작업이 있을 때 유용하다. 하지만 대기 시간이 길어질 경우, 애플리케이션의 응답성이 저하될 수 있으므로 주의가 필요하다.

### 2. **태스크 모음 실행**

실행자 프레임워크는 여러 개의 태스크를 동시에 실행하고 결과를 관리할 수 있는 메서드를 제공한다.

**모든 태스크의 완료를 기다리기 (`invokeAll`)**

`invokeAll()`은 제출된 모든 태스크가 완료될 때까지 기다린다.

```java
List<Callable<String>> tasks = Arrays.asList(
    () -> "Task 1", 
    () -> "Task 2"
);

List<Future<String>> futures = exec.invokeAll(tasks);
System.out.println("All Tasks done");
```

**태스크 중 하나라도 완료되면 반환 (`invokeAny`)**

`invokeAny()`는 여러 태스크 중 가장 먼저 완료된 태스크의 결과만 반환하고 나머지 태스크는 취소한다.

```java
String result = exec.invokeAny(tasks);
System.out.println("Any Task done: " + result);
```

이 방법은 실행 시간이 불확실한 태스크에서 유용하게 사용된다.

### 3. **실행자 서비스가 종료하기를 기다리기**

`awaitTermination()`을 사용하면 실행자 서비스가 종료될 때까지 대기할 수 있다. 특정 시간 동안만 대기할 수도 있다.

```java
Future<String> future = exec.submit(() -> "Task Result");
exec.shutdown();
exec.awaitTermination(10, TimeUnit.SECONDS);
System.out.println("Executor terminated");
```

이 방식은 실행자 서비스의 종료를 명확하게 제어할 수 있으므로 리소스를 효과적으로 관리할 수 있다.

### 4. **완료된 태스크들의 결과를 차례로 받기**

`ExecutorCompletionService`는 태스크가 완료된 순서대로 결과를 반환한다. 이는 태스크 완료 시점을 예측할 수 없는 상황에서 매우 유용하다.

```java
final int MAX_SIZE = 3;
ExecutorService executorService = Executors.newFixedThreadPool(MAX_SIZE);
ExecutorCompletionService<String> ecs = new ExecutorCompletionService<>(executorService);

// 태스크 제출
List<Future<String>> futures = new ArrayList<>();
futures.add(ecs.submit(() -> "Task 1"));
futures.add(ecs.submit(() -> "Task 2"));
futures.add(ecs.submit(() -> "Task 3"));

// 완료된 결과 받기
for (int i = 0; i < MAX_SIZE; i++) {
    try {
        String result = ecs.take().get();
        System.out.println(result);
    } catch (InterruptedException | ExecutionException e) {
        e.printStackTrace();
    }
}
executorService.shutdown();
```

### 5. **태스크를 특정 시간에 또는 주기적으로 실행**

`ScheduledThreadPoolExecutor`는 특정 시간 이후 또는 주기적으로 태스크를 실행한다.

**지정 시간 이후 태스크 실행**

```java
ScheduledThreadPoolExecutor executor = new ScheduledThreadPoolExecutor(1);
executor.schedule(() -> System.out.println("Task after delay"), 5, TimeUnit.SECONDS);
```

**주기적으로 태스크 실행**

```java
executor.scheduleAtFixedRate(() -> {
    System.out.println(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")
            .format(LocalDateTime.now()));
}, 0, 2, TimeUnit.SECONDS);
```

출력 예시:

```
2019-09-30 23:11:22
2019-09-30 23:11:24
2019-09-30 23:11:26
...
```

## 스레드 풀 종류와 사용법

<figure><img src="../../../../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

큐를 둘 이상의 스레드가 처리하게 하고 싶다면 간단히 다른 정적 팩터 리를 이 용하여 다른 종류의 실행자 서비스(스레드 풀)를 생성하면 된다.&#x20;

스레드 풀의 스레드 개수는 고정할 수도 있고 필요에 따라 늘어나거나 줄어들게 설정할 수 도 있다. 여러분에게 필요한 실행자 대부분은 **java.util.concurrent.Executors 의 정적 팩터리들을 이용해 생성할 수 있을 것이다.** 평범하지 않은 실행자를 원한다면 `ThreadPoolExecutor 클래스`를 직접 사용해도 된다. 이 클래스로는 스 레드 풀 동작을 결정하는 거의 모든 속성을 설정할 수 있다.

#### 1. Executors.newCachedThreadPool

* 작은 프로그 램이나 가벼운 서버
* &#x20;특별히 설정할 게 없고 일반적인 용도에 적합하게 동작한다.
* CachedThreadPool은 무거운 프로덕션 서버에는 좋지 못하다!
* Cached ThreadPool에서는 요청받은 태스크들이 큐에 쌓이지 않고 즉시 스레드에 위 임 돼 실행된다.

> 가용한 스레드가 없다면 새로 하나를 생성한다. 서버가 아주 무 겁다면 CPU 이용률이 100%로 치닫고, 새로운 태스크가 도착하는 족족 또 다 른 스레드를 생성하며 상황을 더욱 악화시킨다

#### **2. `newCachedThreadPool`**

* 가벼운 작업에 적합하며 스레드를 유연하게 재사용한다.
* 큐를 사용하지 않고 스레드를 즉시 할당하며, 가용 스레드가 없으면 새 스레드를 생성한다.
* 무거운 서버에서는 스레드 수가 급격히 늘어날 수 있으므로 주의해야 한다.

#### 3. **`newFixedThreadPool`**

* 스레드 개수를 고정해 CPU 자원 낭비를 최소화한다.
* 무거운 프로덕션 서버에 적합하며 예측 가능한 성능을 보장한다.

#### 4. **`ThreadPoolExecutor` 직접 제어**

* 스레드 풀의 동작을 세부적으로 제어할 수 있다.
* 작업 큐, 스레드 생성 정책 등을 필요에 맞게 커스터마이징할 수 있다.

## 실행자 프레임워크(Executor Framework)

`java.util.concurrent` 패키지의 실행자 프레임워크(Executor Framework)는 스레드 관리와 작업 실행을 효율적으로 분리하고 관리하는 기능을 제공한다. 이를 통해 스레드를 직접 다루는 복잡성을 줄이고, 태스크 실행을 유연하게 관리할 수 있다.

### 스레드를 직접 다루는 것을 피해야 하는 이유

<figure><img src="../../../../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

스레드를 직접 다루면 `Thread`가 **작업 단위**와 **실행 메커니즘**을 모두 담당하게 되어 코드가 복잡해진다. 반면, 실행자 프레임워크를 사용하면 **작업 단위**와 **실행 메커니즘**이 분리된다.

#### **작업 단위(Task)**

* 작업 단위를 나타내는 핵심 추상 개념이 바로 태스크(Task)이다.
* 태스크에는 두 가지 유형이 있다:
  * **`Runnable`**: 값을 반환하지 않는 태스크
  * **`Callable`**: 값을 반환하며 예외를 던질 수 있는 태스크

#### **실행 메커니즘**

* 태스크를 수행하는 일반적인 메커니즘이 실행자 서비스(ExecutorService)이다.
* 실행자 서비스에 태스크를 넘기면 **수행 정책**을 유연하게 선택할 수 있다.
* 필요에 따라 언제든지 태스크 수행 방식을 변경할 수 있다.

> **핵심**: 컬렉션 프레임워크가 데이터 관리를 담당하듯, 실행자 프레임워크는 작업 수행을 담당한다.

***

### 포크-조인 프레임워크 (Fork-Join Framework)

자바 7부터 실행자 프레임워크는 **포크-조인(Fork-Join)** 기능을 지원하도록 확장되었다.

#### **포크-조인 태스크(ForkJoinTask)**

* 큰 작업을 **여러 개의 작은 하위 태스크**로 나누어 실행할 수 있다.
* 태스크를 **포크(Fork)** 하여 나눈 후, 완료된 결과를 **조인(Join)** 하는 방식으로 동작한다.

#### **ForkJoinPool**

* 포크-조인 태스크를 실행하기 위해 **ForkJoinPool**이라는 특별한 실행자 서비스가 사용된다.
* **워크 스틸링(Work-Stealing)** 기법을 사용하여 효율적으로 태스크를 분산 처리한다.
  * 먼저 작업을 끝낸 스레드는 다른 스레드의 남은 작업을 가져와 처리한다.

#### **포크-조인의 장점**

* 모든 스레드가 바쁘게 움직여 **CPU 활용도를 극대화**한다.
* **높은 처리량**과 **낮은 지연 시간**을 달성할 수 있다.

#### **예제**

```java
ForkJoinPool pool = new ForkJoinPool();

// 포크-조인 태스크를 실행
pool.invoke(new RecursiveTask<Integer>() {
    @Override
    protected Integer compute() {
        return 1; // 태스크 예제
    }
});
```

#### **포크-조인 스트림과 병렬 처리**

* 병렬 스트림(`Stream.parallel()`)은 내부적으로 포크-조인 풀을 사용하여 병렬 작업을 처리한다.
* 포크-조인 프레임워크를 직접 작성하는 것은 복잡할 수 있지만, 병렬 스트림을 사용하면 **적은 노력**으로 그 이점을 얻을 수 있다.

> 단, 포크-조인 프레임워크는 **포크-조인에 적합한 작업 구조**에서만 효과적이다.

***

## 📚 결론

실행자 프레임워크는 스레드 관리와 태스크 실행을 분리하여 복잡성을 줄이고 효율적으로 작업을 관리할 수 있도록 돕는다.

1. **스레드 관리와 작업 단위를 분리**하여 코드 유지보수가 용이해진다.
2. 다양한 스레드 풀 옵션을 제공해 상황에 맞게 선택할 수 있다.
3. **포크-조인 프레임워크**를 통해 CPU를 최대한 활용하는 병렬 작업이 가능하다. 큰 작업을 나누어 병렬로 처리하고 CPU 활용도를 극대화한다.
4. **태스크의 유형**: `Runnable`과 `Callable`을 사용해 작업을 정의하고 실행자 서비스에 전달한다.4102..102
5. **스레드를 직접 다루지 말자**: 실행자 프레임워크를 사용하면 작업 단위와 실행 메커니즘이 분리되어 유지보수성과 유연성이 높아진다.

이외에도 실행자 프레임워크는 다양한 기능을 제공하며, 필요에 따라 정책과 실행 방식을 유연하게 조정할 수 있다. **스레드를 직접 다루는 대신 실행자 프레임워크를 사용하여 더 안전하고 효율적인 프로그램을 작성하자.**

> 더 깊이 있는 내용은 **『자바 병렬 프로그래밍』** (Goetz, 2008)을 참고하기 바란다.
