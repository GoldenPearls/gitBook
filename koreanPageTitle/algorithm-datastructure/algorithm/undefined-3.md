# 버블 정렬, 삽입 정렬, 선택 정렬

## 정렬 알고리즘

> 참고 동작 방식 링크 : https://visualgo.net/en/sorting

### 🍇 정의

* 어떤 데이터들이 주어졌을때, 이를 정해진 순서대로 나열하는 것
* 뭔가를 `정렬`하는 것
  * A부터 Z까지 기준으로 정렬
  * 큰 수에서 작은 수 기준으로 정렬
* 데이터를 특정한 기준에 따라서 순서대로 나열하는 것
* 프로그램 작성 시 가장 많이 사용
* `이진검색`처럼 빠른 알고리즘 사용을 위해서
* 알고리즘의 `효율성`을 쉽게 이해 가능
* 내림차순은 오름차순은 **reverse**를 이용해서 뒤집으면 됨

### 🍇 버블 정렬

* 두 인접한 데이터를 비교해서 , **앞에 있는 데이터가 뒤에 있는 데이터보다 크면, 자리를 바꾸는** 알고리즘
* 자주 사용되지는 않음 더 빠른 알고리즘이 많기 때문이다.

#### 정렬방법

* 배열의 2개의 아이템을 `선택`
* `비교(comparisons)` : **왼쪽**이 오른쪽보다 **크면** `교환(swap)`
*   오른쪽으로 `이동`해서, 해당 프로세스 `반복` , 예를 들어 5와 6을 비교

    ⇒ 왼쪽이 오른쪽보다 작으므로 교환하지 않음

1. 그 다음은 6과 3을 비교 ⇒ 교환 **결과** : 2 5 3 6 1 4
2. 6과 1을 비교 ⇒ 교환 **결과** : 2 5 3 1 6 4
3. 6과 4를 비교 ⇒ 교환 **결과** : 2 5 3 1 4 6 ⇒ 첫번째 사이클 끝 두번째도 왼쪽에서 처음부터 바꿈

