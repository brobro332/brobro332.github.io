---
title: 🐘 PostgreSQL 기본 11 - SQL 통계 함수
date: 2025-08-19 18:10:00 +0900
categories:
  - Database
tags:
  - Database
  - PostgreSQL
---

![](/assets/image/Pasted%20image%2020250813142544.png)
> 📙 `『실용 SQL』`을 읽고 정리한 글입니다.

### 개요
- 대개는 `SPSS`, `SAS`, `R`, `Python`을 통해 통계 분석을 수행한다.
- 그러나 `PostgreSQL` 구현을 비롯해 표준 `ANSI SQL`은 데이터셋을 다른 프로그램으로 내보낼 필요 없이 데이터에 대한 많은 정보를 보여주는 몇 가지 강력한 통계 기능을 제공한다.

#### 인구조사 통계 테이블 생성하기

```sql
CREATE TABLE acs_2014_2018_stats (
	geoid text CONSTRAINT geoid_key PRIMARY KEY,
	county text NOT NULL,
	st text NOT NULL,
	pct_travel_60_min numeric(5,2),
	pct_bachelors_higher numeric(5,2),
	pct_masters_higher numeric(5,2),
	median_hh_income integer,
	CHECK (pct_masters_higher <= pct_bachelors_higher)
);

COPY acs_2014_2018_stats
FROM 'C:\YourDirectory\acs_2014_2018_stats.csv'
WITH (FORMAT CSV, HEADER);

SELECT * FROM acs_2014_2018_stats;
```
- `pct_travel_60_min`: 출퇴근 시간이 60분 이상인 16세 이상 근로자의 비율이다.
- `pct_bachelors_higher`: 교육 수준이 학사 이상인 25세 이상 인구의 비율이다.
- `pct_masters_higher`: 교육 수준이 석사 이상인 25세 이상 인구의 비율이다.
- `median_hh_income`: 2018년 인플레이션 조정 달러 기준 카운티의 평균 가계 소득이다.
- 미국에서는 학사 학위가 석사 학위 이전 또는 동시에 취득되기 때문에 학사 학위 수치가 석사 학위 수치와 같거나 높은지 확인하기 위해 `CHECK` 제약 조건을 포함한다.

#### `corr(Y, X)`를 사용하여 상관 관계 측정하기
- 상관 관계란 한 변수의 변화가 다른 변수의 변화에 영향을 미치는 정도를 의미한다.
- 이 값은 두 변수 간의 통계적 관계를 측정해 구한다.
- 필요한 배경 지식은 다음과 같다.

- 일반적으로 `r`로 표시되는 피어슨 상관 계수는 두 변수 간의 선형 관계의 강도를 정량화하기 위한 척도다.
- 한 변수의 증가 또는 감소가 다른 변수와 연관되는 정도를 보여준다.
- `r` 값은 -1과 1 사이에 잇다.
- 범위의 끝인 -1과 1은 완벽한 상관 관계를 보여준다.
- 반면, 0에 가까운 값은 상관 관계가 없는 무작위 분포를 나타낸다.
- 양수 `r` 값은 직접 관계에 있는 각 값 쌍을 나타내며, 데이터 포인트는 왼쪽에서 오른쪽 위로 이어진다.
- 음의 `r` 값은 역관계를 나타낸다.
- 한 변수가 증가하면 다른 변수는 감소하며, 데이터 포인트는 왼쪽에서 오른쪽 아래로 기울어진다.

| 상관 계수   | 해석             |
| ----------- | ---------------- |
| 0.00 ~ 0.19 | 매우 약한 상관   |
| 0.20 ~ 0.39 | 약한 상관        |
| 0.40 ~ 0.59 | 중간 정도의 상관 |
| 0.60 ~ 0.79 | 강한 상관        |
| 0.80 ~ 1.00 | 매우 강한 상관   |

