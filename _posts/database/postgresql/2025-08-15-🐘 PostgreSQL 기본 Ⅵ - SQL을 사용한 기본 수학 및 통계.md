---
title: 🐘 PostgreSQL 기본 Ⅵ - SQL을 사용한 기본 수학 및 통계
date: 2025-08-15 22:10:00 +0900
categories:
  - Database
tags:
  - Database
  - PostgreSQL
---

![](/assets/image/Pasted%20image%2020250813142544.png)
> 📙 `『실용 SQL』`을 읽고 정리한 글입니다.

### 수학 연산자와 함수 이해하기
#### 수학과 데이터 타입 이해하기
- 두 숫자 사이에 덧셈과 뺄셈, 곱셈, 나눗셈 연산자를 사용하면 반환되는 값의 데이터 타입은 다음과 같다.

1. 두 정수는 `integer`를 반환한다.
2. 연산자 옆에 `numeric` 타입인 숫자가 하나라도 있으면 `numeric`을 반환한다.
3. 부동 소수점 숫자가 있으면 부동 소수점 타입인 `double precision`을 반환한다.

- 그러나 지수, 제곱근, 팩토리얼 함수는 다르다.
- 각각은 연산자 앞이나 뒤에 하나의 숫자를 취하고, 입력이 정수인 경우에도 `numeric`과 `floating-point` 타입을 반환한다.
- 데이터 타입이 의도한 바와 다르다면 `CAST`를 통해 데이터 타입을 변환하여 사용하면 된다.

#### 더하기, 빼기, 그리고 곱하기

```sql
SELECT 2 + 2;
SELECT 9 - 1;
SELECT 3 * 4;
```
여기서 우리가 열을 따로 지정하지 않았기 때문에 다음과 같이 미상의 열을 뜻하는 `?column?` 아래에 나타난다.

|?column?|
|--------|
|12      |

- 열 이름을 표시하려면 `SELECT 3 * 4 AS result`와 같이 별칭을 사용해야 한다.

#### 나누기와 모듈러 연산 수행하기

```sql
SELECT 11 / 6;
SELECT 11 % 6;
SELECT 11.0 / 6;
SELECT CAST(11 AS numeric(3,1)) / 6;
```
- 참고로 한 번에 몫과 나머지를 제공하는 연산은 존재하지 않는다.
- 두 숫자를 나누고 결과가 `numeric` 타입으로 반환되도록 하려면 피연산자 중 하나라도 `numeric` 타입을 갖도록 캐스팅하면 된다.

#### 지수, 제곱근, 팩토리얼 사용하기

```sql
SELECT 3 ^ 4;

SELECT |/ 10;
SELECT sqrt(10);
SELECT ||/ 10;

SELECT factorial(4);
SELECT 4 !;
```
- 지수 연산자 `^`를 이용하면 주어진 기수를 지수로 올릴 수 있다.
- 숫자의 제곱근은 `|/` 연산자 혹은 `sqrt()`를 사용한다.
- `||/`는 숫자의 세제곱근을 구하기 위한 연산이다.
- 숫자의 팩토리얼을 계산하려면 `factorial()` 혹은 `!` 연산자를 사용하면 된다.
- `SQL` 표준의 일부가 아니며 `PostgreSQL`에만 해당된다.

#### 연산의 순서 유의하기
- 지금까지 설명한 `PostgreSQL`의 연산 순서는 다음과 같다.

1. 지수와 근
2. 곱하기, 나누기, 모듈러
3. 더하기, 빼기

- 후순위에 있는 연산을 먼저 처리하고 싶다면 괄호를 활용해야 한다.
- 나중에 분석을 수정할 필요가 없도록 연산자 우선 순위에 유의해야 한다.


### 인구조사 테이블 열을 이용해 계산하기

```sql
SELECT county_name AS county,
	   state_name AS state,
	   pop_est_2019 AS pop,
	   births_2019 AS births,
	   deaths_2019 AS deaths,
	   international_migr_2019 AS int_migr,
	   domestic_migr_2019 AS dom_migr,
	   residual_2019 AS residual
FROM us_counties_pop_est_2019
LIMIT 4;
```

| county         | state   | pop     | births | deaths | int_migr | dom_migr | residual |
| -------------- | ------- | ------- | ------ | ------ | -------- | -------- | -------- |
| Autauga County | Alabama | 55,869  | 624    | 541    | -16      | 270      | -1       |
| Baldwin County | Alabama | 223,234 | 2,304  | 2,326  | 80       | 5,297    | 24       |
| Barbour County | Alabama | 24,686  | 256    | 312    | 13       | -141     | -2       |
| Bibb County    | Alabama | 22,394  | 240    | 252    | 10       | 31       | -2       |

