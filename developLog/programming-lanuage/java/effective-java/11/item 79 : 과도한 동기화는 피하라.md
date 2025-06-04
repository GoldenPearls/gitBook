# item 79 : 과도한 동기화는 피하라

> 아이템 78에서 충분하지 못한 동기화의 피해를 다뤘다면, 이번 아이템에서는 반대 상황을 다룬다. **과도한 동기화는 성능을 떨어뜨리고, 교착상태에 빠뜨리 고, 심지어 예측할 수 없는 동작을 낳기도 한다.**

## 1.  동기화 메서드와 외계인 메서드

교착상태와 안전 실패를 방지하려면 <mark style="color:red;">동기화된 메서드나 동기화 블록 내부에서 클라이언트에 제어를 절대 양도하면 안 된다.</mark> 특히 동기화된 영역 내부에서는 다음과 같은 행동을 피해야 한다

### 1) 동기화된 영역 내부에서 피해야 할 행동  (외계인 메서드)

<figure><img src="../../../../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

1. **재정의 가능한 메서드 호출 금지**
2. **클라이언트가 제공한 함수 객체 호출 금지** (예: `아이템 24`)

이러한 메서드들은 `외계인 메서드(alien method)`라고 부른다. 외계인 메서드는 어떤 동작을 수행할지 예측할 수 없고, 통제도 불가능하다. 동기화된 영역에서 외계인 메서드를 호출하면 다음과 같은 문제가 발생할 수 있다

### 2) 동기화 된 영역에서 외계인 메서드 호출 시 발생하는 문제

<figure><img src="../../../../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

1. 예외 발생

* 동기화된 영역에서 외계인 메서드를 호출하면 예기치 않은 상태가 발생하거나 예상치 못한 예외가 발생할 수 있다. 이는 프로그램의 안정성을 심각하게 위협할 수 있다.

2. **교착상태**

* 교착상태는 <mark style="color:red;">두 개 이상의 스레드가 서로의 자원을 기다리며 무한히 멈춰 있는 상태</mark>를 말한다.&#x20;
* 동기화된 영역에서 외계인 메서드를 호출하면, 락을 쥔 상태로 다른 락을 기다리게 될 경우 교착상태에 빠질 위험이 크다. 이는 시스템이 응답하지 않게 되는 치명적인 결과를 초래할 수 있다.

3. 데이터 손상

* 동기화된 데이터에 접근하는 동안 외계인 메서드가 예기치 않게 데이터를 수정하면 일관성이 깨질 수 있다.&#x20;
* 이는 데이터의 부정확성을 초래하며, 특히 동시성 환경에서 치명적인 버그로 이어질 가능성이 크다.

외계인 메서드 호출로 인한 문제를 방지하려면 동기화된 영역 내부에서 수행되는 작업을 최소화해야 한다.&#x20;

> 동기화 블록은 락을 얻고, 공유 데이터를 검사하거나 수정한 후, 곧바로 락을 해제하는 방식으로 설계되어야 한다.

***

### 3) 잘못된 코드 예제: 외계인 메서드를 호출하는 경우

<figure><img src="../../../../.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>

다음은 `집합(Set)`을 감싸는 래퍼 클래스이다. 이 클래스는 관찰자 패턴을 사용하여 집합에 원소가 추가될 때 알림을 보낸다. 이 예제는 잘못된 방식으로 동기화된 영역 내부에서 외계인 메서드를 호출하는 상황을 보여준다.

```java
public class ObservableSet<E> extends ForwardingSet<E> {
    // 관찰자를 저장하는 리스트
    private final List<SetObserver<E>> observers = new ArrayList<>();

    // 생성자: 기존 Set 객체를 감싼다
    public ObservableSet(Set<E> set) {
        super(set);
    }

    // 관찰자를 추가하는 메서드
    public void addObserver(SetObserver<E> observer) {
        synchronized (observers) {
            observers.add(observer);
        }
    }

    // 관찰자를 제거하는 메서드
    public boolean removeObserver(SetObserver<E> observer) {
        synchronized (observers) {
            return observers.remove(observer);
        }
    }

    // 새로운 원소가 추가되었음을 관찰자들에게 알리는 메서드
    private void notifyElementAdded(E element) {
        synchronized (observers) {
            for (SetObserver<E> observer : observers) {
                observer.added(this, element); // 외계인 메서드 호출
            }
        }
    }

    // Set 인터페이스의 add 메서드를 재정의하여 알림 기능 추가
    @Override
    public boolean add(E element) {
        boolean added = super.add(element);
        if (added) {
            notifyElementAdded(element);
        }
        return added;
    }
}

// 관찰자 인터페이스: 원소가 추가되었을 때 호출될 메서드 정의
@FunctionalInterface
public interface SetObserver<E> {
    void added(ObservableSet<E> set, E element);
}
```

**문제 상황**

다음 코드는 `ObservableSet`의 관찰자를 추가하고, 특정 조건에서 관찰자를 제거한다. 이 과정에서 문제가 발생한다.

```java
ObservableSet<Integer> set = new ObservableSet<>(new HashSet<>());
// 관찰자 추가
set.addObserver(new SetObserver<>() {
    @Override
    public void added(ObservableSet<Integer> s, Integer e) {
        System.out.println(e);
        if (e == 23) {
            s.removeObserver(this); // 외계인 메서드 호출
        }
    }
});

// 0부터 99까지 추가
for (int i = 0; i < 100; i++) {
    set.add(i);
}
```

**실행 결과**

* `0`부터 `23`까지 출력한 뒤
* `ConcurrentModificationException`이 발생한다.

**원인**