- 표준 `ANSI SQL` 및 `PostgreSQL`에서는 `corr(Y, X)`를 사용하여 피어슨 상관 계수를 계산한다.
- `corr(Y, X)`는 여러 이진 집계 함수 중 하나다.
- 이진 집계 함수란 두 개의 입력을 받아들이는 함수이다.
- 이진 집계 함수에서 입력 `Y`는 다른 변수 값에 의존하여 그에 따라 값이 달라지는 종속 변수이고, `X`는 값이 다른 변수에 종속되지 않는 독립 변수이다.

```sql
SELECT corr(median_hh_income, pct_bachelors_higher)
	AS bachelors_income_r
FROM acs_2014_2018_stats;
```

|bachelors_income_r|
|------------------|
|0.6999086503      |

- 이 양수 `r` 값은 카운티의 교육 정도가 증가함에 따라 가구 소득이 증가하는 경향이 강함을 나타낸다.

#### 추가 상관 관계 나타내기

```sql
SELECT
	round(
		corr(median_hh_income, pct_bachelors_higher)::numeric, 2
		) AS bachelors_income_r,
	round(
		corr(pct_travel_60_min, median_hh_income)::numeric, 2
		) AS income_travel_r,
	round(
		corr(pct_travel_60_min, pct_bachelors_higher)::numeric, 2
		) AS bachelors_travel_r
FROM acs_2014_2018_stats;
```
- 이번에는 소수 값을 반올림하여 출력을 더 읽기 쉽게 만든 쿼리다.
- 출력은 다음과 같다.

|bachelors_income_r|income_travel_r|bachelors_travel_r|
|------------------|---------------|------------------|
|0.7               |0.06           |-0.14             |

- `income_travel_r`, `bachelors_travel_r` 값은 상관 관계가 거의 없음을 나타낸다.
- 특히 `bachelors_travel_r`는 역관계를 나타낸다.
- 상관 관계를 테스트할 때는 다음 몇 가지 주의 사항을 염두에 두어야 한다.

1. 강한 상관 관계조차 인과 관계를 의미하지는 않는다.
2. 상관 관계가 통계적으로 유의한지 여부를 확인하기 위해 테스트를 거쳐야 한다.
	- 이 테스트는 책에서 다루는 범위 이상의 영역이다.

#### 회귀 분석으로 값 예측하기
- 모든 데이터 포인트의 중간을 지나는 직선을 최소 제곱 회귀선이라고 한다.
- 변수 간의 관계를 가장 잘 설명하는 직선의 최적 적합에 가깝다.
- 회귀선에 대한 방정식은 기울기-절편 공식과 유사하지만, 다른 이름의 변수를 사용하여 작성되었다.
- `Y = bX + a`라는 공식의 구성 요소는 다음과 같다.

1. `Y`: 예측된 값이며, `y`축의 값이거나 종속 변수이다.
2. `b`
	- 선의 기울기이며, 양수 또는 음수일 수 있다.
	- `x`축 값의 각 단위에 대해 `y`축 값이 증가하거나 감소할 단위 수를 측정한다.
3. `X`: `x`축의 값 또는 독립 변수를 나타낸다.
4. `a`: `y` 절편, 즉 `x` 값이 0일 때 선이 `y`축과 교차하는 값이다.

- 가령 `x`축이 학사 학위 이상의 비율을 나타내고, `y`축이 가계 소득 중앙값을 나타낸다고 하자.
- 이때 카운티 인구의 30%가 학사 학위 이상을 가지고 있다면 카운티의 가계 소득 중간 값은 얼마일까?
- 회귀선 공식의 `X`자리에 30을 넣으면 `Y = b(30) + a`와 같다.
- 예상 가구 소득 중앙값을 나타내는 `Y`를 구하려면 선의 기울기인 `b`와 `y` 절편인 `a`가 필요하다.
- 이러한 값을 얻기 위해 다음과 같이 `regr_slope(Y, X)`와 `regr_intercept(Y, X)`를 사용한다.

