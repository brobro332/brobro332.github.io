---
title: 💁‍♂️ SQL - SELECT
date: 2025-08-22 21:10:00 +0900
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