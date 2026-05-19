---
title: ⚓ DATT 08 - QueryDSL 기반 장소 검색 API 설계 및 구현
date: 2026-05-19 00:05:00 +0900
categories: [To-Be-Senior]
tags:   
- SpringBoot 
- QueryDSL    
- Pageable 
- PostgreSQL  
- Search  
---

# 개요

DATT는 단순 장소 검색 서비스가 아니다.

핵심 목표는:

```text
특정 장소를 기준으로
주변 경험을 큐레이션하고
친구와 공유하는 플랫폼
```

을 만드는 것이다.

따라서:

```text
검색 성능
검색 확장성
동적 조건 처리
위치 기반 탐색
```

등이 매우 중요하다.

이번 단계에서는:

```text
Spring Data JPA
→ QueryDSL
```

기반으로 검색 API를 고도화하였다.

---

# 왜 QueryDSL을 도입했는가

초기에는 Spring Data JPA의 메서드 기반 조회만으로도 검색 API를 구현할 수 있다.

예를 들면:

```java
findByBizesNmContaining(String keyword)
```

정도의 구조다.

하지만 실제 서비스 검색에서는 다음 문제가 발생한다.

```text
키워드 검색
+ 지역 검색
+ 업종 검색
+ 정렬
+ 페이징
```

조건이 계속 조합되기 시작한다.

예시:

```text
강남구 + 카페 + 스타벅스
서울특별시 + 숙박
한식 + 역삼동
```

이런 조건들을 Repository Method Naming만으로 처리하면:

```text
메서드 수 폭증
가독성 저하
유지보수 어려움
```

문제가 발생한다.

따라서 이번 단계에서는 QueryDSL을 도입하였다.

---

# QueryDSL 환경 구성

DATT는 Spring Boot 4 / Hibernate 7 기반으로 구성되어 있다.

따라서 기존:

```text
com.querydsl
```

이 아니라:

```text
io.github.openfeign.querydsl
```

패키지를 사용하였다.

---

# QueryDSL 설정

## build.gradle

```gradle
implementation 'io.github.openfeign.querydsl:querydsl-jpa:7.0'
annotationProcessor 'io.github.openfeign.querydsl:querydsl-apt:7.0:jpa'

annotationProcessor 'jakarta.persistence:jakarta.persistence-api'
annotationProcessor 'jakarta.annotation:jakarta.annotation-api'
```

---

# QClass 생성 설정

```gradle
def querydslDir = layout.buildDirectory.dir("generated/querydsl").get().asFile

sourceSets {
    main {
        java {
            srcDirs += querydslDir
        }
    }
}

tasks.withType(JavaCompile).configureEach {
    options.generatedSourceOutputDirectory = querydslDir
}
```

---

# JPAQueryFactory Bean 등록

```java
@Configuration
public class QueryDslConfig {

    @Bean
    public JPAQueryFactory jpaQueryFactory(
            EntityManager entityManager
    ) {
        return new JPAQueryFactory(entityManager);
    }
}
```

---

# 검색 조건 객체화를 적용한 이유

검색 조건은 단순 keyword 하나로 끝나지 않는다.

DATT 검색 조건은 다음과 같다.

```text
keyword
ctprvnNm
signguNm
adongNm
indsMclsCd
sortType
```

따라서 이번 단계에서는:

```text
PlaceSearchCondition
```

객체를 별도로 설계하였다.

---

# PlaceSearchCondition

```java
@Getter
@Setter
public class PlaceSearchCondition {

    private String keyword;

    private String ctprvnNm;

    private String signguNm;

    private String adongNm;

    private String indsMclsCd;

    private PlaceSortType sortType = PlaceSortType.LATEST;
}
```

---

# 검색 조건 객체화를 한 이유

검색 조건을 객체화하면 장점이 매우 많다.

## 1. Controller 파라미터 정리

기존 방식:

```java
search(
    String keyword,
    String ctprvnNm,
    String signguNm,
    ...
)
```

문제:

```text
파라미터 증가
가독성 저하
```

---

## 2. 유지보수 용이

조건 추가 시:

```text
DTO 필드 추가
```

만 하면 된다.

---

## 3. QueryDSL과 궁합이 좋음

```java
.where(
    keywordContains(condition.getKeyword()),
    signguNmEq(condition.getSignguNm())
)
```

형태로 매우 자연스럽게 연결된다.

---

# 공공데이터 업종 코드 기반 검색 전략

DATT는:

```text
소상공인시장진흥공단 상권정보 API
```

를 사용한다.

해당 데이터는 업종을:

```text
대분류
중분류
소분류
```

구조로 관리한다.

---

# 왜 중분류만 사용했는가

초기에는:

```text
대분류
중분류
소분류
```

전부 관리하려고 했다.

하지만 실제 검색 기준은 대부분:

```text
한식
중식
카페
숙박
주점
```

정도였다.

즉:

```text
중분류만으로 충분
```

하다고 판단하였다.

---

# 최종 업종 Enum 구조

