---
description: 한권으로 읽는 컴퓨터 구조와 프로그래밍 1장 DeepDive 지식
icon: laptop-binary
---

# deepdive 지식 - 1장

## 1. 아스키 코드의 실제 예제

{% hint style="danger" %}
실제  예제로 stesdg이라고 치면 아스키 코드가 어떻게 수를 사용해 문자나 키보드에 있는 다른 기호 등을 표현 하는 거잖아?
{% endhint %}

맞아! 네가 `"stesdg"`라고 입력하면, 컴퓨터는 이를 **아스키(ASCII) 코드**로 변환해서 저장하고 처리해.\
즉, **문자는 내부적으로 숫자로 저장**되는데, 이 숫자가 바로 **아스키 코드 값**이야.

***

### **🔡 "stesdg" → ASCII 코드 변환 과정**

| 문자 | ASCII (10진수) | ASCII (16진수) | ASCII (2진수) |
| -- | ------------ | ------------ | ----------- |
| s  | 115          | 0x73         | 01110011    |
| t  | 116          | 0x74         | 01110100    |
| e  | 101          | 0x65         | 01100101    |
| s  | 115          | 0x73         | 01110011    |
| d  | 100          | 0x64         | 01100100    |
| g  | 103          | 0x67         | 01100111    |

***

### **📌 ASCII가 문자를 숫자로 변환하는 원리**

<figure><img src="../../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>

1. **문자를 입력**하면, 컴퓨터는 **각 문자를 대응하는 숫자(ASCII 코드)로 변환한다.**
2. 이 숫자는 **2진수로 저장**되며, 컴퓨터가 이를 조합해 화면에 표시하거나 처리할 수 있음.
3.  예를 들어 `"stesdg"`라는 단어를 입력하면, <mark style="color:red;">컴퓨터는 내부적으로 이렇게 저장</mark>

    ```
    01110011 01110100 01100101 01110011 01100100 01100111
    ```
4. 나중에 이 2진수를 **ASCII 코드 테이블을 참고하여 다시 문자로 변환**하면 `"stesdg"`가 출력됨.

***

### **💡 예제: ASCII 변환 코드 (java)**

> Java에서는 `char` 값을 `int`로 변환하면 ASCII 코드 값을 얻을 수 있으며,\
> 2진수 변환은 `Integer.toBinaryString()` 메서드를 활용할 수 있다.

```java
public class ASCIIConverter {
    public static void main(String[] args) {
        String text = "stesdg"; // 변환할 문자열

        System.out.println("ASCII 코드:");
        for (char ch : text.toCharArray()) { // 문자열을 문자 배열로 변환
            int ascii = (int) ch;  // 문자를 ASCII 값으로 변환
            System.out.print(ascii + " ");
        }

        System.out.println("\n2진수:");
        for (char ch : text.toCharArray()) {
            String binary = String.format("%8s", Integer.toBinaryString(ch)).replace(' ', '0');  // 8비트 2진수 변환
            System.out.print(binary + " ");
        }
    }
}

```

* `Integer.toBinaryString(ch)` → 문자의 2진수 변환
* `String.format("%8s", ...).replace(' ', '0')` → 8비트 형식 유지

🔹 실행 결과:

```
ASCII 코드:
115 116 101 115 100 103 

2진수:
01110011 01110100 01100101 01110011 01100100 01100111
```

***

### **✅ 결론**

✔ **ASCII는 문자를 숫자로 변환하는 체계**로, 키보드 입력을 숫자로 저장하고 변환하는 방식이야.\
✔ `"stesdg"` 같은 단어도 내부적으로는 **2진수로 저장**되며, 나중에 다시 문자로 변환할 수 있어!

***

## 2. 컴퓨터가 문자를 저장하는 내부 구조

> **ASCII가 문자를 숫자로 변환하는 원리**에 3번에 컴퓨터는 내부적으로  <mark style="color:red;">문자 ->  비트화해서 저장</mark>한다고 했는데, <mark style="color:red;">컴퓨터는 내부 어디에 저장할까?</mark> binary라는 것이 위치하는 곳에 메모리에 저장하는 걸까?&#x20;

문자를 입력하면 **메모리(RAM)** 및 **CPU 레지스터** 등에 저장되며, 문자 데이터를 처리하는 과정을 정리해보자

<figure><img src="../../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

컴퓨터는 키보드에서 입력된 문자를 받아 내부적으로 **CPU와 메모리**를 거쳐 처리한 후, **디스플레이, 저장장치, 네트워크 등**으로 보낸다. 이 과정에서 여러 핵심 부품이 ASCII 데이터를 다룬다.

### **1️⃣ 키보드 입력 → CPU & RAM 저장**

1. **키보드 입력**
   * 사용자가 `"stesdg"`를 입력하면, 키보드 컨트롤러가 해당 키의 **ASCII 코드**를 생성함.
   * 예: `"s"`(115), `"t"`(116), `"e"`(101), `"s"`(115), `"d"`(100), `"g"`(103)
2. **CPU 레지스터 (Register)로 전달**
   * 키 입력이 발생하면 **인터럽트(Interrupt)** 가 발생하여 CPU가 입력을 처리
   * **CPU의 레지스터(Register)** 가 임시로 데이터를 저장
3.  **RAM(주기억장치)에 저장**

    * 운영체제가 레지스터에 있는 문자를 **RAM(Random Access Memory)** 에 저장
    * `"stesdg"`는 **연속된 바이트(ASCII 코드 값) 형태로 저장됨**
    * 예시 (메모리 저장 형태):

    ```
    주소      데이터 (ASCII 코드)
    0x100    115 ('s')
    0x101    116 ('t')
    0x102    101 ('e')
    0x103    115 ('s')
    0x104    100 ('d')
    0x105    103 ('g')
    ```

    📌 **즉, 문자는 RAM(메모리)에 저장되며, ASCII 코드(숫자)로 변환된 후 2진수로 보관됨.**

### &#x20;**2️⃣ 실제 저장 형태 (Binary & RAM)**

**문자는 결국 2진수(0과 1)로 저장된다.** 각 문자는 8비트(1바이트)로 표현되며, RAM에서 저장되는 실제 값은 표와 같다.

| 문자 | ASCII (10진수) | 2진수 (Binary) |
| -- | ------------ | ------------ |
| s  | 115          | `01110011`   |
| t  | 116          | `01110100`   |
| e  | 101          | `01100101`   |
| s  | 115          | `01110011`   |
| d  | 100          | `01100100`   |
| g  | 103          | `01100111`   |

### **3️⃣ 메모리에서 Binary 값이 위치하는 곳?**

> "Binary라는 것이 위치하는 곳에 메모리에 저장하는 건가?" 🤔

✅ **그렇다!**\
RAM(주기억장치) 내부에는 **데이터를 저장하는 메모리 셀**(Memory Cell)이 존재하며,\
이곳에 **2진수(Binary) 형태의 데이터**가 저장됨.

📌 **RAM 내부 저장 구조 (예제)**

```
+---------+---------+---------+---------+---------+---------+
| 01110011 | 01110100 | 01100101 | 01110011 | 01100100 | 01100111 |
+---------+---------+---------+---------+---------+---------+
```

RAM의 각 주소(0x100, 0x101, ...)에 1바이트(8비트)씩 저장됨.\
따라서, **컴퓨터는 RAM에 있는 바이너리 데이터를 사용하여 문자(ASCII)로 변환하고 화면에 출력함.**

### **4️⃣ 추가적으로 저장될 수 있는 위치**

**1. 캐시(Cache) 메모리**

* CPU가 자주 사용하는 <mark style="color:red;">데이터를 빠르게 접근하기 위해 캐시에도 저장</mark>할 수 있음.
* 예: L1, L2, L3 Cache

**2. 저장장치 (HDD/SSD)**

* <mark style="color:red;">문서를 저장</mark>할 때 파일 시스템을 통해 HDD/SSD에 저장
* 저장될 때는 파일 포맷(예: UTF-8, ASCII, Unicode 등)으로 변환됨.

**3. VRAM (GPU 메모리)**

* 텍스트가 화면에 표시될 때, GPU의 **VRAM** 을 통해 렌더링됨.
* 폰트 렌더링, 디스플레이 표시 작업 수행.

### **🔎 최종 정리**

* 컴퓨터에서 문자는 ASCII 코드로 변환된 후, **RAM에 2진수(Binary) 형태**로 저장됨.
* **RAM 내부의 메모리 셀**에 저장되며, CPU가 연산할 때 **캐시(Cache)**&#xC5D0;도 저장 가능.
* <mark style="color:blue;">디스크(HDD/SSD)에 저장될 경우, UTF-8 등의 인코딩 방식</mark>으로 저장됨.
* 화면 출력 시, GPU의 VRAM을 통해 폰트 렌더링 후 디스플레이에 표시됨.

> 즉, Binary는 RAM 내부의 특정 주소에 저장되며, CPU가 이를 읽고 처리하는 과정이 반복&#xB428;**!**&#x20;

1. **키보드 입력** → 키보드 컨트롤러가 ASCII 코드로 변환
2. **CPU 처리** → 인터럽트 발생 후, 운영체제가 데이터 처리
3. **RAM 저장** → ASCII 코드가 메모리에 저장됨
4. **CPU 연산** → ALU & CU가 데이터를 분석하고 연산
5. **디스플레이 출력** → ASCII 코드가 화면에 글자로 표시됨

***

## 3. 가비지 컬렉션(GC)과 메모리 최적화

> 질문 : 출력할 때 메모리를 쓰이는 걸까? 그렇다면 메모리 소모가 너무 심한데 자바는 가비지 컬렉션(GC)로 불필요한 것들이 정리된다고 들었는데 어떻게 연관될까?