#### 열끼리 더하고 빼기

```sql
SELECT county_name AS county,
	   state_name AS state,
	   births_2019 AS births,
	   deaths_2019 AS deaths,
	   births_2019 - deaths_2019 AS natural_increase
FROM us_counties_pop_est_2019
ORDER BY state_name, county_name;
```
- 위 코드는 각 카운티의 출생자 수에서 사망자 수를 빼 인구 조사에서 자연 증가하는 수를 구한다.

| county         | state   | births | deaths | natural_increase |
| -------------- | ------- | ------ | ------ | ---------------- |
| Autauga County | Alabama | 624    | 541    | 83               |
| Baldwin County | Alabama | 2,304  | 2,326  | -22              |
| Barbour County | Alabama | 256    | 312    | -56              |
| Bibb County    | Alabama | 240    | 252    | -12              |

- 이제 이를 기반으로 데이터를 테스트하고 열을 올바르게 가져왔는지 확인해야 한다.
- 2019년 인구 추정치는 2018년 추정치와 출생자 수, 사망자 수, 이주 및 잔여 요인에 대한 열의 합계와 같아야 한다.

```sql
SELECT county_name AS county,
	state_name AS state,
	pop_est_2019 AS pop,
	pop_est_2018 + births_2019 - deaths_2019 +
		international_migr_2019 + domestic_migr_2019 +
		residual_2019 AS components_total,
	pop_est_2019 - (pop_est_2018 + births_2019 - deaths_2019 +
		international_migr_2019 + domestic_migr_2019 +
		residual_2019) AS difference
FROM us_counties_pop_est_2019
ORDER BY difference DESC;
```
- `pop` 열은 2019년의 인구 추정치이고, `components_total` 열은 2018년 인구 추정치에 인구 변화 요소를 더한 값이다.
- `difference`는 `pop - components_total`와 같다.
- 다음과 같이 `differenct` 값이 0으로 모든 행에 출력되므로, 가져온 데이터가 깨끗하다고 할 수 있다.

| county         | state   | pop     | components_total | difference |
| -------------- | ------- | ------- | ---------------- | ---------- |
| Autauga County | Alabama | 55,869  | 55,869           | 0          |
| Baldwin County | Alabama | 223,234 | 223,234          | 0          |
| Barbour County | Alabama | 24,686  | 24,686           | 0          |
| Bibb County    | Alabama | 22,394  | 22,394           | 0          |

- 새로운 데이터셋을 가져오면 이렇게 작은 테스트를 수행하는 것이 중요하다.
- 분석을 시작하기 전에 데이터를 잘 이해하고 잠재적인 문제를 방지할 수 있다.

#### 데이터의 전체 백분율 구하기

```sql
SELECT county_name AS county,
	state_name AS state,
	area_water::numeric / (area_land + area_water) * 100 AS pct_water
FROM us_counties_pop_est_2019
ORDER BY pct_water DESC;
```
- 카운티에서 물이 차지하는 면적 비율 구하는 쿼리문이다.
- 데이터를 저장되어 있는 그대로 `integer` 타입으로 사용하면 원하는 결과를 얻지 못한다.
- 정수를 정수로 나누기 때문에 모든 행의 결과로 0이 나오게 된다.
- 대신 정수 중 하나를 `numeric` 타입으로 변환하면 결과도 소수로 나온다.
- 위 명령문에서는 코드를 짧게 하기 위해 `::` 연산자를 사용했다. 
- 결과를 표시하기 위해 100을 곱하면 모두가 알고 있는 백분율이 표시된다.

| county             | state         | pct_water     |
| ------------------ | ------------- | ------------- |
| Keweenaw County    | Michigan      | 90.9472374745 |
| Leelanau County    | Michigan      | 86.2885896812 |
| Nantucket County   | Massachusetts | 84.7969249919 |
| St. Bernard Parish | Louisiana     | 82.483711492  |

#### 변화율 계산하기
- 데이터 분석의 또 다른 핵심 지표는 시간에 따른 변화를 나타내는 변화율이다.
- `(new_value - prev_value) / prev_value`와 같이 계산한다.
- 그 예시는 다음과 같다.