```sql
SELECT
	round(
		regr_slope(median_hh_income, pct_bachelors_higher)::numeric, 2
		) AS slope,
	round(
		regr_intercept(median_hh_income, pct_bachelors_higher)::numeric, 2
		) AS y_intercept
FROM acs_2014_2018_stats;
```
- 쿼리를 실행하면 결과가 다음과 같다.

|slope   |y_intercept|
|--------|-----------|
|1,016.55|29,651.42  |

- 이제 두 값을 방정식에 넣어 결과를 구하면 소득은 약 $60.148가 될 것으로 예상할 수 있다.
- 우리가 계산한 상관 계수는 0.70으로, 교육과 소득 사이의 관계는 강력하지만 완벽하지 않음을 기억해야 한다.

#### `r`-제곱을 사용하여 독립 변수의 효과 찾기

```sql
SELECT round(
	regr_r2(median_hh_income, pct_bachelors_higher)::numeric, 3
	) AS r_squared
FROM acs_2014_2018_stats;
```
- `r`-제곱은 결정 계수라고도 하며, 0과 1 사이의 값을 갖고 독립 변수로 설명되는 변동의 백분률을 나타낸다.
- 예를 들어 `r`- 제곱 값이 0.1이면 독립 변수가 종속 변수의 10%를 설명하거나 전혀 설명하지 않는다고 할 수 있다.
- `regr_r2(Y, X)` 함수를 사용하여 `r`-제곱 값을 찾는다.

|r_squared|
|---------|
|0.49     |

- 위 결과는 카운티의 중간 가구 소득 변동의 약 49%가 해당 카운티의 학사 학위 이상을 가진 사람들의 비율로 설명될 수 있음을 나타낸다.
- 나머지 51%를 설명하는 것은 여러 요인이 될 수 있다.
- 다시 한번 상관 관계는 인관 관계를 증명하지는 않음을 명심해야 한다.

#### 분산과 표준편차 찾기
- 분산과 표준편차는 값들이 평균에서 떨어져 있는 정도를 나타낸다.
- 분산은 `(각 숫자 - 평균)^2`의 평균이며 값이 많이 흩어질 수록 분산은 커지며, 금융에서 자주 사용된다.
- 가령 주식 시장 거래자는 분산을 사용해 특정 주식의 변동성을 측정하여 해당 주식이 얼마나 위험한 투자인지 알 수 있다.
- 표준편차는 분산의 제곱근으로, 일반적으로 정규 분포를 형성하는 데이터를 평가하는데 가장 유용하다.
- 정규 분포는 종 모양의 대칭 곡선으로 시각화되며, 값의 약 2/3는 평균의 표준편차 1 이내에 속한다.
- 95%는 표준편차 2개 범위 안에 있다.
- 따라서 표준편차는 대부분의 값이 평균에 얼마나 가까운지 이해하는데 도움이 된다.
- 가령 미국 성인 여성의 평균 키는 약 166cm이고 표준편차는 6.5cm라는 결과가 나왔다고 하면, 키가 정규 분포를 따른다는 점을 감안하면, 이는 여성의 약 2/3가 평균의 6.5cm 이내인 159.5cm에서 172.5cm 사이임을 의미한다.

##### 분산을 계산하는 함수
1. `var_pop(numeric)`
	- 입력 값의 모집단 분산을 계산한다.
	- 이 컨텍스트에서 모집단은 가능한 모든 값을 포함한다.
2. `var_samp(numeric)`
	- 입력 값의 표본 분산을 계산한다.
	- 무작위 표본 조사에서와 같이 모집단에서 샘플링된 데이터와 함께 사용한다.

##### 표준편차를 계산하는 함수
1. `stddev_pop(numeric)`: 모집단 표준편차를 계산한다.
2. `stddev_samp(numeric)`: 샘플 표준편차를 계산한다.

#### 예제 코드
```sql
-- 전체 인구의 분산
SELECT var_pop(median_hh_income)
FROM acs_2014_2018_stats;

-- 전체 인구의 표준편차
SELECT stddev_pop(median_hh_income)
FROM acs_2014_2018_stats;
```


