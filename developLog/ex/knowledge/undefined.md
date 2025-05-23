# 트위터를 통한 모르는 지식을 얻어가기

## 1. 소나큐브란?

{% embed url="https://www.sonarsource.com/products/sonarqube/?s_campaign=SQ-APJ-2-All-PMax&s_content=content&s_term=&s_category=Paid&s_source=Paid%20Cross%20Network&s_origin=Google&cq_src=google_ads&cq_cmp=21442152206&cq_con=&cq_term=&cq_med=&cq_plac=&cq_net=x&cq_pos=&cq_plt=gp&gad_source=1&gclid=EAIaIQobChMImYPL7cuUiAMV3Q97Bx2L3xZ7EAAYASAAEgLgOPD_BwE" %}

소나큐브(SonarQube)는 소프트웨어 개발 과정에서 코드 품질을 관리하고 개선하기 위한 오픈 소스 플랫폼입니다. 소나큐브는 소스 코드를 분석하여 코드의 품질과 보안 문제를 찾아내고, 이를 자동으로 리포트해 주는 역할을 합니다. 이 도구는 다양한 프로그래밍 언어를 지원하며, 지속적인 통합(Continuous Integration, CI) 및 지속적인 전달(Continuous Delivery, CD) 파이프라인에 통합될 수 있습니다.

#### 소나큐브의 주요 기능:

1. **정적 코드 분석**:
   * 소나큐브는 정적 코드 분석을 통해 코드 내의 버그, 보안 취약점, 코드 스멜(Code Smell, 나쁜 코드 습관), 복잡성 등을 찾아냅니다. 이 과정에서 코드를 실제로 실행하지 않고 소스 코드를 분석하여 문제를 식별합니다.
2. **다양한 언어 지원**:
   * 소나큐브는 Java, C#, JavaScript, Python, PHP, C/C++, Kotlin 등 수많은 프로그래밍 언어를 지원합니다. 이는 여러 언어로 작성된 프로젝트에서 코드 품질을 통합적으로 관리할 수 있게 해줍니다.
3. **품질 게이트(Quality Gate)**:
   * 소나큐브는 품질 게이트라는 기능을 통해 코드 품질 기준을 설정하고, 그 기준을 충족하지 못하는 경우 빌드를 실패로 처리할 수 있습니다. 이는 코드 품질을 유지하는 데 중요한 역할을 합니다.
4. **CI/CD 통합**:
   * 소나큐브는 Jenkins, GitLab CI, Azure DevOps 등과 같은 CI/CD 도구와 쉽게 통합될 수 있습니다. 이로 인해 코드를 변경할 때마다 자동으로 코드 품질을 검사하고, 문제를 빠르게 발견하여 수정할 수 있습니다.
5. **지속적인 코드 품질 모니터링**:
   * 소나큐브는 프로젝트의 코드 품질을 지속적으로 모니터링하고, 시간에 따른 코드 품질 변화를 시각적으로 보여줍니다. 이를 통해 개발팀은 코드 품질이 어떻게 변화하고 있는지 쉽게 파악할 수 있습니다.
6. **보안 취약점 발견**:
   * 소나큐브는 OWASP Top 10 및 SANS Top 25와 같은 보안 기준을 기반으로 코드 내 보안 취약점을 식별하고 경고해 줍니다. 이는 보안성이 중요한 애플리케이션에서 매우 유용합니다.

#### 소나큐브를 사용하는 이유:

소나큐브를 사용하면 개발팀은 코드 품질을 자동으로 모니터링하고 개선할 수 있습니다. 이는 특히 대규모 프로젝트나 여러 개발자가 협업하는 환경에서 코드 품질과 일관성을 유지하는 데 큰 도움이 됩니다. 또한, 코드의 잠재적인 버그와 보안 취약점을 조기에 발견하여 수정할 수 있어, 배포 후 발생할 수 있는 문제를 미연에 방지할 수 있습니다.

