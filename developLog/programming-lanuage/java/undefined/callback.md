# 콜백(callback)

> 콜백(callback)은 프로그래밍에서 특정 작업이 완료된 후에 호출되는 함수를 의미합니다.&#x20;

일반적으로 비동기 프로그래밍 또는 이벤트 기반 프로그래밍에서 많이 사용되며, 특정 이벤트가 발생했거나 작업이 완료되었을 때 실행할 코드를 지정하는 방법입니다.

#### 주요 개념

1. **비동기 실행**:
   * 콜백은 비동기 작업이 완료된 후에 호출됩니다. 예를 들어, 데이터 요청이 완료되었거나 파일 읽기가 끝났을 때, 해당 작업이 완료되었음을 알리는 콜백 함수가 호출됩니다.
2. **함수 인자로 전달**:
   * 콜백 함수는 다른 함수의 인자로 전달되며, 주로 이벤트 리스너나 비동기 API에서 사용됩니다.

#### 사용 예시

JavaScript에서 비동기 작업을 수행할 때 콜백을 사용하는 예를 들어보겠습니다.

```javascript
function fetchData(callback) {
    setTimeout(() => {
        const data = "Fetched Data";
        callback(data); // 데이터가 준비되면 콜백 호출
    }, 1000); // 1초 후에 데이터 가져오기
}

function processData(data) {
    console.log("Processing: " + data); // 콜백에서 처리
}

// fetchData 함수 호출 시 processData를 콜백으로 전달
fetchData(processData);
```

#### 콜백의 장점

* **유연성**: 콜백을 통해 코드의 재사용성을 높이고, 다양한 작업을 처리할 수 있습니다.
* **비동기 처리**: 비동기 작업을 쉽게 관리할 수 있어, 사용자 인터페이스가 차단되지 않고 부드럽게 동작하도록 합니다.

#### 메모리 관리

콜백을 사용할 때는 메모리 누수를 방지하기 위해, 더 이상 필요하지 않은 콜백에 대한 참조를 해제해야 합니다. 특히 이벤트 리스너와 함께 사용할 때는 주의가 필요합니다.

#### 요약

콜백은 특정 작업이 완료된 후 호출되는 함수로, 비동기 작업 및 이벤트 기반 프로그래밍에서 흔히 사용됩니다. 이를 통해 코드의 유연성과 재사용성을 높이고, 비동기 처리를 효과적으로 관리할 수 있습니다.

이런 자료를 참고했어요. \[1] Wikipedia - 콜백 - 위키백과, 우리 모두의 백과사전 (https://ko.wikipedia.org/wiki/%EC%BD%9C%EB%B0%B1) \[2] MDN Web Docs - 콜백 함수 - MDN Web Docs 용어 사전: 웹 용어 정의 (https://developer.mozilla.org/ko/docs/Glossary/Callback\_function) \[3] Medium - 콜백이란 무엇인가. 비동기처리… | by Sooka (https://medium.com/@flqjsl/%EC%BD%9C%EB%B0%B1%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-56c26e1f1bc3) \[4] MathWorks - 콜백으로 모델 동작 사용자 지정하기 - MATLAB & Simulink (https://kr.mathworks.com/help/simulink/ug/using-callback-functions.html)

뤼튼 사용하러 가기 > https://agent.wrtn.ai/5xb91l
