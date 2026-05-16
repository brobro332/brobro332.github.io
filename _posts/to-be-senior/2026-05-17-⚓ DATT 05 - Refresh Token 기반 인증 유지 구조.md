---
title: ⚓ DATT 05 - Refresh Token 기반 인증 유지 구조
date: 2026-05-17 00:05:00 +0900
categories: [To-Be-Senior]
tags: [JWT, Refresh Token, Authentication, Logout, Backend, Spring Security]
toc: true
comments: true
---

# 개요

DATT 프로젝트 1주차 5일차에서는 Refresh Token 기반 인증 유지 구조를 구현하였다.

이전까지는 Access Token 기반 인증만 존재했지만, Access Token만 사용하는 구조에는 한계가 존재한다.

이번 작업에서는 다음 내용을 중심으로 구현 및 정리하였다.

- Refresh Token 도입 이유
- JWT 재발급(Reissue) 흐름
- 로그인 시 Refresh Token 저장 구조
- 로그아웃 처리 전략
- 인증 예외 세분화
- 테스트 코드 기반 검증

---

# 왜 Refresh Token이 필요한가

JWT 인증의 핵심 문제 중 하나는 Access Token 만료이다.

보안을 위해 Access Token은 일반적으로 짧은 만료 시간을 가진다.

예:

```text
Access Token: 30분
```

하지만 Access Token이 만료될 때마다 사용자가 다시 로그인해야 한다면 사용자 경험이 매우 불편해진다.

이를 해결하기 위해 Refresh Token을 사용한다.

즉 구조는 다음과 같다.

```text
로그인
→ Access Token 발급
→ Refresh Token 발급

이후 Access Token 만료
→ Refresh Token으로 재발급 요청
→ 새 Access Token 발급
```

즉 Refresh Token의 역할은:

```text
"사용자 재로그인 없이 Access Token을 재발급하기 위한 장치"
```

라고 볼 수 있다.

---

# Access Token과 Refresh Token의 차이

두 토큰은 역할이 다르다.

| 구분 | Access Token | Refresh Token |
|---|---|---|
| 목적 | API 인증 | Access Token 재발급 |
| 사용 빈도 | 매우 높음 | 낮음 |
| 만료 시간 | 짧음 | 김 |
| 서버 전송 빈도 | 모든 요청 | 재발급 시 |
| 탈취 위험 대응 | 짧은 만료로 대응 | 저장소 관리 필요 |

현재 DATT에서는:

```text
Access Token: 30분
Refresh Token: 14일
```

구조로 설정하였다.

---

# Refresh Token 저장 구조

현재 Refresh Token은 DB 기반으로 저장하도록 구현하였다.

Entity 구조:

```java
@Entity
@Table(name = "refresh_token")
public class RefreshToken extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, unique = true)
    private Long memberId;

    @Column(nullable = false, length = 500)
    private String token;

    @Column(nullable = false)
    private LocalDateTime expiredAt;
}
```

현재 정책은:

```text
회원 1명당 Refresh Token 1개
```

이다.

즉:

```text
동일 계정 재로그인 시 기존 Refresh Token 갱신
```

구조로 동작한다.

---

# 왜 DB 기반으로 먼저 구현했는가

실무에서는 Redis 기반 Refresh Token 저장 구조도 많이 사용한다.

하지만 이번 프로젝트에서는 우선 DB 기반으로 구현하였다.

이유는 다음과 같다.

- 인증 흐름 이해 우선
- 구조 단순화
- 운영 인프라 복잡도 감소
- 테스트 환경 단순화
- 인증 구조 자체 검증 목적

이후 Redis 기반 구조로 확장할 예정이다.

---

# 로그인 시 Refresh Token 발급

로그인 성공 시:

```text
1. Access Token 생성
2. Refresh Token 생성
3. Refresh Token 저장
4. 응답 반환
```

구조로 구현하였다.

핵심 코드:

```java
String accessToken = jwtProvider.createAccessToken(
        member.getId(),
        member.getRole().name()
);

String refreshToken = jwtProvider.createRefreshToken(
        member.getId()
);

saveOrUpdateRefreshToken(
        member.getId(),
        refreshToken,
        jwtProvider.getRefreshTokenExpiredAt()
);
```

최종 응답:

```json
{
  "success": true,
  "data": {
    "accessToken": "...",
    "refreshToken": "...",
    "memberId": 1,
    "nickname": "테스트유저"
  }
}
```

---

# JWT 재발급(Reissue) 흐름 분석

재발급 흐름은 다음과 같다.

```text
1. Access Token 만료
2. 서버가 401 반환
3. 클라이언트가 Refresh Token으로 재발급 요청
4. Refresh Token 검증
5. 새 Access Token 발급
```

재발급 API:

```http
POST /api/auth/reissue
```

핵심 검증 흐름:

```java
RefreshToken refreshToken =
        refreshTokenRepository.findByToken(
                request.refreshToken()
        ).orElseThrow(() ->
                new BusinessException(
                        ErrorCode.INVALID_REFRESH_TOKEN
                )
        );

if (refreshToken.isExpired()) {
    throw new BusinessException(
            ErrorCode.EXPIRED_REFRESH_TOKEN
    );
}

jwtProvider.validateToken(request.refreshToken());
```

즉 현재 구조는:

```text
DB 저장 여부 검증
→ DB 만료 검증
→ JWT 유효성 검증
```

순서로 처리된다.

---

# 왜 DB 만료를 먼저 검사했는가

처음에는 JWT 자체를 먼저 검증하였다.

하지만 테스트 과정에서 문제가 발생하였다.

```text
JWT 형식 오류
→ INVALID_TOKEN 발생
→ EXPIRED_REFRESH_TOKEN 테스트 실패
```

즉 JWT 파싱 실패가 먼저 발생해버리는 문제가 있었다.

따라서 현재는:

```text
DB 기준 만료 검사
→ JWT 검증
```

순서로 변경하였다.

이를 통해 인증 정책과 테스트 의도를 더 명확하게 맞출 수 있었다.

---

# 로그아웃 처리 전략

현재 로그아웃은:

```text
Refresh Token 삭제
```

방식으로 구현하였다.

즉:

```text
로그아웃
→ DB의 Refresh Token 삭제
→ 이후 재발급 불가
```

구조이다.

핵심 구현:

```java
RefreshToken refreshToken =
        refreshTokenRepository.findByToken(
                request.refreshToken()
        ).orElseThrow(() ->
                new BusinessException(
                        ErrorCode.INVALID_REFRESH_TOKEN
                )
        );

refreshTokenRepository.delete(refreshToken);
```

즉 현재 구조에서 로그아웃의 핵심은:

```text
"Refresh Token 무효화"
```

라고 볼 수 있다.

---

# 인증 예외 세분화

인증 관련 ErrorCode도 세분화하였다.

```java
INVALID_TOKEN
EXPIRED_TOKEN
INVALID_REFRESH_TOKEN
EXPIRED_REFRESH_TOKEN
INVALID_CREDENTIALS
```

이를 통해:

- JWT 형식 오류
- JWT 만료
- Refresh Token 없음
- Refresh Token 만료
- 로그인 실패

등을 명확하게 구분할 수 있게 되었다.

---

# 테스트 코드 기반 검증

Refresh Token 관련 로직은 모두 테스트 코드 기반으로 검증하였다.

검증 대상:

- 로그인 시 Refresh Token 저장
- 재발급 성공
- 잘못된 Refresh Token 예외
- 만료된 Refresh Token 예외
- 로그아웃 시 Refresh Token 삭제

테스트 메서드명은 BDD 스타일로 작성하였다.

```java
givenValidRefreshToken_whenReissue_thenCreateAccessToken()

givenExpiredRefreshToken_whenReissue_thenThrowException()

givenValidRefreshToken_whenLogout_thenDeleteRefreshToken()
```

---

# 마무리

이번 작업을 통해 DATT 프로젝트의 인증 구조가 단순 JWT 인증 수준에서 실제 서비스 구조에 가까워졌다.

특히 다음 내용을 직접 구현하면서 인증 유지 흐름을 깊게 이해할 수 있었다.

- Refresh Token 도입 이유
- JWT 재발급 흐름
- DB 기반 인증 유지 구조
- 로그아웃 처리 전략
- 인증 예외 세분화
- 테스트 코드 기반 검증

이후에는 Redis 기반 Refresh Token 저장 구조 및 Token Rotation 전략까지 확장할 예정이다.