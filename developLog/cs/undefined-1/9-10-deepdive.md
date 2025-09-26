---
description: 더 깊게 정리하고 싶은 내용 정리하기
icon: fill-drip
---

# 컴퓨터 구조 9-10장 DeepDive 지식

## 🧠 1. 브라우저는 어떻게 ‘작은 운영체제’가 되었는가 – 이벤트 루프와 비동기 처리의 탄생

### 1) 📜 이야기는 이렇게 시작됩니다 – 클릭 한 번으로 바뀌는 세계

여러분이 웹페이지에서 버튼을 클릭하면 어떤 일이 벌어질까요?

단순히 글씨가 바뀌거나, 이미지가 나타나는 걸로 보일 수 있지만, 이 작디작은 변화 하나를 위해 브라우저 내부에서는 어마어마한 일이 벌어집니다. HTML이 펼쳐지고, JavaScript가 실행되고, DOM이 조작되고, 스타일이 다시 계산되고, 레이아웃이 조정되며, 마지막에 다시 그려지는 과정을 거칩니다. 그런데 여기서 가장 핵심적인 건 뭘까요?

> 바로 **브라우저는 사용자의 요청을 "기다리고", 응답하는 "이벤트 기반 구조"를 갖춘 일종의 작은 운영체제**처럼 행동한다는 사실입니다.

### 2) 🧩 브라우저 내부는 어떻게 생겼을까?

일단 브라우저는 단순히 웹페이지를 보여주는 도구가 아닙니다. 다음과 같은 요소들이 합쳐진 거대한 프로그램입니다:

| 구성 요소                 | 설명                              |
| --------------------- | ------------------------------- |
| HTML 파서               | 마크업 언어를 파싱해서 DOM 트리를 구성         |
| CSS 파서                | 스타일을 파싱해서 렌더 트리 생성              |
| JavaScript 엔진 (예: V8) | JS 코드를 해석하고 실행                  |
| 렌더링 엔진                | DOM, CSSOM을 기반으로 실제 레이아웃과 픽셀 출력 |
| 네트워킹 스택               | 요청과 응답을 처리하는 통신 모듈              |
| 이벤트 루프                | 사용자 인터랙션과 스케줄된 작업을 처리하는 구조      |

{% hint style="danger" %}
이 모든 것을 조화롭게 연결하는 핵심은 무엇일까요?
{% endhint %}

바로 **이벤트 루프(Event Loop)**&#xC785;니다.

### 3) 🔁 이벤트 루프란 무엇인가?

