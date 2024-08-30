---
description: github.io로 블로그 만들기 1탄
---

# Jekyll Chirpy 테마를 연동 및 Github 블로그 만들기  1탄 - 로컬 테스트 및 확인

## github.io blog 만들기

### 1) TMI

에서 블로그를 운영하고 있고, 팔로우도 100명을 넘었다.

![](https://velog.velcdn.com/images/prettylee620/post/3f7c738d-963b-4526-aa53-317ec20774c4/image.png)

하지만.. 옮기지는 않고 두 곳을 운영할까 고민중... **Velog에 단점이 내가 쓴 글 한 눈에 안보여 ㅠㅠㅠㅠ**

Github Page를 통한 블로깅을 시도했으나 까다로운 설치와 커스터마이징이,어렵다기에 테마 선택 시 사용자 수가 많고 계속 update 되고있으며, 내 마음에 들고 사용자가많은 테마인 [Chipy](https://github.com/cotes2020/jekyll-theme-chirpy)를 선택!!

### 2) Local에서 테스트 하기 전의 세팅

> [Jekyll-Chirpy-테마 글쓰기, 커스마이징 등](https://www.irgroup.org/posts/jekyll-chirpy/) [Jekyll-Chirpy-테마를-활용한-Github-블로그-만들기(2023-6월-기준)](https://jjikin.com/posts/Jekyll-Chirpy-%ED%85%8C%EB%A7%88%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%9C-Github-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0\(2023-6%EC%9B%94-%EA%B8%B0%EC%A4%80\))

1. 리포지토리 `fork` 해와서 생성하기

* 꼭 리포지토리명을 `github 유저이름.github.io`로 해야 한다고 다!!

![](https://velog.velcdn.com/images/prettylee620/post/ba804212-34fe-4099-8e41-6a6823df1e0b/image.png)

2. branch를 master에서 main으로 변경하고 Branch protection rule도 기본값 (체크 x)으로 설정

![](https://velog.velcdn.com/images/prettylee620/post/b07b61da-641f-4346-b9cf-bbd6495f57e3/image.png)

![](https://velog.velcdn.com/images/prettylee620/post/e792ddd2-8c3f-4a6e-b0eb-a09f62d7570c/image.png) ![](https://velog.velcdn.com/images/prettylee620/post/8766155f-73e3-4313-ab0e-76ae5dae7be3/image.png)

3. git clone 해오기

* 넣어둘 폴더로 이동 해서 clone 해오기

![](https://velog.velcdn.com/images/prettylee620/post/0ca2568f-abc7-4fb6-b3a6-09e06824052b/image.png)

```jsx
cd blog
git clone https://github.com/GoldenPearls/GoldenPerals.github.io.git
```

## Local에서 세팅

> 참고 : [별준 : 루비 설치 하기](https://junstar92.tistory.com/5)

1. CMD로 Ruby 설치 하기

> 꼭 3. 이상 설치 할 것 !

2. Ruby를 설치했다면 버전을 검색시 나올 것임

```jsx
cd GoldenPerals.github.io
ruby -v
```

![](https://velog.velcdn.com/images/prettylee620/post/6dcefcd1-9818-4d14-bd6b-ab3054d3dbbd/image.png)

이렇게만 설치하고 서버 실행하려고 하면 이런 에러가 나올 수도 있음 그런 경우는 `환경 변수 설정`이 필요함

![](https://velog.velcdn.com/images/prettylee620/post/f45f43a4-8214-4e2f-848c-0b7ac29187f8/image.png)

2. 환경 변수 설정이 필요 Path 에서 편집

![](https://velog.velcdn.com/images/prettylee620/post/f94734d6-e67e-4f9f-a644-f224264b0aa3/image.png)

![](https://velog.velcdn.com/images/prettylee620/post/33a3b039-2919-4ee7-a89c-124e4004ff4d/image.png)

```jsx
C:\Users\rmawn\AppData\Local\Microsoft\WindowsApps
```

3. jekyll 실행을 위해 필요한 모듈 설치

![](https://velog.velcdn.com/images/prettylee620/post/0c1d675f-0341-47b3-a015-aecc6926476f/image.png)

4. npm을 통해 node.js 모듈 설치

```jsx
npm install && npm run build
```

![](https://velog.velcdn.com/images/prettylee620/post/ac853e67-6313-4ebf-a208-642aef312503/image.png)

5. 설치 완료 후 아래 명령어를 통해 로컬에서 jekyll을 실행

```jsx
jekyll serve
```

![](https://velog.velcdn.com/images/prettylee620/post/f710e210-be75-4270-86df-c936a55d35e3/image.png)

#### 🙄 **문제 발생**

![](https://velog.velcdn.com/images/prettylee620/post/bd7fa754-ede0-42d6-9a3c-ef3c184a1643/image.png)

먼저 위에서 처럼

1. 환경변수 추가
2. `Gemfile` 파일 수정

![](https://velog.velcdn.com/images/prettylee620/post/343c2a30-b6ff-4e33-b098-8800ddb60167/image.png)

3. 원래의 코드

```jsx
# frozen_string_literal: true

source "https://rubygems.org"

gemspec

group :test do
  gem "html-proofer", "~> 5.0"
end
```

4. 수정

```jsx
# frozen_string_literal: true

source "https://rubygems.org"

gemspec

group :test do
  gem "html-proofer", "~> 5.0"
end

gem "tzinfo"       # 시간대 관리 라이브러리
gem "tzinfo-data"  # Windows에서 tzinfo를 지원하기 위한 데이터
```

6. cmd 껐다가 다시 켜서 확인해보기

```jsx
jekyll serve
```

![](https://velog.velcdn.com/images/prettylee620/post/4577b948-a8b2-492b-b7cc-6a2d52a88a32/image.png)

7. 웹브라우저에서 127.0.0.1:4000 주소로 블로그가 정상적으로 표시되는지 확인하고 블로그 내 여러 메뉴 및 기능들도 정상 동작하는지 확인

![](https://velog.velcdn.com/images/prettylee620/post/612687e4-7490-4f68-8d08-92db45e2de9a/image.png)

![](https://velog.velcdn.com/images/prettylee620/post/58c63c98-fce9-4af0-a80e-668bcc89f588/image.png)

```rust
# frozen_string_literal: true

source "https://rubygems.org"

gemspec

group :test do
  gem "html-proofer", "~> 5.0"
end

gem "tzinfo"       # 시간대 관리 라이브러리
gem "tzinfo-data"  # Windows에서 tzinfo를 지원하기 위한 데이터

```

### 글 쓰는 시간을 줄이기 위한 플러그인

> https://github.com/jekyll/jekyll-compose

### 배포하기

1. 배포하기 전 gitgnore 세팅하기

![](https://velog.velcdn.com/images/prettylee620/post/beb0ace2-c081-4389-8723-3b52daa3d78b/image.png)

* 우선, `.gitignore` 파일을 열어서 아래 내용을 주석 처리

```jsx
# Misc
#_sass/dist
#assets/js/dist
```

2. 배포하기

```jsx
git add .
git commit -m "docs: add new blog post"
git push
```

3. setting > page > git action 추가 ![](https://velog.velcdn.com/images/prettylee620/post/cb6253bb-ef56-4dc0-98a2-e6073bf194bc/image.png)

* configure까지 해줘야 함

![](https://velog.velcdn.com/images/prettylee620/post/e95923a4-4544-446f-bc0a-3f37f955572d/image.png)

안하면 아래와 같은 화면 나옴

<figure><img src="../../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

4. 로컬이랑 맞춰줘야 함

![](https://velog.velcdn.com/images/prettylee620/post/51eb7b1b-9512-4b0f-b59a-e42440c1f450/image.png)

5. github > workflows > startser > pages-deploy.yml 로컬에서 삭제 후 한번 더 git pull

<figure><img src="../../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

6. .gitignore 내 assets/js/dist 디렉토리 내 파일들의 Push가 무시되도록하는 설정을 주석처리\`\`\`ignore

```
# Bundler cache
.bundle
vendor
Gemfile.lock

# Jekyll cache
.jekyll-cache
.jekyll-metadata
_site

# RubyGems
*.gem

# NPM dependencies
node_modules
package-lock.json

# IDE configurations
.idea
.vscode/*
!.vscode/settings.json
!.vscode/extensions.json
!.vscode/tasks.json

# Misc
#_sass/dist
#assets/js/dist
```

> 로컬에서**는 assets/js/dist/\*.min.js 파일이 존재하여 정상 동작했지만, 위 설정을 하지 않고 배포할 경우 Github에는 해당 파일이 push되지 않으므로 블로그 기능이 정상 동작하지 않는다고 함**

7. 커스터마이징을 위한 \_config.yml 수정하기

> 참고로 fork해 온 것은 커밋 규칙 안맞으면 안들어감...

```jsx
git add . 
git commit -m "chore: update config.yml settings"
git push
```

훔... 일단 배포만 해두고 ... 지금 gitbook 테마가 더 마음에 들어서 gitbook에만 해둘까? 아님 github.io랑 연결해서 할 수 있는 방법을 찾을까 고민중임

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

내 [gitbook](https://mellona-log.gitbook.io/log)
