---
title: "[Development] SQL Query"
excerpt: "Development; SQL Query"
categories: 
- Development, SQL Query
tags:
- [Development]
published: true
toc: true
toc_sticky: true
---

## ✅ MySQL 기준 SQL 쿼리
프로그래머스 SQL 문제를 풀면서 SQL 문법 정리

### 1️⃣ 상위 N개의 레코드 조회
```sql
/* 
    1. ORDER BY: 특정 칼럼 기준 내림차순 정렬
    2. LIMIT: 상위 N개의 레코드 조회
*/
SELECT A.NAME 
FROM (
    SELECT NAME, DATETIME 
    FROM ANIMAL_INS 
    ORDER BY DATETIME
) A
LIMIT 1;
```

### 2️⃣ 조건에 맞는 회원수 조회
```sql
/* 
    1. COUNT(*): 조회되는 레코드 수 카운트
    2. LIKE "2021%": JOINED 칼럼값이 2021로 시작하는 경우 조회 목록에 반영  
*/
SELECT Count(*) 
FROM USER_INFO 
WHERE 
    JOINED LIKE "2021%" 
    AND 
    AGE between 20 and 29;
```

### 3️⃣ 여러 기준으로 정렬하기
```sql
SELECT ANIMAL_ID, NAME, DATETIME
FROM ANIMAL_INS
ORDER BY NAME, DATETIME DESC;
```

### 4️⃣ INNER JOIN
```sql
/* 
    1. INNER JOIN: 조인 타겟 테이블을 뒤에 적어야함 
    2. DATE_FORMAT: 날짜 포멧 변경
*/
SELECT A.BOOK_ID, B.AUTHOR_NAME, DATE_FORMAT(A.PUBLISHED_DATE, "%Y-%m-%d")
FROM BOOK A 
INNER JOIN AUTHOR B
ON A.AUTHOR_ID = B.AUTHOR_ID AND A.CATEGORY = "경제"
ORDER BY PUBLISHED_DATE;

/*
    1. WHERE A.DATETIME > B.DATETIME: 시간 비교
*/
SELECT A.ANIMAL_ID, A.NAME
FROM ANIMAL_INS A
INNER JOIN ANIMAL_OUTS B
ON A.ANIMAL_ID = B.ANIMAL_ID
WHERE A.DATETIME > B.DATETIME
ORDER BY A.DATETIME;
```

### 5️⃣ GROUP BY
```sql
/* 
    1. GROUP BY: SUM, COUNT, AVG 등을 집계함수와 같이 사용
*/
SELECT A.PRODUCT_CODE, SUM(A.PRICE * B.SALES_AMOUNT) AS SALES
FROM PRODUCT A
INNER JOIN OFFLINE_SALE B
ON A.PRODUCT_ID = B.PRODUCT_ID
GROUP BY A.PRODUCT_CODE
ORDER BY SALES DESC, A.PRODUCT_CODE
```

### 6️⃣ 애초에 없거나 없어진 기록 찾기
```sql
/* 
    1. 없어진 기록 찾기
    2. RIGHT(LEFT) JOIN: 조인 주체 테이블을 모두 가져온 후 타겟 테이블의 데이터가 없을 경우 NULL로 표시
    3. 이러한 특성을 통해 유실된 데이터 찾기 가능
*/
SELECT B.ANIMAL_ID, B.NAME
FROM ANIMAL_INS A
RIGHT JOIN ANIMAL_OUTS B
ON A.ANIMAL_ID = B.ANIMAL_ID
WHERE A.ANIMAL_ID IS NULL
ORDER BY B.ANIMAL_ID;

/*
    1. 없는 기록 찾기
    2. 아직 입양을 못 간 동물 중, 가장 오래 보호소에 있었던 동물 3마리의 이름과 보호 시작일을 조회하는 SQL문
*/
SELECT A.NAME, A.DATETIME
FROM ANIMAL_INS A
LEFT JOIN ANIMAL_OUTS B
ON A.ANIMAL_ID = B.ANIMAL_ID
WHERE B.ANIMAL_ID IS NULL
ORDER BY A.DATETIME
LIMIT 3;
```

### 7️⃣ 온라인/오프라인 판매 데이터 통합

```sql
/*
    1. UNION ALL: 칼럼 값을 더하는게 아니라, 인스턴스를 함침
    2. NULL: 문제 요구사항에 오프라인 판매 테이블에는 USER_ID 칼럼이 없으므로 NULL 표시하라고 함
*/
SELECT
DATE_FORMAT(SALES_DATE, "%Y-%m-%d") SALES_DATE,
PRODUCT_ID,
USER_ID,
SALES_AMOUNT
FROM ONLINE_SALE
WHERE MONTH(SALES_DATE) = 3

UNION ALL

SELECT
DATE_FORMAT(SALES_DATE, "%Y-%m-%d") SALES_DATE,
PRODUCT_ID,
NULL USER_ID,
SALES_AMOUNT
FROM OFFLINE_SALE
WHERE MONTH(SALES_DATE) = 3

ORDER BY SALES_DATE, PRODUCT_ID, USER_ID;
```

### 8️⃣ IFNULL

```sql
/*
    1. IFNULL(칼럼명, NULL 혹은 문자열): 만약 칼럼명이 NULL일 경우 해당 문자열로 대체
*/
SELECT PT_NAME, PT_NO, GEND_CD, AGE, IFNULL(TLNO, "NONE") AS TLNO
FROM PATIENT 
WHERE GEND_CD = "W" AND AGE <= 12
ORDER BY AGE DESC, PT_NAME;
```

### 9️⃣ 

```sql
/*
문제:
MEMBER_PROFILE와 REST_REVIEW 테이블에서 리뷰를 가장 많이 작성한 회원의 리뷰들을 조회하는 SQL문을 작성해주세요. 
회원 이름, 리뷰 텍스트, 리뷰 작성일이 출력되도록 작성해주시고, 
결과는 리뷰 작성일을 기준으로 오름차순, 리뷰 작성일이 같다면 리뷰 텍스트를 기준으로 오름차순 정렬해주세요.

ORDER BY COUNT(*) DESC LIMIT 1: 레코드가 가장 많은 1명을 뽑기
*/
SELECT A.MEMBER_NAME, B.REVIEW_TEXT, DATE_FORMAT(B.REVIEW_DATE, "%Y-%m-%d") AS REVIEW_DATE
FROM MEMBER_PROFILE A
LEFT JOIN REST_REVIEW B
ON A.MEMBER_ID = B.MEMBER_ID
WHERE A.MEMBER_ID = (
        SELECT MEMBER_ID
        FROM REST_REVIEW
        GROUP BY MEMBER_ID
        ORDER BY COUNT(*) DESC
        LIMIT 1
)
ORDER BY B.REVIEW_DATE, B.REVIEW_TEXT;
```