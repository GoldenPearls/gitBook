# OAuth 앱에 대한 범위 및 gitbook과 github.io를 자동화를 위한 작업

## Github Action이란

`Github action` 은 CI/CD를 자동화시켜주는 `Github`에서 제공하는 자동화 도구이다.

하나의 변경 사항이 생겼을 경우,\
이를 merge 할 때마다 빌드-테스트의 과정을 거쳐 배포까지 거치는 과정을 `Github action`은 이 중요하지만 귀찮은 과정들을 자동화 시켜줄 수 있다!

> 끌어오기 요청이 열리거나 이슈가 생성되는 것과 같은 ‘이벤트’가 리포지토리에서 발생할 때 트리거되도록 GitHub Actions ‘워크플로’를 구성할 수 있다.

각 작업은 자체 가상 머신 ‘실행기’ 또는 컨테이너 내에서 실행되며, 정의한 스크립트를 실행하거나 **워크플로를 간소화할 수 있는 재사용 가능한 확장인 ‘작업’을 실행하는 ‘단계’를 하나 이상 포함**한다.

\+ 각 워크 플로우는 `.yml` 혹은 `.yaml` 확장자를 가진 파일에 작성된다.

\+ `.github/workflows/` 에 action 파일을 올리면 Github이 자동으로 인식한다.

\+ 레포지토리에i  github action을 누르면 알아서 workflows 폴더가 생성 됨

### 워크플로란?

**워크플로**는 하나 이상의 작업을 실행할 구성 가능한 자동화된 프로세스이다. 워크플로는 리포지토리에 체크 인된 YAML 파일에서 정의되며, 리포지토리의 이벤트로 트리거될 때 실행되거나 수동으로 또는 정의된 일정에 따라 트리거될 수 있다.

워크플로는 리포지토리의 `.github/workflows` 디렉터리에 정의

## 이걸 하는 배경

> 이유.. Gitbook은 핸드폰으로 깨짐... 그리고 댓글 달기 기능이 없어서 소통이 불가함솔   ㅠㅠㅠ 솔직히  blog라기 보다는 notion에 잘된 버전? 카테고리 잘 된 버전 느낌이라서 github.io 블로그에 넣어야 하는데 이거... <mark style="color:red;">하나 하나 글 넣어서 넣기 귀찮아..</mark>

✅ git book의 장단점

1. 장점

* gitbook은 github repo에 동기화가 가능해서 commit이 됨, 잔디심기 가능
* url 커스텀 가능&#x20;
* notion이랑 달리.. 카테고리화 및 workspace 안에서 숨긴 글이나 카테고리 지정하면 외부에서는 안보여서 관리하기 좋음

2. 단점

* 문서 홈페이지라서 댓글 기능 x
* 방문 수 확인을 위해서는 pro로 가야함
* 핸드폰에서 깨짐... 반응형이 아닌지 글머리 기호도 안보여...
* h3는 목차로 쳐주지도 않음

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

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

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

### **1. << Github PAT 발급 >>**

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

### 2. 토큰 설정

> gitbook repo에 push 되거나, 일정 시간마다 github.io에 글을 연결해줄 거임

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

### 3. Gitbook repo, Github.io repo에 PAT 등록 ⭐

`Github action` 파일에 개인 레포로 변경사항을 `push`할 수 있도록 하려면\
위에서 발급 받은 개인 토큰을 함께 보내 내 레포에 `push`할 권한을 받은 사람임을 증명 하기 위함

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

* GITBOOK , Github.io레포의 `⚙️ Settings` 클릭
* `⊞ Secrets and variables`를 펼쳐 `Actions` 클릭
* `New repository secret` 클릭
* &#x20;`Name`에는 `.yml` 파일에서 사용할 이름, `Value`에는 아까 발급받은 PAT 작성
* &#x20;`Add Secret` 클릭

## workflow 작성하기

워크플로우에 작업해야 할 것은 큰 틀은&#x20;

> gitbook에 있는 md 파일을 github.io의 `_posts`에 넣어야 함

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### gitbook 을  가져오기 위한 문제가 있는데

1. gitbook은 한글을 저장하지 못하더라?
2. 그래서 한글로 된 타이틀의 경우, github repo에 저장 시, undefinded로 저장됨

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

3. md 파일 코드 안에는 잘 들어가짐&#x20;

