---
title: 🐘 PostgreSQL 기본 Ⅷ - 적시적소에 알맞은 테이블 디자인
date: 2025-08-17 18:10:00 +0900
categories:
  - Database
tags:
  - Database
  - PostgreSQL
---

![](/assets/image/Pasted%20image%2020250813142544.png)
> 📙 `『실용 SQL』`을 읽고 정리한 글입니다.

### 도서관 조사 테이블 만들기

```sql
CREATE TABLE pls_fy2018_libraries (
	stabr text NOT NULL,
	fscskey text CONSTRAINT fscskey_2018_pkey PRIMARY KEY,
	libid text NOT NULL,
	libname text NOT NULL,
	address text NOT NULL,
	city text NOT NULL,
	zip text NOT NULL,
	county text NOT NULL,
	phone text NOT NULL,
	c_relatn text NOT NULL,
	c_legbas text NOT NULL,
	c_admin text NOT NULL,
	c_fscs text NOT NULL,
	geocode text NOT NULL,
	lsabound text NOT NULL,
	startdate text NOT NULL,
	enddate text NOT NULL,
	popu_lsa integer NOT NULL,
	popu_und integer NOT NULL,
	centlib integer NOT NULL,
	branlib integer NOT NULL,
	bkmob integer NOT NULL,
	totstaff numeric(8,2) NOT NULL,
	bkvol integer NOT NULL,
	ebook integer NOT NULL,
	audio_ph integer NOT NULL,
	audio_dl integer NOT NULL,
	video_ph integer NOT NULL,
	video_dl integer NOT NULL,
	ec_lo_ot integer NOT NULL,
	subscrip integer NOT NULL,
	hrs_open integer NOT NULL,
	visits integer NOT NULL,
	reference integer NOT NULL,
	regbor integer NOT NULL,
	totcir integer NOT NULL,
	kidcircl integer NOT NULL,
	totpro integer NOT NULL,
	gpterms integer NOT NULL,
	pitusr integer NOT NULL,
	wifisess integer NOT NULL,
	obereg text NOT NULL,
	statstru text NOT NULL,
	statname text NOT NULL,
	stataddr text NOT NULL,
	longitude numeric(10,7) NOT NULL,
	latitude numeric(10,7) NOT NULL
);

COPY pls_fy2018_libraries
FROM 'C:\YourDirectory\pls_fy2018_libraries.csv'
WITH (FORMAT CSV, HEADER);

CREATE INDEX libname_2018_idx ON pls_fy2018_libraries (libname);
```
- 테이블을 만들고, 데이터를 가져온 후, 쿼리 속도 향상을 위해 인덱스를 생성한다.

#### 2016, 2017년도 도서관 데이터 테이블 만들기

