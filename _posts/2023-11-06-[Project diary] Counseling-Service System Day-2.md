---
title: "[Project diary] Counseling-Service System DAY-2"
excerpt: "etc.; [Project diary] Counseling-Service System DAY-2"
categories: 
- etc
tags:
- [etc]
published: true
toc: true
toc_sticky: true
---

## ✅ DAY-2 일일 목표: 서버 - JWT를 이용한 사용자 인증 구현

- Spring Security
  - Config
  - UserDetailsDTO
  - UserDetailsImpl
- JWT
  - JwtProvider
  - JwtAuthorizationFilter
  - JwtToken(DTO)
- User
  - Controller
  - Service
  - Repository

## ✅ Spring Security

### 1️⃣ Config
- 구현해야할 메서드
  - WebSecurityCustomizer: 정적 자원 및 H2-Console에 대한 스프링 시큐리티 비활성화
  - BCryptPasswordEncoder: 패스워드 암호화를 위해 Config 내에 빈 등록해야함
  - SecurityFilterChain: 
    - ❗ 애로사항: deprecated Error - 스프링 시큐리티 버전 5.2부터 Lambda를 이용하여 HTTP 보안을 구성해야함
    - csrf 비활성화: 서버에 인증정보를 저장하지 않으므로 csrf 공격에 취약하지 않음
    - STATELESS 설정: 세선쿠키 기반의 인증처리를 하지않음
    - Form 기반 로그인 비활성화: 커스텀으로 구성한 필터를 사용해야함
    - HTTP 기본 인증을 비활성화: HTTP 요청 헤더에 사용자 ID와 PW를 인코딩 및 포함시키지 않음
    - Endpoint 접근 거부 및 허용
    - JWT 필터 설정
    - H2-Console에 접속하기 위한 설정 `.headers(authorize -> authorize.frameOptions(HeadersConfigurer.FrameOptionsConfig::sameOrigin));`
    
### 2️⃣ UserDetailsDTO
- UserDetails 인터페이스에 대한 구현체
- 사용자 권한에 대한 getAuthorities 메서드를 구현해야함: 패스워드 없이 username + authorities를 통해 로그인 기능 구현할 예정

### 3️⃣ UserDetailsImpl
- UserDetailsService 인터페이스에 대한 구현체
- 사용자가 로그인하면 loadUserByUsername 메서드에서 사용자 정보를 인터셉트하여 인증을 수행

## ✅ JWT

### 1️⃣ JwtProvider
- generateJwtToken: 각 토큰들은 Jwts를 통해 빌드
  - accessToken: 서비스 이용에 있어 인증 및 권한 검증
    - claim: authorities를 추가하여 추후 로그인에 이용함
    - setExpiration: Date 객체를 생성하여 만료일자 설정 
    - signWith: 서명 키와 서명 알고리즘(HS256) 등록
  - refreshToken: accessToken 재발급을 위한 토큰으로 검증을 위한 중요한 정보는 필요하지 않음
- getAuthentication: 토큰의 사용자 이름과 Claims 헤더의 "auth" 값을 통해 Authentication 객체 생성 및 반환
  - ```java
      Collection<? extends GrantedAuthority> authorities =
          Arrays.stream(claims.get("auth").toString().split(","))
              .map(SimpleGrantedAuthority::new)
              .collect(Collectors.toList());
              ```
    - .stream(): ❗ 추후 내용 추가할 예정
    - .map(): ❗ 추후 내용 추가할 예정
    - .collect(): ❗ 추후 내용 추가할 예정
- validationToken: token의 유효성을 검증 후 예외처리
- parseClaims: accessToken의 유효성을 검사한 후 유효하면 Claims 반환

### 2️⃣ JwtAuthorizationFilter
- GenericFilterBean 인터페이스에 대한 구현체
  - doFilter: token이 유효하면 Authentication 객체를 만들어서 현재 사용자로 설정
  - resolveToken: JWT 토큰 추출
    - Authorization 헤더에는 토큰 값이 포함되어있음
    - "Bearer" 이후 문자열인 토큰값을 추출하여 반환

### 3️⃣ JwtToken

```java
@Builder
@Getter
@AllArgsConstructor
public class JwtToken {

    private String grantType;
    private String accessToken;
    private String refreshToken;
}
```                

### 4️⃣ User
- UserController
- UserService
  - join: 전형적인 회원가입 API 비즈니스 로직
  - login: Authentication 객체를 만들어서 `jwtProvider.generateJwtToken(authentication);`
- UserRepository
  - findOptionalByUsername: Optional 객체의 isPresent() 메서드를 이용하기 위함