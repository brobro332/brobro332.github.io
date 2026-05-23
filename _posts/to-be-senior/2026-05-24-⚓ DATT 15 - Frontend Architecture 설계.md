---
title: ⚓ DATT 15 - Frontend Architecture 설계
date: 2026-05-24 00:05:00 +0900 
categories:
  - To-Be-Senior
tags: 
  - NextJS  
  - React  
  - Frontend  
  - Architecture  
  - TypeScript  
  - TailwindCSS  
  - Zustand  
  - TanStackQuery  
  - KakaoMap
---

# 개요

DATT는 다음 목표를 가진 서비스다.

```text
특정 지역을 기준으로
사용자 경험을 탐색하고
기록하며
성장할 수 있는 플랫폼
```

기존에는 Spring Boot 기반 백엔드 중심 구조였다.

즉:

```text
API 중심 MVP
```

수준에 가까웠다.

하지만 실제 서비스로 확장하기 위해서는:

```text
사용자가 직접 상호작용할 수 있는 프론트엔드
```

가 필요했다.

따라서 이번 단계에서는:

```text
Next.js 기반 Frontend Architecture
```

를 설계하게 되었다.

---

# 왜 Next.js를 선택했는가

프론트엔드 기술 선택 과정에서 다음 후보들이 있었다.

```text
React
Next.js
Vue
Nuxt
```

최종적으로는:

```text
Next.js(App Router)
```

를 선택했다.

---

## 1. React 생태계 기반

Next.js는:

```text
React 기반 프레임워크
```

다.

즉:

```text
컴포넌트 구조
상태 관리
React 생태계
```

를 그대로 활용할 수 있다.

또한 현재 시장에서는:

```text
React + Next.js
```

조합이 사실상 표준에 가깝다.

---

## 2. 라우팅 구조 내장

DATT는:

```text
장소
Anchor
프로필
지도 탐색
```

등 다양한 페이지 흐름이 존재한다.

Next.js는:

```text
파일 기반 라우팅
```

을 제공하기 때문에:

```text
app/
 ├─ places/
 ├─ anchors/
 ├─ map/
 └─ profile/
```

같은 구조를 직관적으로 설계할 수 있다.

---

## 3. App Router 구조

이번 프로젝트에서는:

```text
App Router
```

를 사용했다.

이는:

```text
layout 기반 구조
```

를 매우 쉽게 구성할 수 있기 때문이다.

예:

```text
Global Header
Global Navigation
Layout 분리
```

등을 자연스럽게 구성할 수 있다.

---

## 4. 지도 기반 서비스와 궁합

DATT는:

```text
지도 기반 탐색 서비스
```

다.

즉:

```text
동적 UI
클라이언트 인터랙션
상태 관리
```

비중이 높다.

Next.js는 이런 구조와 잘 맞는다.

특히:

```text
Kakao Map SDK
```

연동 시에도 React 기반 구조가 강력한 장점을 제공했다.

---

# Frontend Architecture 구조

현재 DATT 프론트 구조는 다음과 같다.

```text
next-js-app/
 ├─ app/
 ├─ components/
 ├─ hooks/
 ├─ layouts/
 ├─ lib/
 ├─ providers/
 ├─ services/
 ├─ stores/
 ├─ styles/
 ├─ types/
 └─ utils/
```

---

# 각 디렉토리 역할

## app

```text
페이지 라우팅 관리
```

예:

```text
/login
/places
/anchors
/map
```

---

## components

```text
재사용 UI 컴포넌트
```

예:

```text
Button
Card
Header
PlaceCard
```

---

## layouts

```text
공통 레이아웃 관리
```

예:

```text
MainLayout
```

---

## services

```text
API 호출 관리
```

예:

```text
placeService
anchorService
authService
```

---

## stores

```text
클라이언트 상태 관리
```

예:

```text
로그인 상태
유저 상태
```

---

## lib

```text
공통 라이브러리 설정
```

예:

```text
Axios Client
환경 변수
```

---

# Zustand vs Redux 선택 이유

상태 관리 라이브러리는:

```text
Redux
Zustand
```

등을 비교했다.

최종적으로는:

```text
Zustand
```

를 선택했다.

---

# 왜 Redux를 사용하지 않았는가

Redux는 매우 강력하다.

하지만 현재 DATT 규모에서는:

```text
오버엔지니어링
```

가능성이 높았다.

Redux는 보통:

```text
Action
Reducer
Slice
Dispatch
```

등의 구조가 필요하다.

즉:

```text
초기 생산성 비용
```

이 존재한다.

---

# 왜 Zustand를 선택했는가

Zustand는:

```text
매우 단순한 상태 관리
```

를 제공한다.

예:

```ts
const useAuthStore = create(...)
```

정도로 바로 사용 가능하다.

---

# 현재 Zustand 역할

현재 DATT에서는:

```text
인증 상태 관리
```

용도로 사용 중이다.

예:

```text
AccessToken
로그인 여부
유저 정보
```

등.

---

# API 상태 관리 전략

현대 React 구조에서는:

```text
클라이언트 상태
서버 상태
```

를 분리하는 것이 중요하다.

---

# Zustand는 클라이언트 상태 관리

예:

```text
모달 상태
로그인 여부
선택된 필터
```

등.

---

# TanStack Query는 서버 상태 관리

예:

```text
장소 목록 조회
Anchor 목록
프로필 조회
리뷰 목록
```

등.

---

# 왜 TanStack Query를 사용하는가

기존 방식은:

```text
useEffect
useState
axios
```

조합이 많았다.

하지만 이는:

```text
캐싱
재요청
로딩 상태
에러 처리
```

등을 직접 구현해야 했다.

TanStack Query는 이를 해결한다.

---

# TanStack Query 장점

## 1. 자동 캐싱

동일 API 재호출 최소화.

---

## 2. 서버 상태 관리 단순화

```ts
useQuery(...)
```

만으로 상태 관리 가능.

---

## 3. 로딩/에러 처리 단순화

```text
isLoading
isError
```

등 제공.

---

## 4. 무한스크롤 확장 가능

향후:

```text
Anchor 탐색
장소 탐색
```

등에서 활용 예정이다.

---

# 지도 기반 탐색 UI 설계

DATT의 핵심은:

```text
지도 기반 경험 탐색
```

이다.

즉 단순 리스트 서비스가 아니다.

---

# 핵심 UI 흐름

```text
지역 선택
→ 지도 이동
→ Nearby 탐색
→ Place 탐색
→ Anchor 탐색
→ 사용자 경험 축적
```

구조를 가진다.

---

# Kakao Map 선택 이유

한국 서비스 특성상:

```text
카카오맵
```

이 사용자 경험 측면에서 유리했다.

특히:

```text
국내 장소 데이터
지역 탐색 UX
```

강점이 존재한다.

---

# 현재 지도 구조

현재는:

```text
Kakao Map SDK 연동
```

까지만 완료된 상태다.

향후 추가 예정 기능:

```text
현재 위치 탐색
마커 표시
Nearby Search 연동
Anchor 시각화
지도 기반 필터
```

등.

---

# 현재 Frontend MVP 목표

현재 프론트엔드의 목표는:

```text
서비스 UI 기반 구축
```

이다.

즉:

```text
공통 Layout
상태 관리
API 구조
지도 연동
```

등의 기반을 먼저 안정화하고 있다.

---

# 이후 확장 예정

향후 다음 구조로 확장 예정이다.

```text
무한스크롤
Optimistic Update
SSR 최적화
SEO 대응
실시간 위치 기반 탐색
```

등.

---

# 마무리

DATT 프론트엔드는 단순 화면 구현보다:

```text
경험 탐색 플랫폼
```

이라는 방향성을 중심으로 설계하고 있다.

즉:

```text
탐험
기록
성장
지역 경험
```

을 연결하는 인터랙션 중심 구조를 목표로 한다.

현재는 MVP 단계이지만 이후:

```text
지도 기반 경험 플랫폼
```

으로 확장 가능한 구조를 목표로 개발 중이다.