* gitbook이 ##  까지만 목차로 만들어주는데 그 이유가 우리가 gitbook에서 ##로 목차를 하면 ### 로 #로 하면 ##로 <mark style="color:red;"># 을  하나씩 더 붙여지는 꼴임</mark>
* 즉, 제목이 # 한게 h1임 => 이걸 github io에 넘어가기 전 workflow에서 변경해줘야 함 push가 들어올 때 변경을 하던 아니면 일정시간마다 변경을 하던지

### 즉, workflow에서 작업해야 할 것들을 정리해보면

1. gitbook 커밋 시, <mark style="color:red;">파일 이름</mark>을 다 바꿔줘야 함... md파일 안에 있는 <mark style="color:red;">h1</mark>으로
2. gitbook의  github.io로 변경사항을 가져올 건데, md 파일들을 찾아야 함(main 브랜치에 `checkout`)
3. developLog  >  있는 것들 중 아래 두개를 제외하고 전부 가져오고 그 이후는 변경사항이 있을 때만 가져올 거임

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

4. **폴더**를 `카테고리`로 분류 할 거임&#x20;
5. md파일을 수정해줘야 함 둘이 살짝 달라서 gitbook에서 보낼 때 카테고리, 발급날짜, 타이틀 등을 md파일 안에 넣어줘야 함

✅ gitbook의 경우, github.io와 연동이 안되기에 md 파일 안에 있는 상단에 있는 --- ---  영역인 부분을 삭제 시켜줘야함

✅ 그리고 github.io에 맞춰 안에 내용 수정 사진과 같이 해줘야 함

* title : 파일 제목으로 가져옴
* description : 은 gitbook이랑 똑같이 없으면 들어가지 않게
* author : 무조건 mellona
* date : commit한 날짜와 시간으로
* pin : 기본값 fasle
* mermaid : 기본값 true
* image: gitbook cover로 해서 가져옴 기본값은 없음 .gitbook/assets/DALL·E 2024-08-12 14.38.23 - A blog banner designed with a minimalist winter theme, featuring a snow-covered landscape with a trail of footsteps leading through the snow. The desi.webp

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

✅  타이틀도 그것에 맞게 바꿔줘야 함 타이틀 : 년도-월-일 파일제목&#x20;

6. 모든 작업이 끝났다면, github.io \_posts에 `push` 해준다. - checkout  필요

### workspace 자동화를 위한 스크립트

