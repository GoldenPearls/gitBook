# item 80 : 스레드보다는 실행자, 태스크, 스트림을 애용하라.

## 실행자 프레임워크 (Executor Framework)

`java.util.concurrent` 패키지에는 **인터페이스 기반의 유연한 태스크 실행 기능**을 제공하는 **실행자 프레임워크**(Executor Framework)가 있다. 이 프레임워크는 스레드와 작업 큐를 직접 다룰 필요 없이 효율적으로 태스크를 관리하고 실행할 수 있게 도와준다.

과거에는 단순한 작업 큐를 만들기 위해 수많은 코드를 작성해야 했지만, 실행자 프레임워크를 사용하면 아래와 같이 간단하게 작업 큐를 생성하고 사용할 수 있다.

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

#### 1. **`newCachedThreadPool`**

* 가벼운 작업에 적합하며 스레드를 유연하게 재사용한다.
* 큐를 사용하지 않고 스레드를 즉시 할당하며, 가용 스레드가 없으면 새 스레드를 생성한다.
* 무거운 서버에서는 스레드 수가 급격히 늘어날 수 있으므로 주의해야 한다.

#### 2. **`newFixedThreadPool`**

* 스레드 개수를 고정해 CPU 자원 낭비를 최소화한다.
* 무거운 프로덕션 서버에 적합하며 예측 가능한 성능을 보장한다.

#### 3. **`ThreadPoolExecutor` 직접 제어**

* 스레드 풀의 동작을 세부적으로 제어할 수 있다.
* 작업 큐, 스레드 생성 정책 등을 필요에 맞게 커스터마이징할 수 있다.

## 포크-조인 프레임워크 (Fork-Join Framework)

자바 7부터 도입된 **포크-조인 프레임워크**는 작업을 작은 단위로 나누어 병렬로 실행하는 기능을 제공한다.

#### **특징**

* **ForkJoinTask**: 작업을 작은 하위 태스크로 나눈다.
* **ForkJoinPool**: 하위 태스크를 처리하는 실행자 서비스.
* **워크 스틸링**: 남은 작업이 있는 스레드의 작업을 다른 스레드가 가져와 처리한다.

```java
ForkJoinPool pool = new ForkJoinPool();

pool.invoke(new RecursiveTask<Integer>() {
    @Override
    protected Integer compute() {
        return 1; // 예제 태스크
    }
});
```

포크-조인 프레임워크는 CPU 활용도를 극대화하고, 높은 처리량과 낮은 지연 시간을 보장한다.

***

### 결론

실행자 프레임워크는 스레드 관리와 태스크 실행을 효율적으로 처리하는 도구를 제공한다.

1. **스레드 관리와 작업 단위를 분리**하여 코드 유지보수가 용이해진다.
2. 다양한 스레드 풀 옵션을 제공해 상황에 맞게 선택할 수 있다.
3. **포크-조인 프레임워크**를 통해 CPU를 최대한 활용하는 병렬 작업이 가능하다.

스레드를 직접 다루는 대신 실행자 프레임워크를 사용하면 복잡성을 줄이고 안정적으로 동작하는 애플리케이션을 작성할 수 있다.