![](https://velog.velcdn.com/images/prettylee620/post/24c2746c-813b-40b2-ab58-faffd2907fc7/image.png)

#### ✨ 버블정렬의 시간복잡도

1.  **comparisons(비교)** : `N-1`

    N(item의 마지막 번호), 배열의 N-1의 아이템을 비교

    EX. 아이템이 6개면, 5번 비교
2. **swap(교환)**: 최악의 경우, 모든 아이템을 교환해야 함
3. 그래프

![](https://velog.velcdn.com/images/prettylee620/post/87f518fc-a9d7-465a-a676-504ae5eddbca/image.png)

#### ✨ 버블정렬 예제 1

> 버블 정렬은 간단하지만 큰 리스트에 대해서는 비효율적일 수 있으니 참고

```java
import java.util.ArrayList;
import java.util.Collections;

public class BubbleSorting {

    public static void main(String[] args){
        ArrayList<Integer> dataList = new ArrayList<Integer>();
        dataList.add(9);
        dataList.add(2);
        dataList.add(4);
        dataList.add(8);
        dataList.add(1);

        // 9, 7, 1
        for(int index=0; index < dataList.size()-1; index++) {
            boolean swap = false;
            // 7, 9, 1
            for(int index2 = 0; index2 < dataList.size() - 1 - index; index2++) {
                if (dataList.get(index2) > dataList.get(index2 + 1)) {
                    Collections.swap(dataList, index2, index2 + 1);
                    // 1, 7, 9
                    swap = true;
                }
            }
        }
        System.out.println(dataList);
    }
}
```

#### ✨ 버블정렬 예제 2

* 기존 코드의 클래스를 이용해서 만드는 방법

1. BubbleSorting2

```java
package sorting;

import java.util.ArrayList;
import java.util.Collections;

public class BubbleSorting2
{

	public void sort(ArrayList arr) {
		ArrayList<Integer> dataList = new ArrayList<Integer>();
		dataList = arr;
		
		for (int index = 0; index < dataList.size() - 1; index++)
		{
			boolean swap = false;

			for (int index2 = 0; index2 < dataList.size() - 1 - index; index2++)
			{
				if (dataList.get(index2) > dataList.get(index2 + 1))
				{
					Collections.swap(dataList, index2, index2 + 1);
					swap = true;
				}
			}
			System.out.println(dataList);
		}
	}

}
```

2. BublleSorting

```java
import java.util.ArrayList;

public class BubbleSorting
{

    public static void main(String[] args)
    {
        ArrayList<Integer> testList = new ArrayList<Integer>();

        for (int index = 0; index < 10; index++)
        {
            testList.add((int)(Math.random() * 100));

        }
        System.out.println(testList);

        BubbleSorting2 bSort = new BubbleSorting2();

        bSort.sort(testList);

    }

}
```

#### ✨ 버블정렬 예제 3

1. 문제링크 : https://jungol.co.kr/problem/1157?cursor=eyJwcm9ibGVtc2V0IjoiNiIsImZpZWxkIjo2LCJpZHgiOjJ9
2. 코드

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Scanner;

public class BubbleSorting3 {

    private ArrayList<Integer> sort(ArrayList<Integer> dataList){

        for(int index=0; index < dataList.size()-1; index++) {
            boolean swap = false;

            for(int index2 = 0; index2 < dataList.size() - 1 - index; index2++) {

                if (dataList.get(index2) > dataList.get(index2 + 1)) {

                    Collections.swap(dataList, index2, index2 + 1);
                    swap = true;
                }

            }

            for(int i = 0; i<dataList.size(); i++) {
                System.out.print(dataList.get(i)+ " ");
            }
            System.out.println();

        }
        return dataList;
    }

    public static void main(String[] args){
        Scanner in = new Scanner(System.in);
        ArrayList<Integer> dataList = new ArrayList<Integer>();

        int n = in.nextInt();

        for(int i=0; i<n; i++){
            dataList.add(in.nextInt());
        }

        BubbleSorting3 bubbleSort = new BubbleSorting3();
        bubbleSort.sort(dataList);

    }
}
```

3. 다른 사람 코드

```java
import java.util.Scanner;

public class Main {

   public static void main(String[] args) {
       Scanner sc = new Scanner(System.in);
        int numlength = sc.nextInt();
        int [] arr = new int [numlength];
       
       for (int i = 0; i < numlength; i++) {
           arr[i] = sc.nextInt();
       }
       
        
       for (int i = 0; i < numlength-1; i++) {
    	   
    	   
           for (int j = 0; j < numlength-1; j++) {
        	   
               if (arr[j] > arr[j+1])
               {
                   int temp = arr[j];
                   arr[j]=arr[j+1];
                   arr[j+1]=temp;
                   
               }
               
           }
              
           for (int j = 0; j < numlength; j++) {
               System.out.printf("%d ", arr[j]);
           }
           System.out.println();
           
       
       
       
           }
       
       }
       
}
```

4. 파이썬 코드

```java
def bubble_sort(arr):
    end = len(arr) - 1
    while end > 0:
        last_swap = 0
        for i in range(end):
            if arr[i] > arr[i + 1]:
                arr[i], arr[i + 1] = arr[i + 1], arr[i]
                last_swap = i
        end = last_swap
        print(arr)
```

### 🍇 선택정렬

* 전체 **모든 아이템 스캔**
* 데이터가 무작위로 여러 개 있을 때, 이중에서 **가장 작은 데이터를 선택**해 맨 앞에 있는 데이터와 바꾸고, 그다음 작은 데이터를 선택해 앞에서 두 번째 데이터와 바꾸는 과정 **반복**
* 현재 **데이터의 상태 상관없이** 무조건 모든 원소를 비교하고 위치를 바꿈
* `가장 작은 것`을 선택, `비효율적`

#### ✨ 정렬방법

1. 전체 아이템 중 **가장 작은 아이템의 위치**를 그 위치를 변수에 저장
2. 5부터 차례로 확인 후 맨 처음은 **5가 제일 작기때문에** 위치를 변수에 저장
3. 그 다음 2에서 **2가 5보다 작으니까** 위치를 변수에 저장
4. 쭉 하다가 **1이 2보다 작으니까** 위치를 변수 저장
5.  4까지 확인 후 사이클을 끝냄

    ⇒ **배열에서 가장 작은 숫자**가 어디에 있는지 안다
6. 그 다음 `swaps(바꾸기)` : **가장 작은 숫자(위치**를 알지!) 그것을 **첫 번째 아이템과** 바꿈
7. 그 다음 사이클을 진행시 1부터 시작하지 않음. **1은** `정렬`된 숫자, **정렬되지 않은** 부분 중에서 가장 작은 숫자를 찾음
8. 이것을 반복

![](https://velog.velcdn.com/images/prettylee620/post/d502821c-893b-44ff-99fb-2594dea9ca84/image.png)

#### ✨ 선택정렬 예제1

```java
import java.util.ArrayList;
import java.util.Collections;

public class SelectionSortingEx {

    private ArrayList<Integer> sort(ArrayList<Integer> dataList){
        int lowest;
        for(int stand = 0; stand < dataList.size()-1; stand++){
            lowest = stand;
            for(int index = stand+1; index < dataList.size(); index++){
                if(dataList.get(lowest) > dataList.get(index)) {
                    lowest = index;
                }
            }
            Collections.swap(dataList, lowest, stand);
        }

        return dataList;
    }

    public static void main(String[] args){
        ArrayList<Integer> testData = new ArrayList<Integer>();

        for(int i=0; i<10; i++){
            testData.add((int)(Math.random()*100));
        }
        SelectionSortingEx selection = new SelectionSortingEx();
        System.out.println(selection.sort(testData));
    }
}
```

#### ✨ 선택정렬 예제 2

1. 정올 문제 : https://jungol.co.kr/problem/1146/submission?cursor=eyJwcm9ibGVtc2V0IjoiNiIsImZpZWxkIjo2LCJpZHgiOjB9
2. 코드

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Scanner;

public class SelectionSortingEx2 {

    private ArrayList<Integer> sort(ArrayList<Integer> dataList){
        int lowest;
        for(int stand = 0; stand < dataList.size()-1; stand++){
            lowest = stand;
            for(int index = stand+1; index < dataList.size(); index++){
                if(dataList.get(lowest) > dataList.get(index)) {
                    lowest = index;
                }
            }
            Collections.swap(dataList, lowest, stand);
            for(int i = 0; i<dataList.size(); i++) {
                System.out.print(dataList.get(i)+ " ");
            }
            System.out.println();
        }

        return dataList;
    }

    public static void main(String[] args){
        Scanner in = new Scanner(System.in);
        ArrayList<Integer> testData = new ArrayList<Integer>();

        int n = in.nextInt();

        for(int i=0; i<n; i++){
            testData.add(in.nextInt());
        }
        SelectionSortingEx2 selection = new SelectionSortingEx2();
        selection.sort(testData);
    }
}
```

3. 선택 정렬 다른 사람 코드

```java
import java.util.Scanner;
 
public class Main {
 
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        int i, j, n;
        int arr[] = new int [101];
        int big = 0;
         
        n = scan.nextInt();
         
        for (i = 0; i < n; i++) {
            arr[i] = scan.nextInt();
        }
         
        for (i = 0; i < n-1; i++) {
            int small = arr[i];
            int pos = i;
            for (j = i; j < n; j++) {
                if (small > arr[j]) {
                    small = arr[j];
                    pos = j;
                }
            }
            int c = arr[i];
            arr[i] = arr[pos];
            arr[pos] = c;
                     
            for (j = 0; j < n; j++) {
                System.out.printf("%d ", arr[j]);
            }
            System.out.printf("\n");
         
        }
    }
 
}

```

4. 파이썬 코드

```java
def selection_sort(arr):
    for i in range(len(arr) - 1):
        min_idx = i
        for j in range(i + 1, len(arr)):
            if arr[j] < arr[min_idx]:
                min_idx = j
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
        print(arr)
```

### 🍇 삽입 정렬

* **필요한 아이템만** 스캔
* 선택정렬보다 빠름, 효율적
* 데이터를 하나씩 확인하며, 각 데이터를 적절한 위치에 삽입

⇒ 필요한 때만 위치를 바꾸므로 **데이터가 거의 정렬되어 있을 때** 훨씬 효율적

*   특정한 데이터를 적절한 위치에 ‘삽입’한다는 의미

    ⇒ 특정한 데이터가 적절한 위치에 들어가기 이전에, **그 앞까지의 데이터는 이미 `정렬`되어 있다**고 가정

#### 정렬방법

1.  인덱스 1부터 시작시, **왼쪽에** 숫자 ‘2’보다 큰 숫자가 있는지 확인

    ⇒ 두 번째 데이터 부터 시작하는 이유는 **첫 번째는 그 자체로 정렬되어 있다**고 판단

    ```
    Left       >   Right
    ```
2. 2와 5 자리 바꾸기 `swap`
3.  2번째 사이클 : **6을 선택**, 왼쪽에 더 큰 숫자가 있는지 확인, 오른쪽이 더 크기 때문에 계속 진행

    ```
                        Left       <   Right
    ```
4.  3번째 사이클 : **3을 선택**, 왼쪽에 더 큰 숫자가 있는지 확인,

    ```
                                            Left      >   Right
    ```
5. 6과 3을 `swap`
6.  3 왼쪽에 있는 5를 비교(comparisons)

    ```
                         Left      >   Right
    ```
7. 5과 3을 `swap`
8. 3에 왼쪽에 있는 2는 3보다 작기 때문에 안바꿈
9. 반복

> ❗ 삽입 정렬은, 정렬이 이루어진 원소는 항상 `오름차순`을 유지하기 때문에, 특정한 데이터가 **삽입될 위치를 선정할 때**(삽입될 위치를 찾기 위하여 왼쪽으로 한 칸씩 이동할 때) 삽입될 데이터보다 작은 데이터를 만나면 그 위치에서 멈추면 된다.

> 💡 특정한 데이터의 **왼쪽에 있는 데이터들은 이미 정렬이 된 상태**이므로 자기보다 작은 데이터를 만났다면, 더 이상 데이터 살펴볼 필요 없이 삽입하면 됨

#### ✨ 삽입정렬 코드

```java
import java.util.ArrayList;
import java.util.Collections;

public class InsertionSort {

    public ArrayList<Integer> sort(ArrayList<Integer> dataList){
        for(int index=0; index<dataList.size()-1; index++){
            for(int index2 = index + 1; index2 > 0; index2--){
                if(dataList.get(index2) < dataList.get(index2-1)){
                    Collections.swap(dataList, index2, index2-1);
                }
                else{
                    break;
                }
            }
        }
        return dataList;
    }

    public static void main(String[] args) {
        ArrayList<Integer> dataList = new ArrayList<Integer>();

        for (int i = 0; i < 10; i++) {
            dataList.add((int) (Math.random() * 100));
        }
        InsertionSort insertion = new InsertionSort();
        System.out.println(insertion.sort(dataList));
    }
}
```

#### ✨ 삽입정렬 정올 예제

1. 문제링크 : https://jungol.co.kr/problem/1158?cursor=eyJwcm9ibGVtc2V0IjoiNiIsImZpZWxkIjo2LCJpZHgiOjF9
2. 코드

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Scanner;

public class Main {

    public ArrayList<Integer> sort(ArrayList<Integer> dataList){
        for(int index=0; index<dataList.size()-1; index++){
            for(int index2 = index + 1; index2 > 0; index2--){
                if(dataList.get(index2) < dataList.get(index2-1)){
                    Collections.swap(dataList, index2, index2-1);
                }
                else{
                    break;
                }
            }
            for(int i = 0; i<dataList.size(); i++) {
                System.out.print(dataList.get(i)+ " ");
            }
            System.out.println();
        }
        return dataList;
    }

    public static void main(String[] args) {
        ArrayList<Integer> dataList = new ArrayList<Integer>();
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();

        for (int i = 0; i < n; i++) {
            dataList.add(in.nextInt());
        }
        Main insertion = new Main();
        insertion.sort(dataList);
    }
}
```

3. 다른 사람 코드

```java
import java.util.Scanner;
 
public class Main {
 
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        Scanner scan = new Scanner(System.in);
         
        int n, i, j;
        int arr[] = new int[101];
         
        n = scan.nextInt();
         
        for (i = 0; i < n; i++) {
            arr[i] = scan.nextInt();
        }
         
        for (i = 1; i < n; i++) {
            for (j = i; j > 0; j--) {
                if (arr[j] < arr[j - 1])
                {
                    int c = arr[j];
                    arr[j]=arr[j-1];
                    arr[j-1]=c;
                }
                else break;
            }
            for (j = 0; j < n; j++) {
                System.out.printf("%d ", arr[j]);
            }
            System.out.printf("\n");
        } 
    }
}
```

#### ✨ 예제

```python
array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]

