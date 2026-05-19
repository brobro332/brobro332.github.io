---
title: ⚓ DATT 09 - 좌표 기반 Nearby Search API 설계 및 위치 기반 검색 시스템 구현
date: 2026-05-20 00:05:00 +0900
categories: [To-Be-Senior]
tags:
- SpringBoot 
- QueryDSL 
- Haversine 
- GeoLocation  
- NearbySearch  
- PostgreSQL  
---

# 개요

DATT는 단순 장소 검색 서비스가 아니다.

핵심 목표는:

```text
특정 위치를 기준으로
주변 경험을 큐레이션하는 플랫폼
```

을 만드는 것이다.

즉:

```text
현재 위치
→ 주변 장소 탐색
→ Anchor 생성
→ 친구 공유
```

흐름을 가진다.

따라서:

```text
위치 기반 검색
```

은 DATT의 핵심 기능 중 하나다.

이번 단계에서는:

```text
Nearby Search API
```

를 구현하며:

```text
좌표 기반 반경 검색
거리 계산
거리순 정렬
```

구조를 설계하였다.

---

# 왜 위치 기반 검색이 중요한가

기존 장소 검색은 대부분:

```text
키워드
카테고리
지역명
```

기반이다.

하지만 실제 사용자 경험은 다르다.

예를 들어 사용자는:

```text
"지금 내 주변 카페"
"현재 위치 기준 술집"
"강남역 근처 놀거리"
```

를 더 많이 찾는다.

즉 검색 기준이:

```text
문자열
→ 좌표
```

로 이동한다.

DATT의 Anchor 기능 역시:

```text
특정 위치 기준
→ 주변 맛집/술집/숙소/놀거리 큐레이션
```

구조이므로 위치 기반 검색이 반드시 필요하다.

---

# Nearby Search API 설계

이번 단계에서 설계한 API는 다음과 같다.

```http
GET /api/places/nearby
```

---

# 요청 파라미터

```text
lon
lat
radiusKm
keyword
indsMclsCd
page
size
```

---

# 요청 예시

```http
GET /api/places/nearby?lon=127.0276&lat=37.4979&radiusKm=3
```

---

# 업종 필터 포함

```http
GET /api/places/nearby?lon=127.0276&lat=37.4979&radiusKm=3&indsMclsCd=I212
```

---

# 키워드 포함

```http
GET /api/places/nearby?lon=127.0276&lat=37.4979&radiusKm=3&keyword=스타벅스
```

---

# Nearby 검색 정책

현재 MVP 기준 Nearby 검색 정책은 다음과 같다.

```text
반경 내 장소만 조회
거리순 정렬
업종 필터 optional
키워드 검색 optional
Pageable 지원
```

즉 기본 정렬은:

```text
distance ASC
```

고정이다.

---

# 위치 기반 검색 시스템의 핵심 개념

위치 기반 검색 시스템에서 가장 중요한 것은:

```text
두 좌표 사이 거리 계산
```

이다.

즉:

```text
현재 위치 ↔ 장소 위치
```

사이 거리를 계산해야 한다.

---

# 좌표 데이터

위치 데이터는 보통 다음 형태를 가진다.

```text
위도(latitude)
경도(longitude)
```

예:

```text
lat = 37.4979
lon = 127.0276
```

---

# 위치 기반 검색 핵심 흐름

전체 흐름은 다음과 같다.

```text
현재 위치 입력
→ 각 장소와 거리 계산
→ 반경 내 장소 필터링
→ 거리순 정렬
→ 반환
```

즉 핵심은:

```text
거리 계산 + 반경 필터링
```

이다.

---

# Haversine Formula 기반 거리 계산 구현

현재 MVP에서는:

```text
Haversine Formula
```

를 사용하였다.

---

# Haversine Formula란?

지구는 완전한 평면이 아니다.

따라서 단순 피타고라스 거리 계산으로는 정확도가 떨어진다.

Haversine Formula는:

```text
구면 거리 계산 공식
```

으로 위도/경도 기반 실제 거리를 계산할 수 있다.

---

# 거리 계산 유틸 구현

```java
public final class DistanceCalculator {

    private static final double EARTH_RADIUS_KM = 6371.0;

    private DistanceCalculator() {
    }

    public static double calculateDistanceKm(
        double baseLat,
        double baseLon,
        double targetLat,
        double targetLon
    ) {
        double baseLatRad = Math.toRadians(baseLat);
        double targetLatRad = Math.toRadians(targetLat);

        double deltaLat = Math.toRadians(targetLat - baseLat);
        double deltaLon = Math.toRadians(targetLon - baseLon);

        double a = Math.sin(deltaLat / 2) * Math.sin(deltaLat / 2)
            + Math.cos(baseLatRad)
            * Math.cos(targetLatRad)
            * Math.sin(deltaLon / 2)
            * Math.sin(deltaLon / 2);

        double c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));

        return EARTH_RADIUS_KM * c;
    }
}
```

