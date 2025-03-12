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

### 6) 그렇다면 결국 이미지도 비트아닌가.. 이미지는 어떤식으로 변환되는데?

#### 🖼 **이미지는 어떻게 변환될까?**

(비트와 픽셀, 그리고 GPU의 역할)

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

