# 도우미 메서드

`도우미 메서드(Helper Method)`란, 어떤 작업을 수행할 때 주 메서드의 기능을 보조하거나 반복적인 작업을 처리하기 위해 사용되는 보조적인 메서드입니다.&#x20;

> 도우미 메서드는 코드의 재사용성을 높이고, 주 메서드의 가독성을 향상시키기 위해 주로 사용됩니다.

주 메서드의 복잡한 작업을 여러 작은 작업으로 나눌 때, 각각의 작은 작업을 도우미 메서드로 구현할 수 있습니다. 이를 통해 코드를 모듈화하고, 유지보수하기 쉽게 만들 수 있습니다.

## 1. 도우미 메서드의 역할과 사용 예시

도우미 메서드는 다음과 같은 상황에서 유용합니다:

1. **복잡한 로직을 단순화할 때**: 주 메서드에서 특정 작업을 반복적으로 처리해야 하거나 복잡한 로직이 필요한 경우, 이를 별도의 도우미 메서드로 분리함으로써 주 메서드의 가독성을 높입니다.
2. **코드 중복을 줄일 때**: 여러 메서드에서 반복적으로 사용되는 로직을 도우미 메서드로 분리하면 코드 중복을 줄일 수 있습니다.
3. **캡슐화**: 특정 로직을 외부에 노출하지 않고 내부에서만 사용하고 싶을 때 도우미 메서드로 정의합니다.

### 1) 도우미 메서드의 예

다음은 이전 예제에서 도우미 메서드(`wildcardSwapHelper`)의 역할을 설명한 것입니다.

```java
import java.util.List;

class SwapTest {
    // 와일드카드 타입을 사용한 swap 메서드
    public static void wildcardSwap(List<?> list, int i, int j) {
        // 도우미 메서드를 사용하여 타입 안전성을 확보
        wildcardSwapHelper(list, i, j);
    }

    // 도우미 메서드
    // 이 메서드는 제네릭 타입 <E>을 사용하여 와일드카드로 전달된 리스트를 안전하게 처리합니다.
    private static <E> void wildcardSwapHelper(List<E> list, int i, int j) {
        // 리스트의 두 원소를 교환하는 로직
        list.set(i, list.set(j, list.get(i)));
    }
}
```

### 2) 이 예제에서 도우미 메서드의 역할

* `wildcardSwap` 메서드는 `List<?>` 타입의 리스트를 받아들이기 때문에 리스트의 원소 타입이 어떤 것인지 알 수 없습니다. 이 경우, `null` 외의 값을 리스트에 추가할 수 없습니다.
* `wildcardSwapHelper` 메서드는 도우미 메서드로서, 와일드카드 타입을 실제 제네릭 타입 `<E>`으로 변환해 주어 타입 안정성을 확보합니다. 즉, `List<E>`로 타입을 추론하게 하여 원소를 안전하게 교환할 수 있게 됩니다.
* 도우미 메서드를 사용함으로써 주 메서드(`wildcardSwap`)는 더 간단해지고, 와일드카드 타입의 한계를 극복할 수 있습니다.

### 3) 도우미 메서드가 필요한 이유

* **와일드카드 타입의 제한 극복**: `List<?>`와 같은 와일드카드 타입에서는 타입 안전성 문제로 인해 `null` 외의 값을 추가할 수 없지만, 도우미 메서드에서는 타입 매개변수를 명시해 안전하게 작업을 수행할 수 있습니다.
* **코드의 단순화**: 복잡한 로직을 도우미 메서드로 분리하면 주 메서드의 가독성이 높아집니다.

따라서 도우미 메서드는 주 메서드를 보조하면서도, 특정 작업을 안전하게 처리하거나 반복적인 작업을 모듈화하는 데 중요한 역할을 합니다.
