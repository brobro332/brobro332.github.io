---
title: ⚓ DATT 02 - Flyway 기반 DB Migration 및 Member 도메인 초기 설계
date: 2026-05-16 03:10:00 +0900
categories: [To-Be-Senior]
tags: [Java, SpringBoot, JPA, Flyway, PostgreSQL, Backend, Architecture]
---

# 개요

DATT v2 프로젝트의 두 번째 단계에서는:

- Flyway 기반 DB Migration 관리
- BaseEntity 설계
- JPA Auditing 적용
- Member 도메인 초기 구조

를 구축했다.

이번 단계의 핵심 목표는 단순 CRUD 구현이 아니었다.

오히려:

- DB 변경 이력을 어떻게 관리할 것인가?
- Entity 공통 컬럼을 어떻게 관리할 것인가?
- 생성일/수정일을 어떻게 추적할 것인가?
- 인증 기반 사용자 구조를 어떻게 가져갈 것인가?

같은 운영 관점과 유지보수 관점을 먼저 고민했다.

특히 DATT는 단순 토이 프로젝트가 아니라:

    "실무형 Java 백엔드 포트폴리오"

를 목표로 하고 있기 때문에:

- 운영 가능한 DB 구조
- 확장 가능한 Entity 구조
- 인증 기반 사용자 모델

을 먼저 구축하는 방향으로 진행했다.

이번 글에서는 다음 내용을 정리한다.

- Flyway 기반 Migration 관리
- 왜 JPA ddl-auto 대신 Migration을 사용하는가
- BaseEntity 설계
- JPA Auditing 적용 이유
- Member 도메인 초기 구조
- Repository 테스트 기반 검증

---

# 본문

# 왜 Flyway를 도입했는가

초기 Spring Boot 프로젝트에서는 보통 다음 설정을 많이 사용한다.

```yaml
spring:
  jpa:
    hibernate:
      ddl-auto: create
```

이 방식은 Entity 기준으로 자동 테이블을 생성해준다.

초기 개발 속도는 빠르다.

하지만 운영 환경에서는 치명적인 문제가 존재한다.

예를 들면:

- 누가 언제 컬럼을 변경했는가?
- 운영 DB 스키마가 왜 달라졌는가?
- 어떤 DDL이 배포되었는가?
- 롤백은 가능한가?

같은 문제를 추적하기 어렵다.

즉:

    "DB 변경 이력"

이 남지 않는다.

그래서 DATT에서는 초반부터 Flyway 기반 Migration 관리 구조를 도입했다.

---

# Flyway 기반 Migration 구조

DATT에서는 다음 구조를 사용했다.

```text
src/main/resources/db/migration
```

Migration 파일 예시:

```text
V1__create_member_table.sql
```

Flyway는 파일 이름 규칙 기반으로 Migration 순서를 관리한다.

예:

```text
V1__create_member_table.sql
V2__add_review_table.sql
V3__add_place_index.sql
```

이 방식의 장점은 다음과 같다.

- DB 변경 이력 추적 가능
- 운영 환경 배포 안정성 확보
- 팀 협업 시 스키마 충돌 감소
- 롤백 전략 수립 가능

실무에서는 대부분:

- Flyway
- Liquibase

같은 Migration 도구를 사용한다.

---

# 왜 ddl-auto=create를 사용하지 않았는가

`ddl-auto=create`는 개발 초기에는 편하다.

하지만 운영 환경에서는 위험하다.

예를 들어:

```yaml
ddl-auto: create
```

를 잘못 운영 서버에 배포하면:

    기존 테이블이 삭제될 수도 있다.

또한:

- 컬럼 변경 이력 추적 불가
- 운영 DB 동기화 어려움
- SQL 리뷰 불가능

문제가 존재한다.

그래서 현재 DATT에서는 다음 전략을 사용한다.

```yaml
spring:
  jpa:
    hibernate:
      ddl-auto: validate
```

즉:

- 테이블 생성은 Flyway
- Entity 검증은 Hibernate

가 담당한다.

이 구조가 실무에서 가장 일반적인 패턴 중 하나다.

---

# Member 테이블 설계

초기 Member 테이블은 다음 구조로 설계했다.

```sql
CREATE TABLE member (
    id BIGSERIAL PRIMARY KEY,
    email VARCHAR(100) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    nickname VARCHAR(30) NOT NULL UNIQUE,
    role VARCHAR(20) NOT NULL DEFAULT 'USER',
    level INT NOT NULL DEFAULT 1,
    exp INT NOT NULL DEFAULT 0,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

여기서 중요하게 고려한 부분은 다음과 같다.

- 이메일 중복 방지
- 닉네임 중복 방지
- Enum 기반 권한 구조
- 게임화 시스템 기반 level/exp
- 생성/수정 시간 관리

특히:

```text
level / exp
```

는 추후 DATT의 게임화 요소를 위한 기반 컬럼이다.

---

# 왜 BaseEntity를 도입했는가

모든 Entity에는 보통 다음 컬럼이 반복된다.

- createdAt
- updatedAt

예:

```java
private LocalDateTime createdAt;
private LocalDateTime updatedAt;
```

이를 모든 Entity마다 반복 작성하면:

- 중복 코드 증가
- 유지보수 어려움
- 컬럼 일관성 깨짐

문제가 발생한다.

그래서 DATT에서는 공통 BaseEntity를 도입했다.

---

# BaseEntity 설계

구조는 다음과 같다.

```java
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class BaseEntity {

    @CreatedDate
    private LocalDateTime createdAt;

    @LastModifiedDate
    private LocalDateTime updatedAt;
}
```

핵심 포인트는:

```java
@MappedSuperclass
```

이다.

이 어노테이션은:

    공통 컬럼을 상속받는 용도

로 사용된다.

즉:

```java
public class Member extends BaseEntity
```

처럼 사용할 수 있다.

---

# 왜 JPA Auditing을 적용했는가

처음에는 다음처럼 직접 처리할 수도 있었다.

```java
member.setCreatedAt(LocalDateTime.now());
```

하지만 이 방식은:

- 누락 가능성
- 중복 코드 증가
- 실수 가능성

문제가 존재한다.

그래서 JPA Auditing을 적용했다.

활성화는 다음처럼 진행했다.

```java
@EnableJpaAuditing
```

그리고:

```java
@CreatedDate
@LastModifiedDate
```

를 사용했다.

이 방식의 장점은 다음과 같다.

- 생성일 자동 관리
- 수정일 자동 갱신
- 공통 로직 제거
- 유지보수성 향상

실무에서도 거의 표준처럼 사용된다.

---

# MemberRole Enum 설계

초기 권한 구조는 다음과 같이 설계했다.

```java
public enum MemberRole {

