---
title: 📑 JAVA 02 - JVM ClassLoader와 실행 구조
date: 2026-05-14 00:01:00 +0900  
categories: To-Be-Senior
tags: [Java, JVM, ClassLoader, Reflection, Runtime, Backend]
---

# JVM ClassLoader와 Proxy 구조

# 학습 목표

이번 주제의 핵심은:

- JVM은 왜 Runtime Dynamic Loading 구조를 사용하는가?
- 부모 위임 모델은 왜 필요한가?
- 핵심 클래스를 왜 보호해야 하는가?
- Proxy는 왜 사용하는가?
- Spring은 왜 프록시 기반 프레임워크인가?
- Reflection과 Proxy는 어떤 관계인가?
- 운영 환경에서 어떤 문제가 발생하는가?

를 연결해서 이해하는 것이다.

즉:

> Java Runtime System 전체 흐름

을 이해하는 것이 목표다.

---

# JVM 실행 구조

```
flowchart TD

    A[.java Source] --> B[javac Compile]
    B --> C[.class Bytecode]
    C --> D[JVM Start]
    D --> E[ClassLoader]
    E --> F[Runtime Data Area]
    F --> G[Execution Engine]
```

Java는:

> Runtime 시점에 클래스를 로딩한다.

이게 C/C++와 가장 큰 차이 중 하나다.

---

# ClassLoader란 무엇인가?

ClassLoader는:

> `.class Bytecode를 JVM 메모리에 적재하는 객체`

다.

Java의 모든 클래스는 반드시 ClassLoader를 통해 로딩된다.

---

# JVM ClassLoader 구조

```
flowchart TD

    A[Bootstrap ClassLoader]
        --> B[Platform ClassLoader]
        --> C[Application ClassLoader]
```

---

# 1. Bootstrap ClassLoader

가장 최상위 로더.

핵심 Java 클래스를 로딩한다.

대표:

- java.lang
- java.util
- java.io

예:

```
System.out.println(String.class.getClassLoader());
```

결과:

```
null
```

Bootstrap Loader는 Native(C++) 기반이라 null로 표현된다.

---

# 2. Platform ClassLoader

Java 9 이전에는 Extension Loader였다.

역할:

> JDK 플랫폼 기능 관련 클래스 로딩

대표:

- java.sql
- java.xml
- crypto
- logging

즉:

Bootstrap은 JVM 생존 핵심,  
Platform은 플랫폼 기능 확장이다.

---

# 3. Application ClassLoader

개발자가 작성한 클래스 로딩.

```
public class Main {

    public static void main(String[] args) {
        System.out.println(
            Main.class.getClassLoader()
        );
    }
}
```

---

# 부모 위임 모델 (Parent Delegation Model)

ClassLoader의 핵심 설계다.

```
flowchart TD

    A[클래스 로딩 요청]
        --> B[부모 Loader 위임]

    B --> C{부모가 클래스 발견?}

    C -->|YES| D[부모 Loader 사용]
    C -->|NO| E[현재 Loader 로딩]
```

---

# 왜 이렇게 설계했는가?

핵심 목적은:

- 보안
- JVM 안정성
- 타입 안정성
- 클래스 중복 방지

이다.

---

# 핵심 클래스를 왜 보호해야 하는가?

만약 부모 위임이 없다면:

```
package java.lang;

public class String {
}
```

같은 fake 클래스를 만들 수 있다.

그러면 JVM은:

- 진짜 String
- 가짜 String

중 어떤 걸 써야 할지 모른다.

---

# 왜 위험한가?

## 1. JVM 신뢰성 붕괴

Java는:

- Object
- String
- Thread
- Class

같은 핵심 클래스 기반으로 동작한다.

이게 위조되면 JVM 전체 안정성이 무너질 수 있다.

---

## 2. 보안 문제

예:

```
java.security.SecurityManager
```

