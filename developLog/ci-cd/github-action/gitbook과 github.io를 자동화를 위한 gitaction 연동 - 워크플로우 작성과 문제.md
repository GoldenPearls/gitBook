# gitbook과 github.io를 자동화를 위한 gitaction 연동 - 워크플로우 작성과 문제

## 1. ㅎ 위한 스크립트 및 트러블 슈팅&#x20;

> 📁 이전 글&#x20;
>
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

흑흑.. 줄 알았으나... 카테고리가 얽히는 현상이 나옴...

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

## TMI

GITHUB Action이 편하기는 한다 잘 몰라서... 할 때마다 공부해야 할 듯 ㅋㅋㅋㅋㅋㅋㅋㅋ 너무 많기도 하고... 하지만 자동화 최고



> 📁 참고&#x20;
>
> * [Github action으로 Sync Fork 자동화하기 - push 될 때마다](https://velog.io/@charming-l/Github-action%EC%9C%BC%EB%A1%9C-push-%EB%90%A0-%EB%95%8C%EB%A7%88%EB%8B%A4-%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C-%EC%B5%9C%EC%8B%A0%ED%99%94%ED%95%98%EA%B8%B0to-forked-repo)
> * [GitAction에서 다른 레포로 접근하려면?](https://stackoverflow.com/questions/71068476/accessing-another-repository-with-github-cli-in-github-actions)
> * [GitHub Organization 프로젝트를 vercel 무료로 연동하기 (+git actions)](https://velog.io/@rmaomina/organization-vercel-hobby-deploy)
> * [GitHub Action 이거만 보고 다 할 수 있다!](https://velog.io/@songmag/GitHub-Action-%EC%9D%B4%EA%B1%B0%EB%A7%8C-%EB%B3%B4%EA%B3%A0-%EB%8B%A4-%ED%95%A0-%EC%88%98-%EC%9E%88%EB%8B%A4)