for i in range(1, len(array)):
    for j in range(i, 0, -1): # j변수가 인덱스 i부터 1까지 1씩 감소하면서 반복하는 문법
        if array[j] < array[j-1]: # 한 칸 씩 왼쪽으로 이동
            array[j], array[j-1] = array[j-1], array[j] 
        else: # 자기보다 작은 데이터를 만나면 그 위치에서 멈춤
            break
print(array)
```

> 💡 range의 세 번째 변수 : range의 매개변수는 3개`(start, end, step)`이다. 세 번째 매개변수인 step에 -1이 들어가면 start인덱스부터 시작해서 end+1인덱스까지 1씩 감소한다.

#### ✨ 삽입정렬의 시간복잡도

*   시간복잡도 : $`O(N^2)`$

    ⇒ **2중 반복문**을 이용하기 때문

![](https://velog.velcdn.com/images/prettylee620/post/596fedfe-8e75-40a3-a975-08e13cd66aba/image.png)

⇒ 선택정렬보다 빨라도 시간복잡도는 `같음`

⇒ 삽입 정렬은 현재 리스트의 데이터가 거의 정렬되어 있는 상태라면 매우 빠르게 동작

> ✨ 시간복잡도가 **동일한 이유**는 최악의 시나리오를 보지말고, **평균** 시나리오를 봐야 하기 때문

## 삽입 정렬, 버블 정렬, 선택 정렬 세 가지 비교

1. 버블 정렬

* 맨 처음에 있는 것을 저장 후 다음 있는 것을 비교 해서 다음 것이 지금 있는 것보다 적다면 그 수를 swap함 ⇒ 이런식으로 반복

```java
public static void bubbleSort(int[] array) {
    int n = array.length;
    for (int i = 0; i < n - 1; i++) {    // 모든 요소를 순회
        for (int j = 0; j < n - i - 1; j++) {  // i까지는 이미 정렬되었으므로 비교할 필요가 없음
            if (array[j] > array[j + 1]) {    // 다음 요소가 현재 요소보다 작으면 교환
                int temp = array[j];
                array[j] = array[j + 1];
                array[j + 1] = temp;
            }
        }
    }
}
```

* 파이썬 코드

```java
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(0, n-i-1):
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]