```java
public enum PlaceIndustryCategory {

    KOREAN_FOOD("I201", "한식"),
    CHINESE_FOOD("I202", "중식"),
    JAPANESE_FOOD("I203", "일식"),

    NON_ALCOHOL("I212", "비알코올"),

    GENERAL_ACCOMMODATION("I101", "일반 숙박"),

    SPORTS_SERVICE("R103", "스포츠 서비스");
}
```

---

# 업종 코드 기반 검색 장점

## 1. 정규화된 검색 가능

```text
카페
커피
Coffee
```

같은 문자열 문제 대신:

```text
I212
```

기준으로 검색 가능.

---

## 2. 공공데이터와 직접 연결 가능

Batch 동기화와 검색 기준이 동일하다.

---

## 3. 프론트 필터 구성 용이

```text
카페
숙박
놀거리
```

등을 코드 기반으로 쉽게 연결 가능.

---

# Pageable 기반 검색 응답 구조

검색 API는 반드시:

```text
대량 데이터 대응
```

을 고려해야 한다.

따라서 이번 단계에서는:

```text
Pageable
```

기반 페이징 구조를 적용하였다.

---

# PlaceSearchResponse

목록 조회용 DTO는 가볍게 구성하였다.

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

---

# 왜 목록 DTO와 상세 DTO를 분리했는가

목록 조회는:

```text
빠르고 가벼워야 한다.
```

반면 상세 조회는:

```text
더 많은 정보가 필요하다.
```

따라서:

```text
PlaceSearchResponse
PlaceDetailResponse
```

를 분리하였다.

---

# Pageable 적용 구조

```java
.offset(pageable.getOffset())
.limit(pageable.getPageSize())
```

형태로 적용하였다.

반환은:

```java
Page<PlaceSearchResponse>
```

구조를 사용하였다.

---

# 정렬 조건 설계

초기 MVP에서는 다음 정렬만 지원하였다.

```text
LATEST
NAME
```

---

# PlaceSortType

```java
@Getter
@RequiredArgsConstructor
public enum PlaceSortType {

    LATEST("createdAt", "최신순"),
    NAME("bizesNm", "이름순");

    private final String property;
    private final String description;
}
```

---

# 왜 Enum 기반 정렬 구조를 사용했는가

단순 문자열 정렬은 위험하다.

예:

```text
sort=name
sort=createdAt
sort=abcd
```

같은 값이 직접 들어온다.

따라서:

```text
허용된 정렬만 명시적으로 관리
```

하기 위해 Enum 구조를 사용하였다.

---

# QueryDSL 기반 검색 구현

최종 검색 조건은 다음과 같다.

```java
.where(
    keywordContains(condition.getKeyword()),
    ctprvnNmEq(condition.getCtprvnNm()),
    signguNmEq(condition.getSignguNm()),
    adongNmEq(condition.getAdongNm()),
    indsMclsCdEq(condition.getIndsMclsCd())
)
```

---

# 왜 Elasticsearch를 바로 도입하지 않았는가

검색 서비스를 만들면 가장 먼저 나오는 이야기가:

```text
"Elasticsearch 써야 하는 거 아닌가?"
```

이다.

하지만 이번 MVP에서는 Elasticsearch를 도입하지 않았다.

---

# 이유 1. 현재 데이터 규모가 크지 않다

현재는:

```text
수만 ~ 수십만 수준 데이터
```

이다.

이 정도 규모에서는:

```text
PostgreSQL + Index
```

만으로도 충분히 대응 가능하다.

---

# 이유 2. 운영 복잡도가 급격히 증가한다

Elasticsearch를 도입하면:

```text
동기화
클러스터
인덱스 관리
매핑
운영 비용
```

등이 추가된다.

즉 MVP 단계에서는:

```text
과도한 복잡도 증가
```

가 발생한다.

---

# 이유 3. 검색 요구사항이 아직 단순하다

현재 검색은:

```text
LIKE 검색
지역 검색
업종 검색
```

정도다.

아직은:

```text
형태소 분석
검색 랭킹
자동완성
오타 보정
```

등이 필요하지 않다.

---

# 향후 Elasticsearch 도입 기준

다음 상황이 오면 Elasticsearch를 고려할 예정이다.

```text
검색 응답 지연 증가
복잡한 검색 랭킹 필요
자동완성 필요
형태소 검색 필요
인기 검색어 분석 필요
```

즉 현재는:

```text
"지금 필요한 수준만 구현"
```

전략을 선택하였다.

---

# 현재 검색 구조의 장점

현재 구조는 다음 장점을 가진다.

## 1. 단순하다

운영 복잡도가 낮다.

---

## 2. 유지보수가 쉽다

Spring Data JPA + QueryDSL만으로 관리 가능.

---

## 3. 확장 가능하다

나중에:

```text
Elasticsearch
Redis
PostGIS
```

등으로 확장 가능하다.

---

# 마무리

이번 단계에서는:

```text
공공데이터 기반 장소 검색 API
```

를 QueryDSL 기반으로 고도화하였다.

핵심은:

```text
동적 검색
업종 기반 검색
지역 기반 검색
페이징
정렬
```

을 안정적으로 처리하는 것이다.

이후 단계에서는:

```text
반경 검색
Anchor 큐레이션
공유
Bookmark
Review
```

등을 통해 DATT의 핵심 기능을 확장할 예정이다.