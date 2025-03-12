---
icon: laptop-binary
---

# deepdive 지식 모음

## 1장

### 1) 아스키 코드의 실제 예제

{% hint style="danger" %}
실제  예제로 stesdg이라고 치면 아스키 코드가 어떻게 수를 사용해 문자나 키보드에 있는 다른 기호 등을 표현 하는 거잖아?
{% endhint %}

맞아! 네가 `"stesdg"`라고 입력하면, 컴퓨터는 이를 **아스키(ASCII) 코드**로 변환해서 저장하고 처리해.\
즉, **문자는 내부적으로 숫자로 저장**되는데, 이 숫자가 바로 **아스키 코드 값**이야.

***

#### **🔡 "stesdg" → ASCII 코드 변환 과정**

| 문자 | ASCII (10진수) | ASCII (16진수) | ASCII (2진수) |
| -- | ------------ | ------------ | ----------- |
| s  | 115          | 0x73         | 01110011    |
| t  | 116          | 0x74         | 01110100    |
| e  | 101          | 0x65         | 01100101    |
| s  | 115          | 0x73         | 01110011    |
| d  | 100          | 0x64         | 01100100    |
| g  | 103          | 0x67         | 01100111    |

***

#### **📌 ASCII가 문자를 숫자로 변환하는 원리**

1. **문자를 입력**하면, 컴퓨터는 **각 문자를 대응하는 숫자(ASCII 코드)로 변환**해.
2. 이 숫자는 **2진수로 저장**되며, 컴퓨터가 이를 조합해 화면에 표시하거나 처리할 수 있음.
3.  예를 들어 `"stesdg"`라는 단어를 입력하면, 컴퓨터는 내부적으로 이렇게 저장함:

    ```
    01110011 01110100 01100101 01110011 01100100 01100111
    ```
4. 나중에 이 2진수를 **ASCII 코드 테이블을 참고하여 다시 문자로 변환**하면 `"stesdg"`가 출력됨.

***

#### **💡 예제: ASCII 변환 코드 (Python)**

```python
text = "stesdg"
ascii_values = [ord(char) for char in text]  # 각 문자의 ASCII 값 변환
binary_values = [bin(ord(char))[2:].zfill(8) for char in text]  # 2진수 변환

print("ASCII 코드:", ascii_values)
print("2진수:", binary_values)
```

🔹 실행 결과:

```
ASCII 코드: [115, 116, 101, 115, 100, 103]
2진수: ['01110011', '01110100', '01100101', '01110011', '01100100', '01100111']
```

***

#### **✅ 결론**

✔ **ASCII는 문자를 숫자로 변환하는 체계**로, 키보드 입력을 숫자로 저장하고 변환하는 방식이야.\
✔ `"stesdg"` 같은 단어도 내부적으로는 **2진수로 저장**되며, 나중에 다시 문자로 변환할 수 있어! 🚀



### 2) 아스키 코드의 처리 과정

{% hint style="warning" %}
아스키 코드는 컴퓨터 어디서 처리할까?
{% endhint %}

#### **🔍 컴퓨터에서 ASCII 코드를 처리하는 과정**

컴퓨터는 키보드에서 입력된 문자를 받아 내부적으로 **CPU와 메모리**를 거쳐 처리한 후, **디스플레이, 저장장치, 네트워크 등**으로 보낸다. 이 과정에서 여러 핵심 부품이 ASCII 데이터를 다룬다.

**1️⃣ 키보드 입력 → CPU 처리**

키보드에서 `"stesdg"`를 입력하면, 다음과 같은 과정으로 처리된다.

#### **1. 키보드 입력 (Hardware)**

* 키보드를 누르면, **키보드 컨트롤러**가 해당 키에 대응하는 ASCII 코드를 생성한다.
* 예를 들어, `s`를 누르면 \*\*115(0x73)\*\*이 키보드 컨트롤러에서 만들어짐.

#### **2. 인터럽트 발생 (CPU)**