결국 소나큐브는 코드 품질을 지속적으로 관리하고, 프로젝트의 전반적인 안정성과 유지 보수성을 향상시키는 중요한 도구입니다.



## 2. Linux에 rust 도입 상황

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

좀 더 현대적인 주제인 Linux에 Rust 언어가 도입된 것에 대해 토발즈는 더 빨리 채택되지 않는 것에 실망하고 있습니다. “업데이트가 더 빨라질 것으로 기대했지만, 기존 커널 개발자들은 C에 익숙하고 Rust를 모른다는 것이 문제 중 하나입니다. 어떤 면에서는 매우 다른 새로운 언어를 배워야 하는 것에 대해 그다지 달가워하지 않습니다. 그래서 Rust에 대한 반발이 있었습니다.” 또한 토발즈는 “또 다른 이유는 Rust 인프라 자체가 매우 안정적이지 않았기 때문이기도 합니다. ....”라고 덧붙였습니다.

{% embed url="https://linux.slashdot.org/story/24/08/25/0256228/linus-torvalds-talks-about-rust-adoption-and-ai" %}

## 3. 오픈소스 컨트리뷰터

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://www.contribution.ac/" %}

## 4. 타입 스크립트 밈 이해하기

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 이게 뭔 뜻이냐?

이 밈은 Typescript와 프로그래밍 타입 시스템에 관한 아주 독특한 유머입니다. 밈의 문맥과 주어진 코드 블록을 이해하려면, 다음과 같은 내용을 알아야 합니다.

#### 코드 분석:

```typescript
const ϟ = Symbol()
const ξ = Symbol()
const ϗ = Symbol()
const ℵ = Symbol()
```

여기서 `Symbol()`은 자바스크립트와 타입스크립트에서 유일무이한 값을 생성하는 함수입니다. 각 심볼은 다른 심볼과 절대 같을 수 없으므로, 각 심볼은 고유합니다.

#### 밈의 요점:

1. **"TypeScript is duck typed..."**:
   * TypeScript는 덕 타이핑(duck typing)을 지원하는데, 이는 객체의 속성과 메서드가 객체의 타입을 결정하는 방식입니다. "한 오리를 다른 오리와 혼동해서는 안 된다"는 말은 각 심볼이 고유해야 한다는 것을 나타냅니다.
2. **"structurally typed vs nominally typed":**
   * 구조적 타이핑(structural typing)과 명목적 타이핑(nominal typing) 간의 차이를 언급하고 있습니다. 구조적 타이핑에서는 객체의 구조가 중요하지만, 명목적 타이핑에서는 명시적으로 타입이 정의되어야 합니다. Java나 Haskell 같은 언어는 명목적 타이핑을 사용합니다.
3. **"Summon the void itself...":**
   * 이 부분은 심볼을 이용해 공허(즉, 아무것도 없는 것)를 소환한다는 다소 철학적인 표현입니다. 이 코드에서는 심볼을 `Nil` 타입으로 정의하여 공허를 표현하려는 시도를 합니다.

#### 결론:

이 밈은 Typescript와 타입 시스템에 대한 깊은 이해를 요구하며, 이를 철학적이고 유머러스한 방식으로 표현합니다. 이 밈을 이해하려면, 프로그래밍 언어의 타입 시스템에 대한 지식이 필요하며, 특히 심볼(Symbol)과 타입스크립트의 덕 타이핑 개념을 알고 있어야 합니다.

링크된 글을 보면 아마 이 코드가 더 큰 맥락에서 설명될 것입니다. 이 밈은 단순히 코드 그 자체뿐만 아니라, 타입 시스템의 복잡함과 면접에서의 기술적인 질문들에 대한 풍자를 담고 있습니다.



> Typesrcipt로 proof하려고하면 먼저 N-queens를 구현하려는 면접자의 밈을 들고 마중나오는 이가 있다. 난 이 이야기를 무척 좋아한다 풀어서 설명해줘



