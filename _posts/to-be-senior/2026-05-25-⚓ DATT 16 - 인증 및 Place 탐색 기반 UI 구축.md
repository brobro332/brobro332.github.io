---
title: ⚓ DATT 16 - 인증 및 Place 탐색 기반 UI 구축
date: 2026-05-25 00:05:00 +0900 
categories:
    - To-Be-Senior
tags:
    - NextJS
    - React
    - TypeScript
    - TanStackQuery
    - Zustand
    - KakaoMap
    - UX
    - Architecture
---

# 개요

DATT는 단순한 장소 검색 서비스가 아니다.

핵심 목표는:

- 장소 탐색
- 지역 기반 경험 공유
- Anchor 큐레이션
- 게이미피케이션 기반 리텐션

을 하나의 흐름으로 연결하는 것이다.

이번 프론트엔드 작업에서는:

- JWT 인증 흐름
- Place 탐색 UI
- 상태 관리 구조
- 서버 상태 관리 전략
- 지도 기반 UX 구조

를 중심으로 구조를 설계했다.

---

# 인증 흐름 설계

## 왜 JWT 기반으로 설계했는가

현재 DATT 백엔드는:

- Access Token
- Refresh Token

기반 JWT 인증 구조를 사용한다.

프론트엔드는 다음 흐름으로 동작하도록 구성했다.

    로그인 성공
    → accessToken / refreshToken 저장
    → Zustand 인증 상태 저장
    → Axios Authorization 자동 주입
    → 새로고침 시 로그인 상태 복구

---

## Zustand를 사용한 이유

전역 인증 상태는:

- 로그인 여부
- 사용자 닉네임
- Access Token 상태

정도만 관리하면 된다.

즉:

    복잡한 Flux 아키텍처
    거대한 reducer
    action 분리

까지는 필요하지 않았다.

따라서:

    간단한 인증 상태
    빠른 개발 속도
    낮은 보일러플레이트

에 강한 Zustand를 선택했다.

---

## 인증 상태 저장 구조

```ts
type AuthState = {
    accessToken: string | null;
    refreshToken: string | null;

    member: {
        memberId: number;
        nickname: string;
    } | null;

    isLoggedIn: boolean;
};
```

로그인 성공 시:

    localStorage 저장
    +
    Zustand 상태 저장

두 가지를 동시에 수행한다.

---

## 로그인 유지 처리

새로고침 시 Zustand 메모리는 초기화된다.

이를 해결하기 위해:

    AuthProvider
    restoreAuth()

구조를 도입했다.

앱 시작 시 localStorage의 토큰 및 사용자 정보를 읽어 인증 상태를 복구한다.

---

# TanStack Query 활용 전략

## 왜 React Query(TanStack Query)를 선택했는가

Place 검색 / 상세 / Bookmark는 모두:

    서버 상태(Server State)

이다.

즉:

- 캐싱 필요
- 로딩 상태 필요
- 에러 상태 필요
- 재조회 필요
- invalidate 필요

한 구조였다.

이걸 useEffect + useState로 직접 관리하면:

    로딩 상태 중복
    에러 처리 중복
    캐싱 부재
    데이터 동기화 문제

가 발생한다.

---

## 적용 구조

```ts
usePlaceSearch()
usePlaceDetail()
useAddPlaceBookmark()
useRemovePlaceBookmark()
```

형태로 API 단위 Hook을 분리했다.

특히 Bookmark는:

```ts
queryClient.invalidateQueries()
```

를 활용해:

    상세 페이지
    북마크 목록

을 자동 동기화하도록 설계했다.

---

# Place 탐색 UI 설계

## 초기 문제

초기에는 Place 검색 결과를:

    Card Grid UI

로 구성했다.

하지만 실제 데이터가 많아지자:

    한눈에 보기 어려움
    스크롤 피로 증가
    정보 밀도 부족

문제가 발생했다.

---

# 리스트 기반 UX로 변경

최종적으로는:

    1행 리스트 구조
    +
    페이지네이션

으로 변경했다.

예:

    [카테고리] 매장명
    주소
    지역 정보
    [상세]

형태.

이 구조의 장점은:

- 정보 밀도 증가
- 빠른 탐색
- 모바일 대응 용이
- 실제 지도 서비스와 유사한 UX

이다.

---

# PlaceListItem 컴포넌트 설계

기존:

    PlaceCard

를:

    PlaceListItem

으로 변경했다.

이유는:

    검색 결과는 "탐색"
    상세 페이지는 "집중"

이기 때문이다.

탐색 화면은:

    빠른 스캔
    빠른 이동
    짧은 정보

에 최적화해야 한다.

---

# 공통 Async 상태 처리

검색 페이지에서는 반복적으로:

```tsx
isLoading
isError
empty
```

분기가 발생했다.

이를 해결하기 위해:

    AsyncStateView

컴포넌트를 만들었다.

구조:

```tsx
<AsyncStateView
    isLoading={isLoading}
    isError={isError}
    isEmpty={data?.empty}
>
    ...
</AsyncStateView>
```

이 방식의 장점은:

- 페이지 코드 간결화
- 상태 UI 통일
- 재사용성 증가

이다.

---

# 지도 기반 탐색 UX 설계

DATT의 핵심은:

    지도 기반 경험 탐색

이다.

따라서 단순 리스트 서비스가 아니라:

    지도 ↔ 장소 ↔ Anchor

가 유기적으로 연결되어야 한다.

---

## Kakao Map 선택 이유

현재 대한민국 지역 데이터와 UX 측면에서:

- 주소 정확도
- 로컬 데이터 강점
- 지도 UX 친숙도

를 고려해 Kakao Map SDK를 선택했다.

---

## 지도 UX 핵심 방향

단순 핀 표시가 아니라:

    현재 위치 기반 탐색
    반경 기반 탐색
    Anchor 경로 탐색
    추천 장소 흐름 탐색

까지 확장 가능한 구조를 목표로 하고 있다.

---

# 앞으로의 방향

현재까지는:

    MVP 탐색 구조

를 구축한 상태다.

다음 단계에서는:

- Bookmark 페이지
- 리뷰 시스템
- Anchor 생성
- 지도 Overlay
- 무한스크롤
- Debounce 검색
- Skeleton UI
- 모바일 UX 개선

등을 진행할 예정이다.

---

# 마무리

이번 단계에서 가장 중요하게 생각한 것은:

    "기능 구현"보다
    "확장 가능한 구조"

였다.

특히:

- 인증 상태
- 서버 상태
- 탐색 UI
- 지도 UX

를 분리해두면서 이후 기능 확장이 훨씬 쉬워졌다.

이제부터 DATT는 단순한 장소 검색이 아니라:

    "지역 기반 경험 탐색 플랫폼"

으로 확장될 준비를 갖추게 되었다.