---

# 거리 계산 결과

예:

```text
강남 ↔ 홍대
```

거리 계산 시:

```text
약 11km 전후
```

수준 결과가 나온다.

---

# QueryDSL 기반 거리 계산 구현

단순 Java 계산만으로는 Nearby 검색을 할 수 없다.

왜냐하면:

```text
DB의 모든 장소를 가져온 뒤
Java에서 거리 계산
```

하면 매우 비효율적이기 때문이다.

따라서 이번 단계에서는:

```text
DB Query 내부에서 거리 계산
```

하도록 구현하였다.

---

# QueryDSL distanceExpression

```java
private NumberExpression<Double> distanceExpression(
    Double baseLat,
    Double baseLon
) {
    return Expressions.numberTemplate(
        Double.class,
        """
        (6371 * acos(
            cos(radians({0})) * cos(radians({1})) *
            cos(radians({2}) - radians({3})) +
            sin(radians({0})) * sin(radians({1}))
        ))
        """,
        baseLat,
        placeMaster.lat,
        placeMaster.lon,
        baseLon
    );
}
```

---

# Nearby 검색 Query 구조

```java
.where(
    withinRadius(distanceExpression, condition.getRadiusKm()),
    keywordContains(condition.getKeyword()),
    indsMclsCdEq(condition.getIndsMclsCd())
)
.orderBy(distanceExpression.asc())
```

---

# Nearby Search 핵심 흐름

최종 Nearby Query 흐름은 다음과 같다.

```text
거리 계산
→ 반경 조건 필터링
→ 키워드 조건 적용
→ 업종 조건 적용
→ 거리순 정렬
```

---

# 왜 PostGIS를 지금 도입하지 않았는가

위치 기반 서비스를 만들면 가장 먼저 나오는 이야기가:

```text
"PostGIS 써야 하는 거 아닌가?"
```

이다.

하지만 이번 MVP에서는 PostGIS를 도입하지 않았다.

---

# 이유 1. 현재 데이터 규모가 크지 않다

현재 DATT 데이터 규모는:

```text
수만 ~ 수십만 수준
```

이다.

이 정도는:

```text
PostgreSQL + QueryDSL
```

만으로도 충분히 처리 가능하다.

---

# 이유 2. 운영 복잡도가 증가한다

PostGIS를 도입하면:

```text
공간 타입
공간 인덱스
GIS 함수
운영 복잡도
```

가 추가된다.

즉 MVP 단계에서는:

```text
과도한 복잡도 증가
```

가 발생한다.

---

# 이유 3. 현재 검색 요구사항이 단순하다

현재 Nearby 검색은:

```text
반경 검색
거리순 정렬
```

정도만 필요하다.

아직:

```text
고급 공간 분석
경로 탐색
Polygon Search
```

등은 필요하지 않다.

---

# 현재 Nearby 구조의 장점

현재 구조는 다음 장점을 가진다.

## 1. 단순하다

운영 부담이 낮다.

---

## 2. 구현 속도가 빠르다

MVP 개발에 적합하다.

---

## 3. 확장 가능하다

향후:

```text
PostGIS
GeoHash
Redis GEO
Bounding Box
```

등으로 확장 가능하다.

---

# 향후 고도화 예정

추후 다음 단계에서 성능 개선을 고려할 예정이다.

```text
PostGIS
공간 인덱스
Bounding Box 최적화
Redis GEO
GeoHash
```

또한:

```text
Anchor 기반 주변 추천
```

기능도 Nearby API를 기반으로 구현될 예정이다.

---

# Nearby Search 테스트 전략

이번 단계에서는 다음 항목들을 테스트하였다.

```text
반경 내 장소 조회
거리순 정렬
업종 필터링
키워드 검색
좌표 검증
반경 검증
```

즉:

```text
기능 정확성
```

을 우선 검증하였다.

---

# 마무리

이번 단계에서는:

```text
위치 기반 Nearby Search API
```

를 구현하였다.

핵심은:

```text
좌표 기반 거리 계산
반경 필터링
거리순 정렬
```

이다.

이 Nearby 구조는 이후:

```text
Anchor
주변 추천
위치 기반 큐레이션
지도 탐색
```

등 DATT 핵심 기능의 기반이 된다.