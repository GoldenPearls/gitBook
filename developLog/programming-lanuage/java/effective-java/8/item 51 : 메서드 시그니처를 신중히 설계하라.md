# item 51 : 메서드 시그니처를 신중히 설계하라

## 1. 개별 아이템으로 두기 애매한 API 설계 요령

### 1) 메서드 이름을 신중히 짓자.

> 변수명 지어주는 사이트 : [https://www.curioustore.com/#!/](https://www.curioustore.com/#!/)

<figure><img src="../../../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

> 항상 표준 명명 규칙을 따라야 한다.&#x20;

#### 자바 표준 명명 규칙

1. **클래스와 인터페이스 (Class & Interface)**:
   * **대문자로 시작하며 각 단어의 첫 글자를 대문자로 표기**한다 (PascalCase).
   * 클래스 이름은 보통 **명사** 또는 **명사구**로 표현한다.
   * 예: `Person`, `DataManager`, `HttpRequestHandler`.
2. **메서드 (Method)**:
   * **소문자로 시작하며 각 단어의 첫 글자는 대문자**로 표기한다 (camelCase).
   * 메서드 이름은 **동사** 또는 **동사구**로 작성하여, 메서드가 수행하는 동작을 명확히 설명해야 한다.
   * 예: `calculateTotal()`, `fetchUserData()`, `isUserLoggedIn()`.
3. **변수 (Variable)**:
   * **소문자로 시작하며 각 단어의 첫 글자는 대문자**로 표기한다 (camelCase).
   * 변수 이름은 가능한 구체적이고 명확하게, 변수의 목적을 설명하도록 작성한다.
   * 예: `userName`, `accountBalance`, `maxRetryCount`.
4. **상수 (Constant)**:
   * **모든 글자를 대문자**로 표기하며 단어 간에 밑줄 (`_`)을 사용한다 (SNAKE\_CASE).
   * 예: `MAX_BUFFER_SIZE`, `DEFAULT_TIMEOUT`.
5. **패키지 (Package)**:
   * **모두 소문자**로 표기하며 회사 도메인을 뒤집은 형태로 시작한다.
   * 예: `com.example.app`, `org.openai.utils`.

#### 메서드 이름의 예시와 부가설명

1. **동사 또는 동사구로 명확히 표현**:
   * 예: `save()`, `load()`, `calculateInterest()`.
   * 이처럼 메서드 이름은 그 메서드가 무슨 작업을 하는지 명확하게 나타내야 함
2. **부울 값을 반환하는 메서드는 `is`로 시작**:
   * 예: `isValid()`, `isEmpty()`, `isUserActive()`.
   * 부울 값을 반환하는 메서드의 경우 `is`를 접두사로 붙이는 것이 일반적
3. **설명적인 이름으로 의미 명확히 하기**:
   * 예: `getCustomerData()`, `sendEmailNotification()`.
   * 긴 이름을 피해야 하지만, 너무 짧고 애매한 이름도 피해야 합니다. 적절하게 설명적이면서 이해할 수 있는 이름을 사용하는 것이 중요
4. **자바 라이브러리의 명명 규칙과 일관성 유지**:
   * 예: 자바의 컬렉션 메서드들은 `add()`, `remove()`, `clear()`, `contains()`와 같은 이름을 사용한다.
   * 이처럼 널리 사용되는 메서드 이름을 참조함으로써 일관성을 유지하고 다른 개발자들이 쉽게 이해할 수 있게 된다.

#### 명명 규칙 예시

*   **좋은 예시**:

    ```java
    public void sendEmailNotification(String email) {
        // 이메일 알림을 보낸다.
    }

    public boolean isAccountActive() {
        // 계정이 활성화되어 있는지 여부를 반환한다.
    }

    public double calculateTotalPrice(List<Item> items) {
        // 아이템의 총 가격을 계산한다.
    }
    ```
*   **나쁜 예시**:

    ```java
    public void sE(String e) {
        // 너무 짧고 모호하다. 메서드가 무슨 일을 하는지 전혀 알 수 없다.
    }

    public void performAction() {
        // 메서드가 어떤 동작을 수행하는지 알 수 없다. 메서드 이름이 너무 일반적이다.
    }

    public boolean accountStatus() {
        // 부울 값을 반환하지만, `is` 접두사가 없어 의미가 명확하지 않다.
    }
    ```

#### 요약

* **명확하고 일관성 있게** 메서드 이름을 짓는 것이 중요하다. 이해할 수 있고, 같은 패키지에 속한 다른 이름들과 일관되게 짓는 게  최우선 목표다.
* 메서드 이름은 **무엇을 하는지 쉽게 이해할 수 있도록** 지어야 하며, **동사 형태**로 시작하여 동작을 설명해야 합니다.
* 긴 이름은 피하자.&#x20;
* **자바 표준 명명 규칙**을 따르고, 필요시 **자바 API 가이드**를 참조하여 개발자 커뮤니티에서 널리 받아들여지는 방식으로 이름을 짓는 것이 좋다.

### 2) **메서드의 소임과 직교성 (Orthogonality)**

> 편의 메서드를 너무 많이 만들지 말자.

* **각 메서드는 자신의 역할에만 충실해야 한다**: 많은 기능을 하나의 메서드에 넣으면 복잡해지고 이해하기 어려워지므로, **명확한 단일 책임 원칙**을 지켜야 한다.
* **메서드가 너무 많은 클래스와 인터페이스는 피해야 한다**: 유지보수와 사용이 어렵기 때문에 필요한 최소한의 기능을 메서드로 정의해야 한다.

```java
// 나쁜 예시 - 여러 기능을 하나의 메서드에 몰아넣음
public void manageOrderAndSendReceipt(Order order) {
    // 주문 관리
    processOrder(order);
    // 영수증 발송
    sendReceipt(order);
}

// 좋은 예시 - 메서드가 각각의 소임을 다함
public void processOrder(Order order) {
    // 주문 처리 로직
}

public void sendReceipt(Order order) {
    // 영수증 발송 로직
}

```

> 직교란 수학에서 온 용어로, 서로 직각（90。）을 이루며 교차 한다는 뜻이다. <mark style="color:red;">수학의 관점에서 직교하는 요소들은 서로 독립적이다.</mark>

* **직교성(Orthogonality)**: 기능 간 독립성을 높여 메서드 수를 줄이는 효과를 기대할 수 있다. 기능을 잘게 나누어 **필요한 기본 기능들만 제공**하면, 그 기능들을 조합하여 더 복잡한 동작을 구현할 수 있다. 예시로 `List`의 `subList()`와 `indexOf()` 메서드를 들 수 있다.

```java
List<String> list = Arrays.asList("a", "b", "c", "d", "e");
List<String> subList = list.subList(1, 4);  // ["b", "c", "d"]
int index = subList.indexOf("c");           // 1
```

“직교성이 높다”라고 하면 “공통점이 없는 기능들이 잘 분리되어 있다” 혹은 “기능을 원자적으로 쪼개 제공한다” 정도로 해석할 수 있다. 예를 들어 본문의 List 설명에서 ‘부분리스트 얻기’와 ‘주어진 원소의 인덱스 구하기’는 서로 관련이 없다. 따라서 두 기능을 개별 메서드로 제공해야 직교성이 높다고 할 수 있다.

### **3) 매개변수 목록 유지**

<figure><img src="../../../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

* **매개변수는 4개 이하로 유지**하는 것이 좋다. 매개변수가 많아지면 기억하기 어렵고 사용자가 혼동할 수 있다.
* **같은 타입의 매개변수가 여러 개 연달아 나오는 경우**는 특히 피해야 한다. 매개변수의 순서를 잘못 입력할 가능성이 있기 때문이다.

```java
// 나쁜 예시 - 매개변수가 너무 많음
public void createUser(String firstName, String lastName, String email, String phoneNumber, String address, String city, String zipCode) {
    // 사용자 생성 로직
}

// 좋은 예시 - 매개변수 수를 줄이기 위해 도우미 클래스를 사용
public void createUser(UserInfo userInfo) {
    // 사용자 생성 로직
}

// 도우미 클래스
public class UserInfo {
    private String firstName;
    private String lastName;
    private String email;
    private String phoneNumber;
    private String address;
    private String city;
    private String zipCode;
    
    // 생성자, 게터, 세터 등을 정의
}

```

### **4) 매개변수 수 줄이는 세 가지 기술**

<figure><img src="../../../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

1. **메서드 나누기**: 메서드를 여러 개로 쪼개 각각이 원래 매개변수 목록의 부분집합을 처리하도록 한다. 이렇게 하면 메서드 수는 늘어날 수 있지만 <mark style="color:red;">기능 간의 독립성을 높여 유지보수를 쉽게 한다.</mark> java.util. List 인터페이스가 좋은 예다.

```java
// 나쁜 예시 - 모든 기능을 하나의 메서드로 처리
public void processPayment(String cardNumber, String expiryDate, double amount) {
    // 결제 처리 로직
}

// 좋은 예시 - 메서드를 나눠서 책임 분리
public void validateCard(String cardNumber, String expiryDate) {
    // 카드 유효성 검사
}

public void executePayment(double amount) {
    // 결제 실행
}

```

2. **도우미 클래스 활용**: **매개변수 여러 개를 묶어주는 도우미 클래스를 만들기**. 일반적으로 이런 도우미 클래스는 `정적 멤버 클래스`로 둔다. 특히 잇따른 매개변수 몇 개를 독립된 하나의 개념으로 볼 수 있을 때 추천하는 기법이다. 예를 들어 카드의 숫자와 무늬를 묶어 하나의 매개변수로 만들면 API와 클래스 구현이 깔끔해진다.

```java
// 도우미 클래스를 활용해 매개변수를 묶기
public class CardInfo {
    private String cardNumber;
    private String expiryDate;

    // 생성자와 게터를 정의
}

public void processPayment(CardInfo cardInfo, double amount) {
    // 결제 처리 로직
}

```

3. **빌더 패턴 응용**: 메서드 호출에서도 빌더 패턴을 응용하여 많은 매개변수를 처리한다. 먼저 모든 매개변수를 하나로 추상화한 객체를 정의하고, 클라이언트에서 이 객체의 세터(setter) 메서드를 호출해 필요한 값을 설정하게 하는 것이다.

* 이때 각 세터 메서드는 매개변수 하나 혹은 서로 연관된 몇 개만 설정하게 한다.&#x20;
* 클라이언트는 먼저 필요한 매개변수를 다 설정한 다음, execute 메서드를 호출해 앞서 설정한 매 개변수들의 유효성을 검사한다. 마지막으로, 설정이 완료된 객체를 넘겨 원하 는 계산을 수행한다.

```java
public class Payment {
    private String cardNumber;
    private String expiryDate;
    private double amount;

    public static class Builder {
        private String cardNumber;
        private String expiryDate;
        private double amount;

        public Builder cardNumber(String cardNumber) {
            this.cardNumber = cardNumber;
            return this;
        }

        public Builder expiryDate(String expiryDate) {
            this.expiryDate = expiryDate;
            return this;
        }

        public Builder amount(double amount) {
            this.amount = amount;
            return this;
        }

        public Payment build() {
            Payment payment = new Payment();
            payment.cardNumber = this.cardNumber;
            payment.expiryDate = this.expiryDate;
            payment.amount = this.amount;
            return payment;
        }
    }
}

// 사용 예시
Payment payment = new Payment.Builder()
                        .cardNumber("1234-5678-9012-3456")
                        .expiryDate("12/24")
                        .amount(100.0)
                        .build();

```

### **5) 매개변수 타입 선택**

* **클래스 대신 인터페이스 사용**: 매개변수 타입으로 인터페이스를 사용하는 것이 클래스보다 유연하다. 예를 들어 `Map` 인터페이스를 사용하면 여러 구현체(`HashMap`, `TreeMap` 등)를 사용할 수 있다.

```java
// 나쁜 예시 - 특정 구현체에 의존
public void processData(HashMap<String, String> data) {
    // 데이터 처리 로직
}

// 좋은 예시 - 인터페이스 사용
public void processData(Map<String, String> data) {
    // 데이터 처리 로직
}

```

* **boolean 대신 열거 타입 사용**: 가능하면 **boolean 대신 2개 이상의 값을 가지는 열거형(enum)을 사용**합니다. 열거형은 코드의 의미를 명확히 전달할 수 있고, 선택지를 쉽게 확장할 수 있다.

```java
public enum TemperatureScale { FAHRENHEIT, CELSIUS, KELVIN }

// 나쁜 예시
public Thermometer newInstance(boolean isCelsius) {
    // 섭씨 온도계를 반환
}

// 좋은 예시 - 열거형 사용
public Thermometer newInstance(TemperatureScale scale) {
    // 주어진 온도 단위에 맞는 온도계를 반환
}

```

### 6) **API 설계 원칙**

* **편의성을 위한 고수준 기능 제공은 신중히**: 자주 사용되는 조합이나 성능 최적화를 위한 고수준 기능을 별도로 제공할 수 있지만, 필요 이상의 고수준 기능 추가는 API를 복잡하게 만들 수 있다.
* **직교성(Orthogonality)을 고려한 설계**는 테스트와 유지보수를 용이하게 한다. 기능 간에 서로 독립적이도록 잘 나누는 것이 핵심이다.



