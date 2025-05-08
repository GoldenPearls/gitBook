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

**브라우저가 한 번에 하나의 작업만** 할 수 있는 `단일 스레드(single-threaded) 환경`이라면, 우리는 어떻게 동시에 여러 가지 일을 할 수 있을까요?

바로 "이벤트 루프" 덕분입니다.&#x20;

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p><a href="https://inpa.tistory.com/entry/%F0%9F%94%84-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EA%B5%AC%EC%A1%B0-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC">https://inpa.tistory.com/entry/%F0%9F%94%84-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EA%B5%AC%EC%A1%B0-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC</a></p></figcaption></figure>

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

`JavaScript`는 싱글쓰레드 언어라고 많이 알려져 있습니다. 싱글쓰레드라고 한다면 여러 개의 작업이 있더라도 한 번에 하나의 작업만 수행할 수 있습니다. 하지만 `JavaScript`를 사용해 보면 멀티쓰레드처럼 동시에 여러 작업을 수행할 수 있다는 것을 알 수 있습니다.

{% hint style="danger" %}
&#x20;그렇다면 `JavaScript`는 정말 싱글쓰레드 언어가 맞을까요?
{% endhint %}

맞습니다. 그 이유는 JavaScript의 메인쓰레드인 **이벤트 루프**가 싱글 쓰레드이기 때문입니다. 하지만 **이벤트 루프**만 독립적으로 실행되는것이 아닌 <mark style="color:red;">웹 브라우저나 NodeJS 같은 멀티쓰레드 환경에서 실행되고 이를 적절하게 사용함으로써 멀티쓰레드처럼 사용이 가능한 것입니다.</mark>



<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption><p><a href="https://blog.toktokhan.dev/t-767eb0fa38f3">https://blog.toktokhan.dev/t-767eb0fa38f3</a></p></figcaption></figure>

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

#### Web API <a href="#bb25" id="bb25"></a>

Javscript를 사용하면서 우리가 많이 사용하는 API 들은 사실 JavaScript에서 지원하는 것이 아닌 웹 브라우저에서 제공하는 API로 `DOM` ,`AJAX`, `Timeout` 등이 있습니다.

`Call Stack`에서 실행된 비동기 함수는 `Web API`에서 처리를 하게 되고 그동안에 `Call Stack`은 나머지 동기 함수들을 처리하게 됩니다.

`Web API`는 비동기 함수들을 처리하며 작업이 완료된 비동기 함수들을 `Callback Queue`로 넘겨주게 됩니다.

#### Callback Queue <a href="#id-4bf2" id="id-4bf2"></a>

`Callback Queue`는 비동기 함수들을 보관하는 장소로 `Event Loop`에서 비동기 함수를 꺼내기 전까지는 계속 `Queue`방식으로 보관하게 됩니다.

* Queue : 선입선출(FIFO)로 먼저 들어간 것이 먼저 나가는 방식

#### Event Loop <a href="#c357" id="c357"></a>

`Event Loop`는 `Call Stack`과 `Callback Queue`를 상태를 계속 감시하며 `Call Stack`에 함수들이 존재하지 않는다면 `Callback Queue`에 있는 비동기 함수들을 `Call Stack`에 밀어 넣게 됩니다. 그 후 `Call Stack`에서 비동기 함수를 실행시키게 됩니다.

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

## 2. V8에 대해 좀 더 자세히 알아보자.