### **📌 출력할 때 메모리는 항상 사용되는가?**

{% hint style="danger" %}
출력을 할 때도 메모리를 사용하며, 다음과 같은 방식으로 작동한다.
{% endhint %}

1️⃣ **문자 데이터를 메모리(RAM)에 저장** →

* 키보드 입력 시 **RAM(Heap/Stack 메모리)에 저장**됨.
* 프로그램이 실행 중인 동안 **CPU 레지스터, 캐시, Heap 영역에서 사용**됨.

2️⃣ **CPU가 데이터를 읽어와 처리** →

* RAM에 저장된 2진수(ASCII)를 CPU가 읽고 처리함.
* `ALU(Arithmetic Logic Unit)`에서 연산 후, 화면에 출력할 준비를 함.

3️⃣ **디스플레이에 출력 (GPU VRAM 활용)** →

* 문자 데이터를 폰트로 변환 후 **GPU VRAM(비디오 메모리)에 로드**
* 최종적으로 **픽셀을 조합하여 화면에 표시됨**

즉, 프로그램이 실행 중일 때, **RAM과 GPU** 메모리가 계속 사용된다.\
메모리를 계속 사용하면 부담이 되므로, Java에서는 '가비지 컬렉션'을 이용해 불필요한 데이터를 자동 정리한다.

#### **📌 가비지 컬렉션(GC)과 메모리 최적화 🔄**

<figure><img src="../../.gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>

Java는 **자동 메모리 관리** 기능을 제공하며, 가비지 컬렉터(Garbage Collector, GC)가 사용되지 않는 객체를 정리하여 메모리 누수를 방지함.

1️⃣ **객체가 Heap 메모리에 할당됨**

* Java에서 `String`, `char[]`, `BufferedWriter` 같은 데이터는 Heap에 저장됨.
* 예를 들어, `"stesdg"` 문자열도 Heap 영역에서 관리됨.

2️⃣ **객체가 더 이상 참조되지 않으면 GC가 수집**

* 사용하지 않는 데이터는 **GC가 정리(GC Sweep)** 하여 메모리를 회수함.
*   예제:

    ```java
    String text = "stesdg";  // Heap에 저장
    text = null; // 이전 문자열 "stesdg"는 GC 대상이 됨
    ```

3️⃣ **GC가 작동하면 메모리가 해제됨**

* GC 알고리즘(Mark and Sweep, G1 GC 등)이 실행되어 불필요한 객체를 제거.

### **📌 가비지 컬렉션과 메모리 사용량 간의 관계**

{% hint style="warning" %}
&#x20;Q: "출력할 때도 메모리를 사용하면, 가비지 컬렉션은 언제 역할을 할까?"\
A: 출력하는 순간에는 메모리를 사용하지만, <mark style="color:red;">일정 시간이 지나면 가비지 컬렉터가 불필요한 데이터를 정리</mark>함.
{% endhint %}

**예제 1: 단순한 문자열 출력**

```java
public class MemoryTest {
    public static void main(String[] args) {
        String text = "stesdg";
        System.out.println(text); // 출력 후에도 text는 살아 있음
    }
}
```

💡 `text`는 여전히 참조 중이므로, **가비지 컬렉터가 삭제하지 않음.**

***

**예제 2: 대량의 출력과 GC의 개입**

```java
public class MemoryTest {
    public static void main(String[] args) {
        for (int i = 0; i < 1000000; i++) {
            String text = "stesdg" + i; // 매번 새로운 객체 생성
            System.out.println(text);
        }
        // 가비지 컬렉션 실행 요청 (단, JVM이 필요할 때만 수행)
        System.gc();
    }
}
```

🔹 **여기서 메모리 사용량이 증가하다가, GC가 주기적으로 개입하여 이전 객체를 정리함.**\
🔹 `"stesdg" + i` 는 매번 새로운 문자열 객체를 만들기 때문에,\
이전에 생성된 문자열이 더 이상 사용되지 않으면 **GC가 해제함.**\
🔹 `System.gc();`는 GC 실행 요청을 보내지만, **JVM이 필요하다고 판단할 때만 실행됨.**

### **📌 최적의 메모리 사용을 위한 방법**

<figure><img src="../../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

1️⃣ **String 대신 StringBuilder 사용**

* `String`은 **불변(Immutable)** 이므로, 문자열이 변경될 때마다 새로운 객체가 생성됨.
* `StringBuilder`를 사용하면 **Heap 메모리 재사용** 가능.

```java
StringBuilder sb = new StringBuilder();
sb.append("stesdg");
System.out.println(sb.toString());
```

2️⃣ **대량의 출력을 할 때는 BufferedWriter 활용**

{% hint style="warning" %}
✅ 대량의 데이터를 출력할 때 (로그 파일, CSV 저장, HTML 생성)\
✅ I/O 성능을 최적화하고 싶을 때 (파일 저장, 네트워크 송수신)\
✅ 메모리 사용량을 줄이고 싶을 때 (RAM 부담이 클 때)
{% endhint %}

* `System.out.println()` 대신 **BufferedWriter** 를 사용하면 메모리 사용량 감소.
* `System.out.println()`
  * 한 줄 출력 시 마다, I/O 연산이 수행된다.
  * 즉, **매번 CPU → RAM → OS → 디스크 or 콘솔까지 전달되는 과정**이 반복됨.
  * 많은 문자열을 출력하면 **메모리에 남아 있는 데이터가 많아지고, GC가 자주 개입하게 됨**.
* BufferedWriter
  * 내부적으로 **버퍼(Buffer, 8KB 기본 크기)를 사용하여 일정량이 쌓이면 한꺼번에 출력**
  * 한 줄마다 I/O를 실행하는 것이 아니라 **버퍼가 가득 찰 때만** 실제 I/O가 발생함
  * 따라서 **메모리에 불필요한 데이터가 적게 남고, 가비지 컬렉션(GC)의 부담이 줄어든다**
* `BufferedWriter`는 **파일에 직접 데이터를 쓰는 기능이 없고**, 반드시 **다른 Writer(예: `FileWriter`)를 감싸서 사용해야 한다**.
  * 즉, `BufferedWriter`만 단독으로 사용할 수는 없고, 반드시 파일을 다룰 수 있는 `FileWriter` 같은 기반 스트림이 필요하다.
  * `FileWriter`는 **Java에서 파일에 텍스트 데이터를 저장하는 클래스**로, `java.io.Writer` 클래스를 상속받아 동작하는 **문자 기반 스트림**이다.

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

public class BufferedExample {
    public static void main(String[] args) throws IOException {
        BufferedWriter writer = new BufferedWriter(new FileWriter("output.txt"));
        writer.write("stesdg");
        writer.newLine();
        writer.close();
    }
}
```

> 프로그램 실행 중에는 메모리를 사용하지만, 종료되면 **OS**가 자동으로 회수

참고

| 메모리 영역                 | 역할                    | 예시                           |
| ---------------------- | --------------------- | ---------------------------- |
| **힙(Heap)**            | 동적으로 생성된 객체가 저장됨      | `new BufferedWriter()` 같은 객체 |
| **스택(Stack)**          | 메서드 실행 시 지역 변수 저장     | `int x = 10;` 같은 지역 변수       |
| **메소드(Method, 코드) 영역** | 클래스, 메서드 정보 저장        | `public static void main()`  |
| **네이티브 메모리**           | OS에서 사용하는 메모리 (I/O 등) | 파일 스트림, 네트워크 연결              |

***

## 4. 유니코드 이모지 변환

### **🔢 유니코드 변환 과정**

{% hint style="warning" %}
**🐱의 유니코드 코드 포인트: `U+1F431` (16진수), `128049` (10진수)**\
이 값은 UTF-8, UTF-16, UTF-32와 같은 인코딩 방식으로 변환 가능하다.
{% endhint %}

1. **유니코드 코드 포인트** : `U+1F431`
2. **10진수로 변환** : `128049`
3. **UTF-8 인코딩**
   * **16진수 바이트 표현** : `F0 9F 90 B1`
   *   **바이너리 표현** :

       ```
       11110000 10011111 10010000 10110001
       ```

### **📌 유니코드 변환 방법**

<figure><img src="../../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

유니코드는 **UTF-8, UTF-16, UTF-32** 등의 방식으로 인코딩할 수 있음.

| **인코딩 방식** | **바이트 표현**    |
| ---------- | ------------- |
| UTF-8      | `F0 9F 90 B1` |
| UTF-16     | `D83D DC31`   |
| UTF-32     | `0001F431`    |

### **💡 Java 코드로 변환하기**

**`bytesToHex(byte[] bytes)`**

* 바이트 배열을 **16진수(헥사) 문자열**로 변환하는 메서드
* 바이트 값은 `0~255 (0x00~0xFF)` 범위를 가지므로, 이를 `2자리 16진수(00~FF)`로 변환하여 가독성을 높임

**`bytesToBinary(byte[] bytes)`**

* 바이트 배열을 **2진수(바이너리) 문자열**로 변환하는 메서드
* 각 바이트를 `8자리 2진수 (00000000~11111111)` 형식으로 변환하여 가독성을 높임

```java
import java.nio.charset.StandardCharsets;
import java.util.Arrays;