* 키보드 입력이 감지되면, **CPU의 인터럽트 컨트롤러(Interrupt Controller)** 가 동작하여 CPU에게 "키 입력이 발생했다!"고 알림.
* CPU는 운영체제(OS)에게 요청하여 키 입력을 처리하도록 명령함.

#### **3. 운영체제(OS) 처리**

* 운영체제는 키보드 입력을 받아 적절한 프로그램(예: 메모장, 터미널, 브라우저)으로 전달.
* 이 과정에서 **드라이버(Keyboard Driver)** 가 ASCII 값을 변환하여 프로그램이 사용할 수 있도록 함.

**2️⃣ CPU와 메모리에서 ASCII 처리**

이제 입력된 ASCII 코드를 **CPU와 메모리에서 처리하는 과정**을 살펴보자.

#### **1. 레지스터(Register) 저장**

* CPU의 **레지스터(Registers)** 는 키 입력 데이터를 잠깐 저장하고 처리함.
* 예: `"s"`(115)는 **AX, BX 같은 CPU 레지스터**에 임시 저장됨.

#### **2. 메모리(RAM) 저장**

* 입력된 데이터는 **RAM(주기억장치)** 에 저장됨.
*   `"stesdg"`는 메모리에 **연속된 바이트 배열**로 저장됨.

    ```
    RAM:
    주소   데이터(ASCII 코드)
    0x100  115 ('s')
    0x101  116 ('t')
    0x102  101 ('e')
    0x103  115 ('s')
    0x104  100 ('d')
    0x105  103 ('g')
    ```
* 프로그램이 필요할 때 이 데이터를 불러와 처리함.

#### **3. CPU 연산 (ALU & CU)**

* CPU의 **산술 논리 연산 장치(ALU, Arithmetic Logic Unit)** 와 **제어 장치(CU, Control Unit)** 가 데이터를 분석하고 실행함.
* 예를 들어, 문자열 비교 연산 (`"stesdg" == "test"`) 등을 할 때 ALU에서 **비트 연산**을 수행.

**3️⃣ ASCII 데이터를 화면에 출력하는 과정**

#### **1. 운영체제가 디스플레이로 전달**

* ASCII 데이터를 **GPU(그래픽 프로세서)** 또는 **디스플레이 드라이버**로 보냄.
* 디스플레이 드라이버는 ASCII 값을 폰트로 변환하여 화면에 표시.

#### **2. 모니터 출력**

* `"stesdg"`는 **모니터 픽셀 데이터**로 변환되어 눈으로 보이게 됨.
* 예를 들어, `115('s')`는 화면에서 `"s"`로 보이도록 폰트 엔진이 처리.

***

**📌 ASCII 데이터 활용 예제 (C 코드)**

```c
#include <stdio.h>

int main() {
    char text[] = "stesdg";
    
    // ASCII 코드 출력
    for (int i = 0; text[i] != '\0'; i++) {
        printf("Character: %c, ASCII: %d\n", text[i], text[i]);
    }
    
    return 0;
}
```

**🖥 실행 결과**

```
Character: s, ASCII: 115
Character: t, ASCII: 116
Character: e, ASCII: 101
Character: s, ASCII: 115
Character: d, ASCII: 100
Character: g, ASCII: 103
```

***

**🔎 정리**

1. **키보드 입력** → 키보드 컨트롤러가 ASCII 코드로 변환
2. **CPU 처리** → 인터럽트 발생 후, 운영체제가 데이터 처리
3. **RAM 저장** → ASCII 코드가 메모리에 저장됨
4. **CPU 연산** → ALU & CU가 데이터를 분석하고 연산
5. **디스플레이 출력** → ASCII 코드가 화면에 글자로 표시됨

💡 즉, `"stesdg"` 같은 문자열이 단순히 보이는 게 아니라 **CPU, 메모리, OS, 디스플레이 등 다양한 컴퓨터 부품이 협력하여 처리**하는 과정이 있는 거야! 🚀

### 3) 유니코드 이모지 변환

{% hint style="warning" %}
🐱 유니코드 변환
{% endhint %}

🐱 (고양이 이모지)의 **유니코드 코드 포인트**는 다음과 같다.

