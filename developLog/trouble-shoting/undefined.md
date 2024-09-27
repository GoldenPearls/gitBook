---
icon: arrow-right-from-bracket
---

# 로그 파일 관련

{% hint style="info" %}
이걸 찾게 된 계기는.. 9/26일 이펙티브 자바 스터디에서 logback- sleth에 대한 이야기를 하셔서 내가 근무하는 곳에서는 어떻게 로그파일을 관리하는 지 알고 싶었음
{% endhint %}

{% embed url="https://spring.io/projects/spring-cloud-sleuth" %}

## 1. Logback Seluth 이란?

**Logback**은 자바 애플리케이션에서 널리 사용되는 **로깅 프레임워크**입니다. 이전의 log4j를 개선한 것으로, 성능과 유연성이 뛰어나고 설정이 간단하여 많은 개발자들이 선호합니다.

**Spring Cloud Sleuth**는 **Spring Cloud**에서 제공하는 **분산 추적(distributed tracing)** 도구입니다. 마이크로서비스 아키텍처에서 서비스 간의 호출 관계를 추적하고 모니터링할 수 있도록 해줍니다. Sleuth는 각 요청에 대해 **trace ID**와 **span ID**를 생성하여, 로그에 자동으로 포함시킵니다.

**Logback과 Spring Cloud Sleuth를 함께 사용하면**, 애플리케이션의 로그에 trace ID와 span ID가 자동으로 추가되어 **분산된 시스템에서의 요청 흐름을 추적**할 수 있습니다. 이를 통해 문제 발생 시 어느 서비스에서 문제가 생겼는지 빠르게 파악하고, 성능 병목 지점을 식별할 수 있습니다.



## 2. 비교: 현재의 log4j 설정과 Logback에 Sleuth를 적용하여 OpenSearch로 traceId로 추적하는 방식

### **1. 질문 요약**

* **현재 상황:** 제공해주신 log4j 설정 파일을 보면, **운영(prd), 로컬(local), 스테이징(stg)** 환경 모두에서 `ConsoleAppender`를 사용하고 있습니다.
* **비교 대상:** 이전에 말씀하신 **Logback에 Sleuth를 적용하여 OpenSearch를 사용하고, traceId로 추적하는 방식**과의 차이점을 알고 싶으시며, **패턴**이 무엇인지 궁금하십니다.

***

### **2. 현재의 log4j 설정 분석**

**2.1 공통점**

* **Appender:** 모든 환경에서 `ConsoleAppender`를 사용하고 있습니다.
*   **로그 패턴:** `<ConversionPattern>` 요소에서 로그의 출력 형식을 정의하고 있습니다.

    ```xml
    <param name="ConversionPattern" value="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p [%c:%M() %L] %m %n" />
    ```
* **로거 설정:** 특정 패키지나 클래스에 대한 로그 레벨과 Appender를 지정하고 있습니다.
* **루트 로거 설정:** `<root>` 요소를 통해 기본 로그 레벨과 Appender를 지정합니다.

**2.2 환경별 차이점**

* **로컬(local):**
  * `org.springframework` 패키지의 로그 레벨이 `INFO`로 설정되어 있습니다.
  * `java.sql`과 `jdbc.sqltiming`의 로그 레벨이 `DEBUG`로 설정되어 있어 SQL 관련 디버깅 정보를 확인할 수 있습니다.
* **운영(prd) 및 테스트 :**
  * 대부분의 로그 레벨이 `ERROR`로 설정되어 있어, 에러 상황만 로그에 출력됩니다.
  * `org.springframework`, `org.apache.commons` 등의 패키지에 대한 로그 레벨을 세부적으로 지정하고 있습니다.

***

### **3. 로그 패턴 설명**

로그 패턴은 `<ConversionPattern>` 요소에서 정의되며, 로그 메시지의 출력 형식을 결정합니다.

```xml
<param name="ConversionPattern" value="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p [%c:%M() %L] %m %n" />
```

**패턴 설명:**

* `%d{yyyy-MM-dd HH:mm:ss}`: 로그 발생 시간 (예: 2023-10-25 14:30:45)
* `[%t]`: 스레드 이름 (예: \[main])
* `%-5p`: 로그 레벨을 왼쪽 정렬로 5칸 확보 (예: INFO )
* `[%c:%M() %L]`: 로그를 발생시킨 클래스의 이름, 메서드 이름, 라인 번호
  * `%c`: 클래스 이름 (예: com.example.MyClass)
  * `%M()`: 메서드 이름 (예: myMethod())
  * `%L`: 라인 번호 (예: 123)
* `%m`: 로그 메시지
* `%n`: 개행 문자 (줄바꿈)

**예시 로그 출력:**

```
2023-10-25 14:30:45 [main] INFO  [com.example.MyClass:myMethod() 123] 로그 메시지 내용
```

***

### **4. 이전에 말씀하신 Logback + Sleuth + OpenSearch 방식과의 비교**