```sql
CREATE TABLE pls_fy2017_libraries (
	stabr text NOT NULL,
	fscskey text CONSTRAINT fscskey_17_pkey PRIMARY KEY,
	libid text NOT NULL,
	libname text NOT NULL,
	address text NOT NULL,
	city text NOT NULL,
	zip text NOT NULL,
	county text NOT NULL,
	phone text NOT NULL,
	c_relatn text NOT NULL,
	c_legbas text NOT NULL,
	c_admin text NOT NULL,
	c_fscs text NOT NULL,
	geocode text NOT NULL,
	lsabound text NOT NULL,
	startdate text NOT NULL,
	enddate text NOT NULL,
	popu_lsa integer NOT NULL,
	popu_und integer NOT NULL,
	centlib integer NOT NULL,
	branlib integer NOT NULL,
	bkmob integer NOT NULL,
	totstaff numeric(8,2) NOT NULL,
	bkvol integer NOT NULL,
	ebook integer NOT NULL,
	audio_ph integer NOT NULL,
	audio_dl integer NOT NULL,
	video_ph integer NOT NULL,
	video_dl integer NOT NULL,
	ec_lo_ot integer NOT NULL,
	subscrip integer NOT NULL,
	hrs_open integer NOT NULL,
	visits integer NOT NULL,
	reference integer NOT NULL,
	regbor integer NOT NULL,
	totcir integer NOT NULL,
	kidcircl integer NOT NULL,
	totpro integer NOT NULL,
	gpterms integer NOT NULL,
	pitusr integer NOT NULL,
	wifisess integer NOT NULL,
	obereg text NOT NULL,
	statstru text NOT NULL,
	statname text NOT NULL,
	stataddr text NOT NULL,
	longitude numeric(10,7) NOT NULL,
	latitude numeric(10,7) NOT NULL
);

CREATE TABLE pls_fy2016_libraries (
	stabr text NOT NULL,
	fscskey text CONSTRAINT fscskey_16_pkey PRIMARY KEY,
	libid text NOT NULL,
	libname text NOT NULL,
	address text NOT NULL,
	city text NOT NULL,
	zip text NOT NULL,
	county text NOT NULL,
	phone text NOT NULL,
	c_relatn text NOT NULL,
	c_legbas text NOT NULL,
	c_admin text NOT NULL,
	c_fscs text NOT NULL,
	geocode text NOT NULL,
	lsabound text NOT NULL,
	startdate text NOT NULL,
	enddate text NOT NULL,
	popu_lsa integer NOT NULL,
	popu_und integer NOT NULL,
	centlib integer NOT NULL,
	branlib integer NOT NULL,
	bkmob integer NOT NULL,
	totstaff numeric(8,2) NOT NULL,
	bkvol integer NOT NULL,
	ebook integer NOT NULL,
	audio_ph integer NOT NULL,
	audio_dl integer NOT NULL,
	video_ph integer NOT NULL,
	video_dl integer NOT NULL,
	ec_lo_ot integer NOT NULL,
	subscrip integer NOT NULL,
	hrs_open integer NOT NULL,
	visits integer NOT NULL,
	reference integer NOT NULL,
	regbor integer NOT NULL,
	totcir integer NOT NULL,
	kidcircl integer NOT NULL,
	totpro integer NOT NULL,
	gpterms integer NOT NULL,
	pitusr integer NOT NULL,
	wifisess integer NOT NULL,
	obereg text NOT NULL,
	statstru text NOT NULL,
	statname text NOT NULL,
	stataddr text NOT NULL,
	longitude numeric(10,7) NOT NULL,
	latitude numeric(10,7) NOT NULL
);

COPY pls_fy2017_libraries
FROM 'C:\YourDirectory\pls_fy2017_libraries.csv'
WITH (FORMAT CSV, HEADER);

COPY pls_fy2016_libraries
FROM 'C:\YourDirectory\pls_fy2016_libraries.csv'
WITH (FORMAT CSV, HEADER);

CREATE INDEX libname_2017_idx ON pls_fy2017_libraries (libname);
CREATE INDEX libname_2016_idx ON pls_fy2016_libraries (libname);
```
- 세 테이블 모두 동일한 구조를 가지고 있다.


### 집계 함수를 사용하여 도서관 데이터 탐색하기
#### `count()`를 사용하여 행과 값 세기

```sql
-- count()로 테이블 행 개수 세기
SELECT count(*)
FROM pls_fy2018_libraries;

SELECT count(*)
FROM pls_fy2017_libraries;

SELECT count(*)
FROM pls_fy2016_libraries;

-- count()를 이용한 NULL이 아닌 값 개수 세기
SELECT count(phone)
FROM pls_fy2018_libraries;

-- count()를 사용해 열 안의 고유값 개수 세기
SELECT count(libname)
FROM pls_fy2018_libraries;

SELECT count(DISTINCT libname)
FROM pls_fy2018_libraries;
```
- `count(*)`은 모든 행의 수를 반환하고, `count()`에 인자로 열 이름을 넘기면, 해당 열의 `NULL` 값을 제외한 행 수를 반환한다.
- `DISTINCT` 키워드를 활용하면 해당 열의 고유 값 개수를 셀 수 있다.

#### `min()`과 `max()`를 사용하여 최솟값과 최댓값 찾기
- 이 두 함수는 보고된 값이 범위를 이해하여 예기치 않은 문제를 찾아낼 수 있다.

```sql
SELECT max(visits), min(visits)
FROM pls_fy2018_libraries;
```

|max       |min|
|----------|---|
|16,686,945|-3 |

- 최솟값으로 -3이 조회되었는데, 가능한 수치일까?
- 다소 문제가 있지만, 설문조사를 만든 작성자가 응답 없음을 -1, 해당 없음을 -3 값으로 두어 발생한 일이었다.
- 열을 모두 더할 때 음수 값을 포함하면 합계가 잘못 계산되기 때문에, 데이터를 탐색할 때는 음수 값을 고려하고 제외해야 한다.
- 이는 `WHERE` 절을 사용하여 필터링할 수 있다.

> 더 좋은 방법은, 응답 데이터가 없는 경우 `NULL` 값을 삽입하고, 별도의 `visit_flag` 열을 만들어 그 이유를 설명하는 코드를 보관하는 것이다.