### `SQL`을 사용하여 순위 매기기
#### `rank()` 및 `dense_rank()`로 순위 매기기

```sql
CREATE TABLE widget_companies (
	id integer PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
	company text NOT NULL,
	widget_output integer NOT NULL
);

INSERT INTO widget_companies (company, widget_output)
VALUES
	('Dom Widgets', 125000),
	('Ariadne Widget Masters', 143000),
	('Saito Widget Co.', 201000),
	('Mal Inc.', 133000),
	('Dream Widget Inc.', 196000),
	('Miles Amalgamated', 620000),
	('Arthur Industries', 244000),
	('Fischer Worldwide', 201000);

SELECT
	company,
	widget_output,
	rank() OVER (ORDER BY widget_output DESC),
	dense_rank() OVER (ORDER BY widget_output DESC)
FROM widget_companies
ORDER BY widget_output DESC;
```

|company               |widget_output|rank|dense_rank|
|----------------------|-------------|----|----------|
|Miles Amalgamated     |620,000      |1   |1         |
|Arthur Industries     |244,000      |2   |2         |
|Fischer Worldwide     |201,000      |3   |3         |
|Saito Widget Co.      |201,000      |3   |3         |
|Dream Widget Inc.     |196,000      |5   |4         |
|Ariadne Widget Masters|143,000      |6   |5         |
|Mal Inc.              |133,000      |7   |6         |
|Dom Widgets           |125,000      |8   |7         |

- 결과를 보면 알 수 있듯이, `rank()` 함수는 순위 순서에 간격을 포함하지만 `dense_rank()` 함수는 그렇지 않다.
- 순위 함수 뒤에 `OVER` 키워드를 사용하는데, 괄호 안에는 함수가 작동해야 하는 행의 창을 지정하는 표현식을 배치한다.
- 여기서 윈도우는 현재 행을 기준으로 설정한 행 집합으로, 여기선 두 함수가 내림차순으로 정렬된 `widget_output` 열의 모든 행에서 작동한다.
- 실제로는 `rank()`가 가장 자주 사용되는데, `Dream Widget Inc.`보다 앞선 회사가 총 4개라는 사실을 보여줌으로써 순위가 매겨진 전체 회사 수를 더 정확하게 반영한다.

#### `PARTITION BY`를 사용하여 하위 그룹 내 순위 지정하기
- 때로는 테이블의 행 그룹 내에서 순위를 생성하고자 할 수 있다.
- 가령 각 부서 내 급여 별로 공무원 순위를 매기거나, 각 장르 내 흥행 수익을 기준으로 영화 순위를 매길 수 있다.
- 이러한 방식으로 윈도우 함수를 사용하기 위해 `OVER` 절에 `PARTITION BY`를 추가하여 지정한 열의 값에 따라 테이블 행을 나눈다.

```sql
CREATE TABLE store_sales (
	store text NOT NULL,
	category text NOT NULL,
	unit_sales bigint NOT NULL,
	CONSTRAINT store_category_key PRIMARY KEY (store, category)
);

INSERT INTO store_sales (store, category, unit_sales)
VALUES
	('Broders', 'Cereal', 1104),
	('Wallace', 'Ice Cream', 1863),
	('Broders', 'Ice Cream', 2517),
	('Cramers', 'Ice Cream', 2112),
	('Broders', 'Beer', 641),
	('Cramers', 'Cereal', 1003),
	('Cramers', 'Beer', 640),
	('Wallace', 'Cereal', 980),
	('Wallace', 'Beer', 988);

SELECT
	category,
	store,
	unit_sales,
	rank() OVER (PARTITION BY category ORDER BY unit_sales DESC)
FROM store_sales
ORDER BY category, rank() OVER (PARTITION BY category
	ORDER BY unit_sales DESC);
```