    USER("일반 사용자"),
    ADMIN("관리자");

    private final String description;
}
```

처음에는 단순:

```java
USER
ADMIN
```

만 사용할 수도 있었다.

하지만 description 필드를 추가한 이유는:

- 관리자 페이지
- 로그 출력
- Swagger
- 응답 DTO

등에서 활용 가능성을 고려했기 때문이다.

---

# 왜 ROLE_USER 대신 USER를 사용했는가

Spring Security에서는 보통:

```text
ROLE_USER
ROLE_ADMIN
```

형태를 많이 사용한다.

하지만 DATT에서는 우선:

```text
USER
ADMIN
```

만 저장하도록 설계했다.

이유는:

    권한 Prefix는 Security Layer에서 처리하는 것이 더 유연하기 때문이다.

예:

```java
new SimpleGrantedAuthority(
    "ROLE_" + role.name()
)
```

이런 구조가 일반적이다.

---

# Member Entity 설계 방향

Member Entity에서는:

- Setter 제거
- 정적 생성 메서드 사용
- 생성자 접근 제한

방식을 선택했다.

예:

```java
protected Member() {
}
```

```java
public static Member createUser(...)
```

이 방식의 장점은 다음과 같다.

- 무분별한 상태 변경 방지
- 생성 로직 통일
- 도메인 규칙 강제 가능

즉:

    Entity를 단순 DTO처럼 사용하지 않기 위한 설계

다.

---

# 왜 TestController 대신 RepositoryTest를 선택했는가

초기에는 다음 같은 TestController도 고려했다.

```text
GET /api/members/test
```

하지만 현재는 RepositoryTest 기반 검증을 선택했다.

이유는 다음과 같다.

- 운영 코드 오염 방지
- 테스트 목적 명확
- 자동 rollback 가능
- 실무형 테스트 구조 경험 가능

현재 구조는 다음과 같다.

```java
@SpringBootTest
@Transactional
class MemberRepositoryTest
```

그리고:

```java
@ActiveProfiles("test")
```

를 통해 테스트 전용 설정을 사용하도록 구성했다.

---

# 테스트에서 H2를 사용한 이유

테스트에서는 PostgreSQL 대신 H2를 사용했다.

예:

```yaml
spring:
  datasource:
    url: jdbc:h2:mem:datt-test
```

이유는 다음과 같다.

- 테스트 속도 향상
- 독립적인 테스트 환경
- 테스트 DB 오염 방지
- CI 환경 대응 가능

실무에서도:

- H2
- Testcontainers

등을 많이 사용한다.

---

# 테스트 명명 전략

초기에는 단순:

```java
void saveMember()
```

형태도 고려했다.

하지만 현재는:

```java
givenMember_whenSave_thenPersistMember()
```

형태를 사용한다.

즉:

```text
상황 / 행동 / 결과
```

를 메서드명에 표현한다.

이 방식의 장점은:

- 테스트 의도 명확
- 문서 역할 가능
- 유지보수성 향상

이다.

실무에서는 테스트 코드가:

    "요구사항 문서"

역할까지 수행하는 경우가 많다.

---

# 운영 관점에서 중요했던 부분

이번 작업에서 가장 중요하게 생각한 것은 다음이다.

- DB 변경 이력 추적 가능 여부
- 운영 환경 스키마 안정성
- 공통 컬럼 관리 가능 여부
- Entity 상태 변경 통제 가능 여부
- 테스트 가능한 구조인가

즉 단순 기능 구현이 아니라:

    "운영 가능한 구조"

를 먼저 만드는 데 집중했다.

---

# 마무리

이번 작업을 통해 DATT v2는:

- Flyway 기반 Migration 관리
- BaseEntity 기반 공통 컬럼 관리
- JPA Auditing 기반 자동 감사 처리
- Member 도메인 기반 인증 구조

를 갖추게 되었다.

아직은 초기 단계지만:

    운영 가능한 구조를 먼저 만든다

는 방향으로 프로젝트를 진행하고 있다.

앞으로는:

- JWT 인증
- 회원가입/로그인
- Refresh Token
- Place 검색
- Redis Cache
- 성능 측정

등을 추가적으로 구축할 예정이다.

---

# 핵심 요약

이번 작업의 핵심은 다음과 같다.

- Flyway 기반 DB Migration 관리
- ddl-auto 대신 Migration 기반 운영 구조 선택
- BaseEntity 기반 공통 컬럼 관리
- JPA Auditing 기반 자동 감사 처리
- Member 도메인 초기 구조 설계
- RepositoryTest 기반 검증 구조 구축

그리고 가장 중요한 것은:

    "운영 관점"

에서 DB와 Entity 구조를 설계하기 시작했다는 점이다.