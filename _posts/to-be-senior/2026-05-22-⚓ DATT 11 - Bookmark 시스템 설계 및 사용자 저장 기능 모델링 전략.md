---
title: ⚓ DATT 11 - Bookmark 시스템 설계 및 사용자 저장 기능 모델링 전략
date: 2026-05-21  
categories:
- To-Be-Senior
tags:   
- SpringBoot 
- JPA 
- Bookmark 
- Architecture 
- DomainModeling  
---

# 개요

현재 DATT는 다음 기능들을 지원하고 있다.

```text
장소 검색
Nearby Search
Place Detail API
JWT 인증
```

하지만 사용자 기반 서비스가 되기 위해서는:

```text
저장
개인화
공유
큐레이션
```

기능이 반드시 필요하다.

특히 DATT는 단순 지도 앱이 아니라:

```text
데이트
약속
여행
동선
경험 큐레이션
```

서비스를 목표로 하고 있기 때문에:

```text
"사용자가 장소를 저장한다"
```

는 매우 중요한 기능이 된다.

따라서 이번 단계에서는:

```text
Bookmark 시스템
```

을 구축하였다.

---

# Bookmark 기능이 중요한 이유

처음에는 Bookmark를 단순 저장 기능으로 생각하기 쉽다.

하지만 실제 서비스에서는 Bookmark가:

```text
개인화 데이터
```

의 시작점이 된다.

예를 들어 사용자는:

```text
좋아하는 장소 저장
데이트 장소 저장
가고 싶은 장소 저장
Anchor 생성용 장소 저장
```

등을 수행한다.

즉 Bookmark는 단순 기능이 아니라:

```text
사용자 취향 데이터
```

가 된다.

---

# DATT에서 Bookmark의 역할

DATT에서 Bookmark는 다음 기능들과 연결된다.

```text
Anchor 생성
장소 공유
개인화 추천
Gamification
```

즉 Bookmark는:

```text
DATT 사용자 경험의 핵심 데이터
```

가 된다.

---

# Bookmark 시스템 설계

이번 단계에서 설계한 핵심 구조는 다음과 같다.

```text
Member
↕
PlaceBookmark
↕
PlaceMaster
```

즉:

```text
사용자
↔ 장소
```

관계를 Bookmark Entity로 관리한다.

---

# PlaceBookmark Entity

최종 Entity 구조는 다음과 같다.

```java
@Entity
@Table(
    name = "place_bookmarks",
    uniqueConstraints = {
        @UniqueConstraint(
            name = "uk_place_bookmark_member_place",
            columnNames = {"member_id", "place_id"}
        )
    }
)
public class PlaceBookmark extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY, optional = false)
    @JoinColumn(name = "member_id", nullable = false)
    private Member member;

    @ManyToOne(fetch = FetchType.LAZY, optional = false)
    @JoinColumn(name = "place_id", nullable = false)
    private PlaceMaster placeMaster;
}
```

---

# 왜 ManyToMany를 직접 사용하지 않았는가

JPA를 처음 사용할 때 가장 쉽게 떠올리는 방식은:

```java
@ManyToMany
```

이다.

예:

```java
@ManyToMany
private List<PlaceMaster> bookmarks;
```

하지만 실제 서비스에서는 이 방식이 거의 항상 문제가 된다.

---

# ManyToMany의 문제점

## 1. 확장성이 매우 낮다

Bookmark는 시간이 지나면 다음 정보들이 필요해진다.

```text
저장 일시
메모
공개 여부
폴더
정렬 순서
공유 상태
```

하지만 `@ManyToMany`는:

```text
중간 테이블에 추가 필드 확장
```

이 어렵다.

---

# 2. 비즈니스 로직이 애매해진다

실제 Bookmark는 단순 관계가 아니다.

```text
"사용자가 장소를 저장한 행위"
```

라는 의미를 가진다.

즉 Bookmark 자체가:

```text
독립적인 도메인
```

이 된다.

따라서 별도 Entity로 분리하는 것이 더 자연스럽다.

---

# 3. 유지보수가 어려워진다

`@ManyToMany`는 내부적으로:

```text
숨겨진 중간 테이블
```

을 생성한다.

문제는 시간이 지나면:

```text
쿼리 최적화
삭제 정책
확장 정책
```

등을 제어하기 어려워진다.

---

# 그래서 선택한 구조

최종적으로 다음 구조를 선택하였다.

```text
Member
↕
PlaceBookmark
↕
PlaceMaster
```

즉:

```text
중간 관계 Entity 직접 관리
```

전략이다.

---

# 북마크 중복 방지 전략

Bookmark에서는:

```text
같은 장소 중복 저장
```

을 막아야 한다.

이를 위해:

```java
@UniqueConstraint(
    name = "uk_place_bookmark_member_place",
    columnNames = {"member_id", "place_id"}
)
```

