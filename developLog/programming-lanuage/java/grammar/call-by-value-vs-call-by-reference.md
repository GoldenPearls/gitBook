---
icon: java
---

# Call by value vs Call by Reference과 기본형과 참조형

## 1. Call by Value

* 자바에서 이해할 때 `함수의 인자를 어떻게 처리`하는지 이해해야 한다.
* Java는 엄밀히 말해 **함수 호출 방식**은 "Call by Value"만을 지원 = 인자의 `실제 값을 복사`하여 함수의 매개변수에 전달하는 방식
* 함수에 인자를 전달할 때 항상 값을 복사하여 전달한다는 의미지만 참조형 변수의 경우, 복사되는 값이 변수가 가리키는 객체의 주소(즉, 참조)

> 이 때문에 종종 Java의 동작이 "Call by Reference"처럼 보일 수 있다. 함수 내에서 참조형 변수를 통해 객체를 수정하면 원본 객체에도 변경이 반영되기 때문이다. 그러나 이는 **참조의 값이 복사되었기 때문**이며, 실제로 메모리 주소 자체가 전달되는 것은 아니다.

### ❤️ **기본형 (Primitive Types) = 원시 자료**

* 실제 데이터 값이 복사
* int, short, long, float, double, char, boolean

#### 동작방식

1. **값의 복사**: 함수에 기본형 변수를 인자로 전달할 때, 그 변수의 `값을 복사`하여 함수의 매개변수에 할당
2. **별도의 메모리 공간**: 이 복사된 값은 함수를 위한 별도의 `스택 메모리 공간`에 저장된다. 이 공간은 함수 호출 시 생성되며 함수 종료 시 소멸한다.
3. **원본 값의 보존**: 함수 내에서 매개변수의 값을 변경하더라도 원본 변수에는 영향을 미치지 않는다. 이는 값이 복사되었기 때문에 **원본과는 독립적이다.**

```jsx
public class Test {
    public static void main(String[] args) {
        int n = 10;
        System.out.println(n); //10
        test(10); //5
        System.out.println(n); //10
        // 값이 바뀌지 않는다는 걸 볼 수 있다. 
    }

    public static void test(int n) { //test 함수의 지역 변수 n에 할당
        n -= 5;
        System.out.println(n); 
    }
}

```

### ❤️ 참조 타입

* 객체의 주소값이 복사
* 객체의 참조(주소)를 저장하는 변수를 의미
* 예를 들어 **`String`**, **`Arrays`**, 사용자가 정의한 클래스 객체 등이 여기에 해당

#### 동작방식

1. **참조의 복사**: 참조형 변수를 함수에 전달할 때, 그 변수가 가리키는 `객체의 주소(참조)가 복사`
2. **동일한 객체에 대한 참조**: 복사된 주소를 통해 함수 내부에서 객체에 접근하고 변경할 수 있다. 이 때, 객체 자체는 하나이므로 원본과 함수 내의 참조는 동일한 객체를 가리킴
3. **객체 변경의 영향**: 함수 내에서 객체의 상태를 변경하면, 그 변경 사항은 원본 객체에도 반영 그러나 **참조 자체(즉, 주소)를 변경하는 것은 원본에 영향을 주지 않는다.**

```jsx
public class Test {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3}; 
        for (int num : arr) System.out.print(num + " ");
        System.out.println();
        test(arr); // 1. 참조의 복사 : arr의 주소값이 test 함수의 매개변수 a에 복사

        for (int num : arr) System.out.print(num + " "); // main 메서드로 돌아와서 arr의 값을 다시 출력하면, 수정된 값들(10, 20, 30)이 출력
    }

    public static void test(int[] a) { //a와 arr는 동일한 배열 객체
        for (int i = 0; i < a.length; i++) {
            a[i] *= 10; // 2. 객체 변경의 영향 : test 함수 내에서 a를 통해 배열을 변경하면, 이는 arr이 가르키는 동일한 배열 객체에 영향을 미침
        }
    }
}
// 결과
1 2 3 
10 20 30
```

⇒ **참조 자체의 변경**: 만약 **`test`** 함수 내에서 `a`에 새로운 배열을 할당한다면, 이는 `arr`에 영향을 주지 않는다. 왜냐하면 이 경우 `a`의 참조가 새로운 객체를 가리키게 되지만, `arr`는 여전히 원래의 객체를 가리키기 때문

### ❤️ 참고) 매개변수란?

* `매개변수(parameter)`는 프로그래밍에서 **함수나 메서드를 정의할 때 사용되는 변수**
* 이들은 함수가 호출될 때 **특정한 값을 받아들이기 위해 사용**
* 매개변수는 함수의 정의 부분에 나열되며, 함수가 실행될 때 필요한 데이터를 외부로부터 받아들이는 '입구' 역할을 한다.

#### **매개변수의 역할과 특징**

1. **데이터 수신**: 함수나 메서드가 호출될 때, 매개변수는 호출하는 측에서 제공하는 값을 받는다.
2. **함수 내 사용**: 함수 내부에서는 이 매개변수를 통해 전달된 값을 사용하여 특정 작업을 수행
3. **지역 변수의 성격**: 매개변수는 **해당 함수 또는 메서드 내에서만** `유효한 지역 변수(local variable)`즉, 함수 밖에서는 그 값에 접근할 수 없다.

#### **매개변수와 인자**