ChatGPT

이 밈은 Typescript와 같은 프로그래밍 언어의 복잡성과, 기술 면접에서 흔히 접하게 되는 도전적인 문제들을 풍자하는 내용입니다. 이를 풀어서 설명하면 다음과 같습니다.

#### 핵심 내용:

1. **N-Queens 문제**:
   * N-Queens 문제는 매우 유명한 알고리즘 문제 중 하나로, NxN 체스판 위에 N개의 퀸(Queen)을 서로 공격할 수 없게 배치하는 방법을 찾는 문제입니다. 이 문제는 고도의 알고리즘적 사고와 백트래킹 같은 기술이 필요합니다.
2. **TypeScript로 증명(proof)하기**:
   * "TypeScript로 proof하려고 하면"이라는 표현은 TypeScript를 사용해서 어떤 논리적인 증명이나 복잡한 문제 해결을 시도하는 상황을 묘사합니다. TypeScript는 정적 타입 검사기를 가지고 있기 때문에, 논리적인 증명에 사용될 수 있습니다. 하지만 일반적으로 타입 시스템을 이용해 증명을 하는 것은 복잡하고, 때로는 TypeScript의 한계와 마주하게 됩니다.
3. **면접자의 밈**:
   * "N-queens를 구현하려는 면접자의 밈"이라는 표현은 기술 면접에서 면접관이 갑자기 N-Queens 같은 어려운 문제를 들고 나와서 도전하는 상황을 비유적으로 표현한 것입니다. 면접에서는 때로는 기대치가 높거나, 매우 어려운 문제가 나오기도 하는데, 이걸 밈으로 표현한 것입니다.
4. **"난 이 이야기를 무척 좋아한다"**:
   * 이 문장은 밈의 상황이 유머러스하면서도, 현실적으로 공감이 가기 때문에 이 이야기를 좋아한다는 것을 표현한 것입니다. 어려운 문제나 논리적인 증명을 TypeScript 같은 도구로 해결하려고 할 때 느끼는 어려움과 그것을 보는 유머러스한 시각을 표현한 것입니다.

#### 풀어 말하면:

당신이 TypeScript로 어떤 복잡한 문제를 해결하거나 증명하려고 할 때, 마치 기술 면접에서 어려운 문제(N-Queens 문제와 같은)를 맞닥뜨리는 것처럼 느껴진다는 것을 비유적으로 표현한 것입니다. 즉, TypeScript의 타입 시스템을 이용해서 논리적인 증명을 하려는 것은 마치 면접에서 어려운 문제를 풀려고 시도하는 것과 비슷하게 느껴질 수 있다는 유머입니다. 이 상황을 밈으로 만들어 웃음을 주면서도, 프로그래밍의 복잡함을 풍자하고 있습니다.

이런 맥락에서, 이 이야기를 좋아한다는 것은 프로그램밍의 복잡함과 면접의 도전적인 상황에 대한 공감과 유머를 즐기는 것을 의미합니다.

###

## 5. verse란

> verse 첫인상은 이게 왜 하이프인지 모르겠음 발전하는 걸 더 봐야지

비디오 게임 및 게임과 유사한 경험을 염두에 두고 설계된 프로그래밍 언어입니다. 따라서 게임에 특화된 요구 사항을 처리하는 데 더 적합하대



## 6.  Watchman : 파일 감시 서비스

결론 : 편해서라는 댓글이 있었음

### Watchman이란?

Watchman은 파일을 감시하고 변경 시 기록하기 위해 존재합니다. 또한 파일 변경이 일치할 때 작업(예: 자산 재구축)을 트리거할 수도 있습니다. 특정 폴더나 파일을 감시하다가 변화가 생기면, 특정 동작으로 실행하도록 설정하는 역할을 합니다.

### 개념