> 📁 참고 : [github action의 워크플로우 구문](https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions#jobsjob\_idneeds)

앞에 1, 2은 gitbook repo에 푸시될 때 사용하기 위해 **Gitbook title rename.yml**으로&#x20;

뒤에 3, 4, 5, 6은 github.io repo에 넘어갈 때 작업으로 **Convert and Deploy DevelopLog to Jekyll.ym**l로 작업해야 겠다.

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

1. Gitbook title rename을 dev 브랜치에 먼저 해주기 위해 **Rename and Commit Markdown Files.yml**을 만들어주고 아래의 스크립트 작성

```
name: Rename and Commit Markdown Files

on:
  push:
    branches:
      - dev  # dev 브랜치에 푸시될 때 워크플로우 트리거
  workflow_dispatch:  # 수동으로 워크플로우를 실행할 수 있는 옵션

jobs:
  process-markdown:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Rename Markdown files
      run: |
        for file in developLog/*.md; do
          title=$(grep -m 1 '^# ' "$file" | sed 's/^# //')
          if [ -n "$title" ]; then
            new_filename="developLog/${title// /_}.md"
            mv "$file" "$new_filename"
          fi
        done

    - name: Commit changes
      run: |
        git config --local user.email "your-email@example.com"
        git config --local user.name "Your Name"
        git add .
        git commit -m "Rename Markdown files based on h1 titles"
        git push origin dev  # 변경사항을 dev 브랜치로 푸시

```

✅ 타이틀도 그것에 맞게 바꿔줘야 함 타이틀 : 년도-월-일 파일제목 모든 작업이 끝났다면, github.io \_posts에 push 해준다. - checkout 필요 workspace 자동화를 위한 스크립트&#x20;

📁 참고 : github action의 워크플로우 구문 앞에 1, 2은 gitbook repo에 푸시될 때 사용하기 위해 Gitbook title rename.yml으로 뒤에 3, 4, 5, 6은 github.io repo에 넘어갈 때 작업으로 Convert and Deploy DevelopLog to Jekyll.yml로 작업해야 겠다.&#x20;

한글 이름으로 바꿔주기 위한 작업.. gpt의 도움을 받았습니다... + 문서 Gitbook title rename을 dev 브랜치에 먼저 해주기 위해 Rename and Commit Markdown Files.yml을 만들어주고 아래의 스크립트 작성 name: Rename and Commit Markdown Files

on: push: branches: - dev # dev 브랜치에 푸시될 때 워크플로우 트리거 workflow\_dispatch: # 수동으로 워크플로우를 실행할 수 있는 옵션

jobs: process-markdown: runs-on: ubuntu-latest

```
steps:
- name: Checkout repository
  uses: actions/checkout@v4

- name: Rename Markdown files
  run: |
    for file in developLog/*.md; do
      title=$(grep -m 1 '^# ' "$file" | sed 's/^# //')
      if [ -n "$title" ]; then
        new_filename="developLog/${title// /_}.md"
        mv "$file" "$new_filename"
      fi
    done

- name: Commit changes
  run: |
    git config --local user.email "your-email@example.com"
    git config --local user.name "Your Name"
    git add .
    git commit -m "Rename Markdown files based on h1 titles"
    git push origin dev  # 변경사항을 dev 브랜치로 푸시
```

🔥 문제 : push가 main 브랜치에 되면서, 스크립트 실행 안됨&#x20;

✅ gitbook push 자체가 무조건 main으로 돼서 그냥 일단, main으로 바꾸기로 함&#x20;

<figure><img src="https://velog.velcdn.com/images/prettylee620/post/12ec3c8e-01c6-4c09-92f7-9f9341c08847/image.png" alt=""><figcaption></figcaption></figure>

🔥 이후의 문제는 파일 이름을 변경하려고 할 때 이미 그렇게 h1의 이름으로 되어 있는 것들도 있다는 것이 이미 존재하는 파일 이름과 동일한 이름으로 변경하려고 할 때 발생&#x20;

✅ 파일 이름이 변경되었는지 확인하고, 이미 동일한 이름이라면 mv 명령을 건너뛰도록 수정할 수 있다. name: Rename and Commit Markdown Files

on: push: branches: - main # main 브랜치에 푸시될 때 워크플로우 트리거 workflow\_dispatch: # 수동으로 워크플로우를 실행할 수 있는 옵션

jobs: process-markdown: runs-on: ubuntu-latest # 최신 Ubuntu 환경에서 실행

```
steps:
- name: Checkout repository
  uses: actions/checkout@v4  # 레포지토리의 소스 코드를 체크아웃

- name: Rename Markdown files
  run: |
    for file in developLog/*.md; do
      # 첫 번째 H1 제목을 추출하여 제목으로 사용
      title=$(grep -m 1 '^# ' "$file" | sed 's/^# //')
      
      if [ -n "$title" ]; then
        # 새 파일명을 제목을 기반으로 생성, 공백은 밑줄(_)로 대체
        new_filename="developLog/${title// /_}.md"
        
        # 파일 이름이 동일한 경우에는 mv 명령을 건너뜁니다
        if [ "$file" != "$new_filename" ]; then
          mv "$file" "$new_filename"
        fi
      fi
    done

- name: Commit changes
  run: |
    # Git 사용자 정보 설정
    git config --local user.email "your-email@example.com"
    git config --local user.name "Your Name"
    # 모든 변경사항을 추가하고 커밋
    git add .
    git commit -m "Rename Markdown files based on h1 titles"
    # 변경사항을 원격 저장소의 main 브랜치로 푸시
    git push origin main
```

하지만, 이 또한 문제가 되는데... 🔥 바뀔 것이 없다고 자꾸 뜸&#x20;

<figure><img src="https://velog.velcdn.com/images/prettylee620/post/37ce1966-3382-4b70-8432-fa019dc4b718/image.png" alt=""><figcaption></figcaption></figure>

✅ add -A로 전체로 해주고 그리고 토큰을 이용해서 github action 봇이 넣게 수정

## 일부 코드

```
  git add -A  # 모든 변경사항 추가
        git config --global user.name 'github-actions[bot]'
        git config --global user.email 'github-actions[bot]@users.noreply.github.com'
        git diff --staged --quiet || git commit -m "Rename Markdown files based on h1 titles"
        git push https://${{ secrets.GH_PAT }}@github.com/GoldenPearls/gitBook.git  # 변경사항을 main 브랜치로 푸시
```

&#x20;🔥 실행은 되는데 파일이 바뀌지 않음..&#x20;

<figure><img src="https://velog.velcdn.com/images/prettylee620/post/697143b0-efd2-4a4d-9105-d5013965a29b/image.png" alt=""><figcaption></figcaption></figure>

✅ 한글 인코딩 작업 수행

* name: Set UTF-8 Encoding run: | export LC\_CTYPE="UTF-8" # UTF-8 인코딩을 명시적으로 설정

🔥 mv 명령어로 파일을 이동하려고 할 때, 새 파일 이름에 포함된 디렉토리가 존재하지 않는 경우 오류가 발생한다고 함&#x20;

<figure><img src="https://velog.velcdn.com/images/prettylee620/post/6b52cc8d-22a6-492b-af8b-db5845f9902e/image.png" alt=""><figcaption></figcaption></figure>

✅ 이 문제를 해결하려면, 이동할 경로에 해당하는 디렉토리가 존재하지 않으면 디렉토리를 생성하는 로직을 추가해야 한다.&#x20;

<figure><img src="https://velog.velcdn.com/images/prettylee620/post/29838c61-a71a-4293-bfca-715613c7fa37/image.png" alt=""><figcaption></figcaption></figure>

![](https://velog.velcdn.com/images/prettylee620/post/037def8c-e24e-4b88-a445-c1721ac892d2/image.png)

해결!!!!! ㅠㅜㅠㅠㅠ 40분의 삽질 끝에 잘바뀌었다.. 흑흑.. 줄 알았으나... 카테고리가 얽히는 현상이 나옴...

```
name: Rename and Commit Markdown Files

on:
  push:
    branches:
      - main  # main 브랜치에 푸시될 때 워크플로우 트리거
  workflow_dispatch:  # 수동으로 워크플로우를 실행할 수 있는 옵션

jobs:
  process-markdown:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4
      with: 
          token: ${{ secrets.GITBOOKKEY }}
          fetch-depth: 0
          ref: main

    - name: Set UTF-8 Encoding
      run: |
        export LC_CTYPE="UTF-8"  # UTF-8 인코딩을 명시적으로 설정

    - name: Rename and Update Markdown files
      run: |
        for file in $(find developLog -type f -name '*.md'); do
          # 파일 내용의 첫 번째 제목 줄을 추출
          title=$(grep -m 1 '^#' "$file" | sed 's/^# //')
          
          if [ -n "$title" ]; then
            # 파일의 디렉토리 구조를 유지하면서 제목을 기반으로 새 파일명 생성
            dir=$(dirname "$file")
            new_filename="$dir/$title.md"
            
            # 대상 디렉토리가 없으면 생성
            new_dir=$(dirname "$new_filename")
            mkdir -p "$new_dir"
            
            # 파일 이름이 동일한 경우에도 강제로 업데이트
            if [ "$file" != "$new_filename" ]; then
              echo "Renaming $file to $new_filename"
              mv "$file" "$new_filename"
            else
              echo "Updating timestamp for $file"
              touch "$file"  # 파일의 타임스탬프를 업데이트
            fi
          else
            echo "No valid title found in $file, skipping."
          fi
        done

    - name: Commit changes
      run: |
        git add -A  # 모든 변경사항 추가
        git config --global user.name 'github-actions[bot]'
        git config --global user.email 'github-actions[bot]@users.noreply.github.com'
        git diff --staged --quiet || git commit -m "Rename and update Markdown files based on h1 titles"
        git push https://${{ secrets.GH_PAT }}@github.com/GoldenPearls/gitBook.git  # 변경사항을 main 브랜치로 푸시

```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## TMI

GITHUB Action이 편하기는 한다 잘 몰라서... 할 때마다 공부해야 할 듯 ㅋㅋㅋㅋㅋㅋㅋㅋ 너무 많기도 하고... 하지만 자동화 최고



> 📁 참고&#x20;
>
> * [Github action으로 Sync Fork 자동화하기 - push 될 때마다](https://velog.io/@charming-l/Github-action%EC%9C%BC%EB%A1%9C-push-%EB%90%A0-%EB%95%8C%EB%A7%88%EB%8B%A4-%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C-%EC%B5%9C%EC%8B%A0%ED%99%94%ED%95%98%EA%B8%B0to-forked-repo)
> * [GitAction에서 다른 레포로 접근하려면?](https://stackoverflow.com/questions/71068476/accessing-another-repository-with-github-cli-in-github-actions)
> * [GitHub Organization 프로젝트를 vercel 무료로 연동하기 (+git actions)](https://velog.io/@rmaomina/organization-vercel-hobby-deploy)
