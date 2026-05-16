---
title: ⚓ DATT 01 - 공통 응답, 예외 처리, Request Logging 기반 구축
date: 2026-05-16 02:05:00 +0900
categories: [To-Be-Senior]
tags: [Java, SpringBoot, ExceptionHandling, Logging, MDC, Backend, Architecture]
---

# 개요

DATT v2 프로젝트를 새롭게 시작하면서 가장 먼저 고민한 것은 기능 구현이 아니었다.

오히려 먼저 고민한 것은:

- 응답 구조를 어떻게 통일할 것인가?
- 예외를 어떻게 처리할 것인가?
- 운영 시 로그를 어떻게 추적할 것인가?

였다.

많은 개인 프로젝트들이 기능 구현 자체에만 집중한다.

하지만 실제 운영 환경에서는 기능보다 더 중요한 것이 존재한다.

예를 들면:

- 어떤 요청이 실패했는가?
- 왜 실패했는가?
- 어떤 API에서 병목이 발생했는가?
- 어떤 요청 흐름에서 장애가 발생했는가?

같은 문제들이다.

DATT는 단순 CRUD 프로젝트가 아니라:

    "실무형 Java 백엔드 포트폴리오"

를 목표로 하고 있기 때문에, 가장 먼저 공통 응답 구조와 운영 기반을 구축하기로 했다.

이번 글에서는 다음 내용을 정리한다.

- ApiResponse 기반 공통 응답 구조
- GlobalExceptionHandler 기반 예외 처리
- ErrorCode 설계 전략
- Request Logging 구조
- MDC 기반 traceId 적용 이유

---

# 본문

# 왜 공통 응답 구조가 필요한가

초기 프로젝트에서는 보통 아래처럼 Controller마다 응답 형식이 달라지는 경우가 많다.

예:

성공 응답:

    {
        "id": 1,
        "name": "datt"
    }

실패 응답:

    {
        "message": "잘못된 요청입니다."
    }

또 다른 API:

    {
        "success": true,
        "result": {}
    }

이런 구조는 시간이 지나면 점점 유지보수가 어려워진다.

특히 프론트엔드 입장에서는:

- 어떤 필드가 항상 존재하는가?
- 에러 응답은 어떤 구조인가?
- Validation 에러는 어떻게 처리하는가?

를 API마다 다르게 처리해야 한다.

그래서 DATT에서는 모든 응답 구조를 통일하기로 했다.

---

# ApiResponse 설계

최종적으로 선택한 구조는 다음과 같다.

정상 응답:

    {
        "success": true,
        "data": {},
        "error": null
    }

실패 응답:

    {
        "success": false,
        "data": null,
        "error": {
            "code": "member.not_found",
            "message": "사용자를 찾을 수 없습니다.",
            "status": 404
        }
    }

이를 위한 공통 응답 객체는 다음과 같이 설계했다.

    public record ApiResponse<T>(
            boolean success,
            T data,
            ErrorResponse error
    ) {

        public static <T> ApiResponse<T> success(T data) {
            return new ApiResponse<>(true, data, null);
        }

        public static ApiResponse<Void> success() {
            return new ApiResponse<>(true, null, null);
        }

        public static ApiResponse<Void> fail(ErrorResponse error) {
            return new ApiResponse<>(false, null, error);
        }
    }

---

# 왜 Generic 기반으로 설계했는가

ApiResponse를 Generic으로 설계한 이유는 API마다 응답 타입이 달라지기 때문이다.

예를 들어:

회원 조회 API:

    ApiResponse<MemberResponse>

장소 조회 API:

    ApiResponse<PlaceResponse>

리스트 조회 API:

    ApiResponse<List<PlaceResponse>>

처럼 사용할 수 있다.

이 방식의 장점은 다음과 같다.

- 타입 안정성 확보
- IDE 자동완성 지원
- Swagger/OpenAPI 타입 추론 가능
- 유지보수성 향상

---

# 왜 ErrorCode를 분리했는가

처음에는 단순히 message만 내려주는 구조도 고민했다.

예:

    {
        "message": "사용자를 찾을 수 없습니다."
    }

하지만 운영 환경에서는 message만으로는 부족하다.

왜냐하면:

- 프론트 분기 처리
- 로그 검색
- 장애 추적
- 에러 유형 분류

등이 어렵기 때문이다.

그래서 ErrorCode를 별도로 분리했다.

---

# ErrorCode 설계 전략

초기에는 다음과 같은 숫자 기반 코드도 고려했다.

    MEMBER_001
    COMMON_400

하지만 이 방식은 점점 의미 파악이 어려워진다.

그래서 최종적으로는 semantic naming 방식을 선택했다.

예:

    member.not_found
    member.duplicated_email
    auth.invalid_token
    common.internal_server_error

이 방식의 장점은 다음과 같다.

- 의미 파악이 직관적
- 로그 검색이 쉬움
- 번호 충돌 없음
- 유지보수성 향상