public class UnicodeConversion {
    public static void main(String[] args) {
        // 🐱 이모지
        String emoji = "🐱";

        // 1️⃣ 유니코드 코드 포인트 출력 (10진수, 16진수)
        int codePoint = emoji.codePointAt(0);
        System.out.println("유니코드 코드 포인트 (10진수): " + codePoint);
        System.out.printf("유니코드 코드 포인트 (16진수): U+%X\n", codePoint);

        // 2️⃣ UTF-8 인코딩
        byte[] utf8Bytes = emoji.getBytes(StandardCharsets.UTF_8);
        System.out.println("UTF-8 인코딩 (16진수): " + bytesToHex(utf8Bytes));
        System.out.println("UTF-8 인코딩 (바이너리): " + bytesToBinary(utf8Bytes));

        // 3️⃣ UTF-16 인코딩
        byte[] utf16Bytes = emoji.getBytes(StandardCharsets.UTF_16);
        System.out.println("UTF-16 인코딩 (16진수): " + bytesToHex(utf16Bytes));
        System.out.println("UTF-16 인코딩 (바이너리): " + bytesToBinary(utf16Bytes));

        // 4️⃣ UTF-32 인코딩
        byte[] utf32Bytes = emoji.getBytes(StandardCharsets.UTF_32);
        System.out.println("UTF-32 인코딩 (16진수): " + bytesToHex(utf32Bytes));
        System.out.println("UTF-32 인코딩 (바이너리): " + bytesToBinary(utf32Bytes));
    }

    // 바이트 배열을 16진수 문자열로 변환하는 메서드
    private static String bytesToHex(byte[] bytes) {
        StringBuilder hex = new StringBuilder();
        for (byte b : bytes) {
            hex.append(String.format("%02X ", b));
        }
        return hex.toString().trim();
    }

