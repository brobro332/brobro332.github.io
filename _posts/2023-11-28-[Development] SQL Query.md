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

### 1️⃣ 조건에 맞는 회원수 조회
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
