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

## 5. 클래스명이 앞에만 같은 애들 이벤트 적용

> div 영역에 있는 class명이 pic\_box\_po\_01, pic\_box\_po\_02, pic\_box\_po\_03 ...이런식으로 10까지 있다 $(".pic)\_boc\_po").on("click:) 이런식으로 하면 모든 애들이 적용되나

$(".pic\_box\_po")로는 해당 요소들을 선택할 수 없습니다. jQuery의 클래스 선택자는 클래스 이름이 완전히 일치해야 하므로, pic\_box\_po\_01, pic\_box\_po\_02처럼 번호가 붙은 클래스들을 선택할 수 없습니다. 이를 해결하기 위해 접두사로 일치하는 클래스 **이름을 선택할 수 있는 jQuery의 속성 선택자를 사용해야 합니다.**

올바른 방법 접두사가 동일한 클래스 이름을 선택하려면, 아래와 같이 **^= (시작 문자 일치) 선택자를 사용할 수 있습니다.**

접두사가 동일한 클래스 이름을 선택하려면, 아래와 같이 `^=` (시작 문자 일치) 선택자를 사용할 수 있습니다.

```javascript
$("[class^='pic_box_po']").on("click", function() {
    // 클릭 시 실행할 코드
});
```

#### 설명

* `^=`는 "접두사로 시작"을 의미하는 선택자입니다.
* `"[class^='pic_box_po']"`는 클래스 이름이 `pic_box_po`로 시작하는 모든 요소를 선택합니다.

이렇게 하면 `pic_box_po_01`, `pic_box_po_02` 등 `pic_box_po_`로 시작하는 모든 요소에 클릭 이벤트가 적용됩니다.

## 6. 단일 이미지 처리, 여러 개 처리&#x20;

코드에서 `missionCt == 2` 이상일 때 이미지 `step02_off.png`가 `step02_on.png`로 변경되지 않는 이유는 **`imgElement`의 `src`가 이미 `step01_on.png`로 변경되었기 때문**입니다. `imgElement`는 `.event_status img`로 선택된 **단일 이미지 요소**이기 때문에, 이 코드로는 여러 이미지를 관리할 수 없습니다.

***

#### 문제 분석

```javascript
var missionCt = 0;
const imgElement = document.querySelector('.event_status img');
										
if (missionCt == 3) {
	if (imgElement.src.includes('step01_off.png')) {
		imgElement.src = imgElement.src.replace('step01_off.png', 'step01_on.png');}
												
	if (imgElement.src.includes('step02_off.png')) {
	        imgElement.src = imgElement.src.replace('step02_off.png', 'step02_on.png');}
												
	if (imgElement.src.includes('step03_off.png')) {
		imgElement.src = imgElement.src.replace('step03_off.png', 'step03_on.png');}
											} 											}
```



1. **`imgElement`는 단일 이미지 요소를 참조**:
   * `imgElement`는 `.event_status img` 셀렉터로 선택된 첫 번째 `img` 요소만 참조합니다. 따라서 `step02_off.png`, `step03_off.png` 등의 이미지를 변경하려 해도 다른 이미지를 참조하지 못합니다.
2. **한 개의 이미지에서 조건문을 처리**:
   * `if (imgElement.src.includes('step01_off.png')) { ... }`로 첫 번째 이미지를 변경한 이후, 같은 `imgElement`에서 `step02_off.png`를 찾으려 하지만 해당 이미지 경로가 존재하지 않으므로 조건이 실패합니다.
3. **여러 이미지를 선택 및 처리하지 않음**:
   * `document.querySelector`는 첫 번째 `img` 요소만 선택하기 때문에 다수의 이미지를 처리할 수 없습니다.

***

#### 해결 방법: 여러 이미지를 처리하도록 수정

**수정된 코드**

<pre class="language-javascript"><code class="lang-javascript">const imgElements = document.querySelectorAll('.event_status img'); // 모든 img 태그 선택

<strong>if (data.evegeYn == 'Y') {
</strong>    missionCt = missionCt + 1;

    if (data.stdourseCt > 0) {
        missionCt = missionCt + 1;
    }

    if (data.stdyount >= 4) {
        missionCt = missionCt + 1;
    }

    if (data.lmsnt > 0) {
        missionCt = missionCt + 1;
    }

    // 미션 수에 따라 이미지 상태 변경
    imgElements.forEach((imgElement, index) => {
        const stepIndex = index + 1; // step01, step02, etc.
        if (missionCt >= stepIndex) {
            imgElement.src = imgElement.src.replace(`step0${stepIndex}_off.png`, `step0${stepIndex}_on.png`);
        }
    });
}
</code></pre>

***

#### 변경 내용 설명

1. **`document.querySelectorAll` 사용**:
   * `querySelectorAll`로 `.event_status` 클래스 내부의 모든 `img` 요소를 선택합니다.
   * 반환된 `NodeList`는 각 이미지 요소를 순회하며 변경할 수 있습니다.
2. **`forEach`로 이미지 반복 처리**:
   * 각 이미지의 순서를 `index`로 참조하여 `step01`, `step02`, ...와 매칭합니다.
   * `missionCount`가 해당 `stepIndex`보다 크거나 같으면 이미지를 변경합니다.
3. **동적 파일명 생성**:
   * `step0${stepIndex}_off.png`와 `step0${stepIndex}_on.png`로 파일명을 동적으로 생성하여 코드의 중복을 줄였습니다

## 7. imgElements.length >= 2는 왜 넣는 거야?

**ChatGPT의 말:**

ChatGPT

`imgElements.length >= 2`는 **안전한 코드 실행을 보장하기 위해** 추가된 조건입니다. 이 조건은 이미지 배열(`imgElements`)에 **충분한 개수의 이미지가 있는지 확인**하는 역할을 합니다. 이를 통해 배열 인덱스 초과 오류를 방지하고, 코드 실행 중 문제가 발생하지 않도록 합니다.

***

#### **왜 필요한가?**

**1. `imgElements`에 충분한 이미지가 없을 경우**

만약 `imgElements` 배열에 **이미지가 1개밖에 없는데** `imgElements[1]`를 접근하려고 한다면, JavaScript에서는 `undefined`를 반환합니다. 이런 상태에서 `undefined.src`에 접근하려고 하면 **런타임 오류**가 발생합니다.

예를 들어:

```javascript
const imgElements = document.querySelectorAll('.event_status img'); // img가 1개뿐일 때
console.log(imgElements[1].src); // TypeError: Cannot read properties of undefined (reading 'src')
```

**2. 조건문이 실패해야 하는 경우**

만약 특정 조건(`data.stdyCourseCount > 0`)을 만족하더라도 **대상 이미지가 없으면** 아무것도 실행하지 않는 것이 안전합니다.

***

#### **조건이 없는 경우 발생할 수 있는 문제**

다음 코드를 보세요:

```javascript
if (data > 0) {
    imgElements[1].src = imgElements[1].src.replace('step02_off.png', 'step02_on.png');
}
```

만약 `imgElements`에 이미지가 1개밖에 없다면:

* `imgElements[1]`는 `undefined`를 반환합니다.
* 결과적으로 런타임 오류(`TypeError`)가 발생합니다.

***

#### **`imgElements.length >= 2`가 하는 역할**

```javascript
if (data> 0 && imgElements.length >= 2) {
    imgElements[1].src = imgElements[1].src.replace('step02_off.png', 'step02_on.png');
}
```

여기서 `imgElements.length >= 2`는 다음을 의미합니다:

1. **이미지가 최소 2개 있어야** 해당 코드가 실행됩니다.
2. 배열의 두 번째 이미지(`imgElements[1]`)가 존재하지 않는 경우 실행을 방지합니다.

***

#### **조건이 없다면 어떻게 될까?**

조건 없이 코드를 작성하면:

* 이미지가 충분하지 않은 경우, 런타임 오류가 발생할 수 있습니다.
* 예: `imgElements[3]`를 접근하려 하지만 실제로는 배열 길이가 3보다 작을 때.

***

#### **왜 각 조건에 개별적으로 사용하는가?**

배열의 길이를 조건별로 확인하는 이유는 **배열 내의 인덱스를 접근할 때** 항상 안전성을 보장하기 위해서입니다. 각 조건이 독립적으로 작동하므로, 필요하지 않은 조건까지 검증할 필요가 없습니다.

***

#### **결론**

`imgElements.length >= 2`를 사용한 이유는:

* 배열 인덱스 초과 오류를 방지하기 위함.
* 이미지가 부족한 상황에서도 **코드가 안전하게 작동하도록 보장**.
* 각 조건에 필요한 이미지 개수에 따라 유연하게 처리하기 위해 추가된 조건입니다.

따라서 이런 조건은 **이미지가 동적으로 로드되거나 개수가 불확실한 경우** 반드시 필요합니다! 😊

## 8. **최적화된 코드**success: function(data) {

```javascript
    console.log('API Data:', data); // API 데이터 확인
    const imgElements = document.querySelectorAll('.event_status img'); // 모든 img 태그 선택
    console.log('Selected Images:', imgElements); // 선택된 이미지 출력

    // 필수 조건 체크
    if (data.en == 'Y' && imgElements.length >= 1) {
        console.log('Condition met: data.en == Y'); // 조건 디버깅

        // 조건 배열 정의
        const conditions = [
            { condition: true, index: 0, replace: 'step01_off.png', with: 'step01_on.png' }, // 필수 조건
            { condition: data.stdyCour > 0, index: 1, replace: 'step02_off.png', with: 'step02_on.png' },
            { condition: data.stdyLec >= 4, index: 2, replace: 'step03_off.png', with: 'step03_on.png' },
            { condition: data.lmsPt > 0, index: 3, replace: 'step04_off.png', with: 'step04_on.png' }
        ];

        // 반복문으로 처리
        conditions.forEach(({ condition, index, replace, with: replaceWith }) => {
            if (condition && imgElements.length > index) {
                imgElements[index].src = imgElements[index].src.replace(replace, replaceWith);
                console.log(`Updated Image ${index + 1}: ${imgElements[index].src}`);
            }
        });
    } else {
        console.log('Condition not met: data.eventAgreeYn != Y or no images found');
    }
}
```

***

#### **최적화 방법 설명**

1. **조건 배열로 통합**:
   * 각 조건(`data.stdyCourseCount`, `data.stdyLectCount`, `data.lmsPstCount`)을 객체 배열로 정의합니다.
   * `condition`: 실행 여부를 결정하는 조건.
   * `index`: 해당 이미지의 인덱스.
   * `replace` 및 `with`: `src`를 변경할 파일명 정의.
2. **반복문으로 처리**:
   * `forEach`를 사용해 배열을 순회하며 각 조건을 평가하고, 조건이 충족되면 이미지를 업데이트합니다.
   * `imgElements.length > index`를 통해 배열 범위 초과를 방지합니다.
3. **필수 조건 별도 처리**:
   * 첫 번째 조건(`data.eventAgreeYn == 'Y'`)은 항상 실행되도록 배열에 `condition: true`로 명시합니다.
4. **로그 통합**:
   * 이미지 업데이트 여부를 출력해 디버깅을 용이하게 했습니다.

***

#### **장점**

1. **중복 코드 제거**:
   * 반복문을 사용해 중복된 코드를 제거하고 유지보수를 용이하게 했습니다.
2. **확장성 향상**:
   * 조건이나 이미지가 추가될 경우 객체 배열에 새로운 항목을 추가하기만 하면 됩니다.
3. **가독성 개선**:
   * 조건별 로직이 배열로 정리되어 가독성이 향상되었습니다.

***

#### **테스트**

이 코드는 원래 코드와 동일한 동작을 수행하면서 간결하고 확장 가능하도록 작성되었습니다. 테스트를 통해 조건별로 올바른 이미지가 업데이트되는지 확인하세요. 추가적으로 새로운 조건을 추가해야 한다면 배열에 항목을 추가하면 됩니다. 😊