같은 걸 위조하면 권한 시스템 자체가 깨질 수 있다.

---

## 3. 타입 안정성 붕괴

Java는:

> "핵심 클래스는 안전하다"

를 전제로 동작한다.

핵심 클래스가 변조되면:

- equals
- hashCode
- casting
- synchronization

전부 위험해진다.

---

# 클래스 식별 기준

Java에서 클래스는:

```
클래스 이름 + ClassLoader
```

조합으로 식별된다.

즉:

같은 클래스라도 Loader가 다르면 다른 타입이다.

---

# Loading → Linking → Initialization

클래스 로딩은 단순 파일 읽기가 아니다.

```
flowchart LR

    A[Loading]
        --> B[Linking]
        --> C[Initialization]
```

---

## Loading

.class 읽기.

---

## Linking

검증 및 메모리 연결.

세부:

- Verify
- Prepare
- Resolve

Bytecode 검증이 여기서 수행된다.

---

## Initialization

정적 변수 초기화 수행.

```
static {
    System.out.println("init");
}
```

---

# Runtime Dynamic Loading

Java 핵심 특징 중 하나다.

```
Class<?> clazz =
    Class.forName(
        "com.mysql.jdbc.Driver"
    );
```

실행 시점(Runtime)에 클래스 로딩 가능.

---

# 왜 중요한가?

이 구조 덕분에 Java는:

- Spring
- JDBC
- Tomcat
- JPA Proxy
- AOP Proxy

같은 강력한 런타임 확장 구조를 가진다.

---

# Reflection

Reflection은:

> Runtime 시점에 클래스 정보를 분석/조작

하는 기술이다.

```
Class<?> clazz = User.class;

Method[] methods =
    clazz.getDeclaredMethods();
```

---

# Spring은 왜 Reflection을 사용하는가?

대표적으로:

- Bean 생성
- DI
- AOP
- Annotation 분석

때문이다.

즉:

> Spring 내부는 Reflection 기반 Runtime Framework

라고 볼 수 있다.

---

# Proxy란 무엇인가?

Proxy 핵심은:

> "실제 객체 대신 호출을 가로채는 객체"

다.

---

# 일반 호출 구조

```
flowchart TD

    A[Client]
        --> B[Real Object]
```

---

# Proxy 구조

```
flowchart TD

    A[Client]
        --> B[Proxy]
        --> C[Real Object]
```

즉:

> 중간에 대리 객체를 둔다.

---

# 왜 굳이 Proxy를 쓰는가?

핵심 이유:

> 비즈니스 로직 수정 없이 공통 기능 추가

다.

대표 공통 기능:

- 로그
- 트랜잭션
- 권한 검사
- 캐시
- 성능 측정

---

# @Transactional 실제 구조

```
@Transactional
public void order() {
}
```

실제 내부 구조:

```
sequenceDiagram

    Client->>Transaction Proxy: order()

    Transaction Proxy->>Transaction Manager: begin()

    Transaction Proxy->>Real Service: order()

    Transaction Proxy->>Transaction Manager: commit()
```

즉:

> 트랜잭션 로직을 Proxy가 대신 수행

한다.

---

# Spring이 Proxy를 사용하는 이유

Spring은:

- AOP
- Transaction
- Security
- Cache
- Async

거의 대부분 Proxy 기반이다.

즉:

> Spring은 Proxy Framework에 가깝다.

---

# Proxy 종류

# 1. 정적 Proxy

직접 클래스 작성.

```
class PaymentProxy {
}
```

문제:  
너무 귀찮다.

---

# 2. 동적 Proxy

런타임에 자동 생성.

대표:

- JDK Dynamic Proxy
- CGLIB

---

# JDK Dynamic Proxy

인터페이스 기반.

```
public interface UserService {
}
```

필요.

---

# CGLIB

상속 기반 Proxy.

```
Proxy extends UserService
```

Spring Boot는 대부분 CGLIB 사용.

---

