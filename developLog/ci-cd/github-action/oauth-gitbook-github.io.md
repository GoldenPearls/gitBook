# OAuth 앱에 대한 범위 및 gitbook과 github.io를 자동화를 위한 작업

## Github Action이란

`Github action` 은 CI/CD를 자동화시켜주는 `Github`에서 제공하는 자동화 도구이다.

하나의 변경 사항이 생겼을 경우,\
이를 merge 할 때마다 빌드-테스트의 과정을 거쳐 배포까지 거치는 과정을 `Github action`은 이 중요하지만 귀찮은 과정들을 자동화 시켜줄 수 있다!

\+ 각 워크 플로우는 `.yml` 혹은 `.yaml` 확장자를 가진 파일에 작성된다.

\+ `.github/workflows/` 에 action 파일을 올리면 Github이 자동으로 인식한다.

\+ 레포지토리에i  github action을 누르면 알아서 workflows 폴더가 생성 됨



## 이걸 하는 배경

> 이유.. Gitbook은 핸드폰으로 깨짐... 그리고 댓글 달기 기능이 없어서 소통이 불가함솔   ㅠㅠㅠ 솔직히  blog라기 보다는 notion에 잘된 버전? 카테고리 잘 된 버전 느낌이라서 github.io 블로그에 넣어야 하는데 이거... <mark style="color:red;">하나 하나 글 넣어서 넣기 귀찮아..</mark>

✅ git book의 장단점

1. 장점

* gitbook은 github repo에 동기화가 가능해서 commit이 됨, 잔디심기 가능
* url 커스텀 가능&#x20;

2. 단점

* 문서 홈페이지라서 댓글 기능 x
* 방문 수 확인을 위해서는 pro로 가야함
* 핸드폰에서 깨짐... 반응형이 아닌지 글머리 기호도 안보여...

> 결론 자동화해서 github.io 블로그로 연동하기 위![](<../../.gitbook/assets/image (33).png>)

## Github Action 설정 시, Access Token의 액세스 제한 범위

### Github PAT 발급

> 나임을 증명하여 개인 레포로 푸쉬할 수 있는 권한을 가진 **개인 토큰 (PAT, Personal Access Token)**을 발급 받아야 한다.

**<< Github PAT 발급 >>**

* &#x20;Github에서 로그인 후 우측 상단의 프로필 아이콘 클릭
* `⚙️ Settings` 클릭
* 좌측 메뉴 최하단에 `<> Developer Settings` 클릭
* `Personal access tokens` 펼쳐서 `Tokens (classic)` 클릭
* `Generate new token (classic)` 클릭

✅ 토큰 생성시 고려할 각 항목

* `Note`는 본인이 토큰에 부여할 이름,
* `Expiration`은 토큰 만료 기한,
* `Select scopes`는 해당 토큰에 줄 권한

### 사용 가능한 범위

> 자주 쓰이는 것들만 알아보자

1. (no scope)

* 공개 정보에 대한 <mark style="color:red;">읽기 전용 액세스 권한</mark>을 부여합니다(사용자 프로필 정보, 리포지토리 정보 및 gist 포함)

2. <mark style="color:red;">repo</mark> ⭐&#x20;

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

* 코드, 커밋 상태, 리포지토리 초대, 협력자, 배포 상태 및 리포지토리 웹후크에 대한 읽기 및 쓰기 권한을 포함하여 퍼블릭 및 프라이빗 리포지토리에 대한 모든 권한을 부여합니다.&#x20;
* **참고**: 리포지토리 관련 리소스 외에도 `repo` 범위는 <mark style="color:red;">프로젝트, 초대, 팀 멤버 자격 및 웹후크를 포함하여 조직 소유 리소스를 관리할 수 있는 액세스 권한을 부여</mark>합니다. 이 범위는 사용자가 소유한 프로젝트를 관리하는 기능도 부여합니다.

3. repo : status

* 퍼블릭 및 프라이빗 리포지토리의 커밋 상태에 대한 읽기/쓰기 권한을 부여합니다.&#x20;
* 이 범위는 <mark style="color:red;">코드에 대한 액세스 권한을 부여하지</mark> <mark style="color:red;"></mark>_<mark style="color:red;">않고</mark>_ <mark style="color:red;"></mark><mark style="color:red;">다른 사용자 또는 서비스에 프라이빗 리포지토리 커밋 상태에 대한 액세스 권한을 부여하는 데만 필요</mark>합니다.

4. repo\_deployment

