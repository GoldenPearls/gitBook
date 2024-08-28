# 트위터를 통한 모르는 지식을 얻어가기

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

좀 더 현대적인 주제인 Linux에 Rust 언어가 도입된 것에 대해 토발즈는 더 빨리 채택되지 않는 것에 실망하고 있습니다. “업데이트가 더 빨라질 것으로 기대했지만, 기존 커널 개발자들은 C에 익숙하고 Rust를 모른다는 것이 문제 중 하나입니다. 어떤 면에서는 매우 다른 새로운 언어를 배워야 하는 것에 대해 그다지 달가워하지 않습니다. 그래서 Rust에 대한 반발이 있었습니다.” 또한 토발즈는 “또 다른 이유는 Rust 인프라 자체가 매우 안정적이지 않았기 때문이기도 합니다. ....”라고 덧붙였습니다.

{% embed url="https://linux.slashdot.org/story/24/08/25/0256228/linus-torvalds-talks-about-rust-adoption-and-ai" %}

## 3. 오픈소스 컨트리뷰터

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://www.contribution.ac/" %}

## 4. 타입 스크립트 밈 이해하기

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

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

### 댓글 살펴보기

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

## 5. verse란

> verse 첫인상은 이게 왜 하이프인지 모르겠음 발전하는 걸 더 봐야지

비디오 게임 및 게임과 유사한 경험을 염두에 두고 설계된 프로그래밍 언어입니다. 따라서 게임에 특화된 요구 사항을 처리하는 데 더 적합하대



## 6. 오픈소스 링크

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://openapi.tools/#documentation" %}

## 6. cs 지식 사건 링크

{% embed url="https://csnote.net/" %}