    // 바이트 배열을 2진수 문자열로 변환하는 메서드
    private static String bytesToBinary(byte[] bytes) {
        StringBuilder binary = new StringBuilder();
        for (byte b : bytes) {
            binary.append(String.format("%8s ", Integer.toBinaryString(b & 0xFF)).replace(' ', '0'));
        }
        return binary.toString().trim();
    }
}
```

#### **✅ 실행 결과**

```
유니코드 코드 포인트 (10진수): 128049
유니코드 코드 포인트 (16진수): U+1F431
UTF-8 인코딩 (16진수): F0 9F 90 B1
UTF-8 인코딩 (바이너리): 11110000 10011111 10010000 10110001
UTF-16 인코딩 (16진수): FE FF D8 3D DC 31
UTF-16 인코딩 (바이너리): 11111110 11111111 11011000 00111101 11011100 00110001
UTF-32 인코딩 (16진수): 00 01 F4 31
UTF-32 인코딩 (바이너리): 00000000 00000001 11110100 00110001
```

#### **🔍 상세 설명**

1. **🐱의 유니코드 코드 포인트 (`U+1F431`)**
   * 🐱 이모지는 **U+1F431** (16진수), **128049** (10진수)로 표현됨.
   * `emoji.codePointAt(0)`을 사용하여 코드 포인트(숫자) 출력 가능.
2. **UTF-8 인코딩**
   * **F0 9F 90 B1 (16진수)**
   * **11110000 10011111 10010000 10110001 (2진수)**
   * UTF-8은 1\~4바이트를 사용하며, **🐱 이모지는 4바이트로 저장됨.**
3. **UTF-16 인코딩**
   * **FE FF D8 3D DC 31 (16진수)**
   * **11111110 11111111 11011000 00111101 11011100 00110001 (2진수)**
   * UTF-16은 보통 2바이트를 사용하지만, 🐱는 **서로게이트 페어(Surrogate Pair)** 로 표현되어 **4바이트**가 됨.
4. **UTF-32 인코딩**
   * **00 01 F4 31 (16진수)**
   * **00000000 00000001 11110100 00110001 (2진수)**
   * UTF-32는 모든 문자를 4바이트(32비트)로 표현.

### ✔ **왜 UTF-8에서 🐱 이모지는 4바이트인가?**

* 🐱 (U+1F431)은 **U+10000 이상**이므로 UTF-8에서 4바이트를 사용함.
* 첫 바이트(`11110xxx`)가 **4바이트로 인코딩됨을 의미**.

### ✔ **UTF-16에서 왜 6바이트인가?**

* UTF-16은 **BOM(Byte Order Mark) `FE FF`** 가 추가됨.
* 🐱는 **서로게이트 페어(2개 유니코드 블록 조합)** 로 표현됨.

### ✔ **UTF-32는 왜 4바이트인가?**

* UTF-32는 모든 문자를 **4바이트(32비트)** 로 고정 저장함.

### **🚀 최종 정리**

✅ **🐱의 코드 포인트** = `U+1F431`\
✅ **UTF-8 변환** → `F0 9F 90 B1` (4바이트)\
✅ **UTF-16 변환** → `FE FF D8 3D DC 31` (BOM 포함, 6바이트)\
✅ **UTF-32 변환** → `00 01 F4 31` (4바이트)\
✅ **UTF-8은 문자에 따라 가변 길이 인코딩을 사용하여 메모리 절약 가능!** 🚀

***

## 5. 유니코드 변환은 컴퓨터 내부에서 어디서 처리될까?

컴퓨터 내부 어디서 되고 있는 건데? 이미 이것에 대해서는 컴퓨터 내부에 프로그래밍 되어 있다는 거잖아 유니코드 변환 및 처리는 컴퓨터 내부에서 여러 계층을 거쳐 이루어진다.

<figure><img src="../../.gitbook/assets/image (141).png" alt=""><figcaption></figcaption></figure>

### **1) 키보드 입력 (입력 장치)**

{% hint style="warning" %}
키보드 인터럽트 (Interrupt)와 관련이 있다.&#x20;
{% endhint %}

* 우리가 키보드에서 🐱(U+1F431)을 입력하면, 운영체제(OS)는 이를 입력 이벤트(Input Event)로 처리.
  * 우리가 키보드를 누르면, 키보드 컨트롤러는 **하드웨어 인터럽트(Interrupt, IRQ)** 를 발생시킴.
* 물리적 키보드에서는 유니코드 입력이 불가능하므로, **이모지 입력기 (IME, Input Method Editor)** 또는 OS 내부의 **소프트웨어 키보드**를 사용하여 변환.
  * **운영체제의 인터럽트 핸들러(Interrupt Handler)** 가 이를 감지하고, 키보드 드라이버가 처리.

🔹 **운영체제별 키보드 이벤트 처리 방식(**&#xC6B4;영체제 입력 시스&#xD15C;**)**

* **Windows** → `WM_CHAR`, `WM_KEYDOWN` (Win32 API)
* **Mac/Linux** → `X11 Input Event`, `Wayland Key Event`
* **IME (Input Method Editor)** → 한글, 이모지 같은 **다중 문자 입력** 지원

### **2) 운영체제(OS)에서 처리**

{% hint style="warning" %}
운영체제는 입력된 문자(🐱)를 **유니코드 코드 포인트**(U+1F431)로 변환 후, **응용 프로그램(브라우저, 텍스트 에디터 등)** 에 전달한다.
{% endhint %}

💾 **운영체제의 유니코드 처리 시스템**

**🔹 Windows (윈도우)**

* **Win32 API** → `Text Services Framework (TSF)` 를 사용하여 문자 입력 처리
* **DirectWrite 또는 GDI+** → 폰트 렌더링 및 그래픽 처리

**🔹 MacOS (맥)**

* **CoreText 및 NSInputManager** → 유니코드 문자 입력 및 렌더링

**🔹 Linux (리눅스: Ubuntu, Debian, etc.)**

* **X11 또는 Wayland** → 키보드 이벤트 처리
* **Pango 및 HarfBuzz** → 폰트 렌더링 및 텍스트 레이아웃 처리

### **3) 애플리케이션(웹 브라우저, 터미널, 문서 편집기 등)**

* 웹 브라우저, 터미널, IDE 등 응용 프로그램이 운영체제로부터 유니코드 문자를 받음.
* 애플리케이션 내부에서 문자 데이터를 저장할 때, **UTF-8 / UTF-16 / UTF-32 등으로 인코딩하여 처리**.

🔹 **관련 요소**:

* **웹 브라우저**: HTML, JavaScript에서 문자열을 UTF-8로 처리 (`document.write("🐱")`)
* **터미널**: `echo "🐱"` 실행 시 UTF-8로 출력
* **텍스트 에디터**: Sublime Text, VSCode 등에서 유니코드 기반으로 저장

### **4) 폰트 렌더링 (GPU & 폰트 엔진)**

* 프로그램이 유니코드 문자를 화면에 표시하려면, **폰트 렌더링 엔진**을 통해 문자 → 이미지로 변환해야 함.
* 🐱 이모지를 표현하기 위해 운영체제 또는 웹 브라우저는 `Noto Color Emoji`, `Apple Color Emoji`, `Segoe UI Emoji` 같은 **이모지 폰트**를 사용.

🔹 **관련 요소**:

* **Windows**: `DirectWrite`
* **Mac**: `CoreText`
* **Linux**: `Pango`, `HarfBuzz`
* **웹 브라우저**: CSS `font-family: "Noto Color Emoji";`
* **GPU 렌더링**: 이모지 이미지를 GPU의 VRAM에 로드 후 렌더링

### **5) 출력 장치 (디스플레이, 모니터)**

* 최종적으로 🐱 이모지는 **픽셀 데이터로 변환되어 GPU를 통해 모니터에 표시됨**.
* 모니터에서 **RGB 픽셀 데이터**를 조합하여 🐱 모양의 이미지를 보여줌.

🔹 **관련 요소**:

* **Windows**: `GDI+`, `Direct2D`
* **Mac/Linux**: `Cairo Graphics`, `OpenGL`, `Vulkan`
* **모니터**: 픽셀 데이터 기반으로 🐱를 최종적으로 출력

### &#x20;**6) OS 내부 시스템이 정의된 곳**

운영체제의 유니코드 및 텍스트 렌더링 시스템은 **시스템 라이브러리 및 설정 파일**로 관리됨.

📂 **어디에서 관리될까?**

* Windows → `C:\Windows\Fonts\`
* MacOS → `/System/Library/Fonts/`
* Linux → `/usr/share/fonts/`, `/usr/share/unicode/`

📌 **유니코드 데이터베이스 위치**

* **Windows** → `C:\Windows\System32\unifont.dll`
* **Linux/macOS** → `/usr/share/unicode/`

이처럼 운영체제 내부에는 **유니코드 입력, 변환, 폰트 처리 시스템이 미리 정의되어 있다.**

### **🎯 최종 정리**

🐱 이모지를 입력하고 화면에 출력하는 과정은 다음과 같다, 아스키 코드와 동일함

1️⃣ **입력 단계**: 키보드/IME에서 유니코드 변환 (`U+1F431`)\
2️⃣ **운영체제 처리**: 입력된 유니코드를 메모리에 저장 후 프로그램에 전달\
3️⃣ **애플리케이션 내부 처리**: 유니코드 데이터를 UTF-8 등으로 인코딩하여 저장\
4️⃣ **폰트 렌더링**: 🐱를 표시할 수 있는 폰트(`Apple Color Emoji`, `Noto Emoji`)에서 이모지를 이미지로 변환\
5️⃣ **디스플레이 출력**: GPU가 최종적으로 픽셀을 조합하여 화면에 🐱를 표시

***

## 5. 아스키 코드와 유니코드는 같은 방식으로 동작할까? 그리고 현재 윈도우나 맥은 어떤 걸 쓸까?

{% hint style="info" %}
아니! **아스키(ASCII)와 유니코드(Unicode)는 개념적으로 비슷하지만, 동작 방식과 범위가 다르다.**\
둘 다 **문자를 숫자로 매핑하여 저장하고 처리하는 체계**지만, **지원하는 문자 범위와 인코딩 방식이 다름**.
{% endhint %}

### **1️⃣ 아스키(ASCII)**

✅ **7비트(0\~127)의 코드 체계**\
✅ **총 128개 문자만 표현 가능**\
✅ 영어, 숫자, 특수 기호 등 **기본적인 문자만 포함**\
✅ **고정 길이 인코딩(1바이트 = 1문자)**\
✅ **과거 운영체제(예: MS-DOS)에서는 기본 문자 체계로 사용됨**

#### **🔹 ASCII 예제**

| 문자 | ASCII (10진수) | ASCII (16진수) | 2진수      |
| -- | ------------ | ------------ | -------- |
| A  | 65           | 0x41         | 01000001 |
| B  | 66           | 0x42         | 01000010 |
| C  | 67           | 0x43         | 01000011 |

📌 **ASCII는 간단하고 빠르지만, 영어 이외의 언어를 표현할 수 없다는 한계가 있음.**\
📌 **한글, 중국어, 일본어, 이모지 등은 ASCII로 표현 불가능!**

### **2️⃣ 유니코드(Unicode)**

✅ **전 세계 모든 문자를 표현 가능**\
✅ **UCS(Universal Character Set) 기반으로 100만 개 이상의 문자를 지원**\
✅ **가변 길이 인코딩(1\~4바이트) → UTF-8, UTF-16, UTF-32 등으로 저장 가능**\
✅ **현대 운영체제(Windows, macOS, Linux)는 기본적으로 유니코드(UTF-8/UTF-16) 사용**

#### **🔹 유니코드 예제**

| 문자 | 유니코드 (16진수) | UTF-8 (16진수)  | 2진수 (UTF-8)                         |
| -- | ----------- | ------------- | ----------------------------------- |
| A  | U+0041      | 0x41          | 01000001                            |
| 한  | U+AC00      | 0xEA B0 80    | 11101010 10110000 10000000          |
| 🐱 | U+1F431     | 0xF0 9F 90 B1 | 11110000 10011111 10010000 10110001 |

📌 **UTF-8은 기존 ASCII(1바이트)와 호환되면서, 한글(3바이트), 이모지(4바이트) 등 다양한 문자를 표현할 수 있음.**\
📌 **즉, 영어는 1바이트로 저장되지만, 복잡한 문자(한글, 중국어, 이모지)는 3\~4바이트로 저장됨.**

### **3️⃣ 현대 운영체제(Windows, macOS)는 어떤 문자 체계를 사용하나?**

**🔹 Windows → 기본적으로 "UTF-16(LE)" 사용**\
**🔹 macOS, Linux → 기본적으로 "UTF-8" 사용**

#### 📌 **Windows (UTF-16)**

* Windows는 내부적으로 **UTF-16(LE, Little Endian)** 인코딩을 사용
* UTF-16은 **2바이트(16비트) 단위로 문자 저장**
* 단, **일반적인 텍스트 파일과 웹 브라우저에서는 UTF-8이 기본**
* `C:\Windows\System32\unifont.dll` 같은 시스템 파일에서 유니코드 지원

#### 📌 **macOS & Linux (UTF-8)**

* macOS와 Linux는 대부분 **UTF-8을 기본 문자 인코딩으로 사용**
* UTF-8은 ASCII와 100% 호환되면서, **모든 유니코드 문자를 표현 가능**
* `/usr/share/unicode/`에서 유니코드 데이터베이스를 관리

> 즉, <mark style="color:red;">윈도우는 UTF-16을 기반으로 하지만, 웹과 파일 시스템에서는 UTF-8</mark>을 많이 사용 <mark style="color:red;">맥과 리눅스는 기본적으로 UTF-8을 사용</mark>한다.

### **🔍 정리**

<figure><img src="../../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

| 비교 항목       | ASCII              | 유니코드(Unicode)                       |
| ----------- | ------------------ | ----------------------------------- |
| **비트 크기**   | 7비트(128문자)         | 16비트 이상(10억 개 이상 문자 지원)             |
| **지원 문자**   | 영어, 숫자, 특수기호       | 모든 언어(한글, 이모지 포함)                   |
| **인코딩 방식**  | 고정 길이 (1바이트 = 1문자) | 가변 길이 (UTF-8: 14바이트, UTF-16: 24바이트) |
| **운영체제 사용** | MS-DOS, 초창기 시스템    | Windows(UTF-16), macOS/Linux(UTF-8) |
| **웹 표준**    | ❌ (지원 안 함)         | ✅ UTF-8이 웹 표준                       |

> 과거(도스 시절)에는 ASCII가 쓰였지만, 현재는 Windows, macOS, Linux 모두 유니코드(UTF-8 또는 UTF-16)를 사용한다. 그래서 한글, 이모지, 다양한 언어를 자유롭게 표현할 수 있다.&#x20;



***

## 6. 🐱 **이모지를 인터넷에서 복사하면 어떤 과정이 일어날까?**

<figure><img src="../../.gitbook/assets/image (143).png" alt=""><figcaption></figcaption></figure>

### **1️⃣ 이모지(🐱)를 복사하는 순간**

✅ **🐱(U+1F431) 이모지는 유니코드 문자**\
✅ 복사하면 **OS의 클립보드(Clipboard)** 에 저장됨\
✅ 이 과정에서 운영체제(OS)는 UTF-8 또는 UTF-16 인코딩을 사용하여 **이진 데이터(비트)로 변환 후 저장**

### **2️⃣ 운영체제(OS) 관점에서 복사 & 저장 과정**

**예제: 🐱(U+1F431, CAT FACE)를 복사하면 내부적으로 어떤 과정이 발생할까?**

1. **키보드/마우스 입력 이벤트**
   * 사용자가 `Ctrl + C` (Windows) 또는 `Cmd + C` (Mac) 입력
   * 운영체제는 현재 선택된 텍스트(`🐱`)를 복사해야 한다는 **인터럽트(Interrupt)** 발생
   * 해당 문자를 **클립보드 메모리(Clipboard Memory)에 저장**
2. **유니코드 변환 & 인코딩**
   * 🐱의 유니코드 코드 포인트(U+1F431) 확인
   * UTF-8 인코딩: `F0 9F 90 B1` (4바이트)
   * UTF-16 인코딩: `D83D DC31` (2바이트 쌍)
   * 운영체제(Windows: UTF-16, Mac/Linux: UTF-8)는 이모지를 **메모리에 저장**
3. **클립보드에 저장**
   * Windows: UTF-16으로 저장 (메모리 내부 구조는 Little Endian)
   * macOS/Linux: UTF-8로 저장 (웹 브라우저와의 호환성 고려)
   * 저장된 🐱 데이터는 단순한 문자열이 아니라, **유니코드 문자 데이터**

### **3️⃣ 붙여넣기(Paste) 과정**

✅ 사용자가 `Ctrl + V` (Windows) 또는 `Cmd + V` (Mac)로 붙여넣기 하면?\
✅ 복사된 🐱의 유니코드 값이 프로그램으로 전달됨

**📌 붙여넣기 과정**

1. 운영체제(OS)는 클립보드에 저장된 데이터를 가져옴
2. 해당 프로그램(예: 메모장, 브라우저, VSCode 등)이 유니코드를 지원하는지 확인
3. 🐱의 유니코드 데이터를 적절한 **인코딩(UTF-8 또는 UTF-16)으로 변환**
4. 프로그램이 폰트 엔진(Font Engine)을 통해 🐱를 화면에 표시

### **4️⃣ 웹 브라우저에서 복사/붙여넣기 (HTML & JavaScript 관점)**

✅ 웹 브라우저(Chrome, Firefox, Safari 등)는 기본적으로 UTF-8을 사용\
✅ 사용자가 웹에서 🐱를 복사하면, UTF-8 인코딩으로 **이진 데이터(비트)로 변환**\
✅ 붙여넣을 때, HTML 또는 JavaScript 내부에서 🐱를 어떻게 처리하는지 확인

**📌 웹 브라우저 예제**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Emoji Copy</title>
</head>
<body>
    <p id="emoji">🐱</p>
    <button onclick="copyEmoji()">Copy</button>
    <script>
        function copyEmoji() {
            let emoji = document.getElementById("emoji").innerText;
            navigator.clipboard.writeText(emoji).then(() => {
                alert("Copied: " + emoji);
            });
        }
    </script>
</body>
</html>
```