**4.1 로깅 프레임워크의 차이**

* **현재 설정:** **log4j**를 사용하고 있습니다.
* **비교 대상:** **Logback**을 사용하고 있습니다.
  * Logback은 log4j의 후속으로 개발된 로깅 프레임워크로, 성능과 기능 면에서 개선되었습니다.
  * Spring Boot에서는 기본적으로 Logback을 사용합니다.

**4.2 분산 추적 기능의 유무**

* **현재 설정:** **Spring Cloud Sleuth**를 적용하지 않았습니다.
  * 따라서 로그에 **traceId**나 **spanId**와 같은 분산 추적 정보를 포함하고 있지 않습니다.
* **비교 대상:** **Spring Cloud Sleuth**를 적용하여 **traceId**와 **spanId**를 로그에 포함하고 있습니다.
  * 분산 시스템에서 요청의 흐름을 추적할 수 있습니다.

**4.3 로그 출력 및 수집 방식**

* **현재 설정:** **ConsoleAppender**를 사용하여 로그를 **콘솔에 출력**합니다.
  * 운영 환경에서도 ConsoleAppender를 사용하고 있어, 로그가 콘솔에만 출력되고 **중앙 로그 관리 시스템으로 전송되지 않습니다**.
  * 로그 유실의 위험이 있으며, 로그 분석과 모니터링이 어렵습니다.
* **비교 대상:** 로그를 **OpenSearch**로 전송하여 **중앙에서 수집하고 관리**합니다.
  * 로그 수집기를 통해 로그를 OpenSearch로 전송하고, OpenSearch Dashboards를 통해 로그를 시각화하고 분석합니다.
  * 로그에 포함된 **traceId**를 기반으로 요청의 흐름을 추적할 수 있습니다.

**4.4 로그 패턴의 차이**

* **현재 설정:** 로그 패턴에 **traceId**나 **spanId**가 포함되어 있지 않습니다.
* **비교 대상:** 로그 패턴에 **traceId**와 **spanId**를 포함하여, 각 로그 메시지가 어떤 요청에 속하는지 알 수 있습니다.

**예시 로그 패턴 (Logback + Sleuth):**

```xml
<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg %X{traceId} %X{spanId}%n</pattern>
```

**패턴 설명:**

* `%X{traceId}`: MDC(Mapped Diagnostic Context)에 저장된 `traceId`를 출력합니다.
* `%X{spanId}`: `spanId`를 출력합니다.

**예시 로그 출력:**

```
2023-10-25 14:30:45.123 [main] INFO  com.example.MyClass - 로그 메시지 내용 1234567890abcdef 1234abcd
```

**4.5 운영 환경에서의 적합성**

* **현재 설정:** 운영 환경에서 ConsoleAppender를 사용하여 로그 관리에 어려움이 있습니다.
* **비교 대상:** 운영 환경에서 로그를 중앙 집중식으로 관리하여 로그 유실을 방지하고, 모니터링 및 디버깅에 효과적입니다.

***

### **5. 결론 및 추천 사항**

**5.1 현재 설정의 문제점**

* **운영 환경에서 ConsoleAppender 사용:**
  * 로그가 콘솔에만 출력되어 **로그 유실의 위험**이 있습니다.
  * 로그를 중앙에서 관리하지 못해 **모니터링 및 문제 해결이 어렵습니다**.
* **분산 추적 기능의 부재:**
  * 마이크로서비스 환경에서 **요청의 흐름을 추적하기 어렵습니다**.
  * 문제가 발생했을 때 원인 파악에 시간이 더 소요됩니다.

**5.2 개선 방안**

* **로깅 프레임워크 업그레이드:**
  * **Logback**으로 전환을 고려해보세요. Spring Boot를 사용한다면 기본적으로 Logback이 사용됩니다.
* **Spring Cloud Sleuth 적용:**
  * 애플리케이션에 **Spring Cloud Sleuth**를 추가하여 **traceId**와 **spanId**를 로그에 포함시키세요.
* **로그 수집 및 관리 시스템 도입:**
  * **OpenSearch**와 같은 중앙 집중식 로그 관리 시스템을 도입하여 로그를 수집하고 분석하세요.
  * 로그 수집기(Filebeat, Logstash 등)를 사용하여 로그를 OpenSearch로 전송합니다.
* **운영 환경에서의 Appender 변경:**
  * 운영 환경에서는 `ConsoleAppender` 대신 **`FileAppender`**, **`RollingFileAppender`**, **`SocketAppender`** 등을 사용하여 로그를 파일로 저장하거나 로그 수집 시스템으로 전송하세요.
* **로그 패턴 수정:**
  * 로그 패턴에 **traceId**와 **spanId**를 포함하여 분산 추적이 가능하도록 합니다.
  *   예:

      ```xml
      <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg traceId=%X{traceId} spanId=%X{spanId}%n</pattern>
      ```

***

### **6. 추가 설명: 패턴의 역할과 중요성**

