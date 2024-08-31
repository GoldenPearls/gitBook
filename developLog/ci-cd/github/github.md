---
icon: git
description: github 자주 쓰는 명령어 모아보기
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
---

# github 자주 쓰는 명령어

## 1. 로컬 저장소와 원격 저장소 맞추기

> `git reset --hard` 명령어를 사용하여 특정 커밋으로 되돌렸을 때, 로컬 저장소는 그 커밋 이후의 모든 변경 사항을 잃게 됩니다. 이때 로컬 저장소와 원격 저장소의 상태가 불일치하게 되며, 이를 맞추기 위해 강제로 푸시해야 합니다.

1.  **`git reset --hard` 명령어를 사용하여 특정 커밋으로 되돌림**

    이미 실행한 명령어입니다:

    ```bash
    git reset --hard 6bec4114ed3cfd83858aa59311ca0ac9813d6c18
    ```

    이 명령어는 로컬 저장소를 `6bec4114ed3cfd83858aa59311ca0ac9813d6c18` 커밋으로 되돌립니다.
2.  **원격 저장소에 강제로 푸시**

    로컬 저장소를 원격 저장소와 맞추기 위해 강제로 푸시해야 합니다. 이는 원격 저장소의 히스토리를 덮어쓰는 것이므로 주의가 필요합니다.

    ```bash
    git push origin main --force
    ```

    여기서 `main`은 현재 사용 중인 브랜치 이름입니다. 만약 다른 브랜치를 사용 중이라면 해당 브랜치 이름을 사용해야 합니다.

### 주의사항

* **강제 푸시 (`--force`)**: 원격 저장소의 히스토리가 덮어써지기 때문에, 이 작업은 신중하게 수행해야 합니다. 특히 다른 사람들이 이 저장소를 사용 중이라면, 그들에게 영향을 미칠 수 있습니다.
* **히스토리 손실**: 이 작업을 수행하면 되돌린 이후의 모든 커밋이 히스토리에서 사라지므로, 필요할 경우 백업을 고려해야 합니다.

### 전체적인 절차 요약

1. **로컬 커밋 되돌리기**: `git reset --hard <commit-hash>`
2. **원격 저장소에 강제 푸시**: `git push origin <branch-name> --force`

이 작업을 통해 로컬과 원격 저장소의 상태를 일치시킬 수 있습니다.



## 2. Fork한 레포지토리 commit 기록 남기기 위한 작업

> 참고 : [https://soranhan.tistory.com/11](https://soranhan.tistory.com/11)

1. **일단 내 github에 새로운 레파지토리를 만든다.**
2. **terminal을 연다.**
3. **복사하고자 하는 repository를 bare clone한다.**

```
$ git clone --bare https://github.com/exampleuser/old-repository.git
```

4. **새로운 레파지토리로 Mirror-push**

```
$ cd old-repository.git
$ git push --mirror https://github.com/exampleuser/new-repository.git
```

5. **처음에 임시로 생성했던 local repository를 삭제**

```
$ cd ..
$ rm -rf old-repository.git
```

6. 다만 이게 가끔 안되는 게 있는데 그 이유는 `권한` 때문이기에 토큰을 만들어주고 그걸로 사용해야 함

> GitHub Personal Access Token 생성
>
> 1. GitHub 계정으로 로그인합니다.
> 2. [GitHub의 Personal Access Token 생성 페이지](https://github.com/settings/tokens)로 이동합니다.
> 3. "Generate new token" 버튼을 클릭합니다.
> 4. **권한(Scopes)**에서 `repo`, `workflow`, `admin:repo_hook` 권한을 선택합니다. 이 토큰은 저장소에 대한 읽기/쓰기 권한을 가집니다.
> 5. 생성된 토큰을 복사해둡니다. 이 토큰은 나중에 사용할 것입니다.
> 6. 개인잘페이지에 토큰기록해두기!!!

* Personal Access Token (PAT) 사용 : `git push --mirror` 명령어에 Personal Access Token을 포함하여 인증 문제를 해결할 수 있습니다.

```
git push --mirror https://<TOKEN>@github.com/exampleuser/new-repository.git
```

## 3. 다른 브랜치를 `git clone`

다른 브랜치를 `git clone`하는 경우 기본적으로는 원격 저장소의 기본 브랜치(예: `main` 또는 `master`)가 복제됩니다. 하지만 특정 브랜치를 클론하고 싶다면 다음과 같은 방법을 사용할 수 있습니다.



### 1) 특정 브랜치를 지정하여 클론하기

특정 브랜치를 지정하여 저장소를 클론하려면 `-b` 옵션을 사용하여 브랜치 이름을 명시합니다.

```bash
git clone -b <branch-name> <repository-url>
```

예를 들어, `feature-branch`라는 브랜치를 클론하려면:

```bash
git clone -b feature-branch https://github.com/username/repository.git
```

이 명령은 지정한 브랜치를 기준으로 저장소를 클론합니다.

### 2) 클론 후 브랜치 체크아웃

기본 브랜치로 클론한 후에 다른 브랜치로 전환할 수도 있습니다.

```bash
git clone <repository-url>
cd <repository-directory>
git checkout <branch-name>
```

예를 들어:

```bash
git clone https://github.com/username/repository.git
cd repository
git checkout feature-branch
```

이 방법은 기본 브랜치를 클론한 후에 다른 브랜치로 전환하여 작업을 진행할 수 있습니다.

이렇게 하면 해당 브랜치의 내용으로 작업 디렉토리가 전환됩니다.



### 3) 특수문자를 로컬에 clone하려 하면 문제 생김