✅ **브라우저는 🐱를 UTF-8(4바이트)로 변환하여 클립보드에 저장함**\
✅ **붙여넣을 때 다시 UTF-8에서 유니코드 문자로 변환 후 표시**

### **5️⃣ 아스키(ASCII) 관점에서 보면?**

✅ 🐱는 ASCII로 저장할 수 없음!\
✅ ASCII는 **7비트(0\~127)** 만 사용 → 오직 영어, 숫자, 특수기호만 지원\
✅ 🐱(U+1F431)는 ASCII 범위를 초과하는 문자 → **ASCII 시스템에서는 깨짐(□, ?)**\
✅ 따라서 현대 시스템(Windows, Mac, Linux)은 **유니코드를 기본 문자 인코딩으로 사용**

### **6️⃣ 정리: 이모지 복사 & 붙여넣기 전체 과정**

**🐱를 복사하는 순간**

* 운영체제(OS)는 🐱의 **유니코드 값을 인코딩(UTF-8/UTF-16)하여 클립보드에 저장**
* Windows는 **UTF-16**, macOS/Linux는 **UTF-8**을 사용

**🐱를 붙여넣을 때**

* 프로그램(브라우저, 메모장 등)이 유니코드 데이터를 읽고 폰트 엔진을 통해 표시

**웹 브라우저에서 복사 & 붙여넣기**

* 🐱는 UTF-8(4바이트)로 저장됨
* 붙여넣을 때 JavaScript `navigator.clipboard.writeText()` 사용 가능

**ASCII 시스템에서는 🐱를 표현할 수 없음**

* ASCII(7비트)에는 🐱가 없음 → 깨진 문자(□, ?)로 출력됨

> **우리가 인터넷에서 🐱를 복사하고 붙여넣는 모든 과정에서 OS는 유니코드를 사용하며, 웹 브라우저는 UTF-8을 기본 인코딩으로 사용함!**&#x20;

## **🧐 이모지가 깨지는 이유?**

🐱 **이모지를 복사했을 때 환경에 따라 깨지는 이유** 🤔

🐱(U+1F431, **CAT FACE**) 같은 이모지를 복사하고 붙여넣을 때 **환경(OS, 프로그램, 브라우저 등)에 따라 깨지는 이유**는 다음과 같은 요인이 있다.

<figure><img src="../../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>

**1️⃣ 폰트(Font) 문제**

유니코드 이모지를 표시하려면 해당 문자를 지원하는 **폰트가 필요**하지만, 모든 운영체제와 프로그램이 같은 폰트를 지원하는 것은 아니다.\
폰트가 없으면 이모지가 **□(빈 박스), ?(물음표), ⍰(깨진 문자)** 등으로 표시된다.

📌 **예제: 환경별 이모지 폰트 지원 여부**

| 환경                               | 폰트                    | 🐱 표시 가능? |
| -------------------------------- | --------------------- | --------- |
| Windows 10+                      | Segoe UI Emoji        | ✅         |
| Windows 7                        | Segoe UI Symbol (제한적) | ❌ (□, ?)  |
| MacOS                            | Apple Color Emoji     | ✅         |
| Linux (Ubuntu)                   | Noto Color Emoji      | ✅         |
| Linux (CentOS 7)                 | 기본 폰트 없음              | ❌ (□, ?)  |
| Android                          | Noto Emoji            | ✅         |
| iOS                              | Apple Color Emoji     | ✅         |
| 웹 브라우저 (Chrome, Safari, Firefox) | OS별 기본 폰트 사용          | ✅         |

**해결 방법:**

* 이모지가 깨지는 경우, 해당 운영체제/브라우저에 적절한 **이모지 폰트를 설치**
* 웹에서는 CSS에서 `"Noto Color Emoji", "Apple Color Emoji", "Segoe UI Emoji"` 폰트를 설정하여 해결 가능

***

**2️⃣ 인코딩(Encoding) 문제**

이모지는 **UTF-8(4바이트) 또는 UTF-16(2바이트 쌍)** 으로 저장된다.\
붙여넣을 때 프로그램이 이를 잘못된 인코딩으로 해석하면 **깨진 문자(□, ?)** 로 표시된다.

📌 **예제: 인코딩 오류 발생 가능성**

| 프로그램                 | 기본 문자 인코딩         | 🐱 표시 가능? |
| -------------------- | ----------------- | --------- |
| Windows 메모장 (기본)     | ANSI (ISO-8859-1) | ❌ (깨짐)    |
| Windows 메모장 (UTF-8)  | UTF-8             | ✅         |
| 터미널 (기본 Windows CMD) | CP949             | ❌ (깨짐)    |
| VSCode               | UTF-8             | ✅         |
| macOS 터미널            | UTF-8             | ✅         |

**💡 해결 방법:**

* **UTF-8로 인코딩을 설정**해야 함 (Windows 메모장에서 저장 시 UTF-8 선택)
* 터미널에서 깨질 경우 `chcp 65001` (UTF-8 모드) 사용

**3️⃣ 클립보드(Clipboard) 호환성 문제**

운영체제별로 클립보드 데이터 저장 방식이 다르다.

* **Windows는 UTF-16으로 저장**
* **macOS/Linux는 UTF-8으로 저장**

이 차이로 인해 일부 프로그램에서 깨질 수 있다.

📌 **예제: 클립보드 인코딩 차이**

| 환경                 | 클립보드 저장 인코딩 | 🐱 복사 & 붙여넣기 |
| ------------------ | ----------- | ------------ |
| Windows            | UTF-16      | ✅ (일반적으로 정상) |
| macOS              | UTF-8       | ✅            |
| Linux (Ubuntu)     | UTF-8       | ✅            |
| Linux (서버, X11 없음) | 클립보드 지원 없음  | ❌            |

📌 **깨짐 현상 발생 예제**

```sh
# Windows (UTF-16)에서 클립보드 복사 후 Linux (UTF-8) 터미널에 붙여넣기
🐱 → � (깨진 문자)
```

**💡 해결 방법:**

* Windows에서 UTF-16을 UTF-8로 변환 후 붙여넣기 (`iconv` 사용 가능)
* 클립보드에서 데이터를 직접 UTF-8로 변환하는 프로그램 사용

**4️⃣ 문자 결합 방식(ZWJ, Zero Width Joiner) 문제**

일부 이모지는 여러 개의 문자 조합으로 만들어진다.\
예: 가족 이모지 👨‍👩‍👧‍👦

* `👨(U+1F468) + ZWJ(U+200D) + 👩(U+1F469) + ZWJ(U+200D) + 👧(U+1F467) + ZWJ(U+200D) + 👦(U+1F466)`
* ✅ **일부 프로그램은 ZWJ(Zero Width Joiner)를 제대로 처리하지 못해 깨짐**

📌 **ZWJ 미지원 환경에서는 어떻게 보일까?**

| 환경                                       | 👨‍👩‍👧‍👦 표시 방식 |
| ---------------------------------------- | ----------------- |
| 최신 OS (Windows 10+, MacOS, Android, iOS) | 👨‍👩‍👧‍👦 (정상)  |
| 구형 OS (Windows 7, Ubuntu 14)             | 👨 👩 👧 👦 (분리됨) |
| 일부 터미널                                   | □ □ □ □ (깨짐)      |

**💡 해결 방법:**

* OS 또는 브라우저를 최신 버전으로 업데이트
* ZWJ를 지원하는 프로그램(웹 브라우저, 메시지 앱) 사용

**5️⃣ 이모지 색상(Color Emoji) 지원 문제**

일부 환경에서는 이모지가 **흑백으로 보이거나 깨질 수 있다.**\
이는 OS나 프로그램이 **컬러 이모지를 지원하는 폰트를 가지고 있지 않기 때문**이다.

📌 **예제: 컬러 이모지 지원 여부**

| 환경                           | 🐱 표시 색상 |
| ---------------------------- | -------- |
| Windows 10+ (Segoe UI Emoji) | 🐱 (컬러)  |
| Windows 7 (Segoe UI Symbol)  | 🐱 (흑백)  |
| macOS (Apple Color Emoji)    | 🐱 (컬러)  |
| Linux (Noto Color Emoji)     | 🐱 (컬러)  |
| Linux (기본 폰트 없음)             | □ (깨짐)   |

**💡 해결 방법:**

* 컬러 이모지를 지원하는 폰트를 설치 (`Noto Color Emoji` 등)
* 일부 프로그램에서는 흑백 폰트만 지원 → **컬러 지원 폰트로 변경**

**✅ 결론: 🐱 이모지가 깨지는 이유 & 해결 방법**

| 원인         | 발생 가능성                 | 해결 방법                                                       |
| ---------- | ---------------------- | ----------------------------------------------------------- |
| 폰트 미지원     | OS/프로그램에 따라 다름         | 폰트 설치 (Segoe UI Emoji, Apple Color Emoji, Noto Color Emoji) |
| 잘못된 인코딩    | 일부 프로그램에서 발생           | UTF-8로 저장/읽기 설정                                             |
| 클립보드 호환성   | Windows ↔ Linux 등에서 발생 | UTF-16 → UTF-8 변환 사용 (`iconv`)                              |
| ZWJ 지원 부족  | 구형 OS에서 발생             | 최신 OS/브라우저 사용                                               |
| 컬러 이모지 미지원 | Windows 7, 일부 터미널 등    | 컬러 폰트 설치                                                    |