를 적용하였다.

즉:

```text
member_id + place_id
```

조합은 하나만 존재 가능하다.

---

# 왜 DB 레벨에서도 막았는가

Service에서만 검증하면:

```text
동시성 문제
```

가 발생할 수 있다.

예:

```text
동시에 두 번 저장 요청
```

이 들어오면 중복 저장될 수 있다.

따라서:

```text
Service 검증
+
DB Unique Constraint
```

를 함께 적용하였다.

---

# Bookmark API 설계

이번 단계에서는 다음 API를 구축하였다.

---

# 북마크 추가 API

```http
POST /api/bookmarks/places/{placeId}
```

역할:

```text
현재 로그인 사용자의 장소 저장
```

---

# 북마크 삭제 API

```http
DELETE /api/bookmarks/places/{placeId}
```

역할:

```text
저장한 장소 삭제
```

---

# 내 북마크 목록 조회 API

```http
GET /api/bookmarks/places
```

역할:

```text
내가 저장한 장소 목록 조회
```

---

# 왜 /places 를 붙였는가

초기에는:

```text
/api/bookmarks/{placeId}
```

형태도 고려했다.

하지만 이후:

```text
Anchor Bookmark
```

도 추가될 가능성이 높다.

따라서:

```text
/api/bookmarks/places
/api/bookmarks/anchors
```

처럼 리소스를 명확히 분리하였다.

---

# PlaceBookmarkResponse 설계

북마크 응답 DTO는 다음 구조를 가진다.

```java
public record PlaceBookmarkResponse(
    Long bookmarkId,
    Long placeId,

    String bizesNm,
    String brchNm,

    String indsMclsCd,
    String indsMclsNm,

    String ctprvnNm,
    String signguNm,
    String adongNm,

    String rdnmAdr,

    Double lon,
    Double lat,

    LocalDateTime bookmarkedAt
)
```

---

# 왜 Place 정보도 함께 응답하는가

북마크 목록은 실제로:

```text
카드형 UI
```

로 렌더링된다.

즉 프론트에서는:

```text
장소명
주소
업종
좌표
```

등이 필요하다.

따라서:

```text
Bookmark 자체 정보
+
Place 정보
```

를 함께 내려주도록 설계하였다.

---

# Bookmark 여부 조회 기능

이번 단계에서는:

```text
isBookmarked
```

기능도 추가하였다.

즉 Place Detail API에서:

```text
현재 로그인 사용자가 저장한 장소인지
```

확인 가능하다.

---

# Place Detail API 연동

최종적으로 Place Detail 응답은:

```java
Boolean isBookmarked
```

필드를 포함한다.

즉 프론트에서는:

```text
북마크 버튼 상태
```

를 즉시 렌더링 가능하다.

---

# 인증 사용자 연동

Bookmark는:

```text
로그인 사용자 기반 기능
```

이다.

따라서:

```java
@AuthenticationPrincipal
```

을 사용하여 현재 인증 사용자를 가져오도록 구현하였다.

---

# JWT 기반에서도 가능한가?

가능하다.

중요한 것은 JWT 자체가 아니라:

```text
SecurityContext에 Authentication 등록
```

여부다.

즉 JWT Filter에서:

```java
SecurityContextHolder.getContext()
    .setAuthentication(authentication);
```

를 수행하면:

```java
@AuthenticationPrincipal
```

사용이 가능하다.

---

# 개인화 서비스 구조의 시작점

Bookmark는 단순 저장 기능이 아니다.

실제로는:

```text
사용자 취향 데이터
```

가 된다.

예:

```text
어떤 지역을 저장하는가
어떤 업종을 좋아하는가
어떤 Anchor를 만드는가
```

등이 모두 데이터로 축적된다.

---

# 향후 확장 방향

현재 Bookmark는 MVP 수준이다.

하지만 이후 다음 기능들로 확장 가능하다.

---

# 1. Bookmark 폴더

예:

```text
데이트
여행
맛집
술집
```

별 저장 분리.

---

# 2. 공개 Bookmark

예:

```text
내 저장 리스트 공유
```

---

# 3. Anchor 연동

예:

```text
Bookmark 기반 Anchor 생성
```

---

# 4. 개인화 추천

예:

```text
사용자 취향 기반 장소 추천
```

---

# 마무리

이번 단계에서는:

```text
Bookmark 시스템
```

을 구축하며:

```text
사용자 저장 기능
```

기반을 완성하였다.

핵심은:

```text
Bookmark를 단순 관계가 아닌
독립적인 도메인으로 본 것
```

이다.

현재 구조는:

```text
확장 가능하고
개인화 서비스로 발전 가능한 구조
```

를 목표로 설계하였다.

이후 DATT의 핵심 기능인:

```text
Anchor
공유
Gamification
개인화 추천
```

등이 Bookmark 시스템 기반으로 확장될 예정이다.