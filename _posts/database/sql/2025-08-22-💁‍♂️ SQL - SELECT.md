---
title: 💁‍♂️ SQL - SELECT
date: 2025-08-22 20:53:00 +0900
categories:
  - Database
tags:
  - Database
  - SQL
---

### `SELECT`

#### 조건에 부합하는 중고거래 댓글 조회하기

```sql
SELECT UGB.title AS title,
       UGB.board_id AS board_id,
       UGR.reply_id AS reply_id,
       UGR.writer_id AS writer_id,
       UGR.contents AS contents,
       DATE_FORMAT(UGR.created_date, '%Y-%m-%d') AS created_date
FROM USED_GOODS_BOARD UGB JOIN USED_GOODS_REPLY UGR
ON UGB.board_id = UGR.board_id
WHERE DATE_FORMAT(UGB.created_date, '%Y-%m') = '2022-10'
ORDER BY UGR.created_date, UGB.title;
```
- https://school.programmers.co.kr/learn/courses/30/lessons/164673
- 기억해야 할 부분은 다음과 같다.

1. `DATE_FORMAT()` 함수 사용법
2. 실무에서는 `=` 조건보다는 범위 조건으로 조회해야 함
	- `UGB.created_date`에 인덱스 있을 경우 범위 스캔으로 동작하기 때문에 범위 조건으로 작성해야 훨씬 효율적임

#### 과일로 만든 아이스크림 고르기

```sql
SELECT FH.flavor AS flavor
FROM FIRST_HALF FH JOIN ICECREAM_INFO II
ON FH.flavor = II.flavor
WHERE FH.total_order > 3000
AND II.ingredient_type = 'fruit_based'
ORDER BY FH.total_order DESC;
```
- https://school.programmers.co.kr/learn/courses/30/lessons/133025
- 기억해야 할 부분은 다음과 같다.

1. `flavor` 컬럼에 인덱스가 있으면 조인 성능이 좋아진다.
	- 문제에서 해당 열은 `FH`의 기본 키이므로 `II.flavor`에 인덱스를 걸어주는 것이 좋다.
2. 값 분포에 따라 `FH.total_order`, `II.ingredient_type` 인덱스를 걸면 효율적이다.
3. `FH.total_order`의 경우 인덱스에 의해 정렬 비용이 개선 여부는 실행 계획을 확인해야 한다.

#### 흉부외과 또는 일반외과 의사 목록 출력하기

```sql
SELECT dr_name,
       dr_id,
       mcdp_cd,
       DATE_FORMAT(hire_ymd, '%Y-%m-%d') AS hire_ymd
FROM DOCTOR
WHERE mcdp_cd = 'CS' OR mcdp_cd = 'GS'
ORDER BY hire_ymd DESC, dr_name ASC;
```
- https://school.programmers.co.kr/learn/courses/30/lessons/132203
- 기억해야 할 부분은 다음과 같다.

1. `OR` 연산자는 인덱스를 잘 못 타는 경우가 있음
	- `IN ('CS', 'GS')`와 같이 개선 가능
2. 정렬 연산 비용을 줄이기 위해 `(hire_ymd, dr_name)` 복합 인덱스 고려할 수 있음
	- 단 대용량 데이터거나, 잦은 쿼리 등 정렬 최적화가 필요한 상황일 때만 해당됨

#### 12세 이하인 여자 환자 목록 출력하기

```sql
SELECT pt_name,
       pt_no,
       gend_cd,
       age,
       IFNULL(tlno, 'NONE') AS tlno
FROM PATIENT
WHERE age <= 12 
AND gend_cd = 'W'
ORDER BY age DESC, pt_name ASC;
```
- https://school.programmers.co.kr/learn/courses/30/lessons/132201
- 대용량 테이블이라면 `gend_cd, age` 기준으로 복합 인덱스를 고려할 수 있다.

#### 인기있는 아이스크림

```sql
SELECT flavor
FROM FIRST_HALF
ORDER BY total_order DESC, shipment_id ASC;
```
- https://school.programmers.co.kr/learn/courses/30/lessons/133024