|category |store  |unit_sales|rank|
|---------|-------|----------|----|
|Beer     |Wallace|988       |1   |
|Beer     |Broders|641       |2   |
|Beer     |Cramers|640       |3   |
|Cereal   |Broders|1,104     |1   |
|Cereal   |Cramers|1,003     |2   |
|Cereal   |Wallace|980       |3   |
|Ice Cream|Broders|2,517     |1   |
|Ice Cream|Cramers|2,112     |2   |
|Ice Cream|Wallace|1,863     |3   |

- 마지막 쿼리를 통해 카테고리 별로 각 상점의 판매량 순위를 보여주는 결과 집합을 만든다.
- 각 카테고리에 대한 행은 순위를 표시하는 `rank` 열과 함께 제품 판매량 순으로 정렬된다.
- 이 개념을 다양한 상황에 적용할 수 있다.


### 비율 계산을 통한 의미 있는 결과 찾기
- 개수를 기반으로 한 순위가 항상 의미 있는 것은 아니다.
- 사실, 개수로만 순위를 매기면 오해가 생긴다.
- 가령 2019년에 텍사스 주에서 377, 599명의 아기가 태어났고, 유타주에서 46,826명의 아기가 태어났다고 할 때, 텍사스의 여성들이 아기를 낳을 확률이 더 높다고 속단할 수 있을까?
- 그렇지 않다.
- 2019년 텍사스의 추정 인구는 유타의 9배였고, 이런 상황에서 두 주의 출생 수를 비교하는 것은 그다지 의미가 없다.
- 이 숫자를 비교하는 더 정확한 방법은 비율로 변환하는 것이다.

#### 관광 사업체의 비율 구하기

```sql
CREATE TABLE cbp_naics_72_establishments (
	state_fips text,
	county_fips text,
	county text NOT NULL,
	st text NOT NULL,
	naics_2017 text NOT NULL,
	naics_2017_label text NOT NULL,
	year smallint NOT NULL,
	establishments integer NOT NULL,
	CONSTRAINT cbp_fips_key PRIMARY KEY (state_fips, county_fips)
);

COPY cbp_naics_72_establishments
FROM 'C:\YourDirectory\cbp_naics_72_establishments.csv'
WITH (FORMAT CSV, HEADER);

SELECT *
FROM cbp_naics_72_establishments
ORDER BY state_fips, county_fips
LIMIT 5;
```
- 위 코드는 인구 조사 카운티 기업 패턴 데이터에 대한 테이블을 생성하고 데이터를 가져오는 쿼리다.

```sql
SELECT
	cbp.county,
	cbp.st,
	cbp.establishments,
	pop.pop_est_2018,
	round( (cbp.establishments::numeric / pop.pop_est_2018) * 1000, 1 )
		AS estabs_per_1000
FROM cbp_naics_72_establishments cbp JOIN us_counties_pop_est_2019 pop
	ON cbp.state_fips = pop.state_fips
	AND cbp.county_fips = pop.county_fips
WHERE pop.pop_est_2018 >= 50000
ORDER BY cbp.establishments::numeric / pop.pop_est_2018 DESC;
```
- 인구 천 명당 사업체 수를 확인하기 위한 쿼리다.

|county          |st        |establishments|pop_est_2018|estabs_per_1000|
|----------------|----------|--------------|------------|---------------|
|Cape May County |New Jersey|925           |92,446      |10             |
|Worcester County|Maryland  |453           |51,960      |8.7            |
|Monroe County   |Florida   |540           |74,757      |7.2            |
|Warren County   |New York  |427           |64,215      |6.6            |
|New York County |New York  |10,428        |1,629,055   |6.4            |
|Hancock County  |Maine     |337           |54,734      |6.2            |
|Sevier County   |Tennessee |570           |97,895      |5.8            |

- 위 비율은 단순 사업체의 수를 비교한 것이 아니기 때문에 의미 있는 데이터임을 알 수 있다.


### 고르지 않은 데이터 다듬기
- 이동 평균은 데이터셋에서 일정 기간마다 측정한 평균으로, 일정량의 행을 입력으로 사용한다.