> "매개변수(parameter)"와 "인자(argument)"는 종종 혼용되어 사용되지만, 기술적으로는 약간의 차이가 있다. 매개변수는 함수를 정의할 때 선언되는 변수를 의미하는 반면, 인자는 함수를 호출할 때 실제로 전달되는 값이다. 예를 들어, `add(5, 3)`에서 `5`와 `3`은 **`add`** 함수에 전달되는 인자이다.

## 2. Call by Reference

* 프로그래밍에서 함수 호출 방식 중 하나
* 함수에 인자를 전달할 때 **인자의 실제 메모리 주소를 전달하는 방식**을 말한다. 즉, 함수에 전달된 인자는 원본 인자를 직접 가리키는 참조
* 이 방식을 사용하면 함수 내에서 매개변수를 통해 인자의 `실제 메모리에 접근하고 수정`할 수 있으며, 이러한 변경은 함수 외부의 해당 인자에도 영향을 미친다.

#### ❤️ **Call by Reference의 특징**

1. **메모리 주소의 전달**: 인자로 전달되는 것은 값이 아니라 변수의 메모리 주소
2. **원본 데이터의 직접적 수정**: 함수 내에서 매개변수를 통해 원본 데이터를 직접 수정할 수 있다.
3. **함수 외부에 영향**: 함수 내에서의 변경이 함수를 호출한 영역의 변수에도 영향을 미친다.

```jsx
void exampleFunction(int[] arr) {
    arr[0] = 50;
}

int main() {
    int[] x = {10};
    exampleFunction(x);
    // x[0]은 이제 50이다.
}
```

## 3. 기본형과 참조형의 차이점은?

자바에서는 Primitive Type과 Reference Type이 있다. 이는 기본형과 참조형이라고 하며, 서로 조금은 다른 특징을 가지고 있다.

### ❤️ 기본형(Primitive Type)

* 변수에 값 자체를 저장하며, `stack 영역에 생성`된다.
* 사용하기 전에 반드시 선언되어야 하며, **초기화를 하지 않으면 자료형에 맞는 기본 값이 들어간다.**
* OS에 따라 자료의 길이가 변하지 않는다.
* 비객체 타입이며, `Null 값을 가질 수 없다.`
* 정수(byte, short, int, long), 실수(double, float), 문자(char), 논리(boolean)

### ❤️ 참조형(Reference Type)

* 기본형을 제외하면 참조형이라고 한다.
* 메모리 상에서 객체가 존재하는 주소를 저장하며, `heap 영역에 저장`한다.
* 클래스형, 인터페이스형, 배열형이 있다.

### ❤️ 참고

> `스택(Stack)`과 `힙(Heap)`은 메모리를 할당하고 관리하는 두 가지 주요 방법으로 이들은 메모리 관리의 중요한 측면이며, 특히 자바와 같은 언어에서 중요한 역할

#### **스택 (Stack)**

1. **정의**: 스택 메모리는 주로 프로그램의 런타임에 기본 데이터 타입(예: int, float, double)과 객체 참조 변수를 저장하는 데 사용
2. **특징**:
   * **자동 메모리 관리**: 함수나 메서드 호출 시 자동으로 생성되고, 호출이 완료되면 사라진다.
   * **LIFO (Last In, First Out)**: 마지막에 들어온 요소가 먼저 나가는 구조
   * **속도**: 스택 메모리는 힙에 비해 상대적으로 빠른 접근 속도를 가진다.
   * **크기 제한**: 스택 메모리는 크기가 고정되어 있으며, `스택 오버플로우(stack overflow)`가 발생할 수 있다.

#### **힙 (Heap)**

1. **정의**: 힙 메모리는 주로 동적으로 할당된 메모리를 저장하는 데 사용. 이는 객체와 배열과 같은 참조 데이터 타입에 해당한다.
2. **특징**:
   * **동적 메모리 할당**: 필요에 따라 메모리를 할당하고, 가비지 컬렉터에 의해 더 이상 사용되지 않는 메모리가 회수
   * **메모리 관리 필요**: 힙은 스택보다 관리가 복잡하며. 가비지 컬렉션으로 인한 성능 영향도 고려해야 한다.
   * **크기 제한**: 일반적으로 스택보다 큰 메모리를 가지지만, 메모리가 부족하면 `OutOfMemoryError`가 발생할 수 있다.
   * **속도**: 힙 메모리는 스택에 비해 상대적으로 접근 속도가 느다.

```jsx
[ 스택 메모리 ]     [ 힙 메모리 ]
+-------------+     +-------------+
|             |     |             |
| 함수 호출   |     | 객체        |
| 스택 프레임 |     | 동적 할당   |
| 로컬 변수   |     | 참조형 데이터 |
|             |     |             |
+-------------+     +-------------+
```

#### 스택 오버플로우(Stack Overflow)

* 프로그램에서 스택 메모리가 초과하여 사용되었을 때 발생하는 오류
* 스택은 함수의 호출과 로컬 변수 저장에 사용되는 메모리 영역으로, 크기가 제한되어 있다. 이 크기를 넘어서 스택 메모리를 사용하려 할 때 스택 오버플로우가 발생
* 원인 : 깊은 또한 무한 재귀 혹은 과도한 스택 메모리 할당
* 스택 오버플로우를 방지하기 위해서는 깊은 재귀 호출을 피하고, 스택 메모리의 사용을 최적화하는 것이 중요

```jsx
public void recursiveFunction(int num) {
    if(num == 0) return;
    else recursiveFunction(num + 1);
} // recursiveFunction은 자기 자신을 끝없이 호출히여 각 호출마다 스택 프레임이 하나씩 추가되므로, 결국 스택 메모리의 한계에 도달하여 오버플로우가 발생
```