# 사용 예시
arr = [5, 3, 8, 2, 1, 4]
bubble_sort(arr)
print("버블 정렬 결과:", arr)
```

2. 삽입 정렬

* 모든 데이터를 훑어봄 일단, **가장 작은 데이터를 선택**해 맨 앞에 있는 데이터와 바꾸고, 그다음 작은 데이터를 선택해 앞에서 두 번째 데이터와 바꾸는 과정 **반복**

```java
public static void insertionSort(int[] array) {
    int n = array.length;
    for (int i = 1; i < n; i++) {   // 두 번째 요소부터 시작
        int key = array[i];         // 현재 요소를 저장
        int j = i - 1;
        while (j >= 0 && array[j] > key) {   // 왼쪽의 모든 요소와 비교하여 key보다 큰 값은 오른쪽으로 이동
            array[j + 1] = array[j];
            j--;
        }
        array[j + 1] = key;     // key 값을 적절한 위치에 삽입
    }
}
```

* 파이썬 코드

```java
def insertion_sort(arr):
    n = len(arr)
    for i in range(1, n):
        key = arr[i]
        j = i - 1
        while j >= 0 and key < arr[j]:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = key

# 사용 예시
arr = [5, 3, 8, 2, 1, 4]
insertion_sort(arr)
print("삽입 정렬 결과:", arr)
```

3. 선택 정렬

* 필요할 때만 바꿔서 효율적
* 두 번째 즉, 1인덱스부터 훑어봄 왜냐 첫 번째꺼는 정렬되어 있다고 생각하니까
* 그리고 두번째가 왼쪽을 바라보고 왼쪽이 더 크다면 바꿈
* 이런식으로 적절한 위치에 넣기 때문에 거의 정렬되어 있다고 판단되는 것에 좋음

```java
public static void selectionSort(int[] array) {
    int n = array.length;
    for (int i = 0; i < n - 1; i++) {    // 맨 마지막 요소는 자동으로 정렬되므로 n-1까지만 순회
        int minIndex = i;
        for (int j = i + 1; j < n; j++) {    // i 이후의 요소들과 비교하여 최솟값의 위치를 찾음
            if (array[j] < array[minIndex]) {
                minIndex = j;
            }
        }
        int temp = array[minIndex];     // 최솟값을 현재 위치로 이동
        array[minIndex] = array[i];
        array[i] = temp;
    }
}
```

* 파이썬 코드

```java
def selection_sort(arr):
    n = len(arr)
    for i in range(n):
        min_index = i
        for j in range(i+1, n):
            if arr[j] < arr[min_index]:
                min_index = j
        arr[i], arr[min_index] = arr[min_index], arr[i]

# 사용 예시
arr = [5, 3, 8, 2, 1, 4]
selection_sort(arr)
print("선택 정렬 결과:", arr)
```