* 퍼블릭 및 프라이빗 리포지토리의 [배포 상태](https://docs.github.com/ko/rest/repos#deployments)에 대한 액세스 권한을 부여합니다.
* 이 범위는 코드에 대한 액세스 권한을 부여하지 _않고_ 다른 사용자 또는 서비스에 배포 상태에 대한 액세스 권한을 부여하는 데만 필요합니다.

5. public\_repo

* 퍼블릭 리포지토리에 대한 액세스 제한 여기에는 퍼블릭 리포지토리 및 조직의 코드, 커밋 상태, 리포지토리 프로젝트, 협력자 및 배포 상태에 대한 읽기/쓰기 액세스가 포함됩니다.&#x20;
* 퍼블릭 리포지토리에 별표를 표시하는 경우에도 필요합니다.

6. repo:invite

* 리포지토리에서 협업하는 초대의 수락/거절 기능을 부여합니다. 이 범위는 코드에 대한 액세스 권한을 부여하지 _않고_ <mark style="color:red;">다른 사용자 또는 서비스에 초대에 대한 액세스 권한을 부여하는 데만 필요</mark>합니다.

7. security\_events

* [code scanning API](https://docs.github.com/ko/rest/code-scanning)의 보안 이벤트에 대한 읽기 및 쓰기 액세스를 부여합니다.
* 이 범위는 코드에 대한 액세스 권한을 부여하지 _않고_ 다른 사용자 또는 서비스에 <mark style="color:red;">보안 이벤트에 대한 액세스 권한을 부여하는 데만 필요</mark>합니다.

8. workflow

* GitHub Actions <mark style="color:red;">워크플로 파일을 추가하고 업데이트하는 기능을 부여</mark>합니다.&#x20;
* 경로와 내용이 모두 동일한 파일이 동일한 리포지토리의 다른 분기에 있는 경우 이 범위 없이 워크플로 파일을 커밋할 수 있습니다. 워크플로 파일은 다른 범위 집합을 포함할 수 있는 `GITHUB_TOKEN`을 노출할 수 있습니다.&#x20;
* 자세한 내용은 "[자동 토큰 인증](https://docs.github.com/ko/actions/security-guides/automatic-token-authentication#permissions-for-the-github\_token)"을(를) 참조하세요.

## repo끼리 연결하기 위한 토큰 발급

### **<< Github PAT 발급 >>**

> 이 것을 Gitgook이 Github.io로 연결해주기 위한 토근

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

* Github에서 로그인 후 우측 상단의 프로필 아이콘 클릭
* `⚙️ Settings` 클릭
* 좌측 메뉴 최하단에 `<> Developer Settings` 클릭
* `Personal access tokens` 펼쳐서 `Tokens (classic)` 클릭
* `Generate new token (classic)` 클릭
* &#x20;Note와 Expiration은 알아서 작성하고,
* &#x20;`Select scopes` 중에 `repo`와 `workflow` 선택   ⭐️중요⭐️
* `Generate Token`

### 🤔 write:package는 안필요할까??

`write:packages` 권한은 GitHub의 패키지 레지스트리(GitHub Packages)에 패키지를 푸시하거나 업데이트할 때 필요합니다. 만약 당신의 GitHub Actions 워크플로우가 GitHub Packages에 패키지를 게시하거나 관리하는 작업을 포함하고 있다면, `write:packages` 권한이 필요합니다.

* **`write:packages` 권한**: 이 권한은 **GitHub Packages**와 관련된 작업에 필요합니다. 예를 들어, Docker 이미지, npm 패키지, Maven 아티팩트 등을 GitHub Packages에 푸시할 때 사용됩니다.
* **사용 사례**: 만약 워크플로우가 단순히 코드 리포지토리 간에 커밋을 푸시하거나 업데이트하는 작업만 수행하고, 패키지 관리와 관련된 작업이 없다면, `write:packages` 권한은 필요하지 않습니다.

1. 예시 상황

* **필요하지 않은 경우**:
  * 다른 리포지토리로 코드를 푸시하거나 업데이트만 할 때.
  * GitHub Packages와 관련이 없는 작업을 할 때.
* **필요한 경우**:
  * GitHub Packages에 패키지를 업로드하거나 업데이트할 때.
  * GitHub Actions에서 Docker 이미지를 빌드하고 이를 GitHub Packages에 푸시할 때.

2. 권한 설정:

* 만약 패키지 작업을 하지 않는다면, `repo`, `workflow`, `public_repo` 등의 기본적인 권한만으로 충분합니다.

## 토큰 설정

> gitbook repo에 push 되거나, 일정 시간마다 github.io에 글을 연결해줄 거임

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

### Gitbook repo에 PAT 등록

* GITBOOK 레포의 `⚙️ Settings` 클릭
* `⊞ Secrets and variables`를 펼쳐 `Actions` 클릭
* `New repository secret` 클릭
* &#x20;`Name`에는 `.yml` 파일에서 사용할 이름, `Value`에는 아까 발급받은 PAT 작성
* &#x20;`Add Secret` 클릭



> 📁 참고&#x20;
>
> * [Github action으로 Sync Fork 자동화하기 - push 될 때마다](https://velog.io/@charming-l/Github-action%EC%9C%BC%EB%A1%9C-push-%EB%90%A0-%EB%95%8C%EB%A7%88%EB%8B%A4-%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C-%EC%B5%9C%EC%8B%A0%ED%99%94%ED%95%98%EA%B8%B0to-forked-repo)
> * [GitAction에서 다른 레포로 접근하려면?](https://stackoverflow.com/questions/71068476/accessing-another-repository-with-github-cli-in-github-actions)