> 잘 svg 형태로 정리 되어 있는 곳 : [https://inpa.tistory.com/entry/%F0%9F%94%84-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EA%B5%AC%EC%A1%B0-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC](https://inpa.tistory.com/entry/%F0%9F%94%84-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EA%B5%AC%EC%A1%B0-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC)

**브라우저가 한 번에 하나의 작업만** 할 수 있는 `단일 스레드(single-threaded) 환경`이라면, 우리는 어떻게 동시에 여러 가지 일을 할 수 있을까요?

바로 "이벤트 루프" 덕분입니다.&#x20;

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (2) (1) (1).png" alt=""><figcaption><p><a href="https://inpa.tistory.com/entry/%F0%9F%94%84-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EA%B5%AC%EC%A1%B0-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC">https://inpa.tistory.com/entry/%F0%9F%94%84-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EA%B5%AC%EC%A1%B0-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC</a></p></figcaption></figure>

> 내용 출처 : [https://inpa.tistory.com/entry/%F0%9F%94%84-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EA%B5%AC%EC%A1%B0-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC](https://inpa.tistory.com/entry/%F0%9F%94%84-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EA%B5%AC%EC%A1%B0-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC)

브라우저의 동작 타이밍을 제어하는 관리자라고 보면 됩니다.

이벤트 루프의 동작 과정을 간단히 살펴보자면, 자바스크립트의 setTimeout이나 fetch 와 같은 **비동기 자바스크립트 코드**를 `브라우저 Web APIs`에게 맡기고, **백그라운드 작업이 끝난 결과를 콜백 함수 형태**로 `큐(Callback Queue)`에 넣고 **처리 준비가 되면** `호출 스택(Call Stack)`에 넣어 **마무리 작업을 진행**합니다.

이벤트 루프를 이용한 프로그램 방식을 이벤트 기반(Event Driven) 프로그래밍이라고 합니다.&#x20;

**이벤트 기반 프로그래밍**은 <mark style="color:red;">프로그램의 흐름이 이벤트에 의해 결정되는 방식</mark>이에요. 예를 들어 사용자의 클릭이나 키보드 입력과 같은 이벤트가 발생하면, 그에 맞는 콜백 함수가 실행하는데. 대표적으로 자바스크립트의 addEventListener(이벤트명, 콜백함수) 입니다.

{% hint style="success" %}
**이벤트 기반 프로그래밍**은 <mark style="color:blue;">비동기 작업을 쉽게 처리할 수 있고, 멀티 스레드 언어에 비해 단순하고 직관적인 코드 작성을 가능하게 하며, 브라우저와 같은 환경에서도 안정적인 실행을 가능하게 하여 사용자와의 상호작용을 높일 수 있습니다.</mark>&#x20;
{% endhint %}

따라서 이를 이해하고 <mark style="color:red;">적절한 방식으로 비동기 작업을 처리하는 것은, 자바스크립트를 이용한 웹 애플리케이션 개발에 있어서 매우 중요합니다.</mark>

#### 이벤트 루프를 알기 전 자바 스크립트의 특징을 알아보자

> 내용 출처 : [https://blog.toktokhan.dev/t-767eb0fa38f3](https://blog.toktokhan.dev/t-767eb0fa38f3)

![](https://velog.velcdn.com/images/remon/post/a3736e7a-9721-486e-9361-e625dd85b9ae/image.png)

자바스크립트는 HTML에 종속되어있는 언어입니다. HTML 조작과 변경을 위해 사용합니다.

> 💡 HTML은 웹페이지에 글쓰고, 그림넣는 언어이다.\
> 특징 ) 안 움직임, 글 넣고 그림 넣고 끝

정적 언어인 HTML을 조작해서 웹페이지를 다이나믹하게 바꿔주는 기능을 하는게 자바스크립트입니다.

`JavaScript`는 싱글쓰레드 언어라고 많이 알려져 있습니다. 싱글쓰레드라고 한다면 여러 개의 작업이 있더라도 한 번에 하나의 작업만 수행할 수 있습니다. 하지만 `JavaScript`를 사용해 보면 멀티쓰레드처럼 동시에 여러 작업을 수행할 수 있다는 것을 알 수 있습니다.

{% hint style="danger" %}
&#x20;그렇다면 `JavaScript`는 정말 싱글쓰레드 언어가 맞을까요?
{% endhint %}

맞습니다. 그 이유는 JavaScript의 메인쓰레드인 **이벤트 루프**가 싱글 쓰레드이기 때문입니다. 반면 Java 나 Python은 멀티 스레드를 지원하여 원하는 코드 로직을 동시에 수행 시키는 멀티 작업이 가능합니다.&#x20;

하지만 JavaScript **이벤트 루프**만 독립적으로 실행되는것이 아닌 <mark style="color:red;">웹 브라우저나 NodeJS 같은 멀티쓰레드 환경에서 실행되고 이를 적절하게 사용함으로써 멀티쓰레드처럼 사용이 가능한 것입니다.</mark><mark style="background-color:yellow;">(다만 Web worker 최신 기술을 통해 자바스크립트도 멀티 스레드 구현이 가능해졌습니다. )</mark>

#### 🤔HTML은 자바스크립트가 조작한다면 자바스크립트 해석은 누가할까요? <a href="#html" id="html"></a>

바로 **브라우저**입니다. <mark style="background-color:blue;">브라우저에는 자바스크립트 해석 엔진</mark>이 있습니다. 기존에는 자바스크립트를 인터넷 브라우저 위에서만 실행할 수 있었습니다.\
그러나 2008년에 구글이 V8 엔진을 사용하여 크롬을 출시했습니다. V8 엔진은 엄청 빨랐고, 오픈 소스로 코드도 공개되었습니다. V8 엔진이 너무 뛰어나서 기능을 좀 더 더해서 V8 엔진 기반에 노드 프로젝트를 시작했고, **Node.js(V8)**&#xC774; 등장했습니다. Node.js는 브라우저 내에서 말고도 다른 환경에서 자바스크립트를 사용할 수 있게 해줍니다.

따라서 **Node.js는 JavaScript 실행 환경(=런타임)입니다. (V8과 Node.js에 대한 설명은 뒤에서)**

웹 애플리케이션에서는 **네트워크 요청이나 이벤트 처리, 타이머와 같은 작업을 멀티로 처리해야 하는 경우**가 많은데.. 만일 싱글 스레드로 브라우저 동작이 한번에 하나씩 수행하게 되면, 우리가 파일을 다운로드 받을 동안 브라우저는 파일을 다 받을 때까지 웹서핑도 못하고 멈춰 대기해야 할 것입니다.&#x20;

> 따라서 파일 다운, 네트워크 요청, 타이머, 애니메이션 이러한 <mark style="color:red;">오래 걸리고 반복적인 작업들은 자바스크립트 엔진이 아닌 브라우저 내부의 멀티 스레드인 Web APIs에서 비동기 + 논블로킹으로 처리</mark>됩니다.&#x20;

비동기 + 논블로킹(Async + Non blocking)Visit Website는 메인 스레드가 작업을 다른 곳에 요청하여 대신 실행하고, 그 작업이 완료되면 이벤트나 콜백 함수를 받아 결과를 실행하는 방식을 말합니다.&#x20;

{% hint style="danger" %}
**비동기로 동작하는 핵심요소는 자바스크립트 언어**가 아니라 **브라우저라는 소프트웨어가 가지고 있다고 보면 됩니다.** Node.js 에서는 libuv 내장 라이브러리가 처리합니다.
{% endhint %}

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (2).png" alt=""><figcaption><p><a href="https://blog.toktokhan.dev/t-767eb0fa38f3">https://blog.toktokhan.dev/t-767eb0fa38f3</a></p></figcaption></figure>

이벤트 루프는 브라우저 내부의 태스크 스케줄러 역할을 합니다. 비동기적인 요청과 사용자 인터랙션을 큐에 넣어 하나씩 처리하며, 이로써 동시성을 흉내낼 수 있게 해줍니다.

> 이벤트 루프는 JavaScript의 실행 컨텍스트와 콜백 큐, 마이크로태스크 큐, 태스크 큐를 조율합니다.

#### **먼저, JavaScript 엔진이란?**

자바스크립트(JavaScript)는 그 자체로는 "명령어의 나열일 뿐"입니다. 이걸 **실제로 실행**시켜주는 게 바로 **JavaScript 엔진**입니다.

JavaScript 엔진은 코드를 이해하고 실행을 도와주는 역할을 합니다.

> 💡 마치 "배우가 대본을 해석하고 연기하는 것"처럼, 엔진은 JS 코드를 해석하고 실행해줍니다.

브라우저는 각자 자신만의 JS 엔진을 가지고 있는데요:

* **Chrome / Edge** → V8 (구글 제작)
* **Firefox** → SpiderMonkey
* **Safari** → JavaScriptCore (또는 Nitro)

그중에서도 가장 유명한 엔진이 바로 **V8 엔진**입니다. 구글이 만든 이 엔진은 **빠른 실행 속도와 효율적인 메모리 처리**로 유명하죠.

#### **📦 자바스크립트 엔진 내부 구조**

<figure><img src="https://miro.medium.com/v2/resize:fit:700/0*z9vfRx7x9ioruZv0.png" alt="" height="525" width="700"><figcaption><p>출처 : <a href="https://blog.sessionstack.com/how-does-JavaScript-actually-work-part-1-b0bacc073cf">How JavaScript works: an overview of the engine, the runtime, and the call stack</a></p></figcaption></figure>

엔진은 코드를 실행할 때 내부적으로 **두 개의 핵심 구조**를 사용합니다:

#### 1. 🧠 **Memory Heap (메모리 힙)**

* 말 그대로 "어디에 무엇을 저장해둘지 결정하는 공간"입니다.
* 자바스크립트에서 변수, 객체, 배열 등을 선언하면 이곳에 저장됩니다.
* 동적으로 메모리를 할당하는 공간이며, **크기가 유동적**입니다.

> 📦 예시:\
> `const cat = { name: "복이", age: 3 }`\
> → 이 객체는 메모리 힙에 저장됩니다.

#### 2. 🧾 **Call Stack (호출 스택)**

* 함수 호출이 일어날 때마다, 실행 순서를 기억하는 **작업 목록**이라고 볼 수 있어요.
* **후입선출 (LIFO)** 구조로 작동하며, 가장 나중에 호출된 함수가 먼저 실행되고 끝나면 그다음 함수로 넘어갑니다.

> 📞 예시:
>
> <pre class="language-js"><code class="lang-js"><strong>function hello() {
> </strong>  console.log("안녕!");
> }
> hello();
> </code></pre>
>
> → `hello()`가 호출되면 Call Stack에 쌓이고, 실행 후 제거됩니다.

{% hint style="success" %}
**둘의 관계는?**

* **Memory Heap**은 "무엇을 저장할까?"
* **Call Stack**은 "무엇을 언제 실행할까?"
{% endhint %}

예를 들어, 여러분이 `add(2, 3)` 같은 함수를 호출하면:

1. Call Stack에 `add()`가 올라가고
2. 내부 연산을 위해 필요한 변수는 Memory Heap에 저장됩니다
3. 함수 실행이 끝나면 Call Stack에서 제거되고, 메모리도 정리됩니다 (Garbage Collection)

#### 🔥 부가 설명: 왜 이걸 알아야 할까요?

이 구조를 알면 **다음과 같은 자바스크립트 동작을 이해하기 쉬워집니다:**

* 왜 함수 안에서 선언한 변수는 밖에서 접근할 수 없을까? (→ 스코프 + 스택 구조 이해)
* 왜 무한 재귀 호출을 하면 `Maximum call stack size exceeded` 에러가 뜰까?
* 메모리 누수는 왜 발생할까?

#### 시각적 정리

```
┌───────────────┐
│  Memory Heap │ ← { name: '복이' }
└───────────────┘

┌───────────────┐
│   Call Stack  │ ← hello()
│               │ ← main()
└───────────────┘
```



#### Memory Heap & Call Stack <a href="#id-2fbb" id="id-2fbb"></a>

먼저 `Memory Heap`에 있는 사용자가 작성한 코드들은 `Call Stack`에서 `Stack` 방식으로 쌓이며 코드를 실행하게 되는데 이때 동기 함수들은 그대로 실행하게 되고 비동기 함수들은 `Web API`로 처리하게 되며 일을 분배합니다.

* Stack : 후입선출(LIFO)로 마지막에 들어간 것이 먼저 나가는 방식

#### 브라우저 내부 구성도

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p><a href="https://inpa.tistory.com/entry/%F0%9F%94%84-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EA%B5%AC%EC%A1%B0-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC#%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8_%EB%B9%84%EB%8F%99%EA%B8%B0%EC%99%80_%EC%9D%B4%EB%B2%A4%ED%8A%B8_%EB%A3%A8%ED%94%84">https://inpa.tistory.com/entry/%F0%9F%94%84-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EA%B5%AC%EC%A1%B0-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC#%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8_%EB%B9%84%EB%8F%99%EA%B8%B0%EC%99%80_%EC%9D%B4%EB%B2%A4%ED%8A%B8_%EB%A3%A8%ED%94%84</a></p></figcaption></figure>

브라우저 전체 구성도를 보면 위와 같다.

| 구성 요소               | 역할                                     |
| ------------------- | -------------------------------------- |
| **Call Stack**      | 현재 실행 중인 코드의 함수 호출이 쌓이는 공간 (LIFO 구조)   |
| **Memory Heap**     | 동적으로 생성된 객체, 함수 등 데이터가 저장되는 공간         |
| **Web APIs**        | 브라우저가 제공하는 비동기 API 집합 (AJAX, Timer 등)  |
| **Callback Queue**  | 완료된 비동기 작업의 콜백들이 대기하는 큐 (setTimeout 등) |
| **Microtask Queue** | Promise와 같은 고우선 비동기 작업의 콜백 대기 공간       |
| **Event Table**     | 이벤트와 콜백의 관계를 관리하는 ‘주소록’ 역할             |
| **Event Loop**      | Call Stack이 비어 있는지 감시 → Queue에서 콜백 실행  |

#### Web API – 브라우저의 비동기 도우미들 <a href="#bb25" id="bb25"></a>

Javscript를 사용하면서 우리가 많이 사용하는 API 들은 사실 JavaScript에서 지원하는 것이 아닌 **웹 브라우저에서 제공하는 API**로 `DOM` ,`AJAX`, `Timeout` 등이 있습니다.

{% hint style="danger" %}
자바스크립트 자체에는 비동기 기능이 없음\
→ 브라우저가 비동기 동작을 Web API로 제공함
{% endhint %}

`Call Stack`에서 실행된 비동기 함수는 `Web API`에서 처리를 하게 되고 그동안에 `Call Stack`은 나머지 동기 함수들을 처리하게 됩니다.

`Web API`는 비동기 함수들을 처리하며 작업이 완료된 비동기 함수들을 `Callback Queue`로 넘겨주게 됩니다.

#### 주요 예시

| API 종류          | 설명                          | 동기/비동기 |
| --------------- | --------------------------- | ------ |
| **DOM API**     | 요소 선택, 조작 등                 | 동기     |
| **Timer API**   | `setTimeout`, `setInterval` | 비동기    |
| **XHR / Fetch** | 서버와 비동기 통신                  | 비동기    |
| **Canvas API**  | 그래픽 렌더링                     | 동기     |
| **Geolocation** | 위치 정보 제공                    | 비동기    |
| **Console API** | 로그 출력                       | 동기     |

구조 상 비동기 Web API는 별도의 **스레드**로 작동  → 메인 스레드를 블로킹하지 않음

#### Callback Queue <a href="#id-4bf2" id="id-4bf2"></a>

{% hint style="danger" %}
* Web API가 작업을 마치면 콜백을 Queue에 넣음
* 이 Queue는 `Event Loop`가 Call Stack이 비면 하나씩 꺼내 실행함
{% endhint %}

`Callback Queue`는 비동기 함수들을 보관하는 장소로 `Event Loop`에서 비동기 함수를 꺼내기 전까지는 계속 `Queue`방식으로 보관하게 됩니다.

* Queue : 선입선출(FIFO)로 먼저 들어간 것이 먼저 나가는 방식

#### Event Loop <a href="#c357" id="c357"></a>

`Event Loop`는 `Call Stack`과 `Callback Queue`를 상태를 계속 감시하며 `Call Stack`에 함수들이 존재하지 않는다면 `Callback Queue`에 있는 비동기 함수들을 `Call Stack`에 밀어 넣게 됩니다. 그 후 `Call Stack`에서 비동기 함수를 실행시키게 됩니다.

#### Microtask Queue

* 비동기 중에서도 **우선순위가 높은 큐**
* `Promise.then`, `process.nextTick`, `MutationObserver` 등이 여기에 들어감
* Call Stack이 비자마자 가장 먼저 실행됨

> Promise가 `setTimeout`보다 먼저 실행되는 이유는 이 구조 때문입니다

#### 예시로 살펴보는 이벤트 루프

```javascript
console.log('A');

setTimeout(() => {
  console.log('B');
}, 0);

Promise.resolve().then(() => {
  console.log('C');
});

console.log('D');
```

출력 순서는 어떻게 될까요?

```
A
D
C
B
```

왜 이런 순서로 출력될까요?

| 단계   | 설명                                           |
| ---- | -------------------------------------------- |
| A, D | 동기 실행 – 콜 스택에서 바로 실행                         |
| C    | 마이크로태스크 큐 → 이벤트 루프는 스택이 비면 우선 처리             |
| B    | setTimeout의 콜백 → 태스크 큐에 들어가고, 마이크로태스크 다음에 실행 |

> 결국,  Event Loop은 전체를 연결하는 조율자입니다.
>
> * Call Stack이 비었는지 **계속 감시**
> * Stack이 비면 **Microtask Queue → Callback Queue 순서로** 콜백 실행
> * 매 프레임마다 한 번씩 돌면서 애플리케이션을 부드럽게 실행

### 4) 🛠️ 이벤트 루프는 브라우저 안에서 어떻게 동작할까?

이벤트 루프의 동작 순서를 간단히 그림으로 표현해보면 다음과 같습니다:

```
1. 콜 스택 실행
2. 마이크로태스크 큐 처리
3. 렌더링 단계
4. 태스크 큐에서 콜백 처리 (setTimeout, 이벤트 등)
5. 다시 반복
```

> 이 구조는 CPU 스케줄러와 유사하게 작동하며, 태스크 간 우선순위와 대기열을 관리합니다.

이를 통해 브라우저는 다음과 같은 일을 효율적으로 처리할 수 있습니다:

* 마우스 클릭 이벤트
* setTimeout / setInterval
* XMLHttpRequest / fetch
* DOM 조작 / 렌더링
* Promise.then

#### 비유: "자바스크립트의 오케스트라"

| 역할      | 구성 요소                       | 설명                            |
| ------- | --------------------------- | ----------------------------- |
| 지휘자     | Event Loop                  | 큐와 스택을 관리하며 타이밍을 조율함          |
| 무대 위 배우 | Call Stack                  | 현재 실행되는 함수들                   |
| 대기실     | Task Queue, Microtask Queue | 순서에 따라 무대에 오를 콜백들             |
| 조명/효과팀  | Web APIs                    | 타이머, 네트워크, 이벤트를 처리하는 브라우저 기능들 |

#### 브라우저 성능 최적화를 위한 팁

| 최적화 항목                            | 설명                     |
| --------------------------------- | ---------------------- |
| `Promise`를 활용해 순차 실행              | Microtask로 빠르게 처리 가능   |
| `requestAnimationFrame`           | 애니메이션 최적 타이밍에 실행됨      |
| `setTimeout(..., 0)` 남용 자제        | Task Queue는 한 프레임 지연됨  |
| 대량의 DOM 조작은 `DocumentFragment` 사용 | Stack이 과도하게 쌓이지 않도록 조절 |

* **자바스크립트는 싱글 스레드**지만, **브라우저는 멀티 스레드 구조**
* **Web API, Event Loop, Queue**가 협업하여 비동기 코드를 처리
* **Microtask가 Task보다 먼저 실행**된다는 점은 반드시 기억!

### 5) 🌍 브라우저는 왜 ‘작은 운영체제’인가?

운영체제는 다음과 같은 역할을 합니다:

| 운영체제 기능       | 브라우저의 대응                |
| ------------- | ----------------------- |
| I/O 처리        | 네트워크 요청 처리 (XHR, fetch) |
| 이벤트 관리        | 클릭/입력 이벤트 처리            |
| 메모리 관리        | 가비지 컬렉션 (GC), 힙/스택 구조   |
| 프로세스/스레드 스케줄링 | 이벤트 루프와 태스크 큐           |
| 사용자 인터페이스 처리  | DOM 렌더링과 뷰 업데이트         |

즉, 브라우저는 **시스템 콜 없이도**, 자바스크립트 단 하나만으로 사용자의 요구에 반응하고, 계산하고, 렌더링하고, 응답합니다. 그 자체로 고도로 복잡한 운영체제의 축소판이라 볼 수 있습니다.

### 6) ⚙️ 개발자의 관점 – 비동기 프로그래밍이란?

과거에는 이런 구조를 직접 다뤄야 했습니다. 콜백 헬(callback hell)이 그 대표적인 예입니다:

```javascript
doSomething(function(result) {
  doSomethingElse(result, function(next) {
    doMore(next, function(finalResult) {
      console.log(finalResult);
    });
  });
});
```

그러다 등장한 것이 Promise와 async/await입니다.

#### async/await를 통한 간단한 구조

```javascript
async function run() {
  const result = await doSomething();
  const next = await doSomethingElse(result);
  console.log(next);
}
```

> 이 문법 덕분에 이벤트 루프를 직접 관리하지 않아도 비동기 흐름을 읽기 쉽게 구현할 수 있게 되었습니다.

### 7) 📦 Node.js의 등장은 어떻게 이벤트 루프를 확장했는가?

브라우저에서 탄생한 이벤트 루프는 Node.js에서 서버 측 비동기 처리에도 활용됩니다. Node.js도 이벤트 루프를 기반으로 하며, libuv라는 라이브러리를 통해 파일 시스템 I/O, 네트워크, 타이머, OS 이벤트를 처리합니다.

Node.js의 이벤트 루프 구조는 다음과 같은 단계로 이루어져 있습니다:

1. timers
2. pending callbacks
3. idle, prepare
4. poll
5. check
6. close callbacks

### 8) 🧩 실시간성과 이벤트 루프 – 메타버스, 게임, 채팅 앱

이벤트 루프는 특히 다음과 같은 실시간성이 중요한 영역에서 활약합니다:

* 채팅 애플리케이션 (ex. Slack, Discord)
* 실시간 공동작업 (ex. Figma, Google Docs)
* 게임 (ex. Phaser.js, Babylon.js)
* 메타버스 / 3D 인터랙션 (ex. Three.js + WebSocket)

> 매 프레임마다 사용자 입력, 네트워크 수신, 물리 계산, 화면 그리기 등을 반복 수행하기 위해선 이벤트 루프 기반의 구조가 필수적입니다.

### 9) 🧮 브라우저 성능을 높이기 위한 루프 최적화 기법

이벤트 루프는 무한 반복 구조이기에 성능 저하 요소가 많습니다.

개발자들은 다음과 같은 기법으로 성능을 개선합니다:

* Debounce & Throttle
* requestAnimationFrame
* Web Workers (멀티스레딩 유사 구현)
* Microtask와 Macrotask의 적절한 분리
* Virtual DOM (React의 예)

### 🔚 마무리 요약

> **"브라우저는 더 이상 단순한 문서 뷰어가 아니다. 브라우저는 작은 운영체제다."**

이벤트 루프는 그 운영체제의 CPU 스케줄러 같은 역할을 합니다. 단일 스레드임에도 마치 여러 작업이 동시에 일어나는 것처럼 동작할 수 있는 비결이기도 합니다.

우리가 사용하는 웹 앱은 단순히 HTML과 CSS의 조합이 아니라, 운영체제처럼 동작하는 브라우저와 그 안의 이벤트 루프 덕분에 동작하는 복합 시스템입니다.

## 2. 웹 프론트엔드의 역사와 진화 좀 더 자세히 알아보기

### 1) 검색 엔진과 포털, e-커머스의 발전

문서를 공유하고자 만들어진 웹에서 양<mark style="color:red;">질의 문서들을 찾아서 공유하는 것은 당연한 일</mark>이었습니다.&#x20;

> 초창기 이러한 유용한 링크들을 아카이브 하는 **웹 디렉토리**라는 서비스들이 생겨나고 이는, 웹 디렉토리 → 검색엔진 → 포털 → **전자상거래 (e-커머스)**&#xB85C; 진화하게 됩니다.&#x20;

**웹의 보급 = 정보의 유통 = 상품 판매 수단**으로 변화하였어요.

### 2) Internet Explorer와 웹의 상업화 (1995\~2004)

<figure><img src="../../.gitbook/assets/image (317).png" alt=""><figcaption></figcaption></figure>

Windows에 **IE 기본 탑재**로 인해 웹은 기업의 필수 명함이 되었어요. 웹 디자이너 + 서버스크립트(게시판, 방명록) 필요했졌습니다.

**솔루션 기반의 홈페이지 제작이 보편화**, HTML/CSS만 다루는 웹 개발자에 대한 저평가되었습니다. **디자인 중심** 웹 수요 급증, 기능보단 시각 효과가 핵심이던 시절이었어요.

> **웹페이지 개발의 수요는 올라갔으나** 디자인과 데이터 솔루션을 제외한 **로직의 영역이 중요하지 않았던 시기**였습니다. HTML와 CSS는 다른 개발에 비해 상대적으로 쉬웠고, Javascript나 Flash등의 **동적 스크립트의 영역이 엔터프라이즈급 비지니스 모델과 거의 연관이 없던 시절**이었다고 합니다.

### 3) Web 2.0과 RIA (Rich Web Application)의 탄생 - Flash의 등장과 웹 애니메이션의 진화

2000년대 초 닷컴 버블로 여러가지 웹 비지니스가 망하고 웹 생태계가 재편이 되면서 **web 2.0**이라는 용어가 생겨 납니다. web 2.0에는 여러가지 개념들이 있지만 프론트엔드와 가장 밀접한 키워드는 **RIA(Rich Web Application)** 입니다.

기존의 정적인 문서 구조에서 벗어나 **사용자와 적극적으로 인터렉션**을 하며 **화면이 동적**으로 변하는 것들로 나아가야 한다고 했습니다.

**기존 브라우저의 성능 부족**으로 정교한 애니메이션 표현 어려움 이를 보완하기 위해 등장한 **Flash**는 디자인, 애니메이션, 인터랙션에서 압도적인 성능 제공하였어요.

<figure><img src="../../.gitbook/assets/image (316).png" alt=""><figcaption></figcaption></figure>

웹 디자이너 중심의 Flash 활용이 확대되며 "화려한 웹사이트의 시대" 도래 그러나 **검색 불가, 보안 문제, 무거운 리소스 로딩** 등의 한계로 점차 퇴출되었습니다.

특히 **Apple의 아이폰에서 Flash 비지원 선언** 이후 몰락되었습니다.



### 5) 구글의 도전: 웹을 OS로!

<figure><img src="../../.gitbook/assets/image (318).png" alt=""><figcaption></figcaption></figure>

(출처: [https://www.flickr.com/photos/50114361@N00/3702942040](https://www.flickr.com/photos/50114361@N00/3702942040))

구글은 검색엔진 성공 후 **웹을 새로운 OS로 만들기 위해 Chrome OS 등 시도했습니다.**&#x20;

OS라면 어떠한 것들을 제공해야 할까요? OS만 있어서는 아무것도 할 수 없습니다. OS가 성공하기 위해서는 **좋은 Software가 필요**합니다. 그래서 구글은 Microsoft에 있는 **Office를 웹으로 옮겨오는 시도**를 하게 됩니다. Office → Google Docs로, MS에 대항하기 위한 **웹 기반 소프트웨어 개발 시작했어요.**

이를 위해 Internet Explorer 보다 더 좋은 브라우저가 필요했고 웹 개발에 용이해야 했기에 디버그 기능에 엄청 공을 들였습니다.&#x20;

> 뿐만 아니라 javascript 성능을 올리기 위해 자체적인 **V8 자바스크립트 엔진**도 만들게 됩니다.

구글 뿐만 아니라 MS의 Internet Exploer의 독점적 위치가 대항하기 위한 여러가지 브라우저들이 이 시기에 만들어지기 시작했습니다. 이는 후술할 **웹 표준**과 **웹 접근성**의 토대가 된다고 합니다.&#x20;

### 6) Ajax의 대중화와 JS의 재발견

**웹에서도 Application과 같은 소프트웨어**가 동작하기를 바랬습니다. 그전까지는 열악한 브라우저 컴퓨팅 환경에서 굳이 javascript로 이런 것들을 만들 이유가 없었습니다. 훨씬 더 나은(?) ActiveX와 Flash, Java Applet과 같은 대체재가 얼마든지 있었습니다. 그래서 ActiveX 범벅이었죠. 굳이 웹으로 만들 필요도 없습니다. 하지만 구글은 **다른 vendor에 의존하지 않는 방법이 필요**했습니다.

> **javascript를 통해서 비동기로 화면을 동적으로 구성하는 방법**을 통해서 Google Maps라는 서비스를 만들었고 이것은 웹 개발 생태계에 새로운 파장을 일으켰습니다.

**페이지 새로고침 없이 동적으로 UI 변경 가능** → **웹 생태계에 있는 자원을 그대로 활용해서 동적인 화면들을 만들 수 있다는 것**은 상당한 가치였으며 이를 통해서 **javascript가 재평가**를 받게 됩니다.

### 7) 웹 표준, 접근성, 시맨틱, 크로스브라우징의 등장

다양한 브라우저 생태계로 인해 **웹 표준화의 필요성 대두되었고 ActiveX, Flash 비표준 기술 최소화 + IE 크로스브라우징 고려하였습니다.**

**접근성, 시맨틱 HTML 강조하게 되었고** 디자이너/개발자의 역할 분리 시작었습니다.

### 8) jQuery의 등장과 프론트엔드 재정의

<figure><img src="../../.gitbook/assets/image (320).png" alt=""><figcaption></figcaption></figure>

#### 기존 DOM API의 한계

1. **브라우저 간 호환성 문제**
   * 같은 DOM 함수를 사용해도 브라우저마다 다르게 동작하는 경우가 많았음
   * 개발자는 이를 고려해 **조건문과 예외 처리**를 별도로 구현해야 했음
2. **불편한 문법**
   * 예: `document.getElementsByTagName("big")[0].style.background = "green";`
   * DOM 조작이 직관적이지 않고 **지저분하고 복잡**함

2008년 등장한 jQuery: **DOM 제어, Ajax, 크로스 브라우징 문제 해결**

열악했던 개발환경에서 대체재가 없는 이 javascript는 많은 사람들의 손을 거쳐가며 라이브러리가 발전하고 사용법이 발전을 하게 됩니다.

이러한 노력의 결과로 2008년 **웹표준 API를 모두 포함**하면서 **훨씬 더 쉬운 문법**과 **IE 크로스브라우징**, **Ajax**까지 해결해버린 **jQuery**가 탄생을 하게 되면서 웹개발은 제2의 전성시대를 맞이하게 됩니다. 이때 만들어진 jQuery의 대부분의 문법들은 실제 웹 표준의 API 기능으로 대거 포함이 됩니다.

HTML/CSS를 시멘틱하게 구성하고 JS로 동적 처리하는 **역할 분리 체계 형성하였습니다.**

#### &#x20;jQuery의 한계와 이후의 흐름

jQuery는 **웹 개발의 표준 도구**가 되었지만, 너무 많은 개발자들이 **서로 다른 jQuery 유사 라이브러리**를 만들기 시작했어요.

* 결과적으로 **라이브러리 난립**과 **호환성 문제** 발생
* 이는 훗날 **프레임워크(React, Vue 등)** 시대의 도래로 이어짐

### 9) 크롬 브라우저와 V8 엔진의 탄생

* Google의 V8 JS 엔진 + Webkit → **Chrome 브라우저 개발**
* 빠른 성능 + 강력한 디버깅 환경 → **웹 개발자들의 희망**
* 디버깅이 가능해지면서 **대형 서비스 개발 가능 환경 조성**

### 10) Node.js와 npm의 등장

**V8의 오픈소스**는 새로운 국면을 맞이합니다. **javascript를 가지고 서버사이드 환경을 개발할 수 있는 Node**가 탄생합니다. 이는 javascript 성장에 박차를 가해줍니다.&#x20;

javascript만 할 줄 알던 사람도 **새로운 언어의 학습없이 서버개발을 할 수가 있게** 되었고 서버개발자 역시 javascript를 하면 겸사겸사 브라우저 개발도 할 수 있도록 영역을 넓혀주게 됩니다.

* V8을 활용한 **Node.js의 등장**으로 JS의 서버 확장 가능해짐
* **프론트/백 통합 개발자 등장** + **module 시스템 도입**
* npm을 통한 **모듈 생태계 확장** → 공통 라이브러리와 DevOps 도구(webpack, babel 등)로 발전

### 11) 페이스북과 React의 등장

* **Facebook의 초거대 단일 서비스 운영 필요** → React Framework 탄생
* JSX, Virtual DOM 등 새로운 개발 철학 도입
* 이 시점부터 프론트엔드가 명확히 **독립된 전문 분야로 분화**됨

### 12) 디지털 전환과 하이브리드 앱 시대

* 기업과 비IT 업계의 **디지털 전환 가속화** → 백오피스 시스템 수요 증가
* 정형화된 쇼핑몰 외에도 **데이터 유통 구조를 시각화/조작하는 도구**로 웹 프론트엔드 사용 증가
* **서비스 프로토타이핑**, 관리자 도구, CMS 등에서 프론트엔드의 중요성 증대
* **하이브리드 앱의 성장**: 한 번의 개발로 여러 플랫폼 대응 가능

### ✅ 요약: 웹 프론트엔드는 이제 하나의 독립 생태계

* 문서 중심 → **데이터 중심으로 전환**
* 설치 없이 브라우저만으로 동작 가능한 **강력한 앱 플랫폼**
* **크롬 + V8 + Node.js + npm + React**로 이어지는 혁신 덕분
* 프론트엔드는 더 이상 HTML/CSS만 다루는 직무가 아니라, **복잡한 데이터 흐름과 UX를 책임지는 핵심 분야**

<figure><img src="../../.gitbook/assets/image (321).png" alt=""><figcaption></figcaption></figure>



## 3. V8에 대해 좀 더 자세히 알아보자.

> 출처 : [https://velog.io/@remon/V8-%EC%97%94%EC%A7%84%EC%9D%B4-%EB%8C%80%EC%B2%B4-%EB%AD%90%EC%95%BC](https://velog.io/@remon/V8-%EC%97%94%EC%A7%84%EC%9D%B4-%EB%8C%80%EC%B2%B4-%EB%AD%90%EC%95%BC)

<figure><img src="../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 1) 일단, Node.js란?

> 내용 출처 : [https://velog.io/@remon/%EA%B0%9C%EB%B0%9C-%EA%B8%B0%EB%B3%B8-%EC%A7%80%EC%8B%9D-Node.js%EB%9E%80](https://velog.io/@remon/%EA%B0%9C%EB%B0%9C-%EA%B8%B0%EB%B3%B8-%EC%A7%80%EC%8B%9D-Node.js%EB%9E%80)

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (2) (1).png" alt=""><figcaption><p><a href="https://velog.io/@remon/%EA%B0%9C%EB%B0%9C-%EA%B8%B0%EB%B3%B8-%EC%A7%80%EC%8B%9D-Node.js%EB%9E%80">https://velog.io/@remon/%EA%B0%9C%EB%B0%9C-%EA%B8%B0%EB%B3%B8-%EC%A7%80%EC%8B%9D-Node.js%EB%9E%80</a></p></figcaption></figure>

> _Node.js는 Chrome V8 JavaScript 엔진으로 빌드된 JavaScript 런타임입니다._\
> &#xNAN;_-Node.js는 공식 홈페이지-_

런타임은 특정 언어로 만든 **프로그램들을 실행할 수 있는 환경**을 뜻합니다.\


<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

브라우저에는 자바스크립트 해석 엔진이 있습니다. 기존에는 자바스크립트를 인터넷 브라우저 위에서만 실행할 수 있었습니다.\
그러나 2008년에 구글이 V8 엔진을 사용하여 크롬을 출시했습니다. V8 엔진은 엄청 빨랐고, 오픈 소스로 코드도 공개되었습니다. <mark style="background-color:blue;">V8 엔진이 너무 뛰어나서 기능을 좀 더 더해서 V8 엔진 기반에 노드 프로젝트를 시작했고,</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">**Node.js(V8)**</mark><mark style="background-color:blue;">이 등장</mark>했습니다. <mark style="color:red;">Node.js는 브라우저 내에서 말고도</mark> <mark style="color:red;"></mark><mark style="color:red;">**다른 환경에서 자바스크립트를 사용할 수 있게**</mark> <mark style="color:red;"></mark><mark style="color:red;">해줍니다.</mark>

> 따라서 **Node.js는 JavaScript 실행 환경(=런타임)입니다.**

Node.js를 설치하면 브라우저를 키지 않아도 자바스크립트를 컴퓨터에서 수행할 수 있게됩니다.

Node.js가 자바스크립트를 컴퓨터에서 쉽게 실행시켜줬기 때문에 자바스크립트를 프로그래밍 언어처럼 사용하기 시작했습니다.

많은 사람들이 Node로 서버를 만듭니다.

#### 왜하필 Node.js로 서버를 만들어 쓸까? <a href="#nodejs" id="nodejs"></a>

두 개의 서버(=요청을 처리하는 기계)가 있습니다.\
Node.js로 만들어 진 거 하나, 일반 서버 하나 이렇게 두 개가 있다고 가정해봅시다.\
이 서버는 동일한 작업을 수행하는 서버입니다. 예를 들어 CGV 페이지라고 생각해봅시다.

> 손님이 예매할 영화의 티켓 수를 얘기(요청)하면\
> CGV 페이지의 서버가 요청을 받고 티켓을 줍니다(응답).\
> 손님은 서버가 준 티켓을 받습니다.

#### 일반 서버의 경우 <a href="#undefined" id="undefined"></a>

손님이 4명 있다고 생각해봅시다.\
1번째 손님은 티켓 1장을 요구해서 서버가 티켓 1장을 줬습니다.\
2번째 손님은 1번째 손님이 가기를 기다렸다가 차례가 되면 예매를 합니다. 1장을 요구하고 서버에게 1장을 받고 갑니다.\
그런데 3번째 손님이 200장을 예매합니다. 그러면 서버는 200장의 티켓을 준비하고, 3번째 손님은 기다립니다. 근데 문제는 4번째 손님도 3번째 손님이 마무리될 때까지 기다려야한다는 겁니다.

#### Node.js로 개발한 서버인 경우 <a href="#nodejs" id="nodejs"></a>

모든 손님의 요청을 한번에 받습니다. 그리고 순서와 상관없이 처리 속도가 빠른 것부터 결과를 가져다줍니다. 처리 속도가 빠른 것부터 처리하기 때문에 요청을 놓치지 않고, 4번째 손님이 굳이 3번째 손님의 요청이 끝날 때까지 기다리는 문제도 사라졌습니다.\
이게 Node.js의 **Non-blocking I/O**의 개념입니다.

### 2) V8 JavaScript 엔진의 작동 원리와 최적화 과정

컴퓨터는 0과 1밖에 이해하지 못하는 낮은 수준의 언어입니다. 하지만 고수준 언어인 자바스크립트 파일을 이해하고 명령을 수행할 수 있습니다. 인간이 작성한 자바스크립트 파일을 컴퓨터가 읽을 수 있는 것은 자바스크립트 엔진이 있기 때문에 가능합니다.\
자바스크립트 엔진은 일련의 과정을 거쳐서 컴퓨터에게 최적화된 코드로 변환하여 전달하게 되고, 이러한 과정으로 인해서 컴퓨터가 자바스크립트 파일을 이해하고 수행할 수 있는 것입니다.

> 즉, 자바스크립트 엔진은 <mark style="background-color:purple;">자바스크립트 코드를</mark> <mark style="background-color:purple;"></mark><mark style="background-color:purple;">**마이크로프로세서**</mark><mark style="background-color:purple;">가 이해할 수 있게 기계어로 변환해서 실행하는</mark> <mark style="background-color:purple;"></mark><mark style="background-color:purple;">**프로그램**</mark> <mark style="background-color:purple;"></mark><mark style="background-color:purple;">또는</mark> <mark style="background-color:purple;"></mark><mark style="background-color:purple;">**인터프리터**</mark>를 말합니다.

#### 컴파일러와 인터프리터 <a href="#undefined" id="undefined"></a>

V8엔진이 어떻게 작동하는지 알아보기 전, **프로그래밍에서 사용하는 두 가지 번역 방식**에 대해 먼저 알아봅시다.

| 구분 | 인터프리터      | 컴파일러              |
| -- | ---------- | ----------------- |
| 방식 | 한 줄씩 바로 실행 | 전체 코드를 읽고 변환 후 실행 |
| 장점 | 빠른 초기 실행   | 반복 작업의 최적화 가능     |
| 단점 | 반복 시 느려짐   | 초기 실행 시간 소요       |

#### V8 엔진이란?

V8 엔진은 Google에서 개발한 **오픈소스 자바스크립트 엔진**으로, 다음과 같은 환경에서 사용됩니다:

* **Chrome 브라우저**
* **Node.js 런타임**

> ⚙️ 핵심 개념: 자바스크립트 코드를 **기계어로 변환**해서 CPU가 이해하고 실행할 수 있게 만들어주는 엔진

**V8 엔진이 만들어진 이유**

V8은 웹 브라우저 내부에서 자바스크립트 **수행 속도의 개선을 목표**로 처음 고안되었습니다.\
이전에 여러 자바스크립트 엔진이 사용되고 있었지만 다른 자바스크립트 엔진은 웹 특성상 유저와 상호작용을 위해 즉시성이 있는 인터프리터 방식을 사용하는데, 이는 코드가 많아질수록 속도가 느려진다는 단점이 있습니다.&#x20;

> 이 점을 보완해서 속도 향상을 위해 **v8엔진은 인터프리터를 사용하는 대신 JIT(Just In Time) 컴파일러**를 구현함으로써 코드 실행 시에 **자바스크립트 코드를 머신 코드로 컴파일**합니다.

#### 왜 머신 코드로 컴파일해야할까? <a href="#undefined" id="undefined"></a>

![사진 출처 : https://blog.naver.com/tipsware/221041215416](https://velog.velcdn.com/images/remon/post/0cc68a83-b3d8-4c35-a254-30cce2ef2156/image.png)

우리가 사용하는 컴퓨터 안에는 마이크로프로세서라는 작은 기계가 있는데, 이것은 <mark style="background-color:blue;">CPU의 핵심 기능을 통합한 집적 회로(IC)</mark>입니다. 프로그래머가 어떤 코드를 짜서 컴퓨터에게 일을 시키려면 결국 이 마이크로프로세서가 해석할 수 있는 언어로 전달해야 합니다. 마이크로프로세서는 이것을 만든 회사나 버전에 따라 각자 다른 언어를 사용합니다. 이 언어들은 **하드웨어와 직접적으로 소통할 수 있는 코드들**이고 그래서 **기계어, 머신 코드**라 불립니다. 그러니까 결국 프로그래머들은 프로그래밍 효율을 위해 추상화 수준이 높은 고수준 언어를 사용해서 코딩을 하지만 이는 **결국 머신 코드로 컴파일되어야만 CPU가 이해하고 처리**를 할 수 있다는 말입니다.

#### V8 엔진의 작동 방식 (JIT 컴파일)

V8은 전통적인 인터프리터 방식 대신, **JIT(Just-In-Time) 컴파일** 방식을 채택하여 성능을 최적화합니다.

#### 📚 전체 흐름 요약

<figure><img src="../../.gitbook/assets/image (325).png" alt=""><figcaption></figcaption></figure>

**1. 자바스크립트가 태어나는 순간 – Parser와 토큰화**

웹 브라우저가 자바스크립트 파일을 받으면, "V8 엔진"이라는 아주 똑똑한 두뇌가 그 파일을 읽기 시작합니다.

이 엔진 안에는 먼저 `Parser(파서)`라는 친구가 등장합니다.

#### 🧩 역할:

Parser는 소스코드를 읽으며, 글자를 잘게 잘게 나눠 \*\*토큰(Token)\*\*으로 만듭니다.\
예를 들어 아래 코드를 보면요,

```js
var a = 5;
```

Parser는 이렇게 자릅니다:

```js
["var", "a", "=", "5", ";"]
```

마치 문장을 단어 단위로 끊어서 문법적으로 구조를 분석하기 위한 준비를 하는 것이죠.

**2. 구조를 이해하는 법 – AST, 추상 구문 트리**

Parser가 만든 토큰을 바탕으로 `AST(Abstract Syntax Tree)`라는 나무구조를 만들어냅니다.

이 구조는 **“이 변수는 어디서 정의됐고, 어떤 값이 할당되었고, 어떤 연산이 이뤄지는가”** 등을 트리 구조로 시각화한 것입니다.

즉, **컴퓨터가 코드의 ‘의미’를 이해할 수 있는 형태**로 바꾸는 단계입니다.



**3. 실행 준비! – Ignition이 바이트코드를 점화하다**

이제 V8 엔진 안의 또 다른 인물이 등장합니다. 이름은 **Ignition**. 이름처럼, 이 친구는 "점화기"입니다. 코드를 ‘실행’할 준비를 하죠.

**AST를 받으면**, Ignition은 \*\*바이트코드(Bytecode)\*\*라는 중간 코드를 만들어냅니다.

```js
var a = 5;
```

이라는 고수준 코드를 컴퓨터가 이해할 수 있는 형태로 중간 번역하는 과정이에요.

#### 🛠 바이트코드란?

* **가상머신이 이해할 수 있는 중간 코드**
* 완전한 기계어는 아니지만, **V8 엔진 내부에서 실행 가능한 최소 단위**
* 실행 속도와 메모리 사용을 동시에 고려한 **효율적 구조**

이 중간 번역은 빠르게 이루어지고, **해석된 바이트코드가 바로 실행되기 시작합니다.**



**4. 뜨거운 코드를 감지하는 감시자 – Profiler의 등장**

V8은 똑똑합니다. 단순히 코드를 실행하는 걸로 끝나지 않죠.

**Ignition이 실행하는 동안, Profiler라는 감시자**가 프로그램을 계속 분석합니다.

“어? 이 함수 계속 쓰이네?”\
“이 변수 너무 자주 호출되는데?”\
이런 **"Hot Spot"**, 즉 **자주 쓰이는 부분**을 체크해 나갑니다.



**5. 진짜 컴파일 시작 – TurboFan의 등장**

감시자 Profiler가 **"여기 뜨거워요!"** 하고 보고하면,\
V8 엔진의 터보 부스터인 **TurboFan**이 등장합니다.

이 최적화 컴파일러는 바이트코드를 기반으로, **컴퓨터가 바로 실행할 수 있는 진짜 기계어**로 코드를 변환합니다.

#### TurboFan의 최적화 전략:

* **Hidden Class**: 객체 구조를 예측해서 자주 쓰이는 형태를 빠르게 참조할 수 있게 만듭니다.
* **Inline Caching**: 자주 호출되는 메서드나 함수는, 아예 **그 위치에 코드 자체를 넣어버립니다.** 호출 없이 실행하겠다는 거죠.



**6. 하지만, 너무 과열되면? – Deoptimization**

자바스크립트는 동적 언어다 보니, 값의 타입이나 구조가 중간에 바뀔 수 있어요.

**TurboFan이 최적화한 코드도** 때로는 “어라? 조건이 바뀌었네?” 하고 다시 `비최적화(Deoptimization)`를 하기도 합니다.

하지만 V8은 이조차도 잘 관리합니다.\
필요하다면 언제든 **다시 최적화 → 비최적화 → 재최적화**를 반복하면서 **효율적 실행을 유지**하려고 해요.



**7. 마침내 실행 – 최적화된 코드가 주인공이 된다**

이제 **최적화된 코드가 준비되었고**, 실행할 차례가 오면, 더 이상 느린 바이트코드가 아닌\
**TurboFan이 변환한 기계어**가 메모리에서 직접 실행됩니다.

이게 바로 우리가 "브라우저에서 빠르게 돌아가는 웹 앱"을 느낄 수 있는 이유예요.

### 🔄 전체 흐름 요약

```plaintext
JS 코드 작성
    ↓
Parser → Token으로 분해
    ↓
AST(Abstract Syntax Tree) 생성
    ↓
Ignition 인터프리터 → Bytecode 생성 및 실행
    ↓
Profiler가 자주 실행되는 Hot Spot 코드 감지
    ↓
TurboFan이 해당 코드를 최적화(기계어로 변환)
    ↓
최적화된 코드를 실행
```

#### 💬 비유로 정리하면…

* **Parser**: 자바스크립트 책을 받아 문장을 토막내는 국어 선생님
* **AST**: 토막을 문법 구조로 나누는 독해 트리
* **Ignition**: 트리에 불을 붙여 즉시 실행하는 점화기
* **Profiler**: 주변에서 불이 얼마나 뜨거운지 감지하는 열 감지기
* **TurboFan**: "이 불은 오래 쓸 거니까 아예 화로를 만들어!" 하고 만드는 최적화 장치

###



#### 📌 V8 네이밍의 의미

* **V8**: 실제 자동차 엔진에서 유래된 이름 (8기통 엔진)
* **Ignition**: 점화 장치 → JS 코드 실행의 시동
* **TurboFan**: 고성능 제트엔진 → 최적화로 속도 향상

V8 엔진은 단순한 인터프리터가 아닌, 복잡한 **JIT 컴파일 파이프라인**을 통해 JavaScript 성능을 극한까지 끌어올립니다.

* 처음에는 빠르게 실행 가능한 바이트코드로 실행
* 반복 사용되는 코드는 분석 후 기계어로 최적화
* 예상과 다르면 Deoptimization

이러한 설계는 사용자 경험을 부드럽게 하면서도, 자바스크립트를 **현대적인 고성능 언어 수준으로 끌어올린 핵심 기술**이라고 할 수 있습니다.

***

📎 참고: [V8 공식 블로그](https://v8.dev/), [JSConf EU 2017 발표](https://www.youtube.com/watch?v=UJPdhx5zTaw)
