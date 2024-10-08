# 스프링 부트 서버 개발 프롤로그

![](https://velog.velcdn.com/images/prettylee620/post/09a20276-fa60-42db-88f7-696abfca436f/image.png)

> 출처 : [자바와 스프링 부트로 생애 최초 서버 만들기, 누구나 쉽게 개발부터 배포까지! \[서버 개발 올인원 패키지\]](https://www.inflearn.com/course/%EC%9E%90%EB%B0%94-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%EC%84%9C%EB%B2%84%EA%B0%9C%EB%B0%9C-%EC%98%AC%EC%9D%B8%EC%9B%90/dashboard)

## 1. JVM

### 1) 컴퓨터는 바보다.

#### 컴파일

* 인간이 이해하기 쉬운 언어를 기계어로 번역하는 과정

#### 컴파일러

* 컴파일을 하는 프로그램

#### 바이트 코드

* 0과 1로 이루어진 코드, 컴퓨터가 이해할 수 있다.

### 2) 0과 1의 조합은 운영체제마다 다르다.

![](https://velog.velcdn.com/images/prettylee620/post/fd5daf80-5b93-482e-9307-32182ac70977/image.png)

#### 자바의 경우 Jvm이 운영체제 위에 존재한다

* JVM : 0과 1과 운영체제 사이에서 둘을 호환시켜주는 역할을 하는 것
* JVM 운영체제에게 한 번 더 번역해준다는 것(운영체제 마다 똑같은 결과)

![](https://velog.velcdn.com/images/prettylee620/post/52548fd2-2209-49a6-80e8-13ab9f8b7b8f/image.png)

#### JVM

* 스칼라, 그루비, 코틀린
* 원래는 OS마다 다른 컴파일러가 필요하지만, **JAVA는 JVM과 0과 1을 OS에 맞게 번역해준다.**
  * 즉, 똑같은 JAVA 바이트 코드를 OS마다 다르게 해석해주는 것
* 이 JVM은 인기가 상당해서, JAVA 외의 다른 언어에서도 사용하고 있다.

## 2. JRE? JDK? JVM?

![](https://velog.velcdn.com/images/prettylee620/post/9b2a1f6e-cabb-440e-ae38-c7957b5ac67b/image.png)

### 1) JVM

1. 자바 가상 머신의 약자
2. Java Virtual Machine
3. OS 별로 존재한다
4. 바이너리 코드를 읽고 검증하고 실행한다.

### 2) JRE

1. 자바 실행 환경의 약자
2. Java Runtime Environment
3. JRE = JVM + 자바 프로그램
4. 실행에 필요한 라이브러리 파일
5. JVM의 실행환경을 구성(스캐너)

### 3) JDK

1. 자바 개발 도구의 약자
2. Java Development Kit
3. JDK = JRE + 개발을 위한 도구
4. 컴파일러, 디버그 도구 등이 포함
5. JAVA의 버전 = JDK의 버전

## 3. JDK의 버전

### LTS(Long Time Support)

* 오래 써도 되는 버전
* 우리가 오래 지원할게요\~
* 8과 11

### Oracle JDK VS Open JDK

1. Oracle JDK : 오라클에서 만든 JDK, 개인에게 무료, 기업용은 유료
2. Open JDK : Oracle JDK와 비슷한 성능, 언제나 무료

### JDK의 버전과 종류

1. JDK는 버전이 있고 각 버전별로 새로운 기능이 추가되거나 기존 기능이 사라진다.
2. JDK에는 종류가 있고, 기능 자체는 모두 동일하나 성능과 비용에 약간의 차이가 있을 수 있다.

## 4. 빌드와 실행

### 빌드(Build)

* 소스 코드 파일을 컴퓨터에서 실행할 수 있는 독립 sw 가공물(하나의 파일로) 변환시키는 과정
* 독립 sw 가공물 = Artifact

### 빌드를 세분화 하면

1. 소스 코드를 컴파일
2. 테스트 코드를 컴파일
3. 테스트 코드 실행
4. 테스트 코드 리포트를 작성
5. 기타 추가 설정한 작업들을 진행
6. **패키징을 수행**한다(외부 소스를 가져다가 패키징)
7. 최종 sw 결과물(Artifact)를 만들어 낸다.

### 실행(run)

* 내가 작성한 코드(혹은 테스트 코드)를 컴파일을 거쳐, 작동시켜 보는 것
* 독립 SW 가공물이 나올 수 도 있고, 나오지 않을 수도 있음
* 주의 : `인터프리터 언어`는 컴파일이 필요 없다.

빌드를 수동으로 하면 각기 다른 사람이 다르게 나오고 리소스도 많이 듦 ⇒ 빌트 툴

## 5. JAVA의 빌드 툴

### 1) 빌드 툴(build tool)

* 소스코드의 빌드 과정을 자동으로 처리 해주는 프로그램
* 외부 소스 코드(외부 라이브러리) 자동 추가, 관리

### 2) 빌드 툴 종류

#### 1. Ant = 거의 x

* 설정을 위해 xml을 사용
* 간단하고 사용하기 쉽다고 함
* 복잡한 처리를 하려 하면 빌드 스크립트가 장황해져 관리가 어렵다
* 외부 라이브러리를 관리하는 구조가 없다

#### 2. Maven

* 설정을 위해 xml을 사용한다
* 외부 라이브러리를 관리할 수 있다.
* 장황 빌드 스크립트 문제를 해결했다.
* 특정 경우에 xml이 복잡해진다.
* xml 자체의 한계가 있다.

#### 3. Gradle

* 설정을 위해 groovy 언어를 사용
* 외부 라이브러리를 관리할 수 있다.
* 유연하게 빌드 스크립트를 작성할 수 있다.
* 성능이 뛰어나다.
* 가장 최신에 나온 자바 빌드 툴로 신규 프로젝트에서 많이 사용되고 있다.

## 6. 정리

1. `빌드`란 단순히 실행하는 것과 다르다.
2. **빌드 과정 자동화**와 **외부 라이브러리 관리**를 위해 **빌드 툴**이 사용된다.
3. 널리 쓰였던 (쓰이는) java 빌드 툴에는 ant/maven/gradle 3가지가 있다.
4. 현재 주로 maven/gradle 2가지가 많이 쓰인다.
