# item 56 :  공개된 API 요소에는 항상 문서화 주석을 작성하라

> JavaDoc을 잘 활용하면 API의 클래스, 인터페이스, 메서드, 필드를 더 명확하게 설명할 수 있다. 이를 통해 사용자가 헷갈리지 않게 하고, 오류도 줄일 수 있다. 여기서는 JavaDoc 사용법과 주의할 점을 다뤄보자.

## 1. 참고 사이트&#x20;

### 국내 개발자 센터

* [네이버 개발자 센터 - NAVER Developers](https://developers.naver.com/main/)
* [Kakao Developers](https://developers.kakao.com/)
* [LINE Developers](https://developers.line.biz/en/)
* [쿠팡 Open APIs](https://developers.coupangcorp.com/hc/ko)

### 공개 API 명세 예시

* [NAVER Developers > 네이버 로그인 > API 명세](https://developers.naver.com/docs/login/api/api.md#2--api-%EA%B8%B0%EB%B3%B8-%EC%A0%95%EB%B3%B4)



## 2. 주요 JavaDoc 태그와 사용법

JavaDoc에는 여러 가지 태그가 있다. 각 태그는 특정 상황에서 문서화를 돕는 역할을 한다. 자주 쓰이는 태그들을 알아보자.

### **1) 기본 태그들**

* **@param**: 메서드의 매개변수를 설명하는 데 사용하는 태그이다. 매개변수가 어떤 \*\*전제조건(precondition)\*\*에 영향을 받는다면, 꼭 문서화해야 한다.
  * 보통 **명사구**로 작성하며 **마침표는 붙이지 않는다**.
  * 예: `@param index 반환할 원소의 인덱스; 0 이상이고 리스트 크기보다 작아야 한다.`
* **@return**: 메서드의 반환 값을 설명하는 데 사용한다. 반환 값이 없으면 생략할 수 있다.
  * 역시 명사구로 작성하며, 마침표는 붙이지 않는다.
  * 예: `@return 이 리스트에서 지정한 위치의 원소`
* **@throws**: 메서드에서 발생할 수 있는 **예외**를 설명하는 태그이다. 예외가 발생할 수 있는 조건을 명시한다.
  * 예: `@throws IndexOutOfBoundsException 인덱스가 범위를 벗어나면 발생한다.`

### **2) HTML과 JavaDoc 태그**

* **{@literal}**: HTML 메타 문자나 다른 JavaDoc 태그를 문자 그대로 표시할 때 사용하는 태그이다. 예를 들어 `<`, `>` 같은 기호를 텍스트로 표시하고 싶을 때 사용한다.
  * 예: `{@literal <b>}` (HTML 태그가 아닌 일반 텍스트로 표시하고 싶을 때 사용)
* **{@code}**: **코드 폰트로 렌더링**하여 코드처럼 보이게 하는 태그이다. HTML과 다른 JavaDoc 태그를 무시하고 코드처럼 표현한다.
  * 예: `{@code index == 1}`
* **@implSpec**: 해당 메서드와 하위 클래스 간의 계약을 설명하는 태그이다. 하위 클래스가 메서드를 상속할 때의 동작 규약을 명확히 기술한다.
  *   예:

      ```java
      /**
       * 이 컬렉션이 비었다면 true를 반환한다.
       *
       * @implSpec
       * 이 구현은 {@code this.size() == 0}의 결과를 반환한다.
       *
       * @return 이 컬렉션이 비었다면 true
       */
      public boolean isEmpty() { ... }
      ```
* **{@inheritDoc}**: 상위 타입의 문서화 주석을 상속할 때 사용하는 태그이다. 인터페이스의 문서화를 클래스에서도 사용할 수 있게 해준다.
* **@summary**: Java 10부터 추가된 태그로, 문서의 요약 부분을 명확하게 구분하는 역할을 한다.
* **@index**: Java 9부터 추가된 태그로, 문서 내에서 색인 역할을 할 항목을 표시할 때 사용한다.
  * 예: `/** 이 메서드는 {@index IEEE 754} 표준을 준수한다. */`



## 3. API 문서 작성 지침

공개된 클래스, 인터페이스, 메서드, 필드에는 반드시 JavaDoc을 달아야 한다. **직렬화 가능 여부**나 **동기화 여부** 같은 특성도 명시해주는 것이 좋다. 메서드의 **전제조건(precondition)**, **사후조건(postcondition)**, **부작용(side-effect)** 등도 모두 문서화해야 한다. 예를 들어, 메서드 호출로 **백그라운드 스레드**가 실행되는 부작용이 있다면 이를 반드시 기록해두어야 한다.

### _1) Javadoc_?

소스 코드 파일에서 문서화 주석(_doc comment_; 자바독 주석)이라는 특수한 형태로 기술된 설명을 추려 API 문서로 변환해주는 유틸리티

### 2) 핵심 정리

* API 를 올바로 문서화하려면 **공개된** 모든 클래스, 인터페이스, 메서드, 필드 선언에 문서화 주석(`/** (...) */`)을 달아야 한다.
* 문서화 주석은 마침표로 끝나는 **완전한 문장**으로 작성해야 한다.&#x20;
* 문서가 잘 정리되지 않은 API는 사용자에게 혼란을 주기 쉽고, 오류의 원인이 될 가능성이 크다.**직렬화 가능 여부**나 **동기화 여부** 같은 특성도 명시해주는 것이 좋다.&#x20;
* 문서가 잘 갖춰지지 않은 API 는 쓰기 헷갈려서 오류의 원인이 되기 쉽다.
* 기본 생성자에는 문서화 주석을 달 방법이 없으니 공개 클래스는 절대 기본 생성자를 사용하면 안 된다.
* 유지보수까지 고려한다면 대다수의 **공개되지 않은** 클래스, 인터페이스, 생성자, 메서드, 필드에도 문서화 주석을 달아야 한다.
* 메서드의 **전제조건(precondition)**, **사후조건(postcondition)**, **부작용(side-effect)** 등도 모두 문서화해야 한다. 예를 들어, 메서드 호출로 **백그라운드 스레드**가 실행되는 부작용이 있다면 이를 반드시 기록해두어야 한다.

### _3) Javadoc_ 기본 사용법

#### 공통

* 문서화 주석을 사용하는 첫 문장은 항상 마침표로 끝나야 한다.
* 문서화 주석은 _Javadoc_ 에 의해 HTML 로 변환하므로 HTML 태그를 사용할 수 있다.

[![first-sentence-of-javadoc-is-missing-an-ending-period](https://user-images.githubusercontent.com/38161720/222875500-ddbf8bcd-3301-4bf0-af36-7f2a2f46f473.png)](https://user-images.githubusercontent.com/38161720/222875500-ddbf8bcd-3301-4bf0-af36-7f2a2f46f473.png) [![checkstyle--missing-an-ending-period](https://user-images.githubusercontent.com/38161720/222875457-84e2ba3f-69bf-45e3-ab5b-d995096dcc8f.png)](https://user-images.githubusercontent.com/38161720/222875457-84e2ba3f-69bf-45e3-ab5b-d995096dcc8f.png)

### 4) 메서드

> not "how" but "what"

* 메서드용 문서화 주석에는 해당 메서드와 클라이언트 사이의 규약을 명료하게 기술해야 한다.
  * 그 메서드가 어떻게(_how_) 동작하는지가 아니라 무엇을(_what_) 하는지를 기술해야 한다.
* 문서화 주석에는 클라이언트가 해당 메서드를 호출하기 위한 전제조건과 사후조건, 부작용을 모두 나열해야 한다.
* 관례상 `@param` 태그와 `@return` 태그 설명은 해당 매개변수가 뜻하는 값이나 반환 값을 설명하는 명사구를 사용한다.
* 관례상 `@param`, `@return`, `@throws` 태그의 설명에는 마침표를 붙이지 않는다.
  * 하지만, 한글로 작성할 경우 온전한 종결 어미로 끝나면 마침표를 써주는 게 일관된 모습이다.

#### 전제조건(precondition)

#### 사후조건(postcondition)

### 5) 주의할 점

문서화 주석의 첫 번째 문장은 요약 설명으로 간주된다. 대상의 기능을 명확하게 설명해야 한다. 요약 설명은 완전한 문장이 아니어도 된다. 예를 들어, 영어로 작성할 경우 **3인칭 동사**로 시작하는 불완전한 문장이 올 수 있다. 같은 클래스나 인터페이스 내에서 요약 설명이 중복되면 안 된다. 문서화 주석에 HTML과 JavaDoc 태그를 활용해 가독성을 높이는 것도 좋은 방법이다.

### **6) 제네릭, 열거, 애너테이션 문서화**

* **제네릭**: 모든 **제네릭 타입 매개변수**에 주석을 다는 것이 좋다.
* **열거(enum)**: 열거 타입을 문서화할 때는 **각 상수에도 주석**을 다는 것이 좋다.
* **애너테이션(annotation)**: **애너테이션의 모든 멤버**에도 주석을 작성하는 것이 권장된다.

## 4. JavaDoc 사용 예시

> 아래 예시는 위에서 소개한 문서화 주석 규칙을 모두 적용한 예시로, `List` 인터페이스의 `get(..)` 메서드이다.

*   **`List` 인터페이스의 `get()` 메서드 예시** (영문) _java.util.List.java_

    ```java
    public interface List<E> extends Collection<E> {
        /**
         * Returns the element at the specified position in this list.
         *
         * @param index index of the element to return
         * @return the element at the specified position in this list
         * @throws IndexOutOfBoundsException if the index is out of range
         *         ({@code index < 0 || index >= size()})
         */
        E get(int index);
    }
    ```
*   **`List` 인터페이스의 `get()` 메서드 예시** (한글)

    ```java
    public interface List<E> extends Collection<E> {
        /**
         * 이 리스트에서 지정한 위치의 원소를 반환한다.
         * <p>
         * 이 메서드는 상수 시간에 수행됨을 보장하지 <i>않는다</i>. 
         * 구현에 따라 원소의 위치에 비례해 시간이 걸릴 수도 있다.
         *
         * @param index 반환할 원소의 인덱스; 0 이상이고 리스트 크기보다 작아야 한다.
         * @return 이 리스트에서 지정한 위치의 원소
         * @throws IndexOutOfBoundsException 인덱스가 범위를 벗어나면, 즉, 
         *         ({@code index < 0 || index >= size()}) 이면 발생한다.
         */
        E get(int index);
    }
    ```

## 5. 복잡한 API의 주석 작성 팁

설명이 너무 복잡한 경우, **관련 링크**를 주석에 추가해서 더 자세한 설명으로 연결하는 것도 좋은 방법이다.

## 6. 핵심 정리

공개 API라면 일관성 있는 규칙으로 문서화하는 것이 중요하다. HTML 태그와 JavaDoc 태그를 잘 활용해서 사용자에게 친절한 문서를 작성해야 한다. 유지보수를 고려해 공개되지 않은 클래스, 인터페이스, 메서드, 필드에도 문서화를 추가하는 것이 좋다. JavaDoc은 단순한 주석이 아니라 **API 사용 방법과 주의사항을 명확하게 전달**하는 중요한 수단이다.

JavaDoc을 잘 사용하면 **API 사용성**과 **코드 유지보수성**이 크게 개선되고, 협업에서도 다른 개발자들과의 소통이 훨씬 원활해질 수 있다.



> 참고 및 출처
>
> * [https://github.com/orgs/Study-2-Effective-Java/discussions/140](https://github.com/orgs/Study-2-Effective-Java/discussions/140)