실제 운영 환경에서도 semantic naming 기반 에러 코드가 점점 많아지는 추세다.

---

# GlobalExceptionHandler 도입 이유

초기 프로젝트에서는 보통 Controller마다 try-catch를 남발하게 된다.

예:

    try {
        ...
    } catch (...) {
        ...
    }

하지만 이 방식은:

- 중복 코드 증가
- 응답 구조 불일치
- 유지보수 난이도 증가

문제가 발생한다.

그래서 DATT에서는 GlobalExceptionHandler 기반으로 예외를 중앙 집중 처리하도록 설계했다.

핵심 구조는 다음과 같다.

    Controller
        ↓
    Service
        ↓
    Exception 발생
        ↓
    GlobalExceptionHandler
        ↓
    ApiResponse.fail()

즉:

    비즈니스 로직은 예외만 발생시키고,
    응답 생성은 GlobalExceptionHandler가 담당한다.

---

# Validation 응답을 별도로 분리한 이유

Validation 오류는 일반 예외와 성격이 다르다.

예:

    {
        "field": "email",
        "message": "이메일 형식이 올바르지 않습니다."
    }

처럼 필드 단위 정보가 필요하다.

그래서 ValidationErrorResponse를 별도로 분리했다.

예상 응답:

    {
        "success": false,
        "data": null,
        "error": {
            "code": "common.invalid_input_value",
            "message": "요청 값이 올바르지 않습니다.",
            "status": 400,
            "errors": [
                {
                    "field": "email",
                    "message": "이메일 형식이 올바르지 않습니다."
                }
            ]
        }
    }

---

# 왜 Request Logging을 먼저 구축했는가

기능 개발보다 먼저 Request Logging을 구축한 이유는 운영 관점 때문이다.

실제 운영 환경에서는:

- 어떤 요청이 느린가?
- 어떤 API에서 장애가 발생했는가?
- 어떤 요청 흐름에서 예외가 발생했는가?

를 추적할 수 있어야 한다.

그래서 DATT에서는 OncePerRequestFilter 기반으로 Request Logging을 먼저 구축했다.

예상 로그:

    event=request_completed traceId=abc123 method=GET uri=/api/health status=200 durationMs=12

---

# traceId를 적용한 이유

처음에는 단순 request log만 남길 생각이었다.

하지만 요청 흐름을 추적하려면:

    "같은 요청"

이라는 것을 식별할 수 있어야 한다.

그래서 MDC 기반 traceId를 적용했다.

핵심 흐름:

    요청 시작
        ↓
    traceId 생성
        ↓
    MDC 저장
        ↓
    로그 출력
        ↓
    요청 종료
        ↓
    MDC clear

이 구조를 통해:

- 요청 단위 로그 추적
- 장애 분석
- 요청 흐름 연결

이 가능해진다.

---

# 왜 아직 Structured Logging까지는 하지 않았는가

실무에서는 JSON 기반 Structured Logging도 많이 사용한다.

예:

    {
        "event": "request_completed",
        "traceId": "abc123",
        "durationMs": 12
    }

하지만 현재 DATT 단계에서는 아직:

- Grafana
- Loki
- ELK
- OpenTelemetry

같은 관측 시스템이 없다.

그래서 현재는:

    "단순 문자열 기반 운영 로그"

수준까지만 우선 적용했다.

추후:

- Prometheus
- Grafana
- Loki
- 분산 추적

등을 추가할 예정이다.

---

# 운영 관점에서 중요했던 부분

이번 작업에서 가장 중요하게 생각한 것은:

    "왜 이렇게 설계하는가?"

였다.

단순히:

- 예외 처리 구현
- 응답 구조 구현

이 아니라:

- 운영 시 어떤 문제가 발생하는가?
- 장애 분석은 가능한가?
- 요청 흐름 추적은 가능한가?
- 응답 구조는 일관적인가?

를 계속 고민하면서 설계했다.

---

# 마무리

이번 작업을 통해 DATT v2는 단순 CRUD 프로젝트가 아니라:

- 공통 응답 구조
- 운영 로그 기반
- 예외 처리 전략
- 요청 추적 구조

를 갖춘 형태로 발전하기 시작했다.

아직은 초기 단계지만:

    기능 구현보다
    운영 가능한 구조를 먼저 만든다

는 방향으로 프로젝트를 진행하고 있다.

앞으로는:

- JWT 인증
- Place 검색
- Review
- Redis Cache
- 성능 측정
- 병목 분석

등을 추가적으로 구축할 예정이다.

---

# 핵심 요약

이번 작업의 핵심은 다음과 같다.

- ApiResponse 기반 응답 구조 통일
- ErrorCode 기반 에러 관리
- GlobalExceptionHandler 기반 예외 처리 중앙화
- Request Logging 기반 운영 로그 구축
- MDC traceId 기반 요청 추적 구조 적용

그리고 가장 중요한 것은:

    "실무 운영 관점"

에서 구조를 고민하기 시작했다는 점이다.