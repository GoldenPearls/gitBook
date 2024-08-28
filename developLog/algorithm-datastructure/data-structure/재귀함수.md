# 재귀함수

## 1. 재귀함수

### 🍇 재귀함수란?

* 자기 자신을 다시 호출하는 함수 : **recursive\_function()**
* 특정한 함수가 자기자신을 포함
*   **재귀함수**는 내부적으로 스택 자료구조와 동일

    ⇒ 이유 : 함수를 계속 호출시 가장 마지막에 호출하기 함수가 먼저 수행을 끝내야 그 앞의 함수 호출이 종료되기 때문
* 스택 자료구조를 활용해야 하는 상당수는 **재귀 함수를 이용**해서 간편하게 구현 가능

1. 파이썬 예제

![](https://velog.velcdn.com/images/prettylee620/post/321d34d4-fcd7-4798-a87b-339b7440d1fb/image.png)

* 문자열이 무한히 출력하다가 재귀의 최대 깊이 초과시 오류가 뜸

```python
def recursive_function(): # def는 함수를 만들 때 사용하는 예약어
	print('재귀 함수를 호출합니다.')
	recursive_function()

recursive_function() 
```

### 🍇 자연수(양의 정수)

* 1은 자연수
* 자연수 n의 바로 다음 정수도 자연수이다.

### 🍇정렬알고리즘(병합, 퀵 정렬)

* 이진검색트리 활용되는 알고리즘 이다.

#### 접근 방식

1. 팩토리얼 구하기

* 음이 아닌 정수의 팩토리얼값

```java

0! = 1
n>0 이면 n! = n x (n-1)!

5!
5x4x3x2x1= 120

int result = 0
for(int i = 5; i<0;i--){
result = result * i;

}
```

#### 재귀호출(recursive call)

* 자신과 똑같은 구조의 메소드를 호출하는 방식
* 문제) 0 이상의 두정수 n, m 이 주어졌을때 n 구하시오

#### 재귀 접근

1. 상태 정의

* 재귀는 부분문제를 해결하기 위한 풀이 방법
* 부분문제는 각각 하나의 명확한 문제를 나타내어 하나의 답을 낼 수 있어야 한다.
* 답을 내는데는 입력되는 변수
* 이렇게 답을 결정하는 변수들을 `상태(state)`
* 하나의 상태에 대한 답을 찾는 문제
* 2개의 변수가 있으므로, 상태(n,m) 정의하여 표기

2. 종료 조건

* 부분 문제들을 생성하여 해결해 나가다가 언젠가는 종료해야 한다.
* 답이 나오는 상태를 검사하여 반환할 수 있도록 종료조건
* n의 0제곱을 구할때 (m=0) 답 1
* 0의 m제곱 구할때(n=0) 1
* 1의 m제곱 (n= 1) 1

```java
(n,0)  = 1
(0,m) =1
(1,m) =1
(n,m) = n*(n,m-1)
```

### 🍇 거듭 제곱 값 구하기

