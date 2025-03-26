# 값이 중복되는 객체를 제거할 목적으로 hash set을 쓰려면

## 개념 복습

### 🍌 Hash table

* 배열과 해시 함수를 사용하여 구현(구현체)
* key-value pair들을 저장
* 같은 키는 중복되지 않음

### 🍌 Hash set

* Hash table을 사용해서 구현
* 데이터를 키로 저장
* 같은 데이터는 중복되지 않음

## 만약 개발자가 정의한 `클래스의 객체를 키(key)`로 쓴다면 어떻게 동작할까? ⇒ 핵심 : 중복 객체 어떻게 제거하는가?

### 🍌 객체가 중복 처리 되기 전에 동작 방식

#### Python의 Set\[hash set] 예제

* 클래스 정의

```java
class Location:
	def __init__(self x, y):
		self.x = x
		self.y = y
```

* 클래스 사용 : 중복 처리가 되지 않음

```java
c1 = Location(1, 1)
c2 = Location(1, 1)

unique = {c1, c2} //set

print(len(unique)) //2
print(c1 in unique) //true
print(c2 in unique) //true
```

#### 이유?

1. 따로 설정하지 않는다면 hash function 그 변수의 **메모리 주소를 바탕**으로 해시값을 만듦

* c1과 c2의 메모리 주소는 당연히 다름 ⇒ 다른값이 나옴

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

2. 만약 모듈러 연산해서 **나온 값이 같더라도**, 그 자리에 있는 해시값을 비교해서 다르면 아 키가 다르구나 다른 키라고 판단하고 다른 곳에 저장

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

3. 만약 우연의 확률로 **두 개의 해시값이 비교해서 같더라도**, 아 해시충돌이 나서 같을 수 있으니 실제 키 c1와 c2 객체끼리 비교를 하게 됨
4. 객체끼리의 비교는 어떻게 하냐? 실제 메모리 주소를 가지고 비교를 함
5. 당연히 서로 다른 객체는 서로 다른 메모리 주소를 갖기 때문에 다르다고 봄

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

6. set에 있는지 확인하는 동작방식

* 해시값 비교
* 키 값 비교\[메모리 주소 비교]

7. **보통 객체는 서로 다르게 인식 되기 때문에 서로 다른 객체는 중복 처리가 되지 않는다.**

### 🍌 중복되는 객체는 제거되도록 처리 & 이때 동작 방식

#### 해시값 처리 - 해시 메소드 재정의

1. hash function을 구현 : 값은 해시값을 같게 하기 위해
2. 객체에 넣어주면 hash 값을 빼줌
3. hash(self.x, self.y)를 묶어주면 **하나의 객체로 인식한다.**

```java
class Location:
	def __init__(self x, y):
		self.x = x
		self.y = y

	def __hash __(self):
		return hash(self.x, self.y)
```

#### equals를 위해 처리 - 두 객체를 비교하는 메소드 재정의

4. 다른 객체와 비교할 때 나의 x좌표와 나의 y 좌표를 다른 x좌표와 y좌표와 비교해서 동일하다면 두 객체가 같다고 보자

```java
class Location:
	def __init__(self x, y):
		self.x = x
		self.y = y

	def __hash __(self):
		return hash(self.x, self.y)
	
	def __eq__(self, o):
		return self.x == o.x a and self.y==o.y
```

```java
c1 = Location(1, 1)
c2 = Location(1, 1)

unique = {c1, c2} //set

print(len(unique)) //1
print(c1 in unique) //true
print(c2 in unique) //true
```

#### 변화된 형태에서 실행

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

1. 먼저 같은 인덱스 위치로 갔다면, 해시값 비교
2. 두 번째로 재정의한 메소드로 인해 x, y 좌표 비교
3. 동일하니까 같다고 봄
4. 그 후 아무 작업도 하지 않음

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

5. c1 in unique

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 자기 자신과의 비교이기 때문에 동일하다고 봄

6. c2 in unique

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 마찬가지로 해시값과 x,y 좌표를 비교하기에 같아서, c1이 있음에도 c2 또한 있다고 판단하고 true 반환

#### 자바 예제

```java
class Location{
	public int x;
	public int y;

	public Location(int x, int y){
		this.x = x;
		this.y = y;
	
}

public int hasCode(){
	return (x + ":" + y).hasCode();
}

public boolean equals(Object o){
	Location obj = (Location) o;
	return == obj.x && y == obj.y
	
}

}

Location c1 = new Location(1, 1);
Location c2 = new Location(1, 1);

Set<Location> unique = new HashSet<>();
unique.add(c1);
unique.add(c2);

System.out.println(unique.size()); //1
System.out.println(unique.contains(c1)); //true
System.out.println(unique.contains(c2)); //true
```

### 🍌 중복 처리 관련 주의사항

#### 멤버 변수가 추가될 때

1. 이 경우 x, y 부분은 같고 z부분은 다른데 메소드 부분 수정 안해줘서 **print true**를 내게 된다.

```java
class Location:
	def __init__(self x, y, z):
		self.x = x
		self.y = y
		self.z = z

	def __hash __(self):
		return hash(self.x, self.y)
	
	def __eq__(self, o):
		return self.x == o.x a and self.y==o.y
```

```java
c1 = Location(1, 1, 1)
c2 = Location(1, 1, 10)

print(c1 == c2) //true
```

2. 확장을 했을 때 부작용이 있을지를 꼭 확인!!!!

* 확인하기 힘들다면, 3차원을 표현하는 **클래스를 따로 만들기도 함**

#### 객체를 저장한 후 멤버 변수 값 수정 시 유의할 것

```java
class Location {
    public int x;
    public int y;

    public Location(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int hashCode() {
        return (x + ":" + y).hashCode();
    }

    public boolean equals(Object o) {
        Location obj = (Location) o;
        return x == obj.x && y == obj.y;
    }
}
    Location c1 = new Location(1, 1);

    Set<Location> unique = new HashSet<>();
    unique.add(c1);

    System.out.println(unique.contains(c1)); //true

    c1.x = 10;

    System.out.println(unique.contains(c1)); //false
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 자기 자신과의 비교라 true 리턴

<figure><img src="../../.gitbook/assets/image (13) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 생판 다른 값이 나오게 됨

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

* 만약, 우연의 결과를 해시값이 같게 나온다면, true가 나오긴 함

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

#### c1이 있는데 x 값을 바꿨다고 false 나오는 건 이상함

* `final` 키워드를 넣어서 미연의 방지하는 것도 좋은 방법
* 자바는 컴파일 언어라 컴파일이 안됨

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>
