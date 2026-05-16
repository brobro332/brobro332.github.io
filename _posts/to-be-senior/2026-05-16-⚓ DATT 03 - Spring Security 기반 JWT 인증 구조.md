---
title: ⚓ DATT 03 - Spring Security 기반 JWT 인증 구조
date: 2026-05-16 04:10:00 +0900
categories: [To-Be-Senior]
tags:
  - Spring-Security
  - JWT
  - Authentication
  - SecurityContext
  - Backend
---

# 개요

DATT 프로젝트 1주차 3일차에서는 Spring Security 기반의 JWT 인증 구조를 구축했다.

기존 Session 기반 인증이 아닌 Stateless Authentication 구조를 선택했으며, Access Token 기반 인증 흐름과 SecurityContext 기반 인증 처리 구조를 직접 구현하였다.

이번 작업의 목표는 단순 로그인 구현이 아니라, 이후 확장 가능한 인증/인가 아키텍처의 기반을 만드는 것이었다.

---

# Stateless Authentication을 선택한 이유

기존 Spring Security는 기본적으로 Session 기반 인증 구조를 사용한다.

즉:

```text
로그인 성공
→ Session 생성
→ 서버 메모리(Session Store)에 사용자 인증 정보 저장
→ 이후 요청마다 Session 기반 인증
```

방식이다.

하지만 JWT 기반 인증에서는 서버가 인증 상태를 저장하지 않는다.

즉:

```text
로그인 성공
→ JWT 발급
→ 클라이언트 저장
→ 요청 시 JWT 전달
→ 서버는 토큰만 검증
```

구조로 동작한다.

이를 Stateless Authentication이라고 한다.

DATT에서 Stateless 구조를 선택한 이유는 다음과 같다.

- 서버 확장성 확보
- Session 저장소 의존 제거
- REST API 구조와 높은 궁합
- 모바일/SPA 환경 대응 용이
- 추후 OAuth/JWT 기반 확장 대비

특히 REST API 서버는 상태 저장을 최소화하는 방향이 일반적이기 때문에 JWT 기반 구조가 더 적합하다고 판단했다.

---

# Spring Security 설정

Spring Security 적용 후 기본적으로 모든 요청은 인증이 필요한 상태가 된다.

따라서 SecurityConfig를 통해 인증 정책을 직접 정의하였다.

```java
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http)
        throws Exception {

    return http
            .csrf(AbstractHttpConfigurer::disable)
            .sessionManagement(session ->
                    session.sessionCreationPolicy(
                            SessionCreationPolicy.STATELESS
                    )
            )
            .authorizeHttpRequests(auth -> auth
                    .requestMatchers(
                            "/api/health"
                    ).permitAll()
                    .anyRequest().authenticated()
            )
            .build();
}
```

핵심은 다음 두 가지다.

## 1. CSRF 비활성화

JWT 기반 API 서버에서는 Session을 사용하지 않기 때문에 일반적으로 CSRF를 비활성화한다.

```java
.csrf(AbstractHttpConfigurer::disable)
```

---

## 2. STATELESS 설정

```java
.sessionManagement(session ->
    session.sessionCreationPolicy(
        SessionCreationPolicy.STATELESS
    )
)
```

이 설정이 매우 중요하다.

이 설정을 통해 Spring Security에게:

```text
"Session 만들지 마라"
```

라고 명시하게 된다.

즉 이후 인증은 모두 JWT 기반으로 처리된다.

---

# JWT Provider 구현

JWT 생성 및 검증 역할은 JwtProvider에서 담당하도록 구성했다.

주요 역할은 다음과 같다.

- Access Token 생성
- JWT Claim 추출
- JWT 검증
- 사용자 ID 추출
- Role 추출

---

# 왜 @PostConstruct를 사용했는가

처음에는 constructor 내부에서 secretKey를 초기화하려 했지만 문제가 발생했다.

```java
@Value("${jwt.secret}")
private String secret;
```

필드는 객체 생성 이후 주입된다.

즉 constructor 시점에는 아직 secret 값이 존재하지 않는다.

따라서:

```java
@PostConstruct
protected void init()
```

를 사용하여:

```text
환경 변수 주입 완료 이후
→ SecretKey 초기화
```

흐름으로 구성하였다.

---

# JWT Authentication Filter 구현

JWT 인증의 핵심은 JwtAuthenticationFilter이다.

이 Filter는 모든 요청에서 JWT를 검사한다.

동작 흐름은 다음과 같다.

```text
1. Authorization Header 조회
2. Bearer Token 추출
3. JWT 검증
4. 사용자 정보 추출
5. Authentication 객체 생성
6. SecurityContext 저장
7. 이후 Controller 진입
```

---

# SecurityContext 기반 인증 처리

Spring Security는 내부적으로 SecurityContext를 사용하여 인증 상태를 관리한다.

즉:

```text
SecurityContextHolder
→ 현재 요청의 인증 정보 저장소
```

역할을 한다.

JWT Filter에서 Authentication 객체를 저장하면 이후 Controller나 Service에서 인증 정보를 사용할 수 있게 된다.

---

# AuthenticationEntryPoint 적용

JWT 인증 실패 시 응답 형식을 통일하기 위해 AuthenticationEntryPoint도 구현하였다.

기본 Spring Security 응답은 프로젝트 공통 응답 형식과 맞지 않았기 때문이다.

따라서:

```json
{
  "success": false,
  "error": {
    "code": "AUTH_401",
    "message": "인증이 필요합니다."
  }
}
```

형태로 응답을 통일하였다.

---

# 테스트 코드 기반 검증

기존에는 TestController를 통해 기능을 확인하려 했지만, 현재는 테스트 코드 기반 검증 구조로 변경하였다.

특히 JWT 구조는 HTTP 요청 흐름이 중요하기 때문에 MockMvc 기반 테스트 구조가 더 적합하다고 판단했다.

---

# 마무리

이번 작업을 통해 단순 로그인 기능이 아니라, JWT 기반 인증 아키텍처의 기본 골격을 구축할 수 있었다.

특히 다음 구조들을 직접 구현하면서 Spring Security 내부 흐름을 이해하는 데 큰 도움이 되었다.

- Stateless Authentication
- SecurityFilterChain
- JWT Claim 구조
- Authentication 객체
- SecurityContext
- AuthenticationEntryPoint
- Filter 기반 인증 처리

이후에는 회원가입/로그인 API를 연결하고 Refresh Token 및 Redis 기반 인증 구조까지 확장할 예정이다.