![](https://velog.velcdn.com/images/prettylee620/post/8b722d03-c871-4582-9487-7e30d91951bc/image.png)

```java
import java.util.Scanner;

public class powerEx {
    private static int power(int n, int m){
        if (m == 0 ) return 1;
        if (n == 0) return 1;
        if (n==0) return 1;
        return n*power(n, m-1);
    }

    public static void main(String[] args){
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        int m = in.nextInt();

        int result = power(n, m);
        System.out.printf("%d의 %d 거듭제곱 값 : %d", n, m, result);
    }
}
```

### 🍇 최대 공약수 구하기

![](https://velog.velcdn.com/images/prettylee620/post/8c81b407-4431-4c79-a572-2fab6f6f4d7c/image.png)

```java
import java.util.Scanner;

public class Gcd {

    private static int gCD(int x, int y){
        if(y == 0) return x;
        else return gCD(y, x%y);
     }

    public static void main(String[] args){
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        int m = in.nextInt();

        int result = gCD(n, m);
        System.out.printf("%d의 %d 최대공약수 : %d", n, m, result);
    }
}
```

### 🍇 하노의의 탑

> 세 개의 기둥과 원판들이 주어졌을 때, 한 기둥에서 다른 기둥으로 모든 원판을 옮기는 문제이며, 원판들은 크기에 따라 다르며, 큰 원판 위에 작은 원판이 올라가야 한다.

![](https://velog.velcdn.com/images/prettylee620/post/85c684c3-b80c-48d8-ad9d-8f87597e512f/image.png)

> 핵심 : 목표는 모든 원판을 기둥 A에서 기둥 C로 옮기는 것

1. 문제 : https://school.programmers.co.kr/learn/courses/30/lessons/12946
2. 규칙
   1. 한 번에 한 개의 원판만 이동 가능
   2. 큰 원판 위에 작은 원판 올릴 수 없음
   3. 어떤 기둥 위에 있는 원판은 그 기둥으로만 이동시킬 수 있음
3. 분석

> 결론

* 원판의 개수가 1개일 경우 : 바로 기둥 A에서 기둥 C로 옮길 수 있다.
* 원판의 개수가 2개 이상인 경우 :

1. 가장 작은 원판을 제외한 나머지 원판들을 기둥 A에서 기둥 B로 옮긴다.
2. 가장 큰 원판을 기둥 A에서 기둥 C로 옮긴다.
3. 기둥 B에 있는 원판들을 기둥 C로 옮긴다.
4. 상태(변수)

* 옮기려는 원판의 개수 n
* 원판이 현재 위치하는 기둥값 from
* 원판이 이동해야 하는 기둥 to(n, from, to)
* ex. 원판 n개를 기둥 1에서 기둥 3으로 옮겨라! (n, 1, 3)
  * (n, from, to) ⇒ 원판 n개를 기둥 from에서 기둥 to 옮기는 과정

2. 종료조건

* 원판이 1일 때 종료
* (1, form, to) = \[from, to] ⇒ 원판이 1개일 때는 from에서 to로 옮겨라

3. 점화식

* 상태 : (n, from, to)
* 종료 : (1, from, to)
* 전이상태 : (n-1, from, to)

4 . 코드

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    
private List<int[]> hanoi(int n, int from, int to){
        if(n == 1) return List.of(new int[] {from, to});  //종료조건 n==1 일때 from to 이동만 시켜라
        
        //점화식 구현
        int empty = 6 - from - to;
        
        List<int[]> result = new ArrayList<>();
        result.addAll(hanoi(n-1,from,empty));
        result.addAll(hanoi(1,from,to));
        result.addAll(hanoi(n-1,empty,to));
        return result;
               
    }
    
    
    public int[][] solution(int n) {
       return hanoi(n,1,3).toArray(new int[0][]);
    }
}
```

5. 다른 사람 코드

```java
import java.util.Arrays;

class Hanoi {
    public int[][] hanoi(int n) {
        // 2차원 배열을 완성해 주세요.
        int[][] answer = {{1,3}};

    System.out.println(n);

        return answer;
    }

    // 아래는 테스트로 출력해 보기 위한 코드입니다.
    public static void main(String[] args) {
        Hanoi h = new Hanoi();
        System.out.println(Arrays.toString(h.hanoi(2)));
    }
}
```

### 🍇 하노의 원반의 개수

* 하노이 탑의 동작원리를 알 수 있음

![](https://velog.velcdn.com/images/prettylee620/post/76ce5015-3a6c-4c52-a247-a838c7cbf097/image.png)

```java
// 하노이의 탑
package recurive;
import java.util.Scanner;

class Hanoi {
    //--- no개의 원반을 x번 기둥에서 y번 기둥으로 옮김 ---//
    static void move(int no, int x, int y) {
        if (no > 1)
            move(no - 1, x, 6 - x - y);

        System.out.printf("원반[%d]를 %d번 기둥에서 %d번 기둥으로 옮김\n", no, x, y);

        if (no > 1)
            move(no - 1, 6 - x - y, y);
    }

    public static void main(String[] args) {
        Scanner stdIn = new Scanner(System.in);

        System.out.println("하노이의 탑");
        System.out.print("원반의 개수 : ");
        int n = stdIn.nextInt();

        move(n, 1, 3);    // 제 1 기둥에 쌓인 n개를 제 3 기둥으로 옮김
    }
}
```

### 🍇 모음 사전

해설 : https://ksb-dev.tistory.com/273

* A, E, I, O, U 만을 사용하여 5자리 이내의 문자를 만들고, 정렬하여 주어지는 문자가 몇 번째인지 반환해야 함
* 첫 번째 단어는 "A"이고, 두 번째 단어는 "AA"이며, 마지막 단어는 "UUUUU"

1. 문제 : https://school.programmers.co.kr/learn/courses/30/lessons/84512
2. 분석

* 상태(word) : (word)로 시작되는 모든 단어
* 종료조건 : (길이가 5인 word) = word
* 점화식(word) =\[word]+\[word+'A']+\[word+'E']+\[word+'I']+\[word+'O']+\[word+'U']
* 종료조건 5일 때, 바로 word 반환
* 부분문제를 해결

```java
private List<String> generate(String word){ 
	//종료 조건, 점화식 구현
}

// 종료조건 5일 때, 바로 word 반환
private List<String> generate(String word){
       //종료 조건 , 점화식 구현 
      List<String> wrods = new ArrayList<>();
      words.add(word);
      if(word.length() == 5) return words;

    //점화식  
  }
```

```java
private List<String> generate(String word){
       //종료 조건 , 점화식 구현 
      List<String> wrods = new ArrayList<>();
      words.add(word);
      if(word.length() == 5) return words;

    //점화식   :사용될 수 있는 모든 문자를 words 붙여야 한다. 
      private static fianl char[] CHARS = "AEIOU".toCharArray();
    

  }
```

3. 코드

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    
    private static final char[] CHARS = "AEIOU".toCharArray();
    
    private List<String> generate(String word){
      
      List<String> words = new ArrayList<>();
      words.add(word);
      if(word.length() == 5) return words;

       for(char c : CHARS){
                  words.addAll(generate(word + c));
          }
       return words;
}
  
    
    
    
    public int solution(String word) {
       
        return generate("").indexOf(word);
    }
}
```

4. 다른 사람 풀이

```java
import java.util.*;
class Solution {
    List<String> list = new ArrayList<>();
    void dfs(String str, int len) {
        if(len > 5) return;
        list.add(str);
        for(int i = 0; i < 5; i++) dfs(str + "AEIOU".charAt(i), len + 1);
    }
    public int solution(String word) {
        dfs("", 0);
        return list.indexOf(word);
    }
}
```

```java
class Solution {
    public int solution(String word) {
        int answer = 0, per = 3905;
        for(String s : word.split("")) answer += "AEIOU".indexOf(s) * (per /= 5) + 1;
        return answer;
    }
}
```