***

## 7. 그렇다면 결국 이미지도 비트아닌가.. **🧐**이미지는 어떤식으로 변환되는데? (비트와 픽셀, 그리고 GPU의 역할)

**1️⃣ 모든 데이터는 결국 비트(Binary)**

🐱 이모지도, 📄 텍스트도, 🎵 음악도 결국 **비트(Binary, 0과 1의 조합)** 로 표현됨.\
그렇다면 **이미지는 어떻게 비트로 변환되고, 컴퓨터 내부에서 처리될까?**

**2️⃣ 이미지의 비트 표현 (Raster Graphics)**

이미지는 기본적으로 **픽셀(Pixel, Picture Element)** 로 구성됨.\
각 픽셀은 **RGB (Red, Green, Blue) 색상 값**을 가지며, **비트(Binary)** 로 저장됨.

📌 **예: 24비트 컬러 이미지**

* **RGB 값 각각 8비트(0\~255) → 24비트(3바이트)**
* 🎨 예: **빨간색(RGB: 255, 0, 0)**
  * **2진수:** `11111111 00000000 00000000`
  * **16진수:** `0xFF0000`

📌 **예: 1픽셀만 저장할 경우 (24비트 이미지)**

| 픽셀 색상 | 10진수 (RGB)  | 16진수       | 2진수 (Binary)                 |
| ----- | ----------- | ---------- | ---------------------------- |
| 🔴 빨강 | (255, 0, 0) | `0xFF0000` | `11111111 00000000 00000000` |
| 🟢 초록 | (0, 255, 0) | `0x00FF00` | `00000000 11111111 00000000` |
| 🔵 파랑 | (0, 0, 255) | `0x0000FF` | `00000000 00000000 11111111` |

✅ **정리: 이미지 파일은 픽셀(RGB) 값을 2진수로 변환하여 저장하는 구조!**

**3️⃣ 이미지 파일 포맷 (JPEG, PNG, BMP 등)**

이미지는 **파일 크기를 줄이기 위해 압축(Compression)** 함.\
주요 이미지 파일 형식과 압축 방식:

#### **🔹 비손실 압축 (Lossless Compression)**

* 🎯 원본 품질 그대로 유지
* **PNG, BMP, GIF**
* **예시:** 동일한 픽셀 값이 반복될 경우, 압축하여 저장 (Run-Length Encoding)

#### **🔹 손실 압축 (Lossy Compression)**

* 🎯 용량을 줄이지만, 약간의 화질 손실
* **JPEG, WebP**
* **예시:** 사람 눈에 덜 민감한 색상을 버려서 용량 절약

✅ **정리: 이미지 포맷은 비트 데이터를 효율적으로 저장하기 위한 압축 방식이 다름!**

**4️⃣ 이미지가 모니터에 출력될 때 (GPU & VRAM)**

#### **🖥️ 디지털 이미지 → 화면 출력 과정**

1️⃣ **파일을 읽음 (JPEG, PNG, BMP 등) → 비트 데이터(픽셀)로 변환**\
2️⃣ **CPU가 데이터를 해석 후, GPU에 전달**\
3️⃣ **GPU가 VRAM(Video RAM)에 저장 후, 픽셀 단위로 화면에 출력**\
4️⃣ **모니터가 RGB 픽셀을 조합하여 화면을 표시**

#### **🔹 GPU가 중요한 이유?**

✅ **CPU 대신 GPU가 이미지 연산을 처리하여 빠른 렌더링 가능!**\
✅ **3D 그래픽이나 동영상도 같은 원리로 픽셀 단위로 처리됨!**

**5️⃣ 정리: 이미지도 결국 비트!**

📌 **이미지는 픽셀 단위로 저장되며, RGB 값을 2진수(비트)로 변환하여 저장**\
📌 **GPU가 비트 데이터를 해석하여 모니터에 픽셀 단위로 출력**\
📌 **JPEG, PNG 같은 파일 포맷은 압축 방식에 따라 저장 방식이 다름**

➡ **즉, 이모지도 결국 픽셀 단위로 저장된 비트 데이터!** 🎨🖥️🚀

### 7) 이미지 압축을이용한 알고리즘

웹툰 서비스에서 이미지를 다운로드할 때 파일 크기를 줄이는 방법에는 **이미지 압축 알고리즘, 데이터 인코딩, 스트리밍 기법** 등이 활용됩니다.\
Java에서 웹툰 이미지를 효율적으로 압축하고 다운로드할 수 있도록 **JPEG/PNG 압축, WebP 변환, HTTP 압축 전송** 등을 적용할 수 있습니다.

***

1️⃣ **웹툰 이미지 최적화 원칙**

✅ **비손실 압축 (Lossless Compression)** → PNG, WebP(Lossless) → 그림 선명도 유지\
✅ **손실 압축 (Lossy Compression)** → JPEG, WebP(Lossy) → 용량 감소\
✅ **Resizing (리사이징)** → 모바일, 태블릿, PC별 해상도 조정\
✅ **스트리밍 기법** → Progressive JPEG, Chunked Transfer Encoding

***

**2️⃣ 알고리즘을 활용한 이미지 압축 방법 (Java)**

#### **🔹 1. JPEG 품질 조절을 통한 손실 압축**

* 웹툰은 선명도가 중요한 경우가 많아 **JPEG의 품질(Quality) 조절**을 통해 적절한 압축 필요
* **Java ImageIO + JPEGImageWriteParam**을 사용하여 압축
* **Quality 0.75**(75%) 정도로 설정하면 파일 크기가 30\~50% 감소

```java
import javax.imageio.ImageIO;
import javax.imageio.ImageWriteParam;
import javax.imageio.ImageWriter;
import javax.imageio.stream.ImageOutputStream;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Iterator;

public class ImageCompressor {
    public static void compressJPEG(String inputPath, String outputPath, float quality) throws IOException {
        File inputFile = new File(inputPath);
        BufferedImage image = ImageIO.read(inputFile);

        File compressedFile = new File(outputPath);
        FileOutputStream os = new FileOutputStream(compressedFile);

        Iterator<ImageWriter> writers = ImageIO.getImageWritersByFormatName("jpeg");
        ImageWriter writer = writers.next();

        ImageOutputStream ios = ImageIO.createImageOutputStream(os);
        writer.setOutput(ios);

        ImageWriteParam param = writer.getDefaultWriteParam();
        param.setCompressionMode(ImageWriteParam.MODE_EXPLICIT);
        param.setCompressionQuality(quality); // 0.0 ~ 1.0 (0은 고압축, 1은 무압축)

        writer.write(null, new javax.imageio.IIOImage(image, null, null), param);

        os.close();
        ios.close();
        writer.dispose();
    }

    public static void main(String[] args) throws IOException {
        compressJPEG("webtoon_original.jpg", "webtoon_compressed.jpg", 0.75f);
        System.out.println("JPEG 이미지 압축 완료!");
    }
}
```

🔹 **효과:**\
✅ **이미지 크기 최대 50% 감소**\
✅ **웹툰의 선명도를 유지하면서 압축 가능**

***

#### **🔹 2. PNG를 WebP로 변환하여 압축**

**WebP**는 Google이 개발한 이미지 포맷으로, PNG보다 25\~30% 가볍고, 투명도를 지원\
**Java에서 WebP 변환 라이브러리(`libwebp`) 사용**

```java
import com.luciad.imageio.webp.WebPWriteParam;
import javax.imageio.ImageIO;
import javax.imageio.ImageWriteParam;
import javax.imageio.ImageWriter;
import javax.imageio.stream.ImageOutputStream;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Iterator;

public class PNGtoWebP {
    public static void convertToWebP(String inputPath, String outputPath, float quality) throws IOException {
        File inputFile = new File(inputPath);
        BufferedImage image = ImageIO.read(inputFile);

        File outputFile = new File(outputPath);
        FileOutputStream os = new FileOutputStream(outputFile);

        Iterator<ImageWriter> writers = ImageIO.getImageWritersByFormatName("webp");
        ImageWriter writer = writers.next();

        ImageOutputStream ios = ImageIO.createImageOutputStream(os);
        writer.setOutput(ios);

        WebPWriteParam param = new WebPWriteParam(writer.getLocale());
        param.setCompressionMode(ImageWriteParam.MODE_EXPLICIT);
        param.setCompressionQuality(quality);

        writer.write(null, new javax.imageio.IIOImage(image, null, null), param);

        os.close();
        ios.close();
        writer.dispose();
    }

    public static void main(String[] args) throws IOException {
        convertToWebP("webtoon_original.png", "webtoon_compressed.webp", 0.8f);
        System.out.println("PNG → WebP 변환 완료!");
    }
}
```

🔹 **효과:**\
✅ **PNG보다 파일 크기 30% 이상 감소**\
✅ **투명도 유지 가능 (Alpha 채널 지원)**

***

**3️⃣ HTTP 전송 최적화 (스트리밍 & 압축)**

이미지 크기를 줄이는 것 외에도 **전송 속도를 최적화**해야 함.

#### **🔹 1. HTTP 압축 (Gzip & Brotli)**

웹 서버에서 Gzip 압축을 적용하면 전송 크기가 30\~50% 줄어듦.\
Spring Boot 기반으로 설정 가능:

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.filter.CommonsRequestLoggingFilter;
import org.springframework.boot.web.server.Compression;
import org.springframework.boot.web.server.ConfigurableServletWebServerFactory;
import org.springframework.boot.web.server.WebServerFactoryCustomizer;