1. 자동차 제조사 별 판매 대수 전년 동기 대비 변화
2. 마케팅 회사가 운영하는 메일링 리스트 월간 구독자 수 변화
3. 전국 학교의 연간 등록생 수 증감

```sql
CREATE TABLE percent_change (
	department text,
	spend_2019 numeric(10,2),
	spend_2022 numeric(10,2)
);

INSERT INTO percent_change
VALUES
	('Assessor', 178556, 179500),
	('Building', 250000, 289000),
	('Clerk', 451980, 650000),
	('Library', 87777, 90001),
	('Parks', 250000, 223000),
	('Water', 199000, 195000);

SELECT department,
	   spend_2019,
	   spend_2022,
	   round( (spend_2022 - spend_2019) /
					spend_2019 * 100, 1) AS pct_change
FROM percent_change;
```
- 가상의 지방 정부 부서에서의 지출과 관련된 테스트 데이터에 대한 변화율을 조회하는 명령문이다.
- 위 코드에서는 소수점 아래 한 자리만 출력하도록 `round()` 함수를 사용했다.

| department | spend_2019 | spend_2022 | pct_change |
| ---------- | ---------- | ---------- | ---------- |
| Assessor   | 178,556    | 179,500    | 0.5        |
| Building   | 250,000    | 289,000    | 15.6       |
| Clerk      | 451,980    | 650,000    | 43.8       |
| Library    | 87,777     | 90,001     | 2.5        |
| Parks      | 250,000    | 223,000    | -10.8      |
| Water      | 199,000    | 195,000    | -2         |


### 평균 및 총합 집계 함수 사용하기

```sql
SELECT sum(pop_est_2019) AS county_sum,
	round(avg(pop_est_2019), 0) AS county_average
FROM us_counties_pop_est_2019;
```
- `SQL`에서는 동일한 열 내의 값들을 모아 계산할 수도 있다.
- 가장 많이 사용되는 함수는 `avg()` 또는 `sum()`이다.
- 결과는 다음과 같다.

|county_sum |county_average|
|-----------|--------------|
|328,239,523|104,468       |


### 중앙값 찾기
- 숫자 집합의 중앙값은 평균 만큼이나 중요한 지표다.
- 평균값과 중앙값의 정의는 다음과 같다.

1. 평균값: 모든 값의 값의 개수로 나눈 값
2. 중앙값: 정렬된 값 집합의 중간 값

- 중앙값이 중요한 이유는 그것이 특이치의 영향을 감소시키기 때문이다.
- 가령 6명이 체험 학습을 간다고 가정하자.
- `(10 + 11 + 10 + 9 + 13 + 12) / 6 = 10.8`
- 연령이 좁은 범위 내에 분포되어 있기 때문에 위 평균값은 그룹을 잘 대표한다.
- 만약 나이 많은 보호자가 참가한다면 이야기가 달라진다.
- `(10 + 11 + 10 + 9 + 13 + 12 + 46) / 7 = 15.9```
- 46이라는 특이치가 그룹을 왜곡하여 이제는 평균값은 신뢰할 수 없는 지표가 되었다.
- 이럴 때 중앙값이 요긴하게 쓰인다.
- 정렬된 목록에서 중간에 있는 값은 11로, 그룹의 연령 분포를 감안하면 평균값 15.9보다는 중앙값 11이 그룹 내의 일반적인 연령을 더욱 잘 보여준다.
- 좋은 테스트는 값 그룹에 대한 평균과 중앙값을 계산하는 것이다.
- 두 값이 가까우면 그룹이 정규 분포를 따르니 평균이 유용하고, 아니라면 중앙값이 더 나은 표현이다.
- 참고로 데이터의 개수가 짝수 개여서 논리적인 중앙값이 두 개라면, 그 두 값의 평균이 중앙값이 된다.

#### 백분위수 함수를 사용하여 중앙값 찾기
- 대부분의 관계형 데이터베이스와 마찬가지로 `PostgreSQL`에서는 중앙값을 반환하는 `median()` 함수가 내장되어 있지 않다.
- 대신 우리는 `SQL` 백분위수 함수를 이용하여 중앙값을 찾고 분위수 또는 절단점을 이용해 숫자 그룹을 동일한 크기로 나눌 수 있다.
- 통계에서 백분위수는 정렬된 데이터 내에서 특정 비율의 데이터가 발견되는 지점을 나타낸다.
- 중앙값은 50번째 백분위수가 동일하다.
- `percentile_cont(n)`, `percentile_disc(n)`라는 두 버전의 백분위수 함수가 있다.
- 두 함수 모두 `ANSI SQL`의 일부이다.

