---
title: ⚓ DATT 10 - 장소 상세 조회 API 설계 및 Detail 응답 구조 분리 전략
date: 2026-05-21 00:05:00 +0900
categories:
- To-Be-Senior
tags:  
- SpringBoot  
- RESTAPI  
- DTO  
- Architecture
- Backend  
---

# 개요

현재 DATT는 다음 기능들을 지원하고 있다.

```text
장소 검색
Nearby Search
업종 기반 탐색
좌표 기반 주변 조회
```

하지만 실제 서비스에서는:

```text
목록 조회
→ 상세 페이지 이동
```

흐름이 반드시 필요하다.

예를 들어 사용자는:

```text
검색 결과 리스트
→ 특정 장소 클릭
→ 상세 정보 확인
```

과정을 거친다.

따라서 이번 단계에서는:

```text
Place Detail API
```

를 설계하고:

```text
목록 응답 DTO
상세 응답 DTO
```

를 명확하게 분리하였다.

---

# 왜 Place Detail API가 필요한가

초기 MVP에서는 검색 기능만 있어도 어느 정도 동작한다.

하지만 실제 서비스에서는:

```text
장소 상세 정보
```

가 반드시 필요하다.

예:

```text
업종 정보
상세 주소
좌표
북마크 여부
리뷰
Anchor 연결
```

등이다.

특히 DATT의 핵심 기능인:

```text
Anchor
Bookmark
Gamification
```

등은 모두:

```text
장소 상세 페이지
```

를 중심으로 동작한다.

즉 Detail API는 단순 조회 API가 아니라:

```text
DATT 핵심 기능의 중심 API
```

가 된다.

---

# Place 상세 조회 API 설계

이번 단계에서 설계한 API는 다음과 같다.

```http
GET /api/places/{placeId}
```

---

# 요청 예시

```http
GET /api/places/1
```

---

# 조회 기준

현재 Place 상세 조회는:

```text
PlaceMaster.id
```

기준으로 조회한다.

즉:

```text
내부 PK 기반 조회
```

전략이다.

---

# 왜 bizesId가 아니라 id를 사용했는가

공공데이터에는:

```text
bizesId
```

라는 외부 식별자가 존재한다.

하지만 API URL은:

```text
서비스 내부 리소스 기준
```

으로 단순하게 유지하는 것이 좋다.

따라서:

```text
내부 PK(id)
→ 조회 기준

bizesId
→ 응답 데이터
```

로 역할을 분리하였다.

---

# 응답 구조

최종 응답 구조는 다음과 같다.

```java
ApiResponse<PlaceDetailResponse>
```

---

# PlaceDetailResponse 설계

상세 조회 DTO는 다음 구조를 가진다.

```java
public record PlaceDetailResponse(
    Long id,
    String bizesId,

    String bizesNm,
    String brchNm,

    String indsLclsCd,
    String indsLclsNm,

    String indsMclsCd,
    String indsMclsNm,

    String indsSclsCd,
    String indsSclsNm,

    String ctprvnNm,
    String signguNm,
    String adongNm,
    String ldongNm,

    String lnoAdr,
    String rdnmAdr,
    String newZipcd,

    Double lon,
    Double lat,

    LocalDateTime createdAt,
    LocalDateTime updatedAt
)
```

---

# 왜 목록 DTO와 상세 DTO를 분리했는가

처음 개발할 때 가장 흔한 실수 중 하나는:

```text
DTO 하나로 모든 API 재사용
```

이다.

처음에는 편해 보이지만 실제 서비스에서는 문제가 커진다.

---

# 목록 조회와 상세 조회는 목적이 다르다

## 목록 조회

목록 API는:

```text
빠르고 가벼워야 한다.
```

즉:

```text
최소 데이터
빠른 응답
```

이 중요하다.

---

# PlaceSearchResponse

```java
public record PlaceSearchResponse(
    Long id,
    String bizesNm,
    String brchNm,
    String indsMclsCd,
    String indsMclsNm,
    String ctprvnNm,
    String signguNm,
    String adongNm,
    String rdnmAdr,
    Double lon,
    Double lat
)
```

목록용 DTO는:

```text
카드형 리스트
검색 결과
무한 스크롤
```

등에 적합하도록 가볍게 구성하였다.

---

# Nearby Search 응답

Nearby API는 거리 정보가 추가된다.

```java
public record PlaceNearbyResponse(
    ...
    Double distanceKm
)
```

