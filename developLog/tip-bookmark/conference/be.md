---
description: 점핏 강연에 대한 정리
---

# 점핏 강연 센스있는 BE 되기

## 강의 듣게 된 계기와 글 작성하기

> 별 대단한 계기는 아니고 점핏 카카오톡으로 강연을 한다해서 듣게 되었다. 일단 1부, 2부 나눠졌는데 2부는 아르바이트로 제대로 듣지 못하여 듣고 이번주 중으로 올릴 예정이다!

> 다른 잘 정리 된 글 : [점핏 후기](https://velog.io/@balparang/%EC%A0%90%ED%95%8F-%EB%B0%B1%EC%97%94%EB%93%9C-%EA%B0%9C%EC%B7%A8%EC%BD%98-%ED%9B%84%EA%B8%B0)

## 1. 중요한 건 인터페이스야

### 🌻 점핏 소개 사항

![](https://velog.velcdn.com/images/prettylee620/post/6eda197d-9aea-42b0-ae03-fdc57dc6e4bf/image.png)

#### 손진규 강연자님 소개

* 현재 B2B SaaS reflow를 만들고 있습니다. 강남언니 개발 챕터 리드를 하였고, 이후 스타트업을 대상으로 컨설팅을 하였습니다. Seoul Elixir Meetup 운영진을 맡고 있습니다.

#### 강연자님 추천도서

* 클린애자일

#### 강연자님 블로그

https://json.media/

### 🌻 보다라는 사이드 프로젝트를 만들었다.

* 손진규님은 서버 개발자로 숙면을 도와주는 컨텐트를 제공해주는 앱을 만들고자 했고 api를 먼저 만들었다고 한다.
* 이후 클라이언트 개발자는 **그렇게 만들면 제가 쓰기 어려운데요?**

#### 그렇다면 뭐가 문제였을까?

1. 클라이언트 개발자에게 API를 어떻게 만들어주면 좋을지 `물어보지 않았다.`
2. 다 만들고서 코드를 변경하려면 `추가 비용이 발생한다.`
3. API 개발하는 동안 클라이언트 개발자는 `기다려야 했다.`

### 🌻 Interface란?

* 컴퓨팅에서 인터페이스는 공유 경계입니다. 컴퓨터를 구성하는 두 개 이상의 개별 구성요소
* 시스템 교환 정보

### 🌻 API란?

* Application Process `Interface`

#### API 설계

* 서버 개발 하기 전에 클라이언트 개발자와 `같이` API를 설계한다.
* 변경해야 할 가능성이 `낮아진다.`
* 각자 `병렬적`으로 작업하고 나중에 맞춰볼 수 있다.

### 🌻 그렇다면 이제 해결이 되었을까?

#### 디자이너와의 문제

* 만들기 전에 무엇을 만들 것인지 명확하게 확인하지 않았다
* 무엇을 만들 것인지 정하는데 누군가 참여하지 않았다
* 변경하는데 비용이 많이 발생

#### 함께 제품 기획하기

* 모든 구성원이 자신의 전문성을 활용해 함께 기획한다
* 모든 구성원의 제품에 대한 이해도를 높인다
* 변경해야 할 가능성이 낮아진다

### 🌻 그렇다면 이제 모든 해결이 되었을까?

* 고객이 원하는 제품인지 미리 확인하지 않았다
* 고객의 생각은 내 생각과 다를 가능성이 매우

#### 고객부터 확인하자

#### 프리토타이핑

* 프로토타입보다 더 작은 제품을 만들지도 않고 고객의 반응을 봄
* 고객의 반응은 내 기준으로 생각하면 안됨
* 고객의 반응은 카톡으로도 볼 수 있음 ⇒ 이를 통해 개인의 역량 부족도 알 수 있음

#### 문서화

1. 문서화가 사람들과 개발 협업에 도움은 주나, 문서화는 맥락이 빠지게 돼서 다시 개발하는 일을 있을 수 있음
   * 그 이유는 사람들의 대부분은 자기의 100중 70만 글에 적을 수 있다
   * 또한 그 글을 100을 다 이해하는 사람들도 많지 않다

#### 매우 많은 비용 ⇒ 불안한 부분부터 건들이기

* 고칠 떄 비용이 많은 드는 것 부터 약속하자 ⇒ Interface
* 반드시는 아니다 Case by case
* 고칠 때 비용이 많이 드는 것부터 약속하기 = 불확실성이 높은 것부터 약속하기 = 미루고 싶은 것부터 약속하기
* 클린애자일

### 🌻 질문의 답변

1. 사이드 프로젝트 포기시 아깝지 않았는지?

* A: 수 많은 내가 시간을 투자해서 할 수 있는 것 중 하나를 고르는 것이니 다른 대안도 있기에 하나만에 집중할 필요는 없다.

2. 협업 툴 노션 대체제 노션은 너무 느림

* A : **`피그마의 피그잼`, 일들을 나열하고 연결관계를 한 눈에 보고, 큰 그림을 보기에 좋다** ⇒ 디테일 노션, 리니어

3. 다른 직군들과의 소통하기 위해서는 특히 기획자와 개발자의 경우 기획자는 개발자의 단어를 이해를 못하는 경우가 많다 그럼 어떻게 해야 하는가?

* 소통의 원할함 ⇒ 기획자와 개발자, 기획인 경우 개발을 잘 모르면… ⇒ 단기적으로 해결하기 어려움 ⇒ 다른 직군을 이해하기까지는 최소 1년, 좋은 PO들은 개발언어에 대해 관심을 갖고 공부함

### 🌻 최종 정리글\[유투브 라이브 댓글 ]

* 하나의 제품을 만드는 같은 팀이라는 관점에서 각자의 전문 역할(개발/기획 등)에서만 알 수 있는 **정보를 서로 모두 공유**하고, 현재의 우리 제품에게 **무엇이 최선인지 같이 고민**하고 **같이 결정하고 책임지는 문화가 중요**하다

## 2. 토스뱅크 기업문화와 성장하는 주니어 BE 특징

### 📏 강연자님 소개

![](https://velog.velcdn.com/images/prettylee620/post/e79f4e35-2691-425d-9114-6529ea8ce6f7/image.png)

### 소개

2004년 네이버에서 유저 분석 시스템과 홈페이지, 부동산 서비스 개발을 담당했어요. 그 뒤로 스타트업에 합류하여 매드스마트, 파이낸시스, 열두시, 플레이독소프트를 거쳤고 2017년 12월 비바리퍼블리카(토스)에 합류했어요. 토스뱅크 출범 이후 CTO·테크놀로지 헤드를 맡고 있어요.

#### 강연자님 추천도서

* 클린소프트웨어
* 이유 : 인터페이스가 **어떤 이유로 나왔는지 사례 위주**로 잘 나와있음

#### 강연자님 인터뷰

https://it.chosun.com/site/data/html\_dir/2023/02/01/2023020101890.html

> 사진 출처 : 토스 뱅크 강연자님 자료 발표

### 📏 프롤로그

1. 장애도 많이 일으켜봤다
2. 큰 보부를 가지고 계심 ⇒ 모든 사람이 더 편리해서 쓸 수 있게 더 노력하고 싶다

### 📏 성장에 필요한 영감

![](https://velog.velcdn.com/images/prettylee620/post/37fb4d4d-a2cd-4e47-8e26-266024c7a35b/image.png)

1. 하나하나 깊게 파 봐야 함
2. 하나하나가 유저한테 응답이 감
3. 모든 것에 영향이 감

#### 팀의 세분화 시 문제

![](https://velog.velcdn.com/images/prettylee620/post/02c7125c-a88c-4bbe-8c4a-620734e3b92c/image.png)

1. DB 팀의 벽…
2. DB만 건들면 더 나아질 것 같은데라는 생각을 가져도 세분화가 되어 있으면 각 팀의 업무 범위로 인해 건들 수 없게 됨..

#### 팀의 업무, 팀의 목표

* 팀 간의 목표와 팀의 업무의 범위가 생김

#### 업무 범위의 제약

* DB팀 이 부분의 설정을 바꿔주세요 ⇒ 그거 설정을 바꾸면 다른 문제가 생길 수 있는데 그렇게 되면 **우리의 목표에 어긋나서 안돼요**
* 이유 : 팀의 목표가 따로 있으며 **회사가 업무, 팀 별로 따로 본다면 팀별로의 인센티브와도 연관**이 되기 때문에 제약이 생겨버림

### 📏 내 성장에 도움이 되는 방향은?

1. 회사 안의 분위기 때문에 바꿀 수 없는 것들이라고 하는 경우
   1. `이유`에 대해 들어봐야 함
   2. 자신이 해결 할 수 있는 문제라면 내가 해보겠다 하자
   3. 만약, 자신의 인센티브때문에 하는 거다하면 오케이나 도움은 없을 수 있기에 **유저한테 좋은 응답을 나가기 위해 공부하는 것이다라는 것을 어필**해야 함

### 📏 문제 해결 재발 방지 대책

#### 하지만 그래도 장애 발생

1. **우리는 장애 안 발생기는 게 회사의 첫번째 목표입니다**를 하면 내가 성장하기 위해 `제약`이 더더욱 생김
2. 품질 책임 조직 : 장애를 위해 **QA 팀**을 만드는 경우도 있음

* 버튼의 위치 ⇒ 디자인적 요소 ⇒ 이것도 QA다 이런식으로 나와 버리면 QA의 권한이 점점 넓어져버림

#### 안정적인 고객 서비스 주고 접점을 찾아내고 하는 것이 중요함

#### 출시 일정 & 테스트 가능한 상황

1. 출시 일정 촉박한데 테스트 환경을 출시 2주전까지 만들어달라고 하면, 개발자는 더욱 촉박해짐
2. **테스트 가능한 환경을 만드는 게 목표가 됨** ⇒ 기능이 다 개별되지 않았는데 테스트 환경만
3. 겉보기에 돌아가는 로직만 짜게 됨
4. QA 팀에 빠르게 넘기기 위해서
   1. 점점 더 상황이 안좋아짐
5. 품질 안정화 품질 책임
   1. 개발 완성도 하락
   2. 개발자 책임감, 동기부여를 떨어뜨림
6. 설득하고 바꿔가기 위해 노력해야 함

### 📏 조직 전체의 목표 Align

1. 다 깊게 파해쳐서 더 좋은 서비스를 전달하고 문제를 해결해 내가기 위한 것임
2. 문제가 생긴 분야쪽 팀에게 잘 모르겠는데 이런 문제가 있는데 확인해주실 수있나요? 하고 내 **역량도 그 팀이랑 소통을 위해 공부**해야 함
   1. 예시로 DB면 DB쪽을 공부하고
   2. 데브옵스면 데브옵스쪽 공부

#### 조직 OKR 인센티브

* 사람은 인센티브에 맞춰줘버림

### 📏 나에게 성장하기 위한 회사 조직 환경이 어디인지를 찾아야함

* 이미 소속되어 있다면 바꿔기 위해 그런 문화를 만들기 위해 노력해야 할 것

⇒ 다 바꾸기 힘들더라도 하나씩 해결하기 위해 노력

* 단기 인센티브에는 안맞을 수 있지만, 내 역량과 장기적인 임팩트의 능력은 커짐

#### 개인에게 맞는 환경

* **나에게 맞는 회사 환경이 어떤지**를 생각해서 종합해서 찾아야 함

#### 어떤 간헐적인 문제

* 시스템을 바꾸거나 했을 때 해소될 수 있으나 그대로 넘어가지말고 엔지니어로서 성장하기 위해 끝까지 파봐야 한다

#### 오픈소스 환경

* 문제가 생겼을 때 어디까지 팔 수 있는 지가 중요함 문제들은 자신이 잘못 쓴것들이 대부분
* 발견하고 해결하기 위한 것 중요함

### 📏 결론\[유투브 댓글 좀 참고]

1. 원하는 상태가 현상이 아닐 떄, 상대방을 설득하거나 내 의견을 관철시키기 위해 기저 원인을 파악하기
2. 상대방이 내가 원하는 행동하지 않는 이유나 사유 해소하는것
3. 그 과정에서 내가 좀 손해봐도 주도해서 실행해보는 것이 성장의 포인트
4. 회사에 속해있지 않을 시 내가 성장할 수 있는 환경을 찾는 것 또한 중요하다.

### 📏 질문에 대한 답변

**1. 스페셜리스트와 제널리리스트 중 어느것을 먼저 선호하는지**

* 내가 힘들더라도 다양한 것을 해봐야 함
* 처음에는 10정도의 힘을 내서 다른 것을 만져보고 뒤에 가서는 1정도로만 공부하면 된다.
* 즉, 너무 이분법적으로 생각할 필요 없이 다방면으로 스페셜리스트가 되겠다는 각오로(=끝까지 파보고) 하되, 넓은 시야를 유지하면서 일을 해야한다.(=굳이 선택한다면 제너럴리스트)

**2. 개발환경에 대한 질문?**

* 확장과 트래픽을 받을 구조를 만들어둬야 한다.
* MSA 도입하고 전환 ⇒ 서비스가 작아도 커질거 대비 멀티모듈 방식으로 만드는 것도 좋음 ⇒ 개발 생산성이 높은 방식으로 결정하면 됨

\*\* 3. 백엔드 개발자로 들어왔는데 회사에서 데브옵스만 하는데 서버 개발을 하고 싶다.\*\*

* A: **만든 소프트웨어가 올라가는 환경**에 대해 공부할 것
  * 쿠버네티스에 올라가면 쿠버네티스를
  * AWS, EK 환경이면 AWS, EK 환경을 공부
  * 앞으로 내려오는 네트워크의 흐름
  * 이것들은 알아야 어디가 문제인지 파볼 수 있고, 데브옵스 엔진니어에게 조언도 가능
  * 내가 만든 서비스가 어디에 올라가서 어떻게 응답을 받고 트래픽을 주는지는 알아야
