# 와일드 카드에 대한 정리

와일드카드는 Java의 제네릭 타입에서 유연성을 높이기 위해 도입된 기능으로, 제네릭 타입 매개변수를 특정하지 않고 사용할 수 있게 해줍니다. 주로 세 가지 형태로 사용되며, 각각의 의미와 사용법이 조금씩 다릅니다.

## 1. 와일드카드의 종류

Java에서는 다음 세 가지 형태의 와일드카드를 사용할 수 있습니다.

1. **비한정적 와일드카드 (`?`)**
2. **상한 경계 와일드카드 (`? extends T`)**
3. **하한 경계 와일드카드 (`? super T`)**

이 세 가지 와일드카드는 서로 다른 상황에서 제네릭 타입의 유연성을 높이기 위해 사용됩니다.

## 2. 와일드카드의 종류와 사용법

### **2.1. 비한정적 와일드카드 (`?`)**

* **표기법**: `<?>`
* **의미**: "아무 타입이나" 허용한다는 의미로, 타입 매개변수가 무엇이든 관계없이 사용할 수 있습니다.
* **사용 사례**: 컬렉션의 원소 타입이 무엇인지 신경 쓰지 않고, 모든 타입의 컬렉션을 처리하고자 할 때 사용합니다. 예를 들어, `List<?>`는 어떤 타입의 리스트든 받을 수 있습니다.
* **제약사항**: 비한정적 와일드카드를 사용하는 컬렉션에는 `null` 외의 다른 값을 추가할 수 없습니다.

```java
void printCollection(Collection<?> c) {
    for (Object e : c) {
        System.out.println(e);
    }
}
```

위 코드에서는 `Collection<?>` 타입의 인자를 받기 때문에, 어떤 타입의 컬렉션이든 인자로 전달할 수 있습니다.

### **2.2. 상한 경계 와일드카드 (`? extends T`)**

* **표기법**: `<? extends T>`
* **의미**: 특정 타입 `T`와 그 하위 타입만을 허용합니다.
* **사용 사례**: 주로 읽기 전용 작업에 사용됩니다. 예를 들어, 특정 클래스의 하위 클래스 타입을 다룰 때 유용합니다.
* **제약사항**: 와일드카드 타입으로 컬렉션에 추가할 수 있는 요소의 타입이 불명확하므로, 컬렉션에 요소를 추가할 수 없습니다.

```java
void printNumbers(List<? extends Number> list) {
    for (Number n : list) {
        System.out.println(n);
    }
}
```

위 코드에서 `List<? extends Number>`는 `List<Integer>`, `List<Double>` 등 `Number`의 하위 타입을 인자로 받을 수 있습니다.

### **2.3. 하한 경계 와일드카드 (`? super T`)**

* **표기법**: `<? super T>`
* **의미**: 특정 타입 `T`와 그 상위 타입만을 허용합니다.
* **사용 사례**: 컬렉션에 안전하게 요소를 추가할 수 있도록 보장합니다. `T`와 그 상위 타입만을 허용하므로, 예를 들어 `List<? super Integer>`는 `Integer`, `Number`, `Object` 타입의 요소를 추가할 수 있습니다.
* **제약사항**: 요소를 읽을 때는 `Object` 타입으로 반환됩니다.

```java
void addIntegers(List<? super Integer> list) {
    list.add(10);
    list.add(20);
}
```

위 코드에서 `List<? super Integer>`는 `List<Integer>`, `List<Number>`, `List<Object>`와 같은 상위 타입의 리스트를 인자로 받을 수 있으며, `Integer` 타입의 값을 추가할 수 있습니다.

## 3. 와일드카드 사용 시 주의사항

1. 비한정적 와일드카드 (`?`)는 컬렉션의 타입 매개변수를 알 수 없으므로, 안전한 타입을 보장하기 위해 `null` 외의 값을 추가할 수 없습니다.
2. 상한 경계 와일드카드 (`? extends T`)는 주로 읽기 전용 작업에 사용되며, 컬렉션에 요소를 추가하는 것은 불가능합니다.
3. 하한 경계 와일드카드 (`? super T`)는 안전하게 요소를 추가할 수 있지만, 요소를 읽을 때는 `Object`로 반환되므로 타입 캐스팅이 필요할 수 있습니다.

## 4. 로 타입과의 차이

* 로 타입 (`List`)은 타입 안전성이 보장되지 않으며, 제네릭의 장점을 활용할 수 없습니다. 타입을 지정하지 않으면 컴파일 시점에 타입 오류를 검출할 수 없고, 런타임 오류가 발생할 수 있습니다.
* 와일드카드 (`List<?>`, `List<? extends T>`, `List<? super T>`)를 사용하면 제네릭의 타입 안전성을 유지하면서도, 유연한 타입 처리가 가능합니다.

## 5. 와일드카드 사용 시 유의점

* 와일드카드는 제네릭 메서드 작성 시 유용하며, 특히 라이브러리 설계에서 API의 유연성을 높이는 데 큰 도움이 됩니다.
* 클래스 리터럴에서는 로 타입을 사용해야 하며, `List<?>.class`나 `List<String>.class`는 허용되지 않습니다.
* `instanceof` 연산자와 함께 사용할 때도 로 타입을 사용해야 합니다. 제네릭 타입 정보는 런타임에 지워지기 때문입니다.

```java
// 로 타입을 사용하여 instanceof 연산자 적용
if (o instanceof Set) {
    Set<?> set = (Set<?>) o;
}
```

## 6. PECS(Producer-Extends, Consumer-Super) 공식

그렇다면 도대체 언제 super를 사용해야 하고, 언제 extends를 사용해야 하는지 헷갈릴 수 있다. 그래서 이펙티브 자바에서는 PECS라는 공식을 만들었는데, <mark style="color:red;">이는 Producer-Extends, Consumer-Super의 줄임말이다. 즉, 컬렉션으로부터 와일드카드 타입의 객체를 꺼내서 생성하면(produce) extends를, 갖고 있는 객체를 컬렉션에 사용(consumer)하여 더하면 super를 사용하라는 것</mark>이다.

```java
void printCollection(Collection<? extends MyParent> c) {
    for (MyParent e : c) {
        System.out.println(e);
    }
}

void addElement(Collection<? super MyParent> c) {
    c.add(new MyParent());
}
```

`printCollection` 같은 경우에는 **컬렉션으로부터 원소들을 꺼내면서 와일드카드 타입 객체를 생성(produce)**하고 있다. 반대로 `addElement`의 경우에는 **컬렉션에 해당 타입의 원소를 추가함으로써 객체를 사용(consume)**하고 있다. 그러므로 와일드카드 타입의 객체를 produce하는 printCollection은 extends가, 객체를 consume하는 addElement에는 super가 적합한 것이다.

## 7. 결론

* **비한정적 와일드카드 (`?`)**: 모든 타입을 허용하지만, 읽기 전용 작업에 적합하며 `null` 외에는 값을 추가할 수 없습니다.
* **상한 경계 와일드카드 (`? extends T`)**: 특정 타입의 하위 타입만 허용하며, 주로 읽기 전용 작업에 사용됩니다.
* **하한 경계 와일드카드 (`? super T`)**: 특정 타입의 상위 타입만 허용하며, 컬렉션에 안전하게 요소를 추가할 때 사용됩니다.

와일드카드는 제네릭 타입의 유연성을 높이기 위한 중요한 도구이며, 각각의 와일드카드 타입을 적절히 사용하면 코드의 재사용성과 안전성을 동시에 확보할 수 있습니다.



> 출처 : [https://mangkyu.tistory.com/241](https://mangkyu.tistory.com/241)