```sql
CREATE TABLE us_exports (
	year smallint,
	month smallint,
	citrus_export_value bigint,
	soybeans_export_value bigint
);

COPY us_exports
FROM 'C:\YourDirectory\us_exports.csv'
WITH (FORMAT CSV, HEADER);

-- 월별 감귤류 수출량 확인
SELECT year, month, citrus_export_value
FROM us_exports
ORDER BY year, month;

-- 롤링 평균 계산
SELECT year, month, citrus_export_value,
	round(
		avg(citrus_export_value)
			OVER(ORDER BY year, month
				ROWS BETWEEN 11 PRECEDING AND CURRENT ROW), 0)
	AS twelve_month_avg
FROM us_exports
ORDER BY year, month;
```
- 감귤류와 대두의 월별 수출액을 보여주는 데이터를 가져오는 쿼리다.

|year |month|citrus_export_value|
|-----|-----|-------------------|
|2,019|10   |26,308,151         |
|2,019|11   |60,885,676         |
|2,019|12   |84,873,954         |
|2,020|1    |110,924,836        |
|2,020|2    |171,767,821        |
|2,020|3    |201,231,998        |
|2,020|4    |122,708,243        |
|2,020|5    |75,644,260         |
|2,020|6    |36,090,558         |
|2,020|7    |20,561,815         |
|2,020|8    |15,510,692         |

- 첫 번째 쿼리를 실행한 결과 중 마지막 12개의 행이다.

|year |month|citrus_export_value|twelve_month_avg|
|-----|-----|-------------------|----------------|
|2,019|9    |14,012,305         |74,465,440      |
|2,019|10   |26,308,151         |74,756,757      |
|2,019|11   |60,885,676         |74,853,312      |
|2,019|12   |84,873,954         |74,871,644      |
|2,020|1    |110,924,836        |75,099,275      |
|2,020|2    |171,767,821        |78,874,520      |
|2,020|3    |201,231,998        |79,593,712      |
|2,020|4    |122,708,243        |78,278,945      |
|2,020|5    |75,644,260         |77,999,174      |
|2,020|6    |36,090,558         |78,045,059      |
|2,020|7    |20,561,815         |78,343,206      |
|2,020|8    |15,510,692         |78,376,692      |

- 두 번째 쿼리를 실행한 결과 중 마지막 12개의 행이다.
- 이 결과를 통해 12개월 단위 이동 평균을 계산하여 매월 수출의 연간 추세를 볼 수 있다.
- `OVER` 절을 통해 평균을 계산할 데이터를 연도와 열을 따라 정렬하고 `ROWS BETWEEN 11 PRECEDING AND CURRENT ROW`를 사용하여 평균을 낼 행의 수를 설정한다. 
- 여기서는 현재 행과 그 이전의 11개의 행으로 제한하고 있다.
- 이후에는 `SELECT`에 `avg()` 함수를 배치하여 `citrus_export_value` 열에 있는 값의 평균을 계산한다.
- 엑셀 같은 통계 프로그램으로 결과를 그래프로 표시해보면 12개월 평균이 훨씬 더 일관성이 있음을 알 수 있다.
- 아울러 월간 데이터에서는 움직임을 식별하기 어렵지만, 이동 평균을 보면 제대로 알 수 있다.
- 윈도우 함수는 분석을 위한 여러 옵션을 제공한다.
- 가령 이동 평균을 구하는 대신 `sum()` 함수를 대체하여 일정 기간 동안의 이동 합계를 구할 수도 있다.
- 7일 기준 이동 합계를 계산하면 데이터셋에서 주간 총계가 언제 집계되는지 파악할 수 있다.

> 이동 평균 또는 이동 합계 계산은 데이터의 기간에 누락된 값이 없어야 잘 계산된다.
> 윈도우 함수는 날짜가 아닌 행의 개수를 기준으로 삼으므로 누락된 값이 있을 경우 그만큼 값이 밀린다.