#### **🔢 유니코드 변환 과정**

1. **유니코드 코드 포인트** : `U+1F431`
2. **10진수로 변환** : `128049`
3. **UTF-8 인코딩**
   * **16진수 바이트 표현** : `F0 9F 90 B1`
   *   **바이너리 표현** :

       ```
       11110000 10011111 10010000 10110001
       ```

#### **📌 유니코드 변환 방법**

유니코드는 **UTF-8, UTF-16, UTF-32** 등의 방식으로 인코딩할 수 있음.

| **인코딩 방식** | **바이트 표현**    |
| ---------- | ------------- |
| UTF-8      | `F0 9F 90 B1` |
| UTF-16     | `D83D DC31`   |
| UTF-32     | `0001F431`    |

***

#### **💡 실제 변환 코드 (Python)**

```python
# 🐱의 유니코드 코드 포인트 출력
print(hex(ord("🐱")))  # 0x1f431

# UTF-8 인코딩
print("🐱".encode("utf-8"))  # b'\xf0\x9f\x90\xb1'

# UTF-16 인코딩
print("🐱".encode("utf-16"))  # b'\xff\xfe=\xd8\x31\xdc'

# UTF-32 인코딩
print("🐱".encode("utf-32"))  # b'\xff\xfe\x00\x00\xf4\x01\x00\x00'
```

***

#### **🔍 정리**

* **🐱는 `U+1F431`(16진수) → `128049`(10진수)**
* **UTF-8에서는 `F0 9F 90 B1`로 변환됨.**
* **컴퓨터에서는 바이트 단위로 저장하고 처리함.**
* **다른 문자들도 같은 방식으로 변환 가능!** 🚀

### 4) 유니코드 변환은 컴퓨터 내부에서 어디서 처리될까?

{% hint style="warning" %}
컴퓨터 내부 어디서 되고 있는 건데? 이미 이것에 대해서는 컴퓨터 내부에 프로그래밍 되어 있다는 거잖아
{% endhint %}

#### **🐱 유니코드 변환은 컴퓨터 내부에서 어디서 처리될까?**

> 유니코드 변환 및 처리는 컴퓨터 내부에서 여러 계층을 거쳐 이루어지며, 주요한 단계는 다음과 같다.

**📌 1. 키보드 입력 (입력 장치)**

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

**📌 2. 운영체제(OS)에서 처리**

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



**📌 3. 애플리케이션(웹 브라우저, 터미널, 문서 편집기 등)**

* 웹 브라우저, 터미널, IDE 등 응용 프로그램이 운영체제로부터 유니코드 문자를 받음.
* 애플리케이션 내부에서 문자 데이터를 저장할 때, **UTF-8 / UTF-16 / UTF-32 등으로 인코딩하여 처리**.

🔹 **관련 요소**:

* **웹 브라우저**: HTML, JavaScript에서 문자열을 UTF-8로 처리 (`document.write("🐱")`)
* **터미널**: `echo "🐱"` 실행 시 UTF-8로 출력
* **텍스트 에디터**: Sublime Text, VSCode 등에서 유니코드 기반으로 저장

**📌 4. 폰트 렌더링 (GPU & 폰트 엔진)**

* 프로그램이 유니코드 문자를 화면에 표시하려면, **폰트 렌더링 엔진**을 통해 문자 → 이미지로 변환해야 함.
* 🐱 이모지를 표현하기 위해 운영체제 또는 웹 브라우저는 `Noto Color Emoji`, `Apple Color Emoji`, `Segoe UI Emoji` 같은 **이모지 폰트**를 사용.

🔹 **관련 요소**:

* **Windows**: `DirectWrite`
* **Mac**: `CoreText`
* **Linux**: `Pango`, `HarfBuzz`
* **웹 브라우저**: CSS `font-family: "Noto Color Emoji";`
* **GPU 렌더링**: 이모지 이미지를 GPU의 VRAM에 로드 후 렌더링

**📌 5. 출력 장치 (디스플레이, 모니터)**