@Configuration
public class CompressionConfig {
    @Bean
    public WebServerFactoryCustomizer<ConfigurableServletWebServerFactory> compressionCustomizer() {
        return factory -> {
            Compression compression = new Compression();
            compression.setEnabled(true);
            compression.setMimeTypes(new String[]{"image/webp", "image/jpeg", "image/png"});
            compression.setMinResponseSize(1024); // 1KB 이상일 때만 압축
            factory.setCompression(compression);
        };
    }
}
```

✅ **효과:**\
🚀 이미지 크기 30\~50% 줄이면서 **다운로드 속도 향상**

***

**4️⃣ 이미지 스트리밍 (Chunked Transfer Encoding)**

웹툰의 경우, **한꺼번에 다운로드하지 않고, 부분적으로 스트리밍**하면 사용자 경험이 향상됨.\
**Spring Boot**에서 **StreamingResponseBody**를 이용하여 **Chunked Transfer Encoding** 적용:

```java
import org.springframework.core.io.InputStreamResource;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.io.*;

@RestController
@RequestMapping("/webtoon")
public class WebtoonController {
    
    @GetMapping("/image")
    public ResponseEntity<InputStreamResource> streamWebtoon(@RequestParam String imagePath) throws IOException {
        File file = new File(imagePath);
        InputStream inputStream = new FileInputStream(file);
        InputStreamResource resource = new InputStreamResource(inputStream);

        HttpHeaders headers = new HttpHeaders();
        headers.add("Content-Disposition", "inline; filename=" + file.getName());
        headers.add("Transfer-Encoding", "chunked");

        return new ResponseEntity<>(resource, headers, HttpStatus.OK);
    }
}
```

✅ **효과:**\
🚀 사용자가 웹툰 페이지를 열 때 **점진적으로 로딩 → 빠른 사용자 경험**

***

**5️⃣ 최종 정리 🚀**

💡 **웹툰 서비스를 위한 최적화 방법:**\
✅ **JPEG 압축** (`ImageIO`) → **75% 품질 유지하면서 용량 감소**\
✅ **PNG → WebP 변환** → **파일 크기 30% 절감 & 투명도 유지**\
✅ **Gzip / Brotli 압축 적용** → **전송 크기 50% 절감**\
✅ **Chunked Transfer Streaming** → **웹툰을 점진적으로 로딩하여 빠른 UX 제공**

### **8)** 일부 문자는 1바이트(8비트), 다른 문자는 4바이트(32비트)인가? 분명 한 문자를 8비트로 표현한다고 했는데?

{% hint style="danger" %}
여기서 잠깐 : 왜 일부 문자는 1바이트(8비트), 다른 문자는 4바이트(32비트)인가? 분명 한 문 자를 8비트로 표현한다고 했는데?
{% endhint %}

1. **기본 개념: 바이트와 비트**

* **1바이트(Byte) = 8비트(Bit)**
* **2바이트 = 16비트**
* **3바이트 = 24비트**
* **4바이트 = 32비트**

💡 **즉, "4바이트"라고 하면 32비트(4 × 8)라는 뜻!**\
**하지만, 4바이트로 표현된다고 해서 기본적으로 16비트를 의미하는 것은 아님.**

2. **문자(유니코드)는 크기가 다르다!**

컴퓨터에서 문자를 저장하는 방식은 유니코드(Unicode) + 인코딩(UTF-8, UTF-16, UTF-32)에 따라 달라짐.

* **영어 알파벳 (A, B, C 등) → 1바이트(8비트)**
* **유럽어, 아랍어, 그리스어 문자 → 2바이트(16비트)**
* **한글, 중국어, 일본어 등 → 3바이트(24비트)**
* **이모지(🐱, 😂, 🚀 등) → 4바이트(32비트)**

✅ **즉, 문자의 종류에 따라 필요한 크기가 다르다!**\
이 때문에 일부 문자는 1바이트(8비트), 일부 문자는 4바이트(32비트)를 사용한다.

3. **UTF-8은 가변 길이 인코딩 방식**

> UTF-8은 **문자마다 필요한 만큼 바이트(1\~4바이트)를 할당**하는 **가변 길이 인코딩**을 사용.

| **유니코드 범위**          | **UTF-8 바이트 수** | **비트 수** | **예시 문자**       |
| -------------------- | --------------- | -------- | --------------- |
| `U+0000 ~ U+007F`    | 1바이트            | 8비트      | A (U+0041)      |
| `U+0080 ~ U+07FF`    | 2바이트            | 16비트     | π (U+03C0)      |
| `U+0800 ~ U+FFFF`    | 3바이트            | 24비트     | 한글 '가' (U+AC00) |
| `U+10000 ~ U+10FFFF` | 4바이트            | 32비트     | 🐱 (U+1F431)    |

💡 **즉, UTF-8은 문자의 복잡도에 따라 1\~4바이트를 동적으로 사용하여 저장!**

4. &#x20;**왜 일부 문자는 4바이트(32비트)로 저장될까?**

**이모지(🐱, 😂, 🚀) 같은 특수 문자는 유니코드 코드 포인트가 크기 때문에 4바이트가 필요함.**

예를 들어 🐱(고양이 이모지)의 유니코드 값:

* **U+1F431** → 16진수로 보면 5자리 숫자
* 8비트(1바이트)로 표현할 수 없음 → **4바이트(32비트) 필요**

💡 **즉, 단순한 문자(영어, 숫자)는 1바이트로 충분하지만, 복잡한 문자는 더 많은 바이트(2\~4바이트)가 필요!**

5. **UTF-8의 장점: 왜 가변 길이 인코딩을 사용할까?**

* **공간 절약**: 영어만 사용하면 1바이트만 사용 → 불필요한 공간 낭비 방지.
* **유연성**: 모든 언어(한글, 중국어, 일본어, 이모지 등)를 저장 가능.
* **아스키(ASCII)와 호환**: 기존 영어 기반 시스템에서도 그대로 사용 가능.

#### **최종 정리**

📌 문자마다 저장 크기가 다름 → 간단한 문자는 1바이트, 복잡한 문자는 4바이트 사용.\
📌 UTF-8은 문자의 복잡도에 따라 1\~4바이트를 동적으로 할당하는 방식.\
📌 영어(A, B, C)는 1바이트(8비트), 한글(가, 나, 다)은 3바이트(24비트), 이모지(🐱, 🚀)는 4바이트(32비트).\
📌 UTF-8은 공간 절약 + 아스키(ASCII)와 호환되는 최적의 인코딩 방식!&#x20;

### **9) 유니코드의 UTF-8은 8비트(1바이트)만 있는데 어떻게 4바이트까지 확장 가능할까?**

#### **⭐ 핵심 원리: 다중 바이트를 연결해서 확장**

* 첫 번째 바이트가 몇 바이트를 사용할지를 정해주고,
* 나머지 바이트는 앞쪽에 `10` 비트 패턴을 사용해 연결됨.

> 즉, <mark style="color:red;">각 바이트는 여전히 8비트지만, 여러 개를 합쳐서 32비트까지 확장하는 것</mark>**!**

**🛠️ 예제 분석**

#### **1️⃣ ASCII 문자 ‘A’ (1바이트)**

* **유니코드 코드 포인트:** `U+0041` (10진수 `65`)
* **2진수 표현:** `00000000 01000001` (16비트)
* **UTF-8 인코딩:** `01000001` (1바이트, 아스키와 동일)

➡ **첫 바이트가 `0xxxxxxx`이므로 1바이트만 사용되며, 그대로 저장**

➡ 즉, **A는 아스키 코드 그대로 저장됨** (`0x41`)

***

#### **2️⃣ ‘π’ (그리스 문자, 2바이트)**

* **유니코드 코드 포인트:** `U+03C0` (10진수 `960`)
  * 유니코드 값이 `0x0080~0x07FF` 사이이므로, **2바이트를 사용해야 함**
* **2진수 표현:** `00000011 11000000` (16비트)
* **UTF-8 인코딩:** `11001111 10000000` (2바이트 사용)

```
11001111 10000000  (0xCF 0x80)
```

* 첫 번째 바이트: `110xxxxx` (MSB 3비트를 `110`으로 설정)
* 두 번째 바이트: `10xxxxxx` (MSB 2비트를 `10`으로 설정)
*   실제로 저장되는 데이터:

    ```
    11001111 (첫 번째 바이트, 5비트 데이터 포함)
    10000000 (두 번째 바이트, 6비트 데이터 포함)
    ```
* 이렇게 하면 <mark style="color:red;">11비트(5비트 + 6비트)</mark>를 모두 저장할 수 있음.

***

#### **3️⃣ ‘가’ (한글, 3바이트)**

* **유니코드:** `U+AC00` (10진수 `44032`)
* **2진수 표현:** `10101100 00000000` (16비트)

#### **🔍 UTF-8 인코딩 과정**

`U+AC00`는 **0x0800\~0xFFFF 범위에 속하므로 3바이트 사용**

* 첫 번째 바이트: **1110xxxx** (MSB 4비트를 `1110`으로 설정) ➡ **첫 바이트가 `1110xxxx`이므로 3바이트 사용**
* 두 번째 바이트: **10xxxxxx** (MSB 2비트를 `10`으로 설정)
* 세 번째 바이트: **10xxxxxx** (MSB 2비트를 `10`으로 설정)

#### **✅ 실제 변환 결과**

```scss
11101010 10110000 10000000
(EA B0 80)
```

* **첫 번째 바이트:** `11101010` (4비트 데이터 포함)
* **두 번째 바이트:** `10110000` (6비트 데이터 포함)
* **세 번째 바이트:** `10000000` (6비트 데이터 포함)

➡ **총 16비트(4비트 + 6비트 + 6비트)를 저장**\
➡ **UTF-8에서 한글 1자는 3바이트를 차지함**

***

#### **4️⃣ ‘🐱’ (이모지, 4바이트)**

* **유니코드:** `U+1F431` (10진수 `128049`)
* **2진수 표현:** `00011111 01000011 00010001` (21비트)

#### **🔍 UTF-8 인코딩 과정**

`U+1F431`는 **0x10000\~0x10FFFF 범위에 속하므로 4바이트 사용**

* 첫 번째 바이트: **11110xxx** (MSB 5비트를 `11110`으로 설정) ➡ **첫 바이트가 `11110xxx`이므로 4바이트 사용**
* 두 번째 바이트: **10xxxxxx** (MSB 2비트를 `10`으로 설정)
* 세 번째 바이트: **10xxxxxx** (MSB 2비트를 `10`으로 설정)
* 네 번째 바이트: **10xxxxxx** (MSB 2비트를 `10`으로 설정)

#### **✅ 실제 변환 결과**

```scss
11110000 10011111 10010000 10110001
(F0 9F 90 B1)
```

* **첫 번째 바이트:** `11110000` (3비트 데이터 포함)
* **두 번째 바이트:** `10011111` (6비트 데이터 포함)
* **세 번째 바이트:** `10010000` (6비트 데이터 포함)
* **네 번째 바이트:** `10110001` (6비트 데이터 포함)

➡ **총 21비트(3비트 + 6비트 + 6비트 + 6비트)를 저장**\
➡ **UTF-8에서 이모지는 4바이트를 차지함**



**결론**

* UTF-8은 **첫 번째 바이트의 앞쪽 비트 패턴을 사용해 바이트 수를 결정**
* **모든 문자는 최소 1바이트\~최대 4바이트로 동적 확장 가능**
* **각 바이트는 여전히 8비트지만, 여러 개를 연결하여 32비트까지 표현 가능!**&#x20;



## 10) **📌 Java를 이용한 이미지 압축 및 웹 서비스에서의 이미지 스트리밍 & HTTP 압축 구현**

### **🖼 1. Java를 이용한 이미지(JPEG, PNG) 압축 방법**

이미지 압축에는 **손실 압축(Lossy Compression)** 과 **비손실 압축(Lossless Compression)** 이 있습니다.

* **손실 압축(Lossy Compression)**: 일부 데이터를 버려서 파일 크기를 줄이지만, 화질 손실이 발생할 수 있음 (JPEG, WebP)
* **비손실 압축(Lossless Compression)**: 품질을 유지하면서 압축 (PNG, WebP)

#### **✅ 1-1. JPEG 이미지 압축 (손실 압축)**

Java의 `ImageIO` 와 `ImageWriteParam` 클래스를 사용하면 JPEG 품질을 조절하여 압축할 수 있습니다.

🔹 **JPEG 품질을 75%로 조정하여 압축하는 코드**

```java
import javax.imageio.ImageIO;
import javax.imageio.ImageWriteParam;
import javax.imageio.ImageWriter;
import javax.imageio.stream.ImageOutputStream;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Iterator;