**로그 패턴**은 로그 메시지의 형식을 결정하며, 로그를 분석하고 모니터링하는 데 중요한 역할을 합니다.

* **구성 요소:**
  * **시간 정보:** `%d{패턴}`으로 로그 발생 시각을 표시합니다.
  * **스레드 정보:** `[%t]`로 로그를 발생시킨 스레드 이름을 표시합니다.
  * **로그 레벨:** `%-5p`로 로그의 심각도를 표시합니다.
  * **클래스 및 메서드 정보:** `[%c:%M() %L]`로 로그를 발생시킨 클래스, 메서드, 라인 번호를 표시합니다.
  * **메시지 내용:** `%m`으로 실제 로그 메시지를 표시합니다.
  * **추가 정보:** `%X{key}`로 MDC에 저장된 값을 표시합니다. 예를 들어, `%X{traceId}`는 `traceId`를 출력합니다.
* **패턴의 중요성:**
  * 로그 메시지에 필요한 정보가 포함되어 있어야 문제 발생 시 원인 파악이 쉽습니다.
  * 분산 시스템에서는 **traceId**와 같은 추적 정보가 로그에 포함되어 있어야 요청의 흐름을 추적할 수 있습니다.

***

### **7. 요약**

* **현재의 log4j 설정**은 모든 환경에서 `ConsoleAppender`를 사용하고 있으며, 로그 패턴에 traceId 등이 포함되어 있지 않아 운영 환경에서 로그 관리에 어려움이 있습니다.
* **Logback에 Sleuth를 적용하여 OpenSearch로 traceId로 추적하는 방식**은 로그에 traceId와 spanId를 포함하고, 로그를 중앙에서 수집하여 분산된 시스템의 요청 흐름을 추적하고 로그를 효율적으로 관리할 수 있습니다.
* **추천 사항:** 운영 환경에서는 `ConsoleAppender` 대신 파일이나 중앙 로그 관리 시스템으로 로그를 전송하는 Appender를 사용하고, 로그 패턴에 traceId 등을 포함시키며, Logback과 Sleuth를 활용하여 로그 관리와 분산 추적 기능을 강화하시길 권장합니다.

***

## 3. 그렇다면 ELK란 뭘까?

**ELK 스택**은 **Elasticsearch**, **Logstash**, **Kibana**의 첫 글자를 딴 용어로, 로그 관리와 분석을 위한 강력한 오픈 소스 도구 모음입니다.

* **Elasticsearch**: 분산형 검색 및 분석 엔진으로, 대용량 데이터의 실시간 검색과 분석이 가능합니다.
* **Logstash**: 데이터 처리 파이프라인 도구로, 다양한 소스에서 데이터를 수집하고 변환하여 Elasticsearch로 전달합니다.
* **Kibana**: Elasticsearch 데이터의 시각화 도구로, 대시보드와 그래프 등을 통해 데이터를 분석하고 모니터링할 수 있습니다.

**ELK 스택을 활용하면**, 애플리케이션에서 발생하는 로그를 중앙에서 수집하고, 실시간으로 검색 및 분석할 수 있습니다. Logback과 Spring Cloud Sleuth를 통해 생성된 로그에 포함된 trace ID를 이용하여, 분산된 서비스 간의 요청 흐름을 Kibana에서 시각화하고 추적할 수 있습니다.

## 4. 오픈 트레이싱이란?

**OpenTracing**은 **분산 추적을 위한 오픈 소스 표준 인터페이스**입니다. 다양한 분산 추적 시스템과 애플리케이션 간의 호환성을 높이기 위해 정의된 API 사양입니다.

* **목적**: 개발자들이 특정 분산 추적 솔루션에 종속되지 않고, 애플리케이션에 추적 기능을 쉽게 추가할 수 있도록 합니다.
* **지원 언어 및 프레임워크**: OpenTracing은 자바, Go, Python 등 여러 언어를 지원하며, 다양한 프레임워크와 라이브러리에 통합되어 있습니다.
* **사용 방법**: OpenTracing API를 사용하여 애플리케이션 코드에 추적 포인트를 삽입하면, 백엔드에서 실제 추적 데이터를 수집하고 분석할 수 있습니다.

**OpenTracing을 활용하면**, 마이크로서비스 아키텍처에서 서비스 간 호출과 지연 시간 등을 추적하여, 시스템의 성능을 최적화하고 문제를 진단할 수 있습니다.

## 5. 실제 사용은?

### 스터디에서 들은 분의 이야기

오픈 서치로 써서 로그  검색 후 traceId를 찾으면 그걸로 다시 전체 검색해서 사용 중이라고 했음

대신 zipkin ui이나 다른 모니터링이랑 같이 쓰지 않고 오픈 서치로 로그 확인 하신다고 했음

{% hint style="info" %}
로그를 **OpenSearch**로 전송하여 **중앙에서 수집하고 관리하기에 편리하다는 것**
{% endhint %}