* 최종적으로 🐱 이모지는 **픽셀 데이터로 변환되어 GPU를 통해 모니터에 표시됨**.
* 모니터에서 **RGB 픽셀 데이터**를 조합하여 🐱 모양의 이미지를 보여줌.

🔹 **관련 요소**:

* **Windows**: `GDI+`, `Direct2D`
* **Mac/Linux**: `Cairo Graphics`, `OpenGL`, `Vulkan`
* **모니터**: 픽셀 데이터 기반으로 🐱를 최종적으로 출력

**📌 6. OS 내부 시스템이 정의된 곳**

운영체제의 유니코드 및 텍스트 렌더링 시스템은 **시스템 라이브러리 및 설정 파일**로 관리됨.

📂 **어디에서 관리될까?**

* Windows → `C:\Windows\Fonts\`
* MacOS → `/System/Library/Fonts/`
* Linux → `/usr/share/fonts/`, `/usr/share/unicode/`

📌 **유니코드 데이터베이스 위치**

* **Windows** → `C:\Windows\System32\unifont.dll`
* **Linux/macOS** → `/usr/share/unicode/`

이처럼 운영체제 내부에는 **유니코드 입력, 변환, 폰트 처리 시스템이 미리 정의되어 있다.**

#### **🎯 최종 정리**

🐱 이모지를 입력하고 화면에 출력하는 과정은 다음과 같다:

1️⃣ **입력 단계**: 키보드/IME에서 유니코드 변환 (`U+1F431`)\
2️⃣ **운영체제 처리**: 입력된 유니코드를 메모리에 저장 후 프로그램에 전달\
3️⃣ **애플리케이션 내부 처리**: 유니코드 데이터를 UTF-8 등으로 인코딩하여 저장\
4️⃣ **폰트 렌더링**: 🐱를 표시할 수 있는 폰트(`Apple Color Emoji`, `Noto Emoji`)에서 이모지를 이미지로 변환\
5️⃣ **디스플레이 출력**: GPU가 최종적으로 픽셀을 조합하여 화면에 🐱를 표시

>

### 5) 정의된 시스템

> 정의된 시스템이 어디 있다는 걸까?

#### **🖥️ 유니코드 및 문자 처리 시스템이 정의된 곳은?**

🐱(U+1F431) 같은 유니코드 문자는 컴퓨터 내부에서 여러 **소프트웨어 및 하드웨어 계층**을 거쳐 처리된다.\
이 과정에서 사용되는 **정의된 시스템**은 다음과 같이 분류할 수 있다.

***

**📌 1. 운영체제 (OS)**

운영체제는 키보드 입력을 받아 이를 **유니코드 코드 포인트**로 변환하고, 응용 프로그램이 사용할 수 있도록 제공한다.

#### **💾 OS 내부 유니코드 처리 관련 시스템**

* **Windows**
  * `Win32 API` → `Text Services Framework (TSF)`를 통해 입력된 문자 처리
  * `DirectWrite` 또는 `GDI+`를 이용해 폰트 렌더링
* **MacOS**
  * `CoreText` 및 `NSInputManager`로 유니코드 문자 처리
* **Linux (Ubuntu, Debian, etc.)**
  * `X11` 또는 `Wayland`를 통해 입력 이벤트 처리
  * `Pango`와 `HarfBuzz`를 사용하여 폰트 렌더링

🔹 **어디에 정의되어 있나?**

* OS 내부의 **입력 시스템(IME), 키보드 드라이버, 텍스트 렌더링 라이브러리**에 정의되어 있음.
* `/usr/share/unicode` 또는 `/System/Library/Fonts` 등의 시스템 디렉터리에 폰트 데이터가 저장됨.

**📌 2. 유니코드 표준 (Unicode Standard)**

유니코드는 국제 표준(ISO 10646)으로 정의되어 있으며, **전 세계 모든 문자에 대해 고유한 코드 포인트를 할당**한다.

🔹 **어디에 정의되어 있나?**

* 공식 유니코드 표준 문서 📜
  * [https://www.unicode.org/](https://www.unicode.org/)
  * [Unicode Character Database (UCD)](https://www.unicode.org/ucd/)
  * [Unicode Emoji 표준](https://unicode.org/emoji/)
* 각 OS의 **유니코드 라이브러리**에 내장됨 (Windows, MacOS, Linux 등)

📄 **예시: 유니코드 문서에서 🐱 (U+1F431) 정의**

* **Code Point**: `U+1F431`
* **Name**: CAT FACE
* **Category**: Emoji, Symbol
* **UTF-8 Encoding**: `F0 9F 90 B1`
* **UTF-16 Encoding**: `D83D DC31`

**📌 3. 폰트 시스템 (Font Rendering System)**

🐱 이모지가 화면에 출력되려면, **폰트 파일**이 필요하다.\
OS는 해당 문자(🐱)가 포함된 **폰트 데이터를 찾아서 렌더링**한다.

#### **💾 폰트 렌더링 시스템**

* **Windows**: `Segoe UI Emoji`, `DirectWrite`
* **MacOS**: `Apple Color Emoji`, `CoreText`
* **Linux**: `Noto Color Emoji`, `Pango`, `HarfBuzz`
* **웹 브라우저**: `CSS font-family: "Noto Color Emoji";` 등을 통해 렌더링

🔹 **어디에 정의되어 있나?**

* `/System/Library/Fonts` (MacOS)
* `/usr/share/fonts/` (Linux)
* `C:\Windows\Fonts\` (Windows)

📂 **폰트 파일 예시**

* `NotoColorEmoji.ttf` → Google이 만든 오픈소스 이모지 폰트
* `AppleColorEmoji.ttf` → MacOS, iOS에서 사용하는 기본 이모지 폰트

**📌 4. 응용 프로그램 내부 문자열 처리**

웹 브라우저, 터미널, 텍스트 에디터는 OS에서 받은 유니코드 데이터를 **UTF-8, UTF-16 등으로 변환하여** 사용한다.

#### **💾 문자열 처리 라이브러리**

* **Python**: `unicodedata` 모듈
* **JavaScript**: `String.prototype.charCodeAt()`, `Intl` API
* **Java**: `Character.toChars()`, `String.getBytes()`
* **C/C++**: `wchar_t`, `char16_t`

🔹 **어디에 정의되어 있나?**

* 프로그래밍 언어의 **표준 라이브러리(Standard Library)** 내부에 정의되어 있음.
*   **Python 예시**

    ```python
    import unicodedata

    char = "🐱"
    print(unicodedata.name(char))  # 'CAT FACE'
    print(ord(char))  # 128049 (10진수), U+1F431 (16진수)
    ```
*   **JavaScript 예시**

    ```javascript
    console.log("🐱".charCodeAt(0).toString(16)); // "d83d"
    console.log("🐱".charCodeAt(1).toString(16)); // "dc31"
    ```

**📌 5. 하드웨어 (GPU, 디스플레이)**

🐱는 최종적으로 GPU(Graphics Processing Unit)에서 픽셀 데이터로 변환되고, **디스플레이**에 출력된다.

🔹 **어디에 정의되어 있나?**

* **GPU 드라이버** 내부에서 폰트 데이터를 픽셀로 변환
* Windows: `Direct2D`, MacOS: `Metal API`, Linux: `Mesa`, `OpenGL`

📌 **최종적으로 🐱는 GPU가 픽셀로 변환하여 모니터에서 표시됨!**

```
1️⃣ 키보드 입력 → OS가 유니코드 변환 (U+1F431)
2️⃣ OS가 프로그램에 전달 (Windows API, Mac CoreText, Linux X11 등)
3️⃣ 프로그램이 UTF-8/UTF-16으로 저장 및 처리
4️⃣ 폰트 시스템이 🐱를 찾아서 이미지로 변환
5️⃣ GPU가 🐱를 픽셀 데이터로 변환 후 화면에 출력
```

**🧐 "정의된 시스템"은 OS 내부, 프로그래밍 언어 라이브러리, 폰트 파일, GPU 드라이버 등 다양한 곳에 내장되어 있음!** 🚀
