---
title: ⚓ DATT 17 - Kakao Map 기반 지도 탐색 구조 설계
date: 2026-05-25 23:05:00 +0900 
categories:
    - To-Be-Senior

tags:
    - NextJS
    - React
    - TypeScript
    - KakaoMap
    - Geolocation
    - TanStackQuery
    - UX
    - Architecture
---

# 개요

DATT는 단순한 장소 검색 서비스가 아니다.

핵심은:

    "지도 기반 경험 탐색"

이다.

사용자는 단순히 장소 이름만 검색하는 것이 아니라:

- 현재 위치 주변 탐색
- 지도 기반 장소 비교
- Anchor 기반 경험 탐색
- 저장 장소 탐색

등을 수행하게 된다.

따라서 이번 단계에서는:

- Kakao Map 연동 구조
- 지도 상태 관리
- Place Marker 전략
- 지도 ↔ 검색 결과 동기화
- Geolocation 기반 현재 위치 탐색
- 지도 탐색 UX

를 중심으로 프론트엔드 구조를 설계했다.

---

# Kakao Map 연동 구조 설계

## 왜 Kakao Map을 선택했는가

DATT는 대한민국 기반 서비스다.

따라서 다음 요소들이 중요했다.

- 국내 주소 정확도
- 로컬 POI 데이터 강점
- 지도 UX 친숙도
- 모바일 환경 최적화

이 기준에서 Kakao Map SDK가 가장 적합했다.

---

## SDK 로드 전략

초기에는 페이지마다 직접 SDK를 로드하는 방식도 고려했다.

하지만 이 방식은:

- 중복 로드 위험
- 유지보수 비용 증가
- 지도 초기화 로직 분산

문제가 있었다.

따라서:

    MapContainer

컴포넌트 내부에서 SDK 로드를 공통 처리하도록 구성했다.

구조는 다음과 같다.

```tsx
useEffect(() => {
    if (window.kakao?.maps) {
        window.kakao.maps.load(createMap);
        return;
    }

    const script = document.createElement("script");

    script.src = `
        https://dapi.kakao.com/v2/maps/sdk.js
    `;

    script.onload = () => {
        window.kakao.maps.load(createMap);
    };

    document.head.appendChild(script);
}, []);
```

이 방식의 장점은:

- SDK 로드 공통화
- 지도 생성 로직 캡슐화
- 재사용 가능성 증가

이다.

---

# 지도 상태 관리 전략

## 지도 상태는 왜 서버 상태가 아닌가

지도는 서버 데이터가 아니다.

즉:

- 현재 중심 좌표
- 확대 레벨
- 선택 Marker
- 현재 위치
- Overlay 상태

등은 모두:

    클라이언트 UI 상태

에 가깝다.

따라서 지도 상태는:

- React State
- useRef
- Component Local State

기반으로 관리했다.

---

## 실제 관리 상태

```ts
const [map, setMap] =
    useState<kakao.maps.Map | null>(null);

const [selectedPlace, setSelectedPlace] =
    useState<PlaceSearchResponse | null>(null);

const [currentLocation, setCurrentLocation] =
    useState<{
        lat: number;
        lon: number;
    } | null>(null);
```

반면 Place 검색 데이터는:

```ts
usePlaceSearch()
```

를 통해 TanStack Query 기반 서버 상태로 관리한다.

즉:

    서버 상태와 지도 상태를 명확히 분리

한 것이다.

---

# 지도 기반 Place 탐색 UX 설계

## 초기 문제

초기에는:

- Place 리스트
- 지도

를 분리된 화면처럼 구성했다.

하지만 실제 탐색 UX는:

    "지도와 리스트가 동시에 움직여야 한다"

에 가까웠다.

---

## 최종 UX 방향

최종적으로는:

```text
리스트 클릭
→ 지도 이동

Marker 클릭
→ 리스트 선택 상태 변경
```

구조를 목표로 했다.

즉:

    지도 ↔ 리스트 양방향 동기화

가 핵심이었다.

---

# Place Marker 렌더링 전략

## Marker 구조

Place 검색 결과는 다음 구조를 가진다.

```ts
type PlaceSearchResponse = {
    id: number;
    bizesNm: string;
    lat: number;
    lon: number;
};
```

이를 기반으로 Marker를 생성한다.