public class ImageCompressor {
    public static void compressJPEG(String inputPath, String outputPath, float quality) throws IOException {
        File inputFile = new File(inputPath);
        BufferedImage image = ImageIO.read(inputFile);

        File compressedFile = new File(outputPath);
        FileOutputStream os = new FileOutputStream(compressedFile);

        Iterator<ImageWriter> writers = ImageIO.getImageWritersByFormatName("jpeg");
        ImageWriter writer = writers.next();

        ImageOutputStream ios = ImageIO.createImageOutputStream(os);
        writer.setOutput(ios);

        ImageWriteParam param = writer.getDefaultWriteParam();
        param.setCompressionMode(ImageWriteParam.MODE_EXPLICIT);
        param.setCompressionQuality(quality); // 0.0 (최고 압축) ~ 1.0 (최소 압축)

        writer.write(null, new javax.imageio.IIOImage(image, null, null), param);

        os.close();
        ios.close();
        writer.dispose();
    }

    public static void main(String[] args) throws IOException {
        compressJPEG("input.jpg", "compressed.jpg", 0.75f);
        System.out.println("JPEG 이미지 압축 완료!");
    }
}
```

✅ **효과**:

* JPEG 품질을 75%로 설정하여 파일 크기를 30\~50% 줄일 수 있음
* 웹 사이트에서 이미지 로딩 속도를 개선할 수 있음

***

#### **✅ 1-2. PNG 이미지 압축 (비손실 압축)**

PNG는 **비손실 압축**이기 때문에, 기본적으로 파일 크기를 줄이는 방법은 **팔레트 최적화**나 **WebP 변환**을 사용하는 것입니다.

🔹 **Java에서 PNG를 WebP로 변환하여 압축하는 코드**\
WebP는 PNG보다 30% 이상 압축률이 뛰어나므로 추천됩니다.

```java
import com.luciad.imageio.webp.WebPWriteParam;
import javax.imageio.ImageIO;
import javax.imageio.ImageWriteParam;
import javax.imageio.ImageWriter;
import javax.imageio.stream.ImageOutputStream;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Iterator;

public class PNGtoWebP {
    public static void convertToWebP(String inputPath, String outputPath, float quality) throws IOException {
        File inputFile = new File(inputPath);
        BufferedImage image = ImageIO.read(inputFile);

        File outputFile = new File(outputPath);
        FileOutputStream os = new FileOutputStream(outputFile);

        Iterator<ImageWriter> writers = ImageIO.getImageWritersByFormatName("webp");
        ImageWriter writer = writers.next();

        ImageOutputStream ios = ImageIO.createImageOutputStream(os);
        writer.setOutput(ios);

        WebPWriteParam param = new WebPWriteParam(writer.getLocale());
        param.setCompressionMode(ImageWriteParam.MODE_EXPLICIT);
        param.setCompressionQuality(quality); // 0.0 ~ 1.0 (0은 최대 압축)

        writer.write(null, new javax.imageio.IIOImage(image, null, null), param);

        os.close();
        ios.close();
        writer.dispose();
    }

    public static void main(String[] args) throws IOException {
        convertToWebP("input.png", "compressed.webp", 0.8f);
        System.out.println("PNG → WebP 변환 완료!");
    }
}
```

✅ **효과**:

* PNG → WebP 변환 시 **파일 크기가 30% 이상 감소**
* WebP는 PNG와 달리 투명도를 유지하면서 용량을 줄일 수 있음

***

### **🌍 2. 웹 서비스에서 이미지 스트리밍 & HTTP 압축 구현**

#### **✅ 2-1. HTTP 압축 (Gzip & Brotli)**

웹 서버에서 HTTP 응답을 압축하면 **이미지 및 HTML/CSS/JS 파일의 크기를 50% 이상 줄일 수 있음**.

🔹 **Spring Boot에서 Gzip 압축 활성화**

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.boot.web.server.Compression;
import org.springframework.boot.web.server.ConfigurableServletWebServerFactory;
import org.springframework.boot.web.server.WebServerFactoryCustomizer;

@Configuration
public class CompressionConfig {
    @Bean
    public WebServerFactoryCustomizer<ConfigurableServletWebServerFactory> compressionCustomizer() {
        return factory -> {
            Compression compression = new Compression();
            compression.setEnabled(true);
            compression.setMimeTypes(new String[]{"image/webp", "image/jpeg", "image/png"});
            compression.setMinResponseSize(1024); // 1KB 이상일 때만 압축
            factory.setCompression(compression);
        };
    }
}
```

✅ **효과**:

* **HTTP 압축 적용(Gzip, Brotli)** → 전송 크기 최대 50% 감소
* 브라우저가 자동으로 압축된 데이터를 해석하여 빠르게 표시 가능

***

#### **✅ 2-2. 이미지 스트리밍 (Chunked Transfer Encoding)**

웹툰처럼 한 번에 다운로드하지 않고 **부분적으로 스트리밍(점진적 로딩)** 하면 사용자 경험이 좋아짐.

🔹 **Spring Boot에서 이미지 스트리밍(Chunked Transfer Encoding) 구현**

```java
import org.springframework.core.io.InputStreamResource;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.io.*;

@RestController
@RequestMapping("/webtoon")
public class WebtoonController {

    @GetMapping("/image")
    public ResponseEntity<InputStreamResource> streamWebtoon(@RequestParam String imagePath) throws IOException {
        File file = new File(imagePath);
        InputStream inputStream = new FileInputStream(file);
        InputStreamResource resource = new InputStreamResource(inputStream);

        HttpHeaders headers = new HttpHeaders();
        headers.add("Content-Disposition", "inline; filename=" + file.getName());
        headers.add("Transfer-Encoding", "chunked"); // 점진적 로딩

        return new ResponseEntity<>(resource, headers, HttpStatus.OK);
    }
}
```

✅ **효과**:

* 사용자가 웹툰 페이지를 열 때 **점진적으로 로딩**
* 첫 번째 페이지가 로드될 때 기다릴 필요 없이 **빠른 사용자 경험 제공**

***

### **📌 최종 정리 🚀**

💡 **Java에서 이미지(JPEG, PNG) 압축하는 방법**\
✅ JPEG 품질 조절 (`ImageIO`) → **손실 압축**으로 용량 줄이기\
✅ PNG → WebP 변환 (`libwebp`) → **비손실 압축**으로 품질 유지

💡 **웹 서비스에서 이미지 최적화**\
✅ HTTP 압축 (Gzip, Brotli) → **전송 크기 50% 감소**\
✅ 이미지 스트리밍 (Chunked Transfer Encoding) → **웹툰 같은 서비스에서 빠른 로딩 가능**

📌 **즉, 이미지 최적화를 위해 압축 & 스트리밍을 함께 적용하는 것이 핵심!** 🚀