# 실무 핵심 문제

# self invocation 문제

```
@Transactional
public void a() {
    b();
}

@Transactional
public void b() {
}
```

내부 호출:

```
b();
```

은 Proxy를 안 탄다.

즉:

> Transaction 적용 안될 수 있다.

이건 면접 단골이다.

---

# 운영 환경 핵심 문제

# ClassLoader Memory Leak

특히:

- Tomcat
- DevTools
- Hot Reload

환경에서 유명하다.

---

# 왜 발생하는가?

ClassLoader가 GC되지 못하면:

> 해당 Loader가 로딩한 클래스 전체가 GC 불가

상태가 된다.

대표 원인:

- static 객체
- ThreadLocal
- cache 객체

---

# 실제 장애 사례

재배포 반복 후:

```
java.lang.OutOfMemoryError: Metaspace
```

발생.

원인:

- 이전 WebApp ClassLoader 참조 유지
- static cache 누수

---

# 성능 관점

ClassLoader 자체는 병목이 아니다.

실제 문제는:

- reflection 남용
- excessive proxy
- startup latency 증가

다.

실제 운영 병목은 대부분:

- DB
- Network
- External API

다.

---

# Trade-off

## 장점

- 유연성
- 런타임 확장
- 동적 구조
- 공통 관심사 분리

## 단점

- startup 비용 증가
- 런타임 오류 증가
- 디버깅 어려움
- classloader leak 위험

---

# 자주 발생하는 실수

## 1. Reflection 남용

불필요한 reflection 반복.

---

## 2. self invocation

Proxy 우회 문제.

---

## 3. static 객체 누수

재배포 환경 치명적.

---

## 4. final 메서드 사용

CGLIB Proxy 불가 가능성.

---

# 면접 꼬리 질문

## Q1. 부모 위임 모델은 왜 필요한가?

핵심:

- 보안
- JVM 안정성
- Java Core 보호

---

## Q2. 핵심 클래스를 왜 보호해야 하는가?

핵심:

- 타입 안정성
- JVM 신뢰성
- 보안 유지

---

## Q3. Spring은 왜 Proxy를 사용하는가?

핵심:

- 공통 관심사 분리
- Transaction/AOP 구현

---

## Q4. self invocation 문제란?

핵심:

- 내부 호출은 Proxy를 거치지 않음

---

# 좋은 답변 예시

> JVM은 Runtime 시점에 ClassLoader를 통해 클래스를 로딩합니다.  
> 부모 위임 모델을 통해 Java 핵심 클래스를 보호하며 JVM 안정성과 타입 안정성을 유지합니다.  
> 또한 Spring은 Proxy와 Reflection 기반으로 동작하며 이를 통해 Transaction, AOP, Security 같은 공통 기능을 비즈니스 로직과 분리합니다.  
> 다만 Reflection과 Proxy 구조는 startup 비용 증가와 운영 복잡성을 유발할 수 있습니다.

---

# 관련 CS 개념 연결

- Dynamic Linking
- Runtime System
- Dependency Injection
- AOP
- Proxy Pattern
- Virtual Machine
- Plugin Architecture

---

# 현업에서 특히 중요한 포인트

## 1. Spring 내부 구조 이해

대부분 Reflection + Proxy 기반.

---

## 2. self invocation 문제

실무 장애 원인 자주 됨.

---

## 3. startup latency

MSA 환경에서 중요.

---

## 4. 재배포 Memory Leak

운영 장애 핵심 원인.

---

# 핵심 요약

- Java는 Runtime Dynamic Loading 기반
- ClassLoader는 JVM 핵심 메커니즘
- 부모 위임 모델은 JVM 안정성을 위한 구조
- Spring 내부는 Reflection + Proxy 기반
- Proxy는 공통 기능 분리를 위해 사용된다
- self invocation은 실무 단골 문제
- ClassLoader leak은 실제 운영 장애 원인이다