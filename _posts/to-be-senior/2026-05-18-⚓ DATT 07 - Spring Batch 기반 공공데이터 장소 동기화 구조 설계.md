---
title: "[DATT] Spring Batch 기반 공공데이터 장소 동기화 구조 설계"  
date: 2026-05-18 00:05:00 +0900
categories: [To-Be-Senior]
tags:
- SpringBatch
- Batch
- PublicAPI
- PostgreSQL
- Java
- Backend
- DATT  
---

# 개요

DATT 프로젝트에서는 장소(Place) 데이터를 직접 관리하기 위해  
소상공인시장진흥공단 공공데이터 API를 기반으로 Batch 동기화 구조를 설계했다.

초기에는 CSV 기반 적재를 고려했지만,  
실시간성·확장성·운영 편의성을 고려하여 최종적으로 공공 API 기반 구조를 선택했다.

이번 글에서는 다음 내용을 정리한다.

- Spring Batch 기반 동기화 구조
    
- Upsert 기반 데이터 관리 전략
    
- Reader / Processor / Writer 역할 분리 이유
    
- Chunk 기반 Batch 처리 구조
    
- CSV 대신 공공 API를 선택한 이유
    

---

# 왜 CSV 대신 공공 API를 선택했는가

초기에는 CSV 파일을 다운로드하여 Batch 적재하는 방식을 고려했다.

하지만 다음과 같은 문제가 존재했다.

## 1. 데이터 최신성 문제

CSV는 다운로드 시점의 정적 데이터다.

즉:

```text
신규 업소 추가
폐업
상호 변경
주소 변경
```

등이 즉시 반영되지 않는다.

반면 공공 API는 최신 데이터를 기준으로 동기화할 수 있다.

---

## 2. 운영 자동화 한계

CSV 기반 구조는 다음 작업이 반복된다.

```text
파일 다운로드
파일 교체
파일 업로드
Batch 실행
```

즉 운영 자동화가 어렵다.

공공 API는 Scheduler만 붙이면 자동 동기화가 가능하다.

---

## 3. 확장성 문제

CSV는 데이터 구조 변경 대응이 어렵다.

반면 API 기반 구조는:

```text
대분류 추가
중분류 추가
지역 필터링
페이지 처리
```

등이 상대적으로 유연하다.

---

# Spring Batch 기반 공공데이터 API 동기화 구조

현재 Batch 흐름은 다음과 같다.

```text
공공 API 호출
→ Reader
→ Processor
→ Writer
→ DB 저장
```

전체 구조:

```text
PlacePublicDataClient
        ↓
PlaceApiItemReader
        ↓
PlaceItemProcessor
        ↓
PlaceItemWriter
        ↓
PlaceMaster 저장
```

---

# Reader / Processor / Writer 역할 분리 이유

Spring Batch의 핵심 철학은 역할 분리다.

## 1. Reader

Reader는 데이터를 "읽기만" 한다.

현재 구현:

```text
공공 API 호출
페이지 처리
데이터 순회
```

담당 클래스:

```text
PlaceApiItemReader
```

즉 Reader는:

```text
데이터를 어디서 가져오는가
```

에만 집중한다.

---

## 2. Processor

Processor는 데이터를 "가공"한다.

현재 구현:

```text
DTO → Entity 변환
trim 처리
좌표 변환
필수값 검증
```

담당 클래스:

```text
PlaceItemProcessor
```

예:

```java
if (item == null || isBlank(item.getBizesId())) {
    return null;
}
```

즉:

```text
데이터를 어떻게 정제할 것인가
```

를 담당한다.

---

## 3. Writer

Writer는 데이터를 "저장"한다.

현재 구현:

```text
bizesId 기준 Upsert
신규 데이터 Insert
기존 데이터 Update
```

담당 클래스:

```text
PlaceItemWriter
```

즉:

```text
데이터를 어디에 어떻게 저장할 것인가
```

를 담당한다.

---

# Upsert 기반 장소 데이터 관리 전략

공공데이터는 계속 변경된다.

즉:

```text
신규 업소 추가
상호명 변경
업종 변경
주소 변경
```

등이 발생한다.

따라서 단순 Insert만으로는 운영이 불가능하다.

DATT에서는:

```text
bizesId
```

를 기준으로 Upsert 전략을 선택했다.

---

## 현재 정책

```text
bizesId 없음 → Insert
bizesId 존재 → Update
```

예시:

```java
placeMasterRepository.findByBizesId(placeMaster.getBizesId())
        .ifPresentOrElse(
                existing -> existing.updateFrom(placeMaster),
                () -> placeMasterRepository.save(placeMaster)
        );
```

---

## 왜 bizesId인가

공공데이터에서 제공하는:

```text
bizesId
```

는 업소 고유 식별자 역할을 한다.

즉:

```text
스타벅스 강남점
```

같은 이름보다 훨씬 안정적인 기준이다.

---

# Chunk 기반 Batch 처리 구조 분석

Spring Batch는 Chunk 기반 처리 구조를 가진다.

현재 설정:

```java
.chunk(100)
```

즉:

```text
100개 읽기
→ 100개 처리
→ 100개 저장
→ commit
```

방식으로 동작한다.

---

## 왜 Chunk 기반인가

만약 전체 데이터를 한 번에 처리하면:

```text
메모리 사용량 증가
Transaction 과대화
Rollback 비용 증가
```

문제가 발생한다.

Chunk 기반은 이를 해결한다.

---

## Chunk 처리 장점

### 1. 메모리 안정성

일정 개수만 메모리에 유지한다.

---

### 2. Transaction 분리

Chunk 단위로 commit된다.

즉 일부 실패 시 전체 Rollback을 방지할 수 있다.

---

### 3. 대용량 처리 최적화

Spring Batch가 가장 강력한 이유 중 하나다.

---

# 업종 분류 설계 전략

현재는:

```text
대분류
중분류
소분류
```

전체를 모두 관리하지 않는다.

실제 서비스에서 필요한 핵심 중분류만 Enum으로 관리한다.

예:

```java
KOREAN_FOOD("I201", "한식"),
CAFE("I212", "비알코올"),
GENERAL_ACCOMMODATION("I101", "일반 숙박")
```

---

## 왜 중분류만 관리하는가

소분류까지 모두 관리하면:

```text
Enum 과대화
복잡도 증가
유지보수 비용 증가
```

문제가 발생한다.

현재 서비스 요구사항에서는 중분류만으로 충분하다 판단했다.

---

# 현재 구조의 한계

현재 구현은 MVP 수준이다.

즉 다음 개선이 남아있다.

## 1. Bulk Upsert 최적화

현재는 건별 조회 후 저장한다.

```text
N + 1 가능성
```

존재.

향후 Bulk Upsert 개선 필요.

---

## 2. Retry / Skip 정책

현재는 최소 구현 상태다.

향후:

```text
네트워크 장애 Retry
잘못된 Row Skip
```

정책 추가 예정.

---

## 3. 폐업 처리

현재는 단순 Update만 수행한다.

향후:

```text
lastCollectedAt
closed
```

필드를 기반으로 폐업 동기화 예정.

---

# 마무리

이번 구조 설계를 통해 다음을 확보했다.

- 공공 API 기반 자동 동기화 구조
    
- Spring Batch 기반 대용량 처리 구조
    
- Upsert 기반 데이터 관리 전략
    
- 역할 분리 기반 유지보수성 확보
    

다음 단계에서는:

```text
검색 최적화
공간 좌표 기반 조회
QueryDSL 검색
성능 개선
```

등을 진행할 예정이다.