즉 Nearby는:

```text
거리 기반 응답
```

이라는 별도 목적이 있다.

---

# 상세 조회는 다르다

상세 조회는:

```text
더 풍부한 정보
```

가 필요하다.

예:

```text
업종 대/중/소분류
지번 주소
도로명 주소
우편번호
좌표
생성 시간
수정 시간
```

등이다.

즉:

```text
목록 응답
→ 최소 정보

상세 응답
→ 풍부한 정보
```

로 역할이 완전히 다르다.

---

# DTO 분리의 장점

현재 구조는:

```text
PlaceSearchResponse
PlaceNearbyResponse
PlaceDetailResponse
```

로 분리되어 있다.

이 구조의 장점은 매우 크다.

---

# 장점 1. API 목적이 명확해진다

각 DTO가:

```text
어떤 API를 위한 DTO인지
```

명확해진다.

---

# 장점 2. 성능 최적화가 쉬워진다

목록 API는:

```text
최소 데이터만 조회
```

하면 된다.

즉:

```text
네트워크 비용 감소
Serialization 비용 감소
```

효과가 있다.

---

# 장점 3. 확장성이 좋아진다

나중에:

```text
Bookmark
Review
Anchor
Gamification
```

등이 추가될 때:

```text
PlaceDetailResponse
```

에만 확장하면 된다.

목록 API는 영향을 받지 않는다.

---

# 단건 조회 API 설계 전략

Place Detail API는:

```text
Service → Repository → DTO 변환
```

구조로 설계하였다.

---

# PlaceDetailService

```java
@Service
@RequiredArgsConstructor
@Transactional(readOnly = true)
public class PlaceDetailService {

    private final PlaceMasterRepository placeMasterRepository;

    public PlaceDetailResponse getPlaceDetail(Long placeId) {
        PlaceMaster placeMaster = placeMasterRepository.findById(placeId)
            .orElseThrow(() -> new BusinessException(ErrorCode.PLACE_NOT_FOUND));

        return PlaceDetailResponse.from(placeMaster);
    }
}
```

---

# 핵심 전략

현재 Detail API의 핵심 전략은 다음과 같다.

```text
Entity 직접 반환 금지
DTO 변환 후 반환
```

이다.

---

# 왜 Entity를 직접 반환하지 않았는가

Entity 직접 반환은 매우 위험하다.

예:

```text
불필요한 필드 노출
양방향 참조 문제
Lazy Loading 문제
응답 구조 제어 어려움
```

등이 발생한다.

따라서:

```text
Entity
→ DTO 변환
→ 응답 반환
```

구조를 사용하였다.

---

# 예외 처리 전략

존재하지 않는 장소 요청 시:

```text
PLACE_NOT_FOUND
```

예외를 반환하도록 설계하였다.

예:

```http
GET /api/places/999999
```

결과:

```text
404 NOT FOUND
```

---

# Place Detail 구조와 확장 전략

현재 Place Detail API는:

```text
기본 상세 정보 조회
```

수준이다.

하지만 이후 다음 기능들이 추가될 예정이다.

---

# 향후 확장 예정

## 1. Bookmark 연동

```text
isBookmarked
bookmarkCount
```

---

## 2. Review 연동

```text
averageRating
reviewCount
```

---

## 3. Anchor 연동

```text
relatedAnchors
anchorCount
```

---

## 4. Gamification 연동

```text
popularScore
hotPlaceScore
achievement
```

---

# 현재 구조의 장점

현재 구조는:

```text
단순하면서
확장 가능하다.
```

즉 MVP 단계에서는:

```text
과도한 복잡도 없이
서비스 기반 구조 확보
```

를 목표로 하였다.

---

# 테스트 전략

이번 단계에서는:

```text
PlaceDetailServiceTest
```

기반 단위 테스트를 작성하였다.

검증 항목:

```text
정상 상세 조회
존재하지 않는 장소 조회
예외 발생 여부
응답 DTO 검증
```

이다.

---

# 마무리

이번 단계에서는:

```text
Place Detail API
```

를 구축하며:

```text
목록 응답
상세 응답
```

구조를 명확히 분리하였다.

핵심은:

```text
응답 목적에 맞는 DTO 설계
```

이다.

이후:

```text
Bookmark
Review
Anchor
Gamification
```

등 DATT 핵심 기능들이 Place Detail API 중심으로 확장될 예정이다.