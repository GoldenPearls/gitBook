# 자바 입문 강의 정리

## 1. 자바란?

### 자바 표준 스펙

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### 자바 표준 스펙과 구현

* 자바 표준 스펙
  * 자바는 이렇게 만들어야 한다는 설계도이며, 문서
* 다양한 자바 구현
  * 여러 회사에서 자바 표준 스펙에 맞춰서 실제 작동하는 자바 프로그램을 개발
  * 최적화나 동작하는 것에 약간의 차이들이 있다.

### 컴파일과 실행

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

자바 프로그램은 컴파일과 실행 단계를 거친다.

1. Hello.java와 같은 자바 소스 코드를 개발자가 작성
2. `자바 컴파일러`를 사용해서 소스 코드를 컴파일

* 자바가 제공하는 `javac`라는 프로그램을 사용
* .java ⇒ class 파일이 생성
* 자바 소스 코드를 **바이트코드로 변환**하며 자바 가상 머신에서 더 빠르게 실행될 수 있게 최적화하고 문법 오류 검출

3. 자바 프로그램 실행

* 자바가 제공하는 java라는 프로그램을 사용한다.
* 자바 가상 머신(JVM)이 실행되면서 프로그램이 작

### IDE와 자바

#### 인텔리제이를 통한 자바 설치 관리

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 내부에 자바를 편리하게 설치하고 관리할 수 있는 기능을 제공

#### 인텔리제이를 통한 자바 컴파일, 실행 과정

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

1. **컴파일**

* 자바 코드를 컴파일 하려면 javac 라는 프로그램을 직접 사용해야 하는데, **인텔리제이는 자바 코드를 실행할 때 이 과정을 자동으로 처리해준다.**
* 예) javac Hello.java
* 인텔리제이 화면에서 프로젝트에 있는 `out 폴더`에 가보면 컴파일된 .class 파일이 있는 것을 확인할 수 있다.

2. **실행**

* 자바를 실행하려면 java 라는 프로그램을 사용해야 한다. 이때 컴파일된 .class 파일을 지정해주면 된다.
* 예) java Hello , 참고로 확장자는 제외한다.
* 인텔리제이에서 자바 코드를 실행하면 컴파일과 실행을 모두 한번에 처리한다.

### 자바와 운영체제 독립성

1. 일반적인 프로그램은 다른 운영체제에서 실행 할 수 없다.

<figure><img src="../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

2. 자바 프로그램은 자바가 설치된 모든 OS에서 실행할 수 있다.

* 자바 개발자는 특정 OS에 맞추어 개발을 하지 않아도 된다. 자바 개발자는 자바에 맞추어 개발하면 된다.
* **OS 호환성 문제는 자바가 해결한다.** Hello.class 와 같이 컴파일된 자바 파일은 모든 자바 환경에서 실행할 수 있다.
* 윈도우 자바는 윈도우 OS가 사용하는 명령어들로 구성되어 있다. MAC이나 리눅스 자바도 본인의 OS가 사용하는 명령어들로 구성되어 있다. 개발자는 각 OS에 맞도록 자바를 설치하기만 하면 된다.

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### 자바 개발과 운영 환경

<figure><img src="../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 개발할 때 자바와 서버에서 실행 시 다른 자바를 사용할 수 있다.
* 개발자들은 개발의 편의를 위해 윈도우나 MAC OS를 주로 사용한다.
* 서버는 주로 리눅스 사용. 만약 AWS를 사용한다면 Amazon Corretto 자바를 AWS 리눅스 서버에 설치
* 자바의 운영체제 독립성 덕분에 각가의 환경에 맞춰 자바 설치 가능

## 2. 변수

### 변수의 선언과 초기화

#### 변수의 값 대입

`a = 10`

* 자바에서 `=` 은 **오른쪽에 있는 값을 왼쪽에 저장**한다는 뜻이다. 수학에서 이야기하는 두 값이 같다( equals )와는 다른 뜻이다!
* 숫자를 보관할 수 있는 데이터 저장소인 변수 a 에 값 10 을 저장한다.
* 하나의 변수 선언 시 `메모리 공간 하나`를 차지하는 느낌

#### 변수 값 변경

* 변수는 이름 그대로 변할 수 있는 수이다. 쉽게 이야기해서 변수 a 에 저장된 값을 언제든지 바꿀 수 있다는 뜻이다

#### 초기화 하지 않은 변수 읽기

```java
java: variable a might not have been initialize
```

* 해석해보면 변수가 초기화되지 않았다는 오류이다.

> 🤔 **왜 이런 오류가 발생할까?** 컴퓨터에서 메모리는 여러 시스템이 함께 사용하는 공간이다. 그래서 어떠한 값들이 계속 저장된다.

* 변수를 선언하면 메모리상의 어떤 공간을 차지하고 사용한다. 그런데 그 공간에 기존에 어떤 값이 있었는지는 아무도 모른다.

예를 들어 게임을 하다가 어떤 공간에 7이 저장되어 있었는데 끄게 되면 그 공간 7은 사라지는 것이 아닌 다른 것들이 사용할 수 있기 때문에 뭐가 있는지 정확히 모름

따라서 **초기화를 하지 않으면 이상한 값이 출력될 수 있다.** 이런 문제를 예방하기 위해 **자바는 변수를 초기화 하도록 강제**한다.

> 참고: 지금 학습하는 변수는 `지역 변수(Local Variable)`라고 하는데, 지역 변수는 개발자가 직접 초기화를 해주어야 한다. 나중에 배울 `클래스 변수`와 `인스턴스 변수`는 자**바가 자동으로 초기화를 진행해준다.**

> 참고: `컴파일 에러`는 **자바 문법에 맞지 않았을 때 발생하는 에러**이다. 컴파일 에러는 오류를 빨리, 그리고 명확하게 찾을 수 있기 때문에 사실은 좋은 에러이다. 덕분에 빠르게 버그를 찾아서 고칠 수 있다.

#### 초기화 되지 않은 것은 컴파일 되지 않음

<figure><img src="../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* out에 뜨지 않는다.
* 그리고 컴파일 시 변수를 쓰지 않는다면 자바가 최적화 하여 코드가

```java
package Vaiable;

public class Var6 {
    public static void main(String[] args){
        int a;
    }
}
```

<figure><img src="../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

> tip : 1줄 복사 단축키 `ctrl + D`

#### 리터럴

* 코드에서 개발자가 직접 적은 100 , 10.5 , true , 'A' , "Hello Java" 와 같은 **고정된 값을 프로그래밍 용어**로 `리터럴(literal)`이라 한다
* 변수의 값은 변할 수 있지만 리터럴은 개발자가 직접 입력한 고정된 값이다. 따라서 리터럴 자체는 변하지 않는다. 코드 자체는 바꿔 쓸 수 있지만 그것은 다른 리터럴을 쓴 것이다.

### 2. 숫자 타입

> 메모리를 적게 사용하면 작은 숫자를 표현할 수 있고, 메모리를 많이 사용하면 큰 숫자를 표현할 수 있다. 변수를 선언하면 표현 범위에 따라 **메모리 공간을 차지**한다. 그래서 필요에 맞도록 다양한 타입을 제공한다

```java
package variable;
public class Var8 {
 public static void main(String[] args) {
	 //정수
	 byte b = 127; //-128 ~ 127
	 short s = 32767; // -32,768 ~ 32,767
	 int i = 2147483647; //-2,147,483,648 ~ 2,147,483,647 (약 20억)
 
	 //-9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807
	 long l = 9223372036854775807L; 
	 
		//실수
	 float f = 10.0f;
	 double d = 10.0;
 }
}
```

#### 데이터 타입 크기

* 정수형
  * byte : -128 \~ 127 (1byte, 2⁸)
  * short : -32,768 \~ 32,767 (2byte, 2¹⁶)
  * int : -2,147,483,648 \~ 2,147,483,647 (약 20억) (4byte, 2³²)
  * long : -9,223,372,036,854,775,808 \~ 9,223,372,036,854,775,807 (8byte, 2⁶⁴)
* 실수형
  * float : 대략 -3.4E38 \~ 3.4E38, 7자리 정밀도 (4byte, 2³²)
  * double : 대략 -1.7E308 \~ 1.7E308, 15자리 정밀도 (8byte, 2⁶⁴) ⇒ 보통의 실수는 `double` 사용
* 기타
  * boolean : true , false (1byte)
  * char : 문자 하나(2byte)
  * String : 문자열을 표현한다. 메모리 사용량은 문자 길이에 따라 동적으로 달라진다. (특별한 타입이다. 자세한 내용은 뒤에서 학습한다

### 변수 타입 정리

> 🤔 그렇다면 이렇게 많은 변수들을 실제로 외우고 사용해야 할까?

#### 다음 타입은 실무에서 거의 사용하지 않는다.

1. byte

* 표현 길이가 너무 작다.
* 또 자바는 기본으로 `4byte( int )`를 효율적으로 계산하도록 설계되어 있다. int를 사용하자.
* byte 타입을 직접 선언하고 여기에 숫자 값을 대입해서 계산하는 일은 거의 없다.
* 대신에 파일을 바이트 단위로 다루기 때문에 **파일 전송, 파일 복사 등에 주로 사용**된다.

2. short

* 표현 길이가 너무 작다.
* 또 자바는 기본으로 `4byte( int )`를 효율적으로 계산하도록 설계되어 있다. int 를 사용하

3. float

* 표현 길이와 정밀도가 낮다.
* 실수형은 double 을 사용하자.

4. char

* 문자 하나를 표현하는 일은 거의 없다.
* 문자 하나를 표현할 때도 문자열을 사용할 수 있다.
* 예를 들어 String a = "b" 와 같이 사용하면 된다.

> **메모리 용량은 매우 저렴하다.** 따라서 메모리 용량을 약간 절약하기 보다는 개발 속도나 효율에 초첨을 맞추는 것이 더 효과적이다.

#### 자주 사용하는 타입

1. 정수

* int , long : 자바는 정수에 기본으로 int 를 사용한다. 만약 20억이 넘을 것 같으면 long 을 쓰면 된다.
* 파일을 다룰 때는 byte 를 사용한다.

2. 실수

* `double` : 실수는 고민하지 말고 double 을 쓰면 된다.

3. 불린형

* boolean : true , false 참 거짓을 표현한다. 이후 `조건문`에서 자주 사용된다.

4. 문자열

* String : 문자를 다룰 때는 문자 하나든 문자열이든 모두 String 을 사용하는 것이 편리하다.

자주 사용하는 타입을 제외하고 실무에서 나머지를 사용하는 경우는 거의 없다. 그나마 파일 전송시에 byte 를 사용하는 것 정도이다. 따라서 자주 사용하는 타입만 이해하고 나머지는 이런게 있구나 하고 넘어가도 충분하다.

## 3. 연산자

> tip : soutv 명령어 치면 아래를

```java
public class Var6 {
    public static void main(String[] args){
        int sum1 = 1+ 2 * 3;
        int sum2 = (1+2) * 3;
    }
}
```

이와 같이 만들어줌

```java
package Vaiable;

public class Var6 {
    public static void main(String[] args){
        int sum1 = 1+ 2 * 3;
        int sum2 = (1+2) * 3;
        System.out.println("sum1 = " + sum1);
        System.out.println("sum2 = " + sum2);
        
    }
}
```

#### 연산자 우선순위 암기법

높은 것에서 낮은 순으로 적었다. 처음에 나오는 `괄호 ()` 가 **우선순위가 가장 높고,** 마지막의 `대입 연산자( = )`가 **우선순위가 가장 낮다.**

1. 괄호 ()
2. 단항 연산자 (예: ++ , -- , ! , \~ , new , (type) )
3. 산술 연산자 ( \* , / , % 우선, 그 다음에 + , - )
4. Shift 연산자 ( << , >> , >>> )
5. 비교 연산자 ( < , <= , > , >= , instanceof )
6. 등식 연산자 ( == , != )
7. 비트 연산자 ( & , ^ , | )
8. 논리 연산자 ( && , || )
9. 삼항 연산자 ( ? : )
10. 대입 연산자 ( = , += , -= , \*= , /= , %= 등등)

> 그러면 이 많은 우선순위를 어떻게 외워야 할까? 사실 대부분의 실무 개발자들은 연산자 우선순위를 외우지 않는다. 연산자 우선순위는 **딱 2가지만 기억하면 된다.**

1. **상식선에서 우선순위를 사용하자**

* 우선순위는 상식선에서 생각하면 대부분 문제가 없다.
* 다음 예를 보자

```java
int sum = 1 + 2 * 3
```

당연히 `+` 보다 `*` 이 **우선순위가 높다.** 다음으로 산술 연산자( + )와 대입연산자( = )를 비교하는 예를 보자. **int sum = 1 + 2**

````
int sum = 1 + 2
int sum = 3 //산술 연산자가 먼저 처리된다.
sum = 3 //대입 연산자가 마지막에 처리된다. ```

````

1 + 2 를 먼저 처리한 다음에 그 결과 값을 변수 sum 에 넣어야 한다. 대입 연산자인 = 이 먼저 수행된다고 생각하기가 사실 더 어렵다.

**2. 애매하면 `괄호()`를 사용하자**

* 코드를 딱 보았을 때 연산자 우선순위를 고민해야 할 것 같으면, 그러니까 뭔가 복잡해보이면 나 뿐만 아니라 모든 사람 이 그렇게 느낀다. 이때는 다음과 같이 괄호를 사용해서 우선순위를 명시적으로 지정하면 된다.
* ((2 \* 2) + (3 \* 3)) / (3 + 2)

> 연산자 우선순위는 상식선에서 생각하고, 애매하면 `괄호`를 사용하자 누구나 코드를 보고 쉽고 명확하게 이해할 수 있어야 한다. 개발자들이 연산자 우선순위를 외우고 개발하는 것이아니다! 복잡하면 명확하게 괄호를 넣어라! 개발에서 가장 중요한 것은 **단순함과 명확함**이다! 애매하거나 복잡하면 안된다

### 증감 연산자

```java
package operator;

public class OperatorAdd1 {
 public static void main(String[] args) {
		 int a = 0;

		 a = a + 1;
		 System.out.println("a = " + a); //1

		 a = a + 1;
		 System.out.println("a = " + a); //2

		 //증감 연산자
		 ++a; //a = a + 1
		 System.out.println("a = " + a); //3

		 ++a; //a = a + 1
		 System.out.println("a = " + a); //4
 }
}
```

#### 전위, 후위 증감 연산자

증감 연산자는 피연산자 앞에 두거나 뒤에 둘 수 있으며, 연산자의 위치에 따라 연산이 수행되는 시점이 달라진다.

1. `++a` : 증감 연산자를 피연산자 앞에 둘 수 있다. 이것을 앞에 있다고 해서 **전위(Prefix) 증감 연산자**라 한다.

* 증감 연산이 먼저 수행된 후 나머지 연산이 수행된다

<figure><img src="../../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```java
a = 1, b = 0
b = ++a //전위 증감 연산자 : a의 값을 먼저 증가시키고, 그 결과를 b에 대입

a = a + 1 //1. a의 증감 연산이 먼저 진행, a = 2
b = a //2.  이후에 a를 대입 b = 2

결과: a = 2, b = 2
```

2. `a++` : 증감 연산자를 피연산자 뒤에 둘 수 있다. 이것을 뒤에 있다고 해서 **후위(Postfix) 증감 연산자**라 한다

* **다른 연산이 먼저 수행된 후** 증감 연산이 수행된다.

<figure><img src="../../../.gitbook/assets/image (11) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```java
a = 1, b = 0
b = a++ //후위 증감 연산자

b = a; //1. a의 값을 먼저 b에 대입 b = 1
a = a + 1; //2. 이후에 a의 값을 증가 a = 2

결과: a = 2, b = 1
```

3. 예시

```java
package operator;

public class OperatorAdd2 {
 public static void main(String[] args) {
	 // 전위 증감 연산자 사용 예
	 int a = 1;
	 int b = 0;
	 b = ++a; // a의 값을 먼저 증가시키고, 그 결과를 b에 대입
	 System.out.println("a = " + a + ", b = " + b); // 결과: a = 2, b = 2

	 // 후위 증감 연산자 사용 예
	 a = 1; // a 값을 다시 1로 지정
	 b = 0; // b 값을 다시 0으로 지정
	 b = a++; // a의 현재 값을 b에 먼저 대입하고, 그 후 a 값을 증가시킴
	 System.out.println("a = " + a + ", b = " + b); // 결과: a = 2, b = 1
	 }
}
```

### 비교 연산자

* 여기서 주의할 점은 = 와 == ( = x2)이 다르다는 점이다.

1. `=` : 대입 연산자, 변수에 값을 대입한다.
2. `==` : 동등한지 확인하는 비교 연산자
3. 불일치 연산자는 `!=` 를 사용한다. `!` 는 반대라는 뜻이다

#### 문자열의 비교

* 문자열이 같은지 비교할 때는 == 이 아니라 `.equals()` 메서드를 사용해야 한다.
* \== 를 사용하면 성공할 때도 있지만 실패할 때도 있다. 지금은 이 부분을 이해하기 어려우므로 지금은 단순히 문자열의 비교는 .equals() 메서드를 사용해야 한다 정도로 알고 있자.

```java
package operator;
public class Comp2 {
 public static void main(String[] args) {
	 String str1 = "문자열1";
	 String str2 = "문자열2";
 
	 boolean result1 = "hello".equals("hello"); //리터럴 비교
	 boolean result2 = str1.equals("문자열1");//문자열 변수, 리터럴 비교
	 boolean result3 = str1.equals(str2);//문자열 변수 비교
 
	 System.out.println("result1 = " + result1);
	 System.out.println("result2 = " + result2);
	 System.out.println("result3 = " + result3);
 }
}
```

> 문장 완성 단축키 : ctrl + shift + enter tip : 라인 복사 ctrl + D ⇒ shift를 누른 채 영역 지정 후 복사도 가 빌드 단축키 : crl + shift + f10

### 논리연산자

1. `&& (그리고`) : 두 피연산자가 모두 참이면 참을 반환, **둘 중 하나라도 거짓이면 거짓**을 반환
2. `|| (또는)` : 두 피연산자 중 하나라도 참이면 참을 반환, **둘 다 거짓이면 거짓**을 반환
3. `! (부정)` : 피연산자의 논리적 부정을 반환. 즉, **참이면 거짓을, 거짓이면 참을 반환**

## 4. 조건문

#### if만 쓸 경우 문제점

* **불필요한 조건 검사** : 이미 조건을 만족해도 불필요한 다음 조건을 계속 검사한다. 예를 들어서 나이가 5살이라면 미취학이 이미 출력이 된다. 그런데 **나머지 if 문을 통한 조건 검사도 모두 실행해야 한다.**
* **코드 효율성**: 예를 들어서 나이가 8살인 초등학생이라면 미취학을 체크하는 조건인 age <= 7 을 통해 나이가 **이미 8살이 넘는 다는 사실을 알 수 있다.** 그런데 바로 다음에 있는 초등학생을 체크하는 조건에서 `age >= 8 && age <= 13` 라는 2가지 조건을 모두 수행한다. 여기서 age >= 8 이라는 조건은 이미 앞의 age <= 7 이라는 조건과 관련이 있다. **결과적으로 조건을 중복 체크한 것이다.**

> 이런 코드에 `else if` 를 사용하면 불필요한 조건 검사를 피하고 코드의 효율성을 향상시킬 수 있다.

### if문과 else if문

> if 문에 else if 를 함께 사용하는 것은 **서로 연관된 조건일 때 사용**한다. 그런데 서로 관련이 `없는` 독립 조건이면 else if 를 사용하지 않고 if 문을 `각각` 따로 사용해야 한다

#### 문제

온라인 쇼핑몰의 할인 시스템을 개발해야 한다. 한 사용자가 어떤 상품을 구매할 때, 다양한 할인 조건에 따라 총 할인 금액이 달라질 수 있다.

**할인조건**

> 할인 시스템의 핵심은 한 사용자가 **`동시에` 여러 할인을 받을 수 있다는 점**이다. 예를 들어, 10000원짜리 아이템을 구매할 때 1000원 할인을 받고, 동시에 나이가 10살 이하이면 추가로 1000원 더 할인을 받는다. 그래서 총 2000원 까지 할인을 받을 수 있다

1. 아이템 가격이 10000원 이상일 때, 1000원 할인
2. 나이가 10살 이하일 때 1000원 할인

```java
package comd;

public class If5 {
    public static void main(String[] args) {
        int price = 1000;
        int age = 10;
        int discount = 0;

        if(age <= 10){
            discount = discount + 1000;
            System.out.println("10살 이하라서 1000원 할인");
        }

        if(price >=10000){
            discount = discount +1000;
            System.out.println("물건 가격 100000원 이상이랑 1000원 할인");
        }

        System.out.println("총 할인 받은 금액"+discount+"입니다.");
    }
}
```

다음과 같은 경우에는 두번째 문장은 if 문과 무관하다. 만약 둘다 if 문 안에 포함하려면 {} 를 사용해야 한다.

```java
if (true)
System.out.println("if문에서 실행됨");
System.out.println("if문에서 실행 안됨");
```

프로그래밍 스타일에 따라 다르겠지만, 일반적으로는 **if 문의 명령이 한개만 있을 경우에도** 다음과 같은 이유로 중괄호를 사용하는 것이 좋다.

1. **가독성:** 중괄호를 사용하면 코드를 더 읽기 쉽게 만들어 준다. 조건문의 범위가 명확하게 표시되므로 코드의 흐름을 더 쉽게 이해할 수 있다.
2. **유지보수성:** 중괄호를 사용하면 나중에 코드를 수정할 때 오류를 덜 발생시킬 수 있다. 예를 들어, if 문에 또 다른 코드를 추가하려고 할 때, 중괄호가 없으면 이 코드가 if 문의 일부라는 것이 명확하지 않을 수 있다.

### switch 문

> 참고로 if 문은 비교 연산자를 사용할 수 있지만, `switch` 문은 **단순히 값이 같은지만 비교할 수 있다.**

```java
public class Switch2 {
    public static void main(String[] args) {
        int grade = 2;

        int coupon;

        switch(grade){
            case 1:
                coupon = 1000;
                break;
            case 2:
                coupon = 2000;
                break;
            case 3:
                coupon = 3000;
                break;
            default:
                coupon = 500;
        }

        System.out.println(coupon);
    }
}
```

#### if문 vs switch 문

* switch 문의 조건식을 넣는 부분을 잘 보면 x > 10 과 같은 참 거짓의 결과가 나오는 조건이 아니라, 단순히 값만 넣을 수 있다.
* `switch` 문은 조건식이 **특정 case 와 같은지만 체크할 수 있다.** 쉽게 이야기해서 값이 같은지 확인하는 연산만 가능하다. (문자도 가능)
* 반면에 `if 문`은 참 거짓의 결과가 나오는 조건식을 자유롭게 적을 수 있다. 예) x > 10 , x == 10 정리하자면 swtich 문 없이 if 문만 사용해도 된다. 하지만 특정 값에 따라 코드를 실행할 때는 switch 문을 사용하면 if 문 보다 간결한 코드를 작성할 수 있다

### 자바 14 새로운 switch문

```java
package cond;

public class Switch3 {
 public static void main(String[] args) {
	 //grade 1:1000, 2:2000, 3:3000, 나머지: 500
	 int grade = 2;

	 int coupon = switch (grade) {
		 case 1 -> 1000; 
		 case 2 -> 2000;
		 case 3 -> 3000;
		 default -> 500;
 };

 System.out.println("발급받은 쿠폰 " + coupon);
 }
}
```

* `->` 를 사용한다.
* 선택된 데이터를 반환할 수 있다.
* 화살표 오른쪽에 있는 것이 coupon에 대입되는 것

### 삼항 연산자

이렇게 단순히 **참과 거짓에 따라서 특정 값을 구하는 경우** **삼항 연산자 또는 조건 연산자**라고 불리는 `?`: 연산자를 사용 할 수 있다. 이 연산자를 사용하면 if 문과 비교해서 코드를 단순화 할 수 있다.

```java
public class CondOp2 {
 public static void main(String[] args) {
	 int age = 18;
	 String status = (age >= 18) ? "성인" : "미성년자";
	 System.out.println("age = " + age + " status = " + status);
 }
}
```

#### 삼항 연산자

```java
(조건) ? 참_표현식 : 거짓_표현식
```

### 참고

변수가 초기화 되었는지 확인하는 것은 자바가 제공하는 JIT 컴파일러가 아니라 일반 컴파일러 입니다. 일반 컴파일러는 .java 파일을 바이트 코드인 .class파일로 변환하는 역할을 담당합니다. 추가로 소스 코드를 읽어서, 문법 오류를 찾아줍니다.

`JIT 컴파일러`는 자바의 실행 시점에 작동하고 바이트코드를 특정 플랫폼에 최적화된 기계어로 변환하는 역할을 합니다. 주로 성능 최적화에 사용됩니다.

## 5. 반복문

### while

```java
while (조건식) {
 // 코드
}
```

* 조건식을 확인한다. 참이면 코드 블럭을 실행하고, 거짓이면 while문을 벗어난다.
* 조건식이 참이면 코드 블럭을 실행한다. 이후에 코드 블럭이 끝나면 다시 조건식 검사로 돌아가서 조건식을 검사한다.(무한 반복)

#### 동작 방식

> 쓰는 이유는 코드를 `유연하게` 바꾸기 위해서이다.

<figure><img src="../../../.gitbook/assets/image (12) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (13) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### do-while문

* do-while 문은 while 문과 비슷하지만, **조건에 상관없이** `무조건 한 번은 코드를 실행`한다

```java
do {
 // 코드
} while (조건식);
```

```java
package loop;

public class DoWhile2 {
	 public static void main(String[] args) {
		 int i = 10;
		 do {
			 System.out.println("현재 숫자는:" + i);
			 i++;
	 } while (i < 3);

 }
}
```

### break, countinue

* break 와 continue 는 반복문에서 사용할 수 있는 키워드다.
* `break` 는 **반복문을 즉시 종료하고 나간다.** `continue` 는 **반복문의 나머지 부분을 건너뛰고 다음 반복으로 진행하는데 사용**된다.

1. break

* break 를 만나면 코드2 가 실행되지 않고 `while문이 종료`된다.

```java
while(조건식) {
 코드1;
 break;//즉시 while문 종료로 이동한다.
 코드2;
}
//while문 종료
```

```java
public class Break1 {
    public static void main(String[] args) {
        int sum = 0;
        int i = 1;

        while(true){
            sum += i;
            if(sum > 10){
                System.out.println("i의 값은 :"+i + "sum의 값은 : "+sum);
                break;
            }
						i++;
        }
    }
}
```

2. continue

* continue 를 만나면 코드2 가 실행되지 않고 다시 `조건식으로 이동`한다. 조건식이 참이면 while 문을 실행한다

```java
while(조건식) {
 코드1;
 continue;//즉시 조건식으로 이동한다.
 코드2;
}
```

```java
public class Continue1 {
    public static void main(String[] args) {
        int i = 1;

        while(i<=5){
            if(i==3){
                i++;
                continue;
            }
            System.out.println(i);
            i++;
        }
    }
}
```

### for문

```java
for (1.초기식; 2.조건식; 4.증감식) {
 // 3.코드
}
```

* 무한반복 코드

```java
for (;;) {
 // 코드
}
```

문제 : 1부터 시작하여 숫자를 계속 누적해서 더하다가 합계가 10보다 큰 처음값은?

```java
public class Break2 {
 public static void main(String[] args) {
	 int sum = 0;
	 int i = 1;

	 for (; ; ) {
		 sum += i;
		 if (sum > 10) {
			 System.out.println("합이 10보다 크면 종료: i=" + i + " sum=" + sum);
			 break;
			 }
			 i++;
	 }
	 }
}
```

```java
public class Break2 {
 public static void main(String[] args) {
	 int sum = 0;

	 for (int i = 1;; i++) {
		 sum += i;
		 if (sum > 10) {
			 System.out.println("합이 10보다 크면 종료: i=" + i + " sum=" + sum);
			 break;
			 }
			 
	 }
	 }
}
```

### 여러 개의 조건식

```java
package loop.ex;

public class ForEx2 {
 public static void main(String[] args) {
	 for (int num = 2, count = 1; count <= 10; num += 2, count++) {
		 System.out.println(num);
	 }
 }
}
```

### 정리

#### while vs for

#### for문

* **장점:**

1. 초기화, 조건 체크, 반복 후의 작업을 한 줄에서 처리할 수 있어 편리하다.
2. 정해진 횟수만큼의 반복을 수행하는 경우에 사용하기 적합하다.
3. 루프 변수의 범위가 for 루프 블록에 제한되므로, 다른 곳에서 이 변수를 실수로 변경할 가능성이 적다.

* 단점:

1. 루프의 조건이 루프 내부에서 변경되는 경우, for 루프는 관리하기 어렵다.
2. 복잡한 조건을 가진 반복문을 작성하기에는 while문이 더 적합할 수 있다.

#### while문

* **장점:**

1. 루프의 조건이 루프 내부에서 변경되는 경우, while 루프는 이를 관리하기 쉽다.
2. for 루프보다 더 복잡한 조건과 시나리오에 적합하다.
3. 조건이 충족되는 동안 계속해서 루프를 실행하며, 종료 시점을 명확하게 알 수 없는 경우에 유용하다.

* **단점:**

1. 초기화, 조건 체크, 반복 후의 작업이 분산되어 있어 코드를 이해하거나 작성하기 어려울 수 있다.
2. 루프 변수가 while 블록 바깥에서도 접근 가능하므로, 이 변수를 실수로 변경하는 상황이 발생할 수 있다.

> 한줄로 정리하자면 `정해진 횟수만큼 반복을 수행`해야 하면 **for문**을 사용하고 그렇지 않으면 while문을 사용하면 된다.

## 6. 스코프

### 지역변수와 스코프

> `변수`는 선언한 위치에 따라 지역 변수, 멤버 변수(클래스 변수, 인스턴스 변수)와 같이 분류된다. 우리가 지금까지 학습한 변수들은 모두 영어로 **로컬 변수(Local Variable) 한글로 지역 변수**라 한다.

#### 지역변수

1. 지역 변수는 이름 그대로 **특정 지역에서만 사용할 수 있는 변수**라는 뜻이다. 그 특정 지역을 벗어나면 사용할 수 없다.
2. 여기서 말하는 지역이 바로 변수가 선언된 `코드 블록( {} )`이다. `지역 변수`는 **자신이 선언된 코드 블록( {} ) 안에서만 생존하고, 자신이 선언된 코드 블록을 벗어나면 제거**된다. 따라서 이후에는 접근할 수 없다

#### 스코프

1. 이렇게 변수의 접근 가능한 범위를 `스코프(Scope)`라 한다.
2. 참고로 Scope를 번역하면 범위라는 뜻이다.

```java
package scope;

public class Scope2 {
 public static void main(String[] args) {
	 int m = 10;
	 for (int i = 0; i < 2; i++) { //블록 내부, for문 내
		 System.out.println("for m = " + m); //블록 내부에서 외부는 접근 가능
		 System.out.println("for i = " + i);
		 } //i 생존 종료

		 //System.out.println("main i = " + i); //오류, i에 접근 불가
			System.out.println("main m = " + m);
 }
}
```

1. for 문의 경우 for(int i=0;..) 과 같이 for 문 안에서 초기식에 직접 변수를 선언할 수 있다. 그리고 이렇게 선언한 변수는 **for 문 코드 블록 안에서만 사용할 수 있다.**

### 스코프의 존재 이유

#### 지역 변수를 사용하지 않았을 때

```java
package scope;

public class Scope3_1 {
 public static void main(String[] args) {
	 int m = 10;
	 int temp = 0;
	 if (m > 0) {
		 temp = m * 2;
		 System.out.println("temp = " + temp);
	 }
	 System.out.println("m = " + m);
 }
}
```

* 여기서 2배 증가한 값을 저장해두기 위해 **임시 변수** `temp` 를 사용했다. 그런데 이 코드는 좋은 코드라고 보기는 어렵다.
* 왜냐하면 임시 변수 temp 는 if 조건이 만족할때 임시로 잠깐 사용하는 변수이다. 그런데 임시 변수 temp main() 코드 블록에 선언되어 있다. 이렇게 되면 다음과 같은 **문제**가 발생한다.

1. **비효율적인 메모리 사용**

* temp 는 if 코드 블록에서만 필요하지만, main() 코드 블록이 종료될 때 까지 메모리 에 유지된다. 따라서 **불필요한 메모리가 낭비된다.**
* 만약 if 코드 블록 안에 temp 를 선언했다면 자바를 구현하는 곳에서 if 코드 블록의 종료 시점에 이 변수를 메모리에서 제거해서 더 효율적으로 메모리를 사용할 수 있다.

1. **코드 복잡성 증가**

* 좋은 코드는 군더더기 없는 단순한 코드이다. temp 는 if 코드 블록에서만 필요하고, 여기서만 사용하면 된다. 만약 if 코드 블록 안에 temp 를 선언했다면 if 가 끝나고 나면 temp 를 전혀 생각하지 않아도 된다. 머리속에서 생각할 변수를 하나 줄일 수 있다. 그런데 지금 작성한 코드는 if 코드 블록이 끝나도 main() 어디서나 temp 를 여전히 접근할 수 있다.
* 누군가 이 코드를 유지보수 할 때 m 은 물론이고 temp 까지 계속 신경써야 한다. **스코프가 불필요하게 넓은 것이다.**

#### 스코프 적용 코드

```java
package scope;

public class Scope3_2 {
 public static void main(String[] args) {
	 int m = 10;
	 if (m > 0) {
	 int temp = m * 2;
	 System.out.println("temp = " + temp);
	 }
	 System.out.println("m = " + m);
 }
}
```

### while문 vs for 문 - 스코프 관점

#### while문

```java
package loop;

public class While2_3 {
 public static void main(String[] args) {
	 int sum = 0;
	 int i = 1;
	 int endNum = 3;
	 while (i <= endNum) {
		 sum = sum + i;
		 System.out.println("i=" + i + " sum=" + sum);
		 i++;
	 }
 //... 아래에 더 많은 코드들이 있다고 가정
 }
}
```

#### for문

```java
package loop;

public class For2 {
 public static void main(String[] args) {
	 int sum = 0;
	 int endNum = 3;
	 for (int i = 1; i <= endNum; i++) {
		 sum = sum + i;
		 System.out.println("i=" + i + " sum=" + sum);
	 }
 //... 아래에 더 많은 코드들이 있다고 가정
 }
}
```

변수의 스코프 관점에서 카운터 변수 i 를 비교해보자.

* `while 문`의 경우 **변수 i 의 스코프가 main() 메서드 전체**가 된다. 반면에 `for 문`의 경우 변수 i 의 스코프가 for문 안으로 **한정**된다.
* 따라서 변수 i 와 같이 **for 문 안에서만 사용되는 카운터 변수가 있다면** while 문 보다는 for 문을 사용해서 **스코프의 범위를 제한하는 것이 메모리 사용과 유지보수 관점에서 더 좋다.**

#### 정리

* 변수는 꼭 필요한 범위로 한정해서 사용하는 것이 좋다. 변수의 스코프는 꼭 필요한 곳으로 한정해서 사용하자. 메모리를 효율적으로 사용하고 더 유지보수하기 좋은 코드를 만들 수 있다.
* **좋은 프로그램은 무한한 자유가 있는 프로그램이 아니라 적절한 제약이 있는 프로그램이다.**

### 형변환 : 자동형변환

#### 형변환

1. 작은 범위에서 큰 범위로는 당연히 값을 넣을 수 있다.

* 예) int long double

2. **큰 범위에서 작은 범위는** 다음과 같은 문제가 발생할 수 있다.

* 소수점 버림
* 오버플로우

#### 자동형변환

* 하지만 결국 대입하는 형(타입)을 맞추어야 하기 때문에 개념적으로는 다음과 같이 동작한다

```java
//intValue = 10
doubleValue = intValue
doubleValue = (double) intValue //형 맞추기
doubleValue = (double) 10 //변수 값 읽기
doubleValue = 10.0 //형변환
```

* 이렇게 앞에 (double) 과 같이 적어주면 int 형이 double 형으로 형이 변한다. 이렇게 형이 변경되는 것을 형변환이라 한다.
* **작은 범위 숫자 타입에서 큰 범위 숫자 타입**으로의 대입은 개발자가 이렇게 직접 형변환을 하지 않아도 된다. 이런 과정이 자동으로 일어나기 때문에 `자동 형변환, 또는 묵시적 형변환`이라 한다

### 형변환 : 명시적 형변환

> 이번에는 반대로 큰 범위에서 작은 범위로 대입해보자. 큰 범위에서 작은 범위 대입은 `명시적 형변환`이 필요하다

```java
package casting;

public class Casting2 {
    public static void main(String[] args) {
        double doubleValue = 1.5;
        int intValue = 0;

        //inValue = doubleValue; //컴파일 오류 발생
        intValue = (int) doubleValue; //형변환
        System.out.println(intValue);
    }
}
```

<figure><img src="../../../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

> 이런 문제는 매우 큰 버그를 유발할 수 있다. 예를 들어서 은행 프로그램이 고객에게 은행 이자를 계산해서 입금해야 하는데 만약 이런 코드가 아무런 오류 없이 수행된다면 끔찍한 문제를 만들 수 있다. 그래서 자바는 이런 경우 컴파일 오류를 발생시킨다. 항상 강조하지만 `컴파일 오류`는 **문제를 가장 빨리 발견할 수 있는 좋은 오류**이다

#### 형변환 = Casting

1. 만약 이런 위험을 개발자가 직접 감수하고도 값을 대입하고 싶다면 데이터 타입을 강제로 변경할 수 있다.
2. `형변환`은 다음과 같이 변경하고 싶은 데이터 타입을 **(int) 와 같이 괄호를 사용해서 명시적으로 입력하면 된다.**

```java
intValue = (int) doubleValue; //형변환
```

이것을 형(타입)을 바꾼다고 해서 형변환이라 한다. 영어로는 캐스팅이라 한다. 그리고 개발자가 직접 형변환 코드를 입력한다고 해서 `명시적 형변환`이라 한다

```java
//doubleValue = 1.5
intValue = (int) doubleValue;
intValue = (int) 1.5; //doubleValue에 있는 값을 읽는다.
intValue = 1; //(int)로 형변환 한다. intValue에 int형인 숫자 1을 대입한다
```

doubleValue 안에 들어있는 값은 1.5 로 그대로 유지된다. 참고로 **변수의 값은 `대입연산자( = )`를 사용해서 직접 대입할 때만 변경**된다.

```java
package casting;

public class Casting3 {
    public static void main(String[] args) {
       long maxIntValue = 2147483647; //int 최고값
        long maxIntOver = 2147483648L; //int 최고값 + 1(초과)
        int intValue = 0;

        intValue =(int)maxIntValue;
        System.out.println("maxIntValue casting="+intValue);

        intValue =(int)maxIntOver;
        System.out.println("maxIntOver casting="+intValue);
    }
}
```

<figure><img src="../../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

#### 정상 범위

이 경우 int 로 표현할 수 있는 범위에 포함되기 때문에 다음과 같이 **long int 로 형변환을 해도 아무런 문제가 없다.**

```java
intValue = (int) maxIntValue; //형변환
```

#### 초과 범위

1. 다음으로 long maxIntOver = 2147483648L 를 보면 int 로 표현할 수 있는 가장 큰 숫자인 2147483647 보다 1큰 숫자를 입력했다.
2. 이 숫자는 리터럴은 int 범위를 넘어가기 때문에 마지막에 L 을 붙여서 long 형을 사용해야 한다.
3. 이 경우 int 로 표현할 수 있는 범위를 넘기 때문에 다음과 같이 long int 로 형변환 하면 문제가 발생한다. ⇒ `오버플로우`

```java
maxIntOver = 2147483648L; //int 최고값 + 1
intValue = (int) maxIntOver; //변수 값 읽기
intValue = (int) 2147483648L; //형변환 시도
intValue = -2147483648
```

* 이렇게 기존 범위를 초과해서 표현하게 되면 전혀 다른 숫자가 표현되는데, 이런 현상을 `오버플로우`라한다.
* 보통 오버플로우가 발생하면 **마치 시계가 한바퀴 돈 것 처럼 다시 처음부터 시작한다.** 참고로 -2147483648 숫자는 **int 의 가장 작은 숫자**이다

> 중요한 것은 오버플로우가 발생하는 것 자체가 문제라는 점이다! 오버플로우가 발생했을 때 결과가 어떻게 되는지 계산하는데 시간을 낭비하면 안된다! 오버플로우 자체가 발생하지 않도록 막아야 한다. 이 경우 단순히 대입하는 `변수( intValue )`의 타입을 **int long 으로 변경해서 사이즈를 늘리면 오버플로우 문제가 해결된다.**

### 계산과 형변환

```java
package casting;

public class Casting4 {
 public static void main(String[] args) {
	 int div1 = 3 / 2;
	 System.out.println("div1 = " + div1); //1

	 double div2 = 3 / 2;
	 System.out.println("div2 = " + div2); //1.0
 
	 double div3 = 3.0 / 2;
	 System.out.println("div3 = " + div3); //1.5
 
	 double div4 = (double) 3 / 2;
	 System.out.println("div4 = " + div4); //1.5
 
	 int a = 3;
   int b = 2;

	 double result = (double) a / b;
	 System.out.println("result = " + result); //1.5
 }
}
```

자바에서 계산은 다음 `2가지를 기억`하자.

1. **같은 타입끼리의 계산은 같은 타입의 결과**를 낸다.

* int + int 는 `int` 를, double + double 은 `double` 의 결과가 나온다.

2. **서로 다른 타입의 계산**은 **큰 범위로** `자동 형변환`이 일어난다.

* int + long 은 long + long 으로 자동 형변환이 일어난다.
* int + double 은 double + double 로 자동 형변환이 일어난다

#### 예시

1. int

```java
int div1 = 3 / 2; //int / int
int div1 = 1; //int / int이므로 int타입으로 결과가 나온다.
```

2. double

> 3 / 2 와 같이 int 형끼리 나눗샘을 해서 소수까지 구하고 싶다면 div4 의 예제처럼 명시적 형변환을 사용하면 된다.

```java
double div2 = 3 / 2; //int / int
double div2 = 1; //int / int이므로 int타입으로 결과가 나온다.
double div2 = (double) 1; //int -> double에 대입해야 한다. 자동 형변환 발생
double div2 = 1.0; // 1(int) -> 1.0(double)로 형변환 되었다.
```

```java
double div3 = 3.0 / 2; //double / int
double div3 = 3.0 / (double) 2; //double / int이므로, double / double로 형변환이 발생한
다.
double div3 = 3.0 / 2.0; //double / double -> double이 된다.
double div3 = 1.5
```

```java
double div4 = (double) 3 / 2; //명시적 형변환을 사용했다. (double) int / int
double div4 = (double) 3 / (double) 2; //double / int이므로, double / double로 형변
환이 발생한다.
double div4 = 3.0 / 2.0; //double / double -> double이 된다.
double div4 = 1.5
```

## 7. 훈련

### Scanner

* System.out 을 통해서 출력을 했듯이, [System.in](http://system.in) 을 통해서 사용자의 입력을 받을 수 있다. 그런데 자바가 제공하는 [System.in](http://system.in) 을 통해서 사용자 입력을 받으려면 여러 과정을 거쳐야해서 복잡하고 어렵다.
* 자바는 이런 문제를 해결하기 위해 Scanner 라는 클래스를 제공한다. 이 클래스를 사용하면 사용자 입력을 매우 편리하게 받을 수 있다

```java
package Scanner;

import java.util.Scanner;

public class Scanner1 {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("input : ");
        String str = scanner.nextLine(); //입력을 String으로 가져온다.
        System.out.println("output : " +str);

        System.out.print("input int: ");
        int intValue = scanner.nextInt(); //입력을 int로 가져온다.
        System.out.println("output : " +intValue);

        System.out.print("double int: ");
        double doubleValue = scanner.nextDouble();//입력을 double로 가져온다.
        System.out.println("output : " +doubleValue);
    }
}
```

* 변수명 한 번에 바꾸기 단축키 : 드래그 후 shift + f6
