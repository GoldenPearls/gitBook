# 무상태(stateless) 객체란

> 그 객체가 내부 상태를 가지지 않거나, 상태 정보를 유지하지 않는 객체를 의미합니다. 즉, 객체의 상태가 외부에 의존하지 않고, 매번 동일한 입력에 대해 동일한 출력을 반환하는 특성을 가지고 있습니다.

예를 들어, 수학적인 계산을 수행하는 함수는 입력값만 있으면 결과를 도출할 수 있고, 이 과정에서 내부에 어떤 상태를 저장하지 않기 때문에 무상태 객체로 볼 수 있습니다. 이러한 특성 덕분에 무상태 객체는 <mark style="color:red;">**여러 스레드에서 동시에 사용하더라도 안전하게 작동할 수 있습니다.**</mark>

반면, 상태를 가지는 객체는 그 객체의 상태에 따라 결과가 달라질 수 있기 때문에, 동시성 문제나 상태 관리가 필요하게 됩니다. 그래서 싱글턴 패턴을 사용할 때는 이러한 무상태 객체와의 조합이 유용할 수 있습니다.



무상태 객체의 간단한 예시로, 두 숫자의 합을 계산하는 함수를 정의해 보겠습니다. 이 함수는 내부 상태를 가지지 않으며, 입력값에 따라 항상 동일한 출력을 제공합니다.

```python
class StatelessCalculator:
    @staticmethod
    def add(a, b):
        return a + b

# 사용 예시
result = StatelessCalculator.add(5, 3)
print(result)  # 출력: 8
```

위 코드에서 `StatelessCalculator` 클래스는 `add`라는 정적 메서드를 가지고 있습니다. 이 메서드는 두 개의 인자를 받아서 그 합을 반환하며, 내부에 어떤 상태도 유지하지 않습니다. 따라서 언제든지 호출할 수 있고, 여러 스레드에서 동시에 사용해도 안전합니다.



> 읽어보면 좋은 링크
>
> 1. [무상태-프로그래밍-객체의-세계-외부와의-소통](https://effectiveprogramming.tistory.com/entry/%EB%AC%B4%EC%83%81%ED%83%9C-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EA%B0%9D%EC%B2%B4%EC%9D%98-%EC%84%B8%EA%B3%84-%EC%99%B8%EB%B6%80%EC%99%80%EC%9D%98-%EC%86%8C%ED%86%B5)
> 2. [스프링 컨테이너가 싱글톤을  보장하는 원리](https://effectiveprogramming.tistory.com/entry/%EB%AC%B4%EC%83%81%ED%83%9C-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EA%B0%9D%EC%B2%B4%EC%9D%98-%EC%84%B8%EA%B3%84-%EC%99%B8%EB%B6%80%EC%99%80%EC%9D%98-%EC%86%8C%ED%86%B5)