#### `GROUP BY`를 사용하여 데이터 집계하기
- 집계 함수와 `GROUP BY` 절을 함께 사용하면 한 개 이상의 열에 있는 값에 따라 결과를 분류할 수 있다.
- 이를 통해 테이블에 있는 모든 주 또는 모든 유형의 도서관 기관에 대해 `sum()` 또는 `count()`와 같은 작업을 수행할 수 있다.

```sql
SELECT stabr
FROM pls_fy2018_libraries
GROUP BY stabr
ORDER BY stabr;

SELECT stabr
FROM pls_fy2017_libraries
GROUP BY stabr
ORDER BY stabr;
```
- `GROUB BY`는 `DISTINCT`와 유사하게 중복 값을 제거한다.
- 물론 단 하나의 열로만 그룹화할 수 있는 것은 아니다.

```sql
SELECT city, stabr
FROM pls_fy2018_libraries
GROUP BY city, stabr
ORDER BY city, stabr;
```
- 결과는 `ORDER BY` 절에 의해 도시 별로 정렬된 뒤, 주 별로 정렬된다.

#### `GROUP BY`와 `count()` 결합하기

```sql
SELECT stabr, count(*)
FROM pls_fy2018_libraries
GROUP BY stabr
ORDER BY count(*) DESC;

SELECT libname, count(libname)
FROM pls_fy2018_libraries
GROUP BY libname
ORDER BY count(libname) DESC;
```
- 첫 번째 쿼리는 주 별로 도서관 수를 조회하여 어느 주에 도서관이 가장 많은지 알 수 있다.
- 두 번째 쿼리처럼 개별 열을 집계 함수와 함께 조회할 때는 `GROUP BY` 절에 그 열을 포함해야 한다.
- 열을 포함하지 않으면 데이터베이스는 오류를 반환한다.

#### 여러 개의 행에서 `GROUP BY`를 `count()`와 함께 사용하기

```sql
SELECT stabr, stataddr, count(*)
FROM pls_fy2018_libraries
GROUP BY stabr, stataddr
ORDER BY stabr, stataddr;

SELECT city, stabr, count(*)
FROM pls_fy2018_libraries
GROUP BY city, stabr
ORDER BY count(*) DESC;
```
- 위 쿼리를 통해 두 열의 고유한 조합 수를 조회할 수 있다.
- 두 쿼리의 차이점은 단순히 정렬 방식에 있다.

#### `sum()`을 사용해 도서관 방문 수 살펴보기

```sql
-- 2018
SELECT sum(visits) AS visits_2018
FROM pls_fy2018_libraries
WHERE visits >= 0;

-- 2017
SELECT sum(visits) AS visits_2017
FROM pls_fy2017_libraries
WHERE visits >= 0;

-- 2016
SELECT sum(visits) AS visits_2016
FROM pls_fy2016_libraries
WHERE visits >= 0;
```
- 응답 없음과 해당 없음 값은 음수 값을 갖고 있는데, 분석에 영향을 끼치지 않도록 음수 값을 제외하였다.

```sql
-- 방문자 수
SELECT sum(pls18.visits) AS visits_2018,
	sum(pls17.visits) AS visits_2017,
	sum(pls16.visits) AS visits_2016
FROM pls_fy2018_libraries pls18
	JOIN pls_fy2017_libraries pls17 ON pls18.fscskey = pls17.fscskey
	JOIN pls_fy2016_libraries pls16 ON pls18.fscskey = pls16.fscskey
WHERE pls18.visits >= 0
	AND pls17.visits >= 0
	AND pls16.visits >= 0;

-- 와이파이 세션 수
SELECT sum(pls18.wifisess) AS wifi_2018,
	sum(pls17.wifisess) AS wifi_2017,
	sum(pls16.wifisess) AS wifi_2016
FROM pls_fy2018_libraries pls18
	JOIN pls_fy2017_libraries pls17 ON pls18.fscskey = pls17.fscskey
	JOIN pls_fy2016_libraries pls16 ON pls18.fscskey = pls16.fscskey
WHERE pls18.wifisess >= 0
	AND pls17.wifisess >= 0
	AND pls16.wifisess >= 0;
```
- `INNER JOIN`을 사용하여 세 테이블의 집계 값을 한 번에 출력한다.
- `fscskey` 열을 기준으로 테이블을 조인했지만 두 테이블 모두에 표시되는 도서관 중 일부가 3년 사이에 병합되거나 분할되었을 가능성이 있다.
- 작업 전에는 이러한 데이터에 대한 주의 사항을 조사해야 한다.

#### 주 별로 방문 합계 그룹화하기

```sql
SELECT pls18.stabr,
	sum(pls18.visits) AS visits_2018,
	sum(pls17.visits) AS visits_2017,
	sum(pls16.visits) AS visits_2016,
	round( (sum(pls18.visits::numeric) - sum(pls17.visits)) /
		sum(pls17.visits) * 100, 1 ) AS chg_2018_17,
	round( (sum(pls17.visits::numeric) - sum(pls16.visits)) /
		sum(pls16.visits) * 100, 1 ) AS chg_2017_16
FROM pls_fy2018_libraries pls18
	JOIN pls_fy2017_libraries pls17 ON pls18.fscskey = pls17.fscskey
	JOIN pls_fy2016_libraries pls16 ON pls18.fscskey = pls16.fscskey
WHERE pls18.visits >= 0
	AND pls17.visits >= 0
	AND pls16.visits >= 0
GROUP BY pls18.stabr
ORDER BY chg_2018_17 DESC;
```
- 집계 함수를 통해 변화율 공식을 포함하고, `chg_2018_17` 별칭을 `ORDER BY` 절에 사용했다.
- 결과는 다음과 같다.

|stabr|visits_2018|visits_2017|visits_2016|chg_2018_17|chg_2017_16|
|-----|-----------|-----------|-----------|-----------|-----------|
|SD   |3,824,804  |3,699,212  |3,722,376  |3.4        |-0.6       |
|MT   |4,332,900  |4,215,484  |4,298,268  |2.8        |-1.9       |
|FL   |68,423,689 |66,697,122 |70,991,029 |2.6        |-6         |
|ND   |2,216,377  |2,162,189  |2,201,730  |2.5        |-1.8       |
|ID   |8,179,077  |8,029,503  |8,597,955  |1.9        |-6.6       |
|DC   |3,632,539  |3,593,201  |3,930,763  |1.1        |-8.6       |
|ME   |6,746,380  |6,731,768  |6,811,441  |0.2        |-1.2       |
|NH   |7,045,010  |7,028,800  |7,236,567  |0.2        |-2.9       |
|UT   |15,326,963 |15,295,494 |16,096,911 |0.2        |-5         |
|DE   |4,122,181  |4,117,904  |4,125,899  |0.1        |-0.2       |
|OK   |13,399,265 |13,491,194 |13,112,511 |-0.7       |2.9        |
|WY   |3,338,772  |3,367,413  |3,536,788  |-0.9       |-4.8       |
|MA   |39,926,583 |40,453,003 |40,427,356 |-1.3       |0.1        |
|WA   |37,338,635 |37,916,034 |38,634,499 |-1.5       |-1.9       |
|MN   |22,952,388 |23,326,303 |24,033,731 |-1.6       |-2.9       |
|NM   |6,908,686  |7,036,582  |7,178,428  |-1.8       |-2         |
|VA   |33,913,162 |34,563,079 |35,649,602 |-1.9       |-3         |
|KS   |13,483,333 |13,737,900 |13,699,223 |-1.9       |0.3        |
|NY   |97,921,323 |100,012,193|103,081,304|-2.1       |-3         |
|WI   |30,097,183 |30,865,470 |31,442,577 |-2.5       |-1.8       |
|AL   |14,188,647 |14,583,055 |15,637,164 |-2.7       |-6.7       |
|CO   |31,085,356 |31,975,615 |32,011,432 |-2.8       |-0.1       |
|MI   |44,758,918 |46,052,561 |46,734,166 |-2.8       |-1.5       |
|CA   |146,656,984|151,056,672|155,613,529|-2.9       |-2.9       |
|NJ   |40,947,978 |42,181,061 |42,429,576 |-2.9       |-0.6       |
|CT   |20,423,515 |21,051,597 |21,603,777 |-3         |-2.6       |
|RI   |5,490,076  |5,669,309  |5,778,025  |-3.2       |-1.9       |
|IN   |30,836,051 |31,849,195 |33,363,879 |-3.2       |-4.5       |
|PA   |40,885,876 |42,243,049 |44,105,513 |-3.2       |-4.2       |
|OR   |19,592,295 |20,244,499 |20,391,927 |-3.2       |-0.7       |
|IA   |16,674,976 |17,245,764 |17,753,953 |-3.3       |-2.9       |
|NE   |7,449,868  |7,726,127  |7,873,829  |-3.6       |-1.9       |
|NV   |9,334,070  |9,684,935  |9,733,359  |-3.6       |-0.5       |
|AK   |3,268,073  |3,402,486  |3,467,234  |-4         |-1.9       |
|VT   |3,526,357  |3,673,501  |3,721,332  |-4         |-1.3       |
|SC   |13,989,511 |14,567,585 |15,802,934 |-4         |-7.8       |
|IL   |63,466,887 |66,166,082 |67,336,230 |-4.1       |-1.7       |
|NC   |31,263,894 |32,621,293 |33,605,264 |-4.2       |-2.9       |
|MD   |24,976,429 |26,089,963 |27,481,583 |-4.3       |-5.1       |
|AZ   |23,439,707 |24,584,201 |25,315,276 |-4.7       |-2.9       |
|OH   |68,176,967 |71,895,854 |74,119,719 |-5.2       |-3         |
|WV   |4,944,242  |5,231,251  |5,231,443  |-5.5       |0          |
|KY   |16,910,828 |17,909,495 |18,028,488 |-5.6       |-0.7       |
|MO   |24,663,467 |26,117,633 |27,065,546 |-5.6       |-3.5       |
|LA   |16,227,594 |17,211,007 |20,262,385 |-5.7       |-15.1      |
|TX   |66,168,387 |70,514,138 |70,975,901 |-6.2       |-0.7       |
|TN   |18,102,460 |19,396,554 |18,701,973 |-6.7       |3.7        |
|GA   |26,835,701 |28,816,233 |27,987,249 |-6.9       |3          |
|AR   |9,551,686  |10,358,181 |10,596,035 |-7.8       |-2.2       |
|GU   |75,119     |81,572     |71,813     |-7.9       |13.6       |
|MS   |7,602,710  |8,581,994  |8,915,406  |-11.4      |-3.7       |
|HI   |3,456,131  |4,135,229  |4,490,320  |-16.4      |-7.9       |
|AS   |48,828     |67,848     |63,166     |-28        |7.4        |

- 이 유용한 데이터는 데이터 분석가가 특히 가장 큰 변화의 원인을 조사하도록 유도한다.

#### `HAVING`을 사용하여 집계 쿼리 필터링하기

```sql
SELECT pls18.stabr,
	sum(pls18.visits) AS visits_2018,
	sum(pls17.visits) AS visits_2017,
	sum(pls16.visits) AS visits_2016,
	round( (sum(pls18.visits::numeric) - sum(pls17.visits)) /
		sum(pls17.visits) * 100, 1 ) AS chg_2018_17,
	round( (sum(pls17.visits::numeric) - sum(pls16.visits)) /
		sum(pls16.visits) * 100, 1 ) AS chg_2017_16
FROM pls_fy2018_libraries pls18
	JOIN pls_fy2017_libraries pls17 ON pls18.fscskey = pls17.fscskey
	JOIN pls_fy2016_libraries pls16 ON pls18.fscskey = pls16.fscskey
WHERE pls18.visits >= 0
	AND pls17.visits >= 0
	AND pls16.visits >= 0
GROUP BY pls18.stabr
HAVING sum(pls18.visits) > 50000000
ORDER BY chg_2018_17 DESC;
```
- 집계 함수에 조건을 적용하기 위해서는 `WHERE` 키워드가 아닌 `HAVING` 키워드를 사용해야 한다.
- `HAVING`을 사용하여 쿼리 결과에 총 방문 횟수가 5천만 이상인 행만 포함되도록 하였다.
- 결과는 다음과 같다.

|stabr|visits_2018|visits_2017|visits_2016|chg_2018_17|chg_2017_16|
|-----|-----------|-----------|-----------|-----------|-----------|
|FL   |68,423,689 |66,697,122 |70,991,029 |2.6        |-6         |
|NY   |97,921,323 |100,012,193|103,081,304|-2.1       |-3         |
|CA   |146,656,984|151,056,672|155,613,529|-2.9       |-2.9       |
|IL   |63,466,887 |66,166,082 |67,336,230 |-4.1       |-1.7       |
|OH   |68,176,967 |71,895,854 |74,119,719 |-5.2       |-3         |
|TX   |66,168,387 |70,514,138 |70,975,901 |-6.2       |-0.7       |

- 위 쿼리 결과를 통해 "도서관 방문 수가 가장 많은 주 가운데, 2017 ~ 2018년 사이 방문 수가 증가한 곳은 플로리다주가 유일했고, 나머지는 방문 수가 2 ~ 6% 정도 감소했습니다."라는 식으로 문장을 작성할 수 있게 된다.