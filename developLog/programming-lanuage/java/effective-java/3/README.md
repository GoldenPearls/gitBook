# 3장 : 모든 객체의 공통 메서드

<mark style="color:red;">Object</mark>는 객체를 만들 수 있는 구체 클래스지만 기본적으로는 상속해서 사용하 도록 설계되었다. Object에서 final이 아닌 메서드(equals, hashCode, toString, clone, finalize)는 모두 재정의(overriding)를 염두에 두고 설계된 것이라 **재정의 시 지켜야 하는 일반 규약이 명확히 정의되어 있다.**&#x20;

> 그래서 Object를 상 속하는 클래스, 즉 모든 클래스는 이 메서드들을 일반 규약에 맞게 `재정의`해야 한다.&#x20;

메서드를 잘못 구현하면 대상 클래스가 이 규약을 준수한다고 가정 하는 클래스(HashMap과 HashSet 등)를 오동작하게 만들 수 있다. 이번 장에 서는 <mark style="color:red;">final이 아닌 Object 메서드들을 언제 어떻게 재정의해야 하는지를 다룬다.</mark>