```sql
CREATE TABLE percentile_test (
	numbers integer
);

INSERT INTO percentile_test (numbers) VALUES
	(1), (2), (3), (4), (5), (6);

SELECT
	percentile_cont(.5)
	WITHIN GROUP (ORDER BY numbers),
	percentile_disc(.5)
	WITHIN GROUP (ORDER BY numbers)
FROM percentile_test;
```
- `percentile_cont()` 함수는 백분위수를 연속 값으로 계산하기 때문에 결과가 집합 내의 숫자들로만 표시되지는 않는다.
- 예를 들어 짝수 개의 중앙값을 계산하듯, 두 중간 숫자의 평균을 나타낸다.
- 반면 `percentile_disc()` 함수는 집합의 숫자 중 하나로 반올림 된다. 
- 결과는 다음과 같다.

| percentile_cont | percentile_disc |
| --------------- | --------------- |
| 3.5             | 3               |

- 상황에 따라 다르겠지만, 본디 중앙값 개념에 충실한 `percentile_cont()` 함수를 사용하는 것이 권장된다.

#### 인구 조사 데이터로 중앙값 및 백분위수 계산하기

```sql
SELECT sum(pop_est_2019) AS county_sum,
	round(avg(pop_est_2019), 0) AS county_average,
	percentile_cont(.5)
	WITHIN GROUP (ORDER BY pop_est_2019) AS county_median
FROM us_counties_pop_est_2019;
```

|county_sum |county_average|county_median|
|-----------|--------------|-------------|
|328,239,523|104,468       |25,726       |

- 중앙값과 평균값이 멀리 떨어져 있는데, 이는 평균값이 오도될 수 있음을 의미한다.
- 단순히 집계치를 제공할 것이 아니라, 의미있는 집계치를 제공하는 것이 올바르다.

#### 백분위수 함수를 사용하며 다른 분위수 찾기
- 데이터를 동일한 크기의 더 작은 그룹들로 분할할 수도 있다.
- 가장 일반적인 것은 사분위수, 오분위수, 십분위수이다.
- 개별 값을 찾으려면 `percentile_cont(.25)`와 같이 백분위수 함수에 연결하기만 하면 된다.
- 그러나 여러 개의 절단점을 생성하려는 경우에는 배열을 통해 다음과 같이 사용할 수 있다.

```sql
-- 사분위수
SELECT percentile_cont(ARRAY[.25,.5,.75])
	WITHIN GROUP (ORDER BY pop_est_2019) AS quartiles
FROM us_counties_pop_est_2019;

-- 오분위수
SELECT percentile_cont(ARRAY[.2,.4,.6,.8])
	WITHIN GROUP (ORDER BY pop_est_2019) AS quintiles
FROM us_counties_pop_est_2019;

-- 십분위수
SELECT percentile_cont(ARRAY[.1,.2,.3,.4,.5,.6,.7,.8,.9])
	WITHIN GROUP (ORDER BY pop_est_2019) AS deciles
FROM us_counties_pop_est_2019;
```
- 배열은 `ANSI SQL` 표준이며, 여기에서 사용하는 문법은 `PostgreSQL`에서 배열을 사용하는 여러 방법 중 하나이다.

```sql
SELECT unnest(
	percentile_cont(ARRAY[.25,.5,.75])
	WITHIN GROUP (ORDER BY pop_est_2019)
	) AS quartiles
FROM us_counties_pop_est_2019;
```
- `unnest()` 함수를 사용하여 배열을 여러 개의 행으로 출력할 수도 있다.


### 최빈값 찾기

```sql
SELECT mode() WITHIN GROUP (ORDER BY births_2019)
FROM us_counties_pop_est_2019;
```
- `PostgreSQL`에서는 `mode()` 함수를 사용하여 열에서 가장 많이 등장하는 값인 최빈값을 구할 수 있다.
- `WITHIN GROUP (ORDER BY ...)`는 집계 함수가 계산에 사용할 값의 순서를 지정하는 것이다.
- 가령 데이터가 `[1, 2, 2, 3, 3]`일 때 2와 3에 동률이 존재하는데, `WITHIN GROUP (ORDER BY column ASC)`라면 2가 나올 것이고, `WITHIN GROUP (ORDER BY column DESC)`라면 3이 나올 것이다.