```tsx
places.forEach((place) => {
    const marker = new kakao.maps.Marker({
        map,
        position: new kakao.maps.LatLng(
            place.lat,
            place.lon,
        ),
    });
});
```

---

## Marker 상태 관리

Marker는 React 컴포넌트가 아니다.

따라서:

```ts
useRef<kakao.maps.Marker[]>
```

기반으로 관리했다.

구조:

```tsx
markersRef.current.forEach((marker) => {
    marker.setMap(null);
});
```

이 방식으로:

- Marker 재생성
- Marker 정리
- 메모리 누수 방지

를 처리했다.

---

# 지도와 검색 결과 동기화 구조

## 핵심 문제

지도 기반 서비스에서 가장 중요한 것은:

    선택 상태 동기화

이다.

즉:

- Marker 클릭
- 리스트 클릭

모두 동일한 상태를 변경해야 한다.

---

## 공통 선택 함수 구조

따라서:

```tsx
function handlePlaceSelect(
    place: PlaceSearchResponse,
) {
    setSelectedPlace(place);

    const position =
        new kakao.maps.LatLng(
            place.lat,
            place.lon,
        );

    map.setCenter(position);
}
```

하나의 함수로:

- Overlay 변경
- 지도 이동
- 리스트 선택 상태

를 모두 처리했다.

이 방식의 장점은:

- 상태 흐름 단순화
- UX 일관성 증가
- 유지보수성 향상

이다.

---

# Overlay 기반 탐색 UX

## 왜 Overlay를 사용했는가

초기에는 지도 하단 absolute panel 방식도 고려했다.

하지만 이 방식은:

- 지도와 정보 연결감 부족
- 모바일 UX 부자연스러움
- Marker와 정보 거리 발생

문제가 있었다.

따라서:

    Marker 바로 위에 정보 표시

가 가능한 Custom Overlay 기반 구조로 변경했다.

---

## Overlay 표시 정보

Overlay에는:

- 장소명
- 카테고리
- 주소
- 상세 이동 링크

를 표시한다.

즉:

    "짧고 빠른 탐색 정보"

에 집중했다.

---

# Geolocation API 활용 전략

## 현재 위치 기반 탐색

DATT의 핵심 UX 중 하나는:

    "현재 위치 주변 탐색"

이다.

이를 위해 브라우저 Geolocation API를 활용했다.

---

## 위치 조회 구조

```tsx
navigator.geolocation.getCurrentPosition(
    (position) => {
        const lat = position.coords.latitude;
        const lon = position.coords.longitude;
    }
);
```

위치 조회 성공 시:

- 현재 위치 Marker 표시
- 지도 중심 이동

을 수행한다.

---

## 현재 위치 상태 관리

현재 위치는:

```ts
const [currentLocation, setCurrentLocation]
```

상태로 관리했다.

이 상태를 기반으로:

```tsx
<CurrentLocationMarker />
```

컴포넌트를 렌더링한다.

---

# 지도 탐색 UX 개선

## 최종 레이아웃 구조

최종적으로는:

```text
좌측
→ 지도

우측
→ 검색 결과 리스트
```

구조를 선택했다.

이 구조의 장점은:

- 지도와 결과 동시 탐색 가능
- 비교 탐색 UX 강화
- 데스크탑 활용도 증가

이다.

---

## 모바일 UX 대응

모바일에서는:

```text
지도
↓
검색 결과
```

순으로 세로 배치되도록 구성했다.

즉:

    반응형 기반 탐색 UX

를 고려했다.

---

# 앞으로의 개선 방향

현재 지도 구조는 MVP 수준이다.

추후에는:

- Cluster Marker
- 반경 탐색
- 지도 Bounds 기반 검색
- Debounce 검색
- Anchor 경로 탐색
- 지도 애니메이션
- Overlay 최적화

등을 추가할 예정이다.

---

# 마무리

이번 단계에서 가장 중요하게 생각한 것은:

    "지도는 단순 UI가 아니라
    탐색 경험 그 자체"

라는 점이었다.

특히:

- 지도 상태 분리
- Marker 관리
- Overlay UX
- 리스트 동기화

를 구조적으로 설계하면서:

    단순 지도 출력이 아닌
    "탐색 플랫폼"

에 가까운 구조를 만들 수 있었다.

이제 DATT는:

    장소 데이터 기반 서비스

를 넘어:

    지도 중심 경험 탐색 서비스

로 확장될 수 있는 기반을 갖추게 되었다.