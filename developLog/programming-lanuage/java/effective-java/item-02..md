# item 02. 생성자에 매개변수가 많다면 빌더를 고려하라

> 정적 팩터리와 생성자에는 똑같은 제약이 하나 있다. **선택적 매개변수가 많을 때 적절히 대응하기 어렵다**는 점이다.

## 클래스용 생성자 혹은 정적 팩토리의 모습

> 프로그래머들은 이럴 때, `점층적 생성자 패턴(telescoping constructor pattern)`을 즐겨 사용했다.

### 점층적 생성자 패턴이란?

필수 매개변수만 받는 생성자, 필수 매개변수와 선택 매개변수 1개를 받는 생성자, 선택 매개변수를 2개까지 받는 생성자... 형태로 **선택 매개변수를 전부 다 받는 생성자까지 늘려가는 방식**이다.

```java
public class NutritionFacts {
    // 필수 필드
    private final int servingSize; // (ml, 1 회 제공량)
    private final int servings;    // (회, 총 n회 제공량)

    // 선택적 필드
    private final int calories;    // (1회 제공량당)
    private final int fat;         // (g/1 회 제공량)
    private final int sodium;      // (mg/1 회 제공량)
    private final int carbohydrate;// (g/1 회 제공량)

    // 필수 필드만 받는 생성자
    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0); // 다른 생성자 호출
    }

    // 필수 필드 + 선택적 필드 (calories)
    public NutritionFacts(int servingSize, int servings, int calories) {
        this(servingSize, servings, calories, 0); // 다른 생성자 호출
    }

    // 필수 필드 + 선택적 필드 (calories, fat)
    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this(servingSize, servings, calories, fat, 0); // 다른 생성자 호출
    }

    // 필수 필드 + 선택적 필드 (calories, fat, sodium)
    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0); // 다른 생성자 호출
    }

    // 모든 필드를 받는 생성자 (최종 생성자)
    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
        this.fat = fat;
        this.sodium = sodium;
        this.carbohydrate = carbohydrate;
    }
}

```

이 클래스의 인스턴스를 만들려면 원하는 매개변수를 모두 포함한 생성자 중 <mark style="color:red;">**가장 짧은 것을 골라 호출**</mark>하면 된다.

```java
NutritionFacts cocaCola = new NutritionFacts(240, 8, 100, 0, 35, 27);
```
