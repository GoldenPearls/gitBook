# 알아가는 것들

## 1. javascript:void(0); 개념 정리

`javascript:void(0);`은 JavaScript에서 **아무런 동작을 하지 않고, 페이지를 다시 로드하거나 이동시키지 않도록 하는 방법**입니다. 주로 링크(`<a>`) 요소에 사용되어, 링크가 클릭되었을 때 다른 페이지로 이동하거나 아무 페이지 액션이 발생하지 않도록 하기 위해 쓰입니다.

### `javascript:void(0);`의 사용 사례

```html
<a href="javascript:void(0);">Click me</a>
```

위의 코드에서 링크를 클릭하더라도 페이지가 다시 로드되거나 다른 곳으로 이동하지 않습니다. 이는 주로 자바스크립트로만 동작을 제어하고자 할 때 유용합니다.



## 2. 클릭 이벤트

```
$(".클래스명").click(function () {
    eventWinPop("");
});
```

만약 클래스명이 공백으로 layout new 이런식이라면

```
.layout.new로 정의해야 함
```



## 3. 링크에서 페이지 상단으로 스크롤 방지

HTML의 `<a href="#top">` 같은 링크를 클릭하면 페이지 상단으로 이동하지만, `javascript:void(0);`를 사용하면 **링크를 클릭해도 페이지가 스크롤되거나 이동하지 않도록** 방지할 수 있습니다. 예를 들어, 페이지 상단으로 스크롤을 방지하고 자바스크립트를 실행하기 위한 용도로 사용됩니다.

```html
html코드 복사<a href="javascript:void(0);" onclick="scrollToTop();">Scroll to Top</a>

<script>
function scrollToTop() {
  window.scrollTo({top: 0, behavior: 'smooth'});
}
</script>
```

위 코드는 링크를 클릭하면 페이지 상단으로 스크롤되도록 하는 동작을 정의합니다. 링크는 `javascript:void(0);`을 사용해 기본 이동을 방지하고, JavaScript 함수로 스크롤을 직접 제어합니다.



## 4. **onclick="함수명(event);**

클릭 이벤트가 발생하면 함수명 자바스크립트 함수가 실행됩니다.

`event`는 자바스크립트에서 **이벤트 객체**를 의미합니다. 이 객체는 특정 이벤트(예: 클릭, 키보드 입력 등)가 발생했을 때 해당 이벤트와 관련된 정보를 담고 있습니다.

#### 예시로 설명:

```html
<a href="javascript:void(0);" onclick="handleClick(event)">클릭하세요</a>
```

위 HTML에서 링크를 클릭하면 `handleClick` 함수가 실행되며, `event`라는 객체가 자동으로 함수에 전달됩니다.

#### JavaScript:

```javascript
jfunction handleClick(event) {
    console.log(event); // 클릭 이벤트와 관련된 정보를 출력
    event.preventDefault(); // 기본 동작(예: 링크 이동)을 막음
}
```

### `event` 객체의 주요 역할:

1. **이벤트의 기본 동작을 막을 수 있음**: 예를 들어, 링크 클릭 시 페이지가 이동하는 기본 동작을 `event.preventDefault()`로 막을 수 있습니다.
2. **이벤트가 발생한 요소를 확인할 수 있음**: `event.target`을 사용하여 사용자가 클릭한 요소에 접근할 수 있습니다.
3. **이벤트의 전파를 제어할 수 있음**: `event.stopPropagation()`을 통해 이벤트가 부모 요소로 전파되는 것을 막을 수 있습니다.

### 주요 속성과 메서드:

* `event.target`: 이벤트가 발생한 요소.
* `event.preventDefault()`: 기본 동작을 막음 (예: 링크 클릭 시 페이지 이동).
* `event.stopPropagation()`: 이벤트가 부모 요소로 전파되는 것을 막음.

`event` 객체는 다양한 이벤트와 관련된 정보를 제공하여, 사용자가 클릭한 위치, 발생한 이벤트 종류 등을 다룰 수 있게 도와줍니다.

### 문제가 있을  수 있는 상황

1. **이벤트 객체 전달 여부**:
   * `event` 객체는 **브라우저에서 자동으로 전달**되므로, `onclick="함수명(event)"`과 같은 방식으로 제대로 전달되어야 합니다. 만약 `event` 객체를 함수에서 수신하지 못한다면, 함수 호출 부분에 문제가 있을 수 있습니다.
   * 올바른 방법으로 호출하는지 확인하세요: `onclick="myFunction(event)"`
2.  **이벤트 객체의 사용 가능 여부**:

    * `event` 객체는 **이벤트 핸들러 함수**에서만 사용할 수 있습니다. HTML 요소의 `onclick` 속성에서 직접 호출하거나, 자바스크립트에서 이벤트 리스너를 추가할 때 사용할 수 있습니다.

    예시:

    ```html
    <a href="#" onclick="handleClick(event)">클릭하세요</a>
    ```
3. **기본 동작이 막히지 않는 문제**:
   * `event.preventDefault()`를 호출해도 기본 동작(예: 링크 이동)이 막히지 않는다면, 이벤트 객체가 제대로 전달되지 않거나, 이벤트가 잘못된 방식으로 바인딩되었을 수 있습니다.
   * `event.preventDefault()`가 호출되기 전에 페이지가 이동하는 등의 동작이 일어나면, 이벤트 객체를 제대로 처리하지 못한 것입니다.
4.  **다른 방법으로 이벤트를 처리하는 방법**: 만약 `onclick="함수명(event)"`이 예상대로 작동하지 않으면, 다른 방식으로 이벤트를 처리할 수 있습니다.

    예시: **이벤트 리스너로 처리하기** (추천 방식)

    ```html
    <a href="#" id="myLink">클릭하세요</a>
    ```

    ```javascript
    javascript코드 복사document.getElementById("myLink").addEventListener("click", function(event) {
        event.preventDefault(); // 기본 동작을 막음
        alert("이벤트가 처리되었습니다.");
    });
    ```

    이 방식에서는 `event` 객체가 자동으로 함수에 전달되며, `preventDefault()`도 확실하게 작동합니다.

#### 요약:

* **`event` 객체가 전달되지 않으면**: `onclick` 속성에서 직접 전달하는지 확인하거나, 이벤트 리스너 방식으로 구현합니다.
* **`event.preventDefault()`가 작동하지 않으면**: 이벤트가 제대로 바인딩되었는지 확인하고, 리스너를 사용해 이벤트를 처리하는 방법을 사용해보세요.