* Watchman은 하나 이상의 디렉토리 트리(루트라고 함)를 재귀적으로 감시할 수 있습니다.
* Watchman은 심볼릭 링크를 따르지 않습니다. 심볼릭 링크가 존재한다는 것을 알고 있지만, 보고에서 다른 파일과 동일하게 표시됩니다.
* Watchman은 알림이나 명령 실행을 시작하기 전에 뿌리가 안정될 때까지 기다립니다.
* Watchman은 보수적이며, 신중을 기하는 편입니다. 감시를 시작할 때나 감시를 시작할 때의 불확실한 시점에는 파일이 새로 변경된 것으로 간주합니다.
* 마지막으로 확인한 이후의 파일 변경 사항이나 트리의 현재 상태를 루트에서 쿼리할 수 있습니다.
* 루트에서 발생하는 파일 변경 사항을 구독할 수 있습니다.

### 설치

> Watchman: [https://facebook.github.io/watchman/](https://facebook.github.io/watchman/)

* `brew install watchman` 설치를 완료하면, `watchman --version`
* 정상적으로 설치가 되어 있으면 `4.9.0`

## 7. L1 캐시

이미지 속 문구는 "내 방바닥은 웃장의 L1 캐시"라는 표현을 사용하고 있습니다. 이 문구는 캐시의 개념을 재미있게 비유한 유머입니다. L1 캐시는 CPU에서 가장 가까운 메모리 계층으로, 빠르게 접근할 수 있는 저장 공간입니다. 이를 자신의 방바닥에 비유한 것으로, 방바닥을 마치 CPU의 L1 캐시처럼 중요한 물건이나 자주 쓰는 물건을 가까이에 두는 상황을 웃음으로 표현한 것 같습니다.

### L1 캐시란?

**L1 캐시**는 CPU에 있는 가장 빠르고 작은 메모리입니다. CPU는 작업을 수행할 때, 자주 사용되는 데이터를 RAM에서 직접 읽어오는 대신, 속도가 훨씬 빠른 L1 캐시에서 먼저 데이터를 찾습니다. L1 캐시는 CPU 코어마다 독립적으로 존재하며, 접근 속도가 매우 빠르기 때문에 CPU의 성능을 크게 향상시킵니다.

캐시는 보통 계층 구조로 이루어져 있는데, CPU 내부에 있는 가장 빠른 메모리부터 순차적으로 외부의 느린 메모리까지 이어집니다.

### 다른 캐시 종류:

1. **L1 캐시 (Level 1 Cache)**:
   * CPU에서 가장 가까운 캐시.
   * 용량이 매우 작지만(보통 수십 KB), 속도가 가장 빠릅니다.
   * CPU 코어마다 별도로 존재.
2. **L2 캐시 (Level 2 Cache)**:
   * L1 캐시보다 약간 느리지만, 용량은 더 큽니다(보통 수백 KB에서 수 MB).
   * L1 캐시에 없던 데이터는 L2 캐시에서 찾게 됩니다.
   * 일부 CPU에서는 코어마다 L2 캐시가 독립적으로 존재하며, 다른 CPU에서는 공유되기도 합니다.
3. **L3 캐시 (Level 3 Cache)**:
   * L2 캐시보다 더 느리고, 용량은 더 큽니다(보통 수 MB에서 수십 MB).
   * 일반적으로 여러 CPU 코어가 공유하는 캐시입니다.
   * L1, L2 캐시에서 데이터를 찾지 못했을 때, L3 캐시에서 데이터를 찾습니다.
4. **메인 메모리 (RAM)**:
   * 캐시에서 데이터를 찾지 못하면, CPU는 상대적으로 느린 메인 메모리(RAM)에서 데이터를 가져옵니다.
   * RAM은 캐시에 비해 훨씬 느리기 때문에 캐시를 사용하는 것이 CPU의 성능에 매우 중요합니다.
5. **스토리지 (HDD/SSD)**:
   * 캐시와 RAM에서도 데이터를 찾지 못하면, 가장 느린 계층인 하드 디스크나 SSD 같은 스토리지 장치에서 데이터를 가져옵니다.

### 결론:

* **L1 캐시**는 CPU에 가장 가까운, 가장 빠른 메모리입니다. 이 캐시를 방바닥에 비유한 것은 자주 쓰는 물건을 가까이에 두는 상황을 재미있게 표현한 유머입니다.
* 캐시는 CPU 성능을 높이기 위한 필수적인 메커니즘으로, CPU는 작업 효율성을 극대화하기 위해 빠른 캐시 메모리부터 데이터를 탐색한 후, 캐시에 없는 데이터는 느린 메모리 계층에서 찾아오는 방식으로 작동합니다.

## 8. Cursor IDE 툴

> * [CURSOR 홈페이지](https://www.cursor.com/)
> * [모든 기능 완벽 정리 | AI 코드 편집기 이거로 정착하세요](https://www.youtube.com/watch?v=Gt40VneLdX4)

[커서란](https://chucoding.tistory.com/143)  Cursor는 Microsoft의 오픈 소스 Visual Studio Code 에디터를 기반으로 한 포크(fork)이며, 개발자들이 코드를 작성하고 디버깅하는 과정을 보다 효율적으로 만들기 위해 OpenAI의 기능들을 통합하고 있습니다.

AI의 힘을 빌어 개발자는 코드 개선사항을 빠르게 파악하고 적용함으로써, 전반적인 개발 과정의 생산성을 향상시킬 수 있습니다.

또한, Cursor IDE는 VSCode의 확장 기능, 테마, 키바인딩 등을 손쉽게 마이그레이션하여 사용할 수 있도록 지원합니다.

## 9. 한국 사람들은 잘 모르는 거 같은 무료 자기계발 사이트 추천

### 1) [코세라 (Coursera)](https://www.coursera.org/)

코세라는 세계 유명 대학들이 제공하는 강의를 무료로 들을 수 있는 플랫폼임. 인문학, 기술, 비즈니스, 데이터 과학 등 다양한 분야에서 강좌를 제공함. 강의 끝나면 유료로 인증서도 발급받을 수 있음.\
추천 강좌: 예일대학교의 “행복의 과학”, 구글의 “데이터 분석”

### 2) [칸 아카데미 (Khan Academy)](https://www.khanacademy.org/)

칸 아카데미는 수학, 과학, 경제학 등 기본적인 과목들을 무료로 배울 수 있는 비영리 교육 사이트임. 초중고 학생들을 위한 기초 교육부터 시작해서 대학 수준까지 커버됨. 추천 강좌: 수학 단계별 강좌, 경제학 개론

### 3) [에드엑스 (edX)](https://www.edx.org/)

하버드와 MIT가 만든 플랫폼임. 대학 수준의 강의를 무료로 들을 수 있음. 컴퓨터 과학, 인문학, 공학 등 다양한 주제를 다룸. 공식적인 수료증 발급은 유료임.\
추천 강좌: MIT의 “컴퓨터 과학 및 프로그래밍”, 하버드대학교의 “데이터 과학: R 기초”

### 4) [미래학습관 (FutureLearn)](https://www.futurelearn.com/)

영국 오픈 대학교와 BBC가 만든 온라인 학습 플랫폼임. 다양한 주제의 강좌가 무료로 제공되며, 완료 후 인증서도 유료로 발급 가능함. 추천 강좌: “디지털 마케팅 기초”, “인간의 뇌 이해하기”



## 10.[ 뤼튼](https://wrtn.ai/)

새로운  AI



## 11. React 관련

### [tamagui](https://github.com/tamagui/tamagui)

#### React(웹 및 네이티브)의 스타일과 UI가 최적화 컴파일러를 만납니다.

Tamagui를 사용하면 일반적으로 저하되는 두 가지 측면, 즉 성능과 코드 품질을 희생하지 않고도 웹 및 네이티브 앱 간에 더 많은 코드를 공유할 수 있습니다.

이는 플랫폼별 최적화를 출력하는 최적화 컴파일러를 통해 수행됩니다. 복잡한 논리나 교차 모듈 가져오기가 있는 스타일이 적용된 구성 요소를 `div`웹에서는 간단하고 원자적인 CSS로, 네이티브에 스타일 객체를 끌어올린 뷰로 변환합니다.

Tamagui의 모든 기능은 컴파일 시점과 런타임에 작동하며 점진적으로 설정할 수 있으며, 초기 사용은 이를 가져와서 기본 뷰와 스타일이 적용된 함수를 사용하는 것만큼 간단합니다.

스타터를 확인해 보는 것이 좋습니다 `npm create tamagui@latest`. 간단한 학습 예제부터 프로덕션에 바로 적용할 수 있는 모노레포까지 다양합니다.

### react 기반 프레임워크

* vsc가 가장 많고 웹스톰이 두 번째&#x20;

## 12. db 관련

### 인덱스 튜닝

인덱스 타고 안타고는 서버 백대를 추가하는 것과 같은 차이를 보일 수 있습죠. 사실 거의 모든 스케일링의 핵심은 분산처리는 대부분 쿼리 처리

## 🌱 링크 모음

### 1. CS 용어 모음

{% embed url="https://csnote.net/" %}

### 2. 여러분의 CS 교육에서 누락된 학기

{% embed url="https://missing-semester-kr.github.io/" %}

### 3. package.json 설정하는 지를 넘어서 테스트랑 배포 도구 설정 정리 글

{% embed url="https://www.totaltypescript.com/how-to-create-an-npm-package" %}

#### 번들러(Bundle)란?

**번들러**는 여러 개의 소스 파일(자바스크립트, CSS, 이미지 등)을 하나의 파일이나 적은 수의 파일로 묶어주는 도구입니다. 웹 애플리케이션을 만들 때, 다양한 자바스크립트 파일, CSS 파일, 이미지 파일, 그리고 기타 리소스들이 사용되는데, 이를 그대로 서버에 올리고 클라이언트(사용자의 브라우저)로 전송하면 수십, 수백 개의 파일 요청이 발생할 수 있습니다. 이렇게 많은 파일 요청이 생기면 서버 성능에 부담이 되고, 로딩 시간도 길어질 수 있습니다.

**번들러**는 이 문제를 해결하기 위해 개발되었습니다. 번들러는 다양한 파일을 분석하여 의존 관계를 파악하고, 이를 하나의 파일 또는 적은 수의 파일로 묶어줍니다. 그럼으로써 파일 요청 수가 줄어들고, 웹 애플리케이션의 성능이 향상됩니다. 대표적인 번들러로는 **Webpack**, **Parcel**, **Rollup**, **Tsup** 등이 있습니다.

#### 번들러의 주요 기능:

1. **파일 결합**: 여러 파일을 하나의 파일로 결합해 네트워크 요청을 최소화합니다.
2. **의존성 관리**: 모듈 간의 의존성을 파악하여 파일을 올바르게 결합합니다.
3. **최적화**: 코드 압축, 불필요한 코드 제거(Tree Shaking), 이미지 최적화 등으로 파일 크기를 줄이고 성능을 높입니다.
4. **코드 분할**: 필요한 경우 코드 분할(Chunking)을 통해 웹 애플리케이션을 동적으로 로드할 수 있도록 지원합니다.

***

#### 대화 해석과 풀어서 설명:

1.  **XiNiHa의 말**:

    XiNiHa는 `tsup`이라는 빌드 도구가 \*\*모든 아웃풋(출력 파일)\*\*을 하나의 큰 파일로 번들링하는 것에 대해 아쉬움을 표하고 있습니다.

    * **"tsup이 좋긴 하지만 아웃풋을 죄다 번들링 해버린다는 측면에서"**:
      * `tsup`이라는 도구는 빌드 성능이 좋고 간편하지만, 출력되는 결과 파일을 모두 하나의 큰 파일로 묶어버린다는 점에서 불편하다는 의견입니다. 즉, 파일을 개별적으로 분리해서 유지하는 것이 아니라, 모든 것을 하나의 파일로 번들링해버린다는 것입니다.
    * **"mkdist 내지 유사한 트랜스파일 기반 빌드툴"**:
      * `mkdist`는 번들러와는 달리, 각 파일을 개별적으로 트랜스파일(타입스크립트나 ES6 코드를 구버전 자바스크립트로 변환)하여 파일들을 개별적으로 유지할 수 있게 해주는 도구입니다. XiNiHa는 번들링보다는 트랜스파일러 방식으로 파일을 관리하는 것이 더 좋다고 생각하고 있으며, mkdist 같은 도구가 더 발전되길 바라고 있습니다.
    * **"빨랑 궤도에 올라왔음 좋겠다"**:
      * `mkdist` 같은 도구가 좀 더 인기를 얻고, 더욱 발전해서 빠르게 널리 사용되었으면 좋겠다는 뜻입니다. 즉, 번들링보다는 파일을 개별적으로 분리하는 방식이 더 널리 쓰였으면 좋겠다는 의미입니다.
2.  **Basix의 말**:

    Basix는 XiNiHa의 의견에 공감하며, 다음과 같이 말합니다.

    * **"번들러 기반으로 도구를 만들어도 충분히 파일 분리 달성이 가능한데"**:
      * Basix는 사실 번들러도 적절히 설정하면 각 파일을 개별적으로 나눠서 관리할 수 있다고 지적하고 있습니다. 번들링 도구도 잘 사용하면 파일을 분리하는 구조로 설정할 수 있지만, 이를 쉽게 지원하지 않거나, 일부 설정이 불편하다는 아쉬움을 표현한 것입니다.
    * **"좀 아쉬운 일이죠"**:
      * `tsup` 같은 도구가 강력하고 성능이 좋지만, 파일을 더 세부적으로 관리할 수 있는 기능이 부족하거나 불편하다는 점에서 아쉽다는 것입니다.

***

#### 요약:

* **XiNiHa**: `tsup`은 강력하지만, 모든 파일을 하나로 번들링하는 방식이 아쉽다. 대신 `mkdist` 같은 트랜스파일러 도구가 빨리 자리 잡아 더 발전했으면 좋겠다.
* **Basix**: 번들러도 파일을 분리해서 관리할 수 있지만, 그런 기능이 완벽하게 구현되지 않아서 아쉬운 점이 있다는 의견을 제시.

이 대화는 **번들링 도구와 트랜스파일러 방식**의 차이에 대해 토론하고 있으며, **번들링의 장점과 단점**을 지적하며 더 발전된 도구의 필요성을 언급하고 있습니다.

### 4. 오픈소스 링크

{% embed url="https://openapi.tools/#documentation" %}

### 5. 스프링 캠프 2024 영상

{% embed url="https://t.co/Ka7uSa6dEA" %}

### 6. 함수형 코딩 관련 블로그 글

{% embed url="https://medium.com/@bsidekr/%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%BD%94%EB%93%9C%EB%A5%BC-%EC%9E%98-%EC%9E%91%EC%84%B1%ED%95%A0-%EC%88%98-%EC%9E%88%EC%9D%84%EA%B9%8C-503a74a9981a" %}

### 7. ai 기반 책 요약 사이트

{% embed url="https://sobrief.com/" %}

### 8. 프론트 엔드  읽을 거리&#x20;

{% embed url="https://velog.io/@teo/frontend" %}