* `notifyElementAdded` 메서드가 관찰자 리스트를 순회하면서 외계인 메서드(`added`)를 호출한다.
* 외계인 메서드는 다시 `removeObserver`를 호출하여 리스트를 수정하려 한다.
* 리스트를 순회 중에 수정했기 때문에 문제가 발생한 것이다.

***

### 개선 방법 1: 동기화 블록 밖으로 외계인 메서드 이동

<figure><img src="../../../../.gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>

외계인 메서드를 호출하기 전에 관찰자 리스트를 복사하여 동기화 블록 밖에서 순회하도록 수정하면 문제가 해결된다.

```java
private void notifyElementAdded(E element) {
    // 관찰자 리스트 복사
    List<SetObserver<E>> snapshot;
    synchronized (observers) {
        snapshot = new ArrayList<>(observers);
    }
    // 복사된 리스트를 사용해 순회하며 메서드 호출
    for (SetObserver<E> observer : snapshot) {
        observer.added(this, element);
    }
}
```

#### 개선된 동작

* 관찰자 리스트를 복사한 후 동기화 블록 바깥에서 순회한다.
* **`ConcurrentModificationException` 발생하지 않는다.**

이 방식은 복사본을 사용하는 만큼 약간의 메모리 오버헤드가 발생할 수 있지만, 안전한 동작을 보장한다.

**추가 설명**

복사된 리스트는 동기화와 독립적으로 처리되므로, 관찰자가 호출 중 다른 작업을 수행하더라도 원본 리스트에는 영향을 주지 않는다. 이를 통해 동기화 관련 예외 상황을 완전히 방지할 수 있다.

***

### 개선 방법 2: CopyOnWriteArrayList 사용

`CopyOnWriteArrayList`는 리스트 복사를 생략하면서도 안전하게 동작할 수 있도록 설계된 자바의 동시성 컬렉션이다. 수정 작업이 발생할 때마다 새로운 복사본을 생성하여 내부 일관성을 유지한다. 이를 활용하면 동기화 문제를 간단히 해결할 수 있다.

#### 수정된 코드

```java
import java.util.concurrent.CopyOnWriteArrayList;

public class ObservableSet<E> extends ForwardingSet<E> {
    // CopyOnWriteArrayList를 사용해 관찰자 저장
    private final List<SetObserver<E>> observers = new CopyOnWriteArrayList<>();

    // 생성자: 기존 Set 객체를 감싼다
    public ObservableSet(Set<E> set) {
        super(set);
    }

    // 관찰자를 추가하는 메서드
    public void addObserver(SetObserver<E> observer) {
        observers.add(observer);
    }

    // 관찰자를 제거하는 메서드
    public boolean removeObserver(SetObserver<E> observer) {
        return observers.remove(observer);
    }

    // 새로운 원소가 추가되었음을 관찰자들에게 알리는 메서드
    private void notifyElementAdded(E element) {
        for (SetObserver<E> observer : observers) {
            observer.added(this, element); // 안전한 호출
        }
    }
}
```

#### CopyOnWriteArrayList의 장점

<figure><img src="../../../../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>

* **동기화 필요 없음**: 읽기 작업은 동기화 없이 안전하게 수행된다.
* **코드 단순화**: 리스트 복사를 제거하여 코드가 더 간결해진다.
* **안전한 수정**: 수정 작업이 복사본에서 이루어지기 때문에 동기화 문제가 발생하지 않는다.

#### 추가적인 활용 방안

* `CopyOnWriteArrayList`는 다중 스레드 환경에서 주로 읽기 작업이 많고 쓰기 작업이 적을 때 최적의 성능을 발휘한다.
* GUI 이벤트 리스너와 같은 상황에서도 사용될 수 있으며, 이벤트 호출 중 발생할 수 있는 동기화 문제를 방지한다.

#### 단점

* 수정 작업이 빈번한 경우 성능 저하가 발생할 수 있다.
* 메모리 사용량이 증가할 수 있다.

***

### 📚 핵심 정리

<figure><img src="../../../../.gitbook/assets/image (91).png" alt=""><figcaption></figcaption></figure>

교착상태와 데이터 손상을 방지하려면 동기화 블록 내부에서 외계인 메서드를 호출하지 말아야 한다. 이를 위해 다음 지침을 따른다:

1. **동기화 블록 내부 작업 최소화**:
   * 락을 얻고 데이터를 검사 및 수정한 뒤, 바로 락을 해제한다.
2. **외계인 메서드는 열린 호출(Open Call)로 처리**:
   * 동기화 블록 외부에서 호출되도록 설계한다.
3. **적절한 동시성 도구 사용**:
   * `CopyOnWriteArrayList`와 같은 동시성 컬렉션을 적극 활용한다.

멀티코어 환경에서는 과도한 동기화를 피하는 것이 특히 중요하다. 내부 동기화는 필요할 때만 사용하고, 이를 명확히 문서화해야 한다. 동기화 설계에서 실수는 치명적인 결과를 초래할 수 있으므로, 철저한 검토가 필요하다.

**부록: CopyOnWriteArrayList와 ArrayList 비교**

<figure><img src="../../../../.gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>

| 특징         | CopyOnWriteArrayList | ArrayList     |
| ---------- | -------------------- | ------------- |
| 쓰기 작업 중 동작 | 새로운 복사본 생성           | 기존 리스트 수정     |
| 읽기 작업의 안전성 | 동기화 없이 안전            | 동기화 필요        |
| 성능         | 읽기 작업 많을 때 유리        | 쓰기 작업 많을 때 유리 |
| 메모리 사용량    | 높음                   | 낮음            |

적절한 선택을 통해 동시성 문제를 예방하고 효율적인 코드 작성을 실현할 수 있다.
