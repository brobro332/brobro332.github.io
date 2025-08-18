---
title: 🐘 PostgreSQL 기본 Ⅴ - 데이터 가져오고 내보내기
date: 2025-08-15 19:11:00 +0900
categories:
  - Database
tags:
  - Database
  - PostgreSQL
---

![](/assets/image/Pasted%20image%2020250813142544.png)
> 📙 `『실용 SQL』`을 읽고 정리한 글입니다.

### 개요
- 구분된 텍스트 파일에 데이터가 있는 경우 `PostgreSQL`은 `COPY` 명령을 통해 대량으로 데이터를 가져올 수 있다.
- `PostgreSQL` 전용인 `COPY` 명령에는 열을 포함하거나 제외하고 다양한 구분된 텍스트 타입을 처리하는 옵션이 포함되어 있다.
- 반대로 `COPY`는 테이블 또는 쿼리 결과를 구분된 텍스트 파일로 내보내기도 한다.
- 이 기술은 동료와 데이터를 공유하거나 엑셀 파일과 같은 다른 형식으로 데이터를 옮기는 경우에 유용하다.
- 일반적으로 다음 과정을 따른다.

1. 구분된 텍스트 파일 형식의 소스 데이터를 준비
2. 데이터를 저장할 테이블 생성
3. `COPY` 스크립트를 작성하여 데이터 가져오기

- 참고로 `Microsoft` 엑세스 또는 `MySQL`과 같은 데이터베이스에서 `PostgreSQL`로 직접 데이터를 옮기려면 서드파티 도구를 사용해야 한다.


### 구분된 텍스트 파일을 이용하여 작업하기
- 구분된 텍스트 파일에서는 데이터 행이 포함되어 있으며, 각 행은 테이블의 한 행을 나타낸다.
- 각 행에서 문자는 각각의 데이터 열을 분리하거나 구분한다.
- 쉼표가 가장 일반적으로 사용되며, 따라서 자주 볼 수 있는 파일은 쉼표로 분리된 값을 의미하는 `CSV` 파일이다.

#### 헤더 행 처리하기
- 구분된 텍스트 파일에는 보통 헤더 행이 포함된다.
- 헤더 행은 각 열의 데이터를 식별하는 데 사용되며, 일부 데이터베이스 관리자는 헤더 행을 사용하여 구분된 파일의 열을 가져오기 테이블의 올바른 열에 매핑한다.
- 그러나 `PostgreSQL`는 헤더 행을 사용하지 않는다.
- 따라서 헤더 행을 제외하기 위해 `COPY` 명령에서 특정 옵션을 사용한다.

#### 큰따옴표로 묶은 값 읽어오기
- 쉼표를 열 구분 기호로 사용하는 경우는 잠재적인 딜레마가 도사리고 있다.
- 가령 "안녕하세요, 자기소개입니다."와 같이 열의 값에 쉼표가 포함되어 있다면 어떻게 해야 할까?
- 이런 경우 구분 기호가 포함된 열을 텍스트 한정자라는 임의의 문자로 감싸 `SQL`에 포함된 구분 기호를 무시하도록 지시한다.
- 대부분 구분된 파일에서 사용하는 텍스트 한정자는 큰따옴표이다.
- 기본적으로 `PostgreSQL`은 기본적으로 큰따옴표로 묶인 열 안의 구분 기호를 무시하지만, 가져오기에 필요한 경우 다른 텍스트 한정자를 지정할 수 있다.
- 마지막으로 `PostgreSQL`은 다음과 같이 큰따옴표로 묶인 열 안에서 텍스트 한정자가 두 번 연속으로 나오면 하나를 제거한다.

```bash
# PostgreSQL이 읽은 열
"123 Main St."" Apartment 200"

# PostgreSQL이 처리한 열
123 Main St." Apartment 200
```


### `COPY`를 사용해 데이터 가져오기

```sql
COPY table_name
FROM 'C:\directory\file.csv'
WITH (FORMAT CSV, HEADER);
```
- 먼저 소스 파일의 열과 데이터 타입을 확인하고 그 데이터를 보관할 테이블을 만들어야 한다.
- 코드는 `COPY` 키워드로 시작하여 대상 테이블의 이름이 뒤따른다.
- `FROM` 키워드는 이름을 포함하여 소스 파일의 전체 경로를 식별한다.
- 운영체제에 따라 경로 문자열의 형식이 다르다.
- `WITH` 키워드는 원하는 옵션을 괄호로 감싸 입력하거나, 출력 형식에 맞게 조정할 수 있는 옵션을 지정한다.
- 일반적으로 사용할 옵션은 다음과 같다.

1. 입력 및 출력 파일 형식
	- `FORMAT` 키워드를 통해 읽거나 쓰는 파일의 형식을 지정한다.
	- 파일 형식에는 `CSV`, `TEXT`, `BINARY` 등이 있다.
2. 헤더 행 포함 여부
	 - 데이터를 가져올 때 `HEADER` 키워드를 사용하여 소스 파일에 제외 할 헤더 행이 있음을 지정한다.
	 - 내보낼 때는 `HEADER`를 사용하면 데이터베이스가 헤더 행을 포함하도록 지시한다.
3. 구분 기호
	- `DELIMITER` 키워드를 통해 가져오기 또는 내보내기 파일에서 구분자로 사용할 문자를 지정한다.
4. 인용 문자
	- `QUOTE` 키워드를 통해 큰따옴표가 아닌 텍스트 한정자를 지정할 수 있다.


### 카운티 인구조사 데이터 가져오기
- 지금 사용할 데이터는 인구조사의 연간 인구 추정치이다.
- 이들은 최근의 10년 인구 조사를 통해 출생과 사망, 국내 및 국제 이주를 고려하여 매년 국가와 주, 카운티 및 기타 지역의 인구 추정치를 산출한다.

#### `us_counties_pop_est_2019` 테이블 만들기

```sql
CREATE TABLE us_counties_pop_est_2019 (
	state_fips text,
	county_fips text,
	region smallint,
	state_name text,
	county_name text,
	area_land bigint,
	area_water bigint,
	internal_point_lat numeric(10,7),
	internal_point_lon numeric(10,7),
	pop_est_2018 integer,
	pop_est_2019 integer,
	births_2019 integer,
	deaths_2019 integer,
	international_migr_2019 integer,
	domestic_migr_2019 integer,
	residual_2019 integer,
	CONSTRAINT counties_2019_key PRIMARY KEY (state_fips, county_fips)
);
```

#### 인구 조사 데이터 열과 데이터 타입 이해하기
- 존재한다면, 데이터 사전을 확인하거나 온라인을 통해 데이터를 확인하는 것이 중요하다.
- `state_fips`, `county_fips` 등 코드 값은 정수로 저장하면 0으로 시작되는 코드가 망가질 수 있으므로 `text` 타입을 사용해야 한다.
- 또한 이 값들로 계산을 수행할 일도 없다.
- `region`에는 1에서 4까지의 숫자가 저장되므로 `smallint` 타입으로 열을 정의한다.
- `state_name`, `county_name`에는 `text` 타입을 사용한다.
- 카운티에 있는 토지와 물에 대한 면적은 `area_land`, `area_water`에 기록된다.
- 이 둘을 합치면 카운티의 전체 면적을 구할 수 있다.
- 알래스카처럼 눈이 잔뜩 쌓인 지역은 `integer` 타입의 최댓값을 넘길 가능성이 높으므로 `bigint` 타입을 사용한다.
- `internal_point_lat`, `internal_point_lon`은 각각 내점의 위도와 경도를 나타낸다.
- 인구조사국은 내점을 기록할 때 소수점 이하 7자리까지 사용하기 때문에, 정수 부분의 최댓값인 180까지 사용하려면 필요한 자릿수는 총 10자리다.
- 그러므로 `numeric(10, 7)`을 사용한다.

#### `COPY`로 인구조사 데이터 가져오기

```sql
COPY us_counties_pop_est_2019
FROM 'C:\YourDirectory\us_counties_pop_est_2019.csv'
WITH (FORMAT CSV, HEADER);
```
- `Docker`를 사용한다면 `docker cp us_counties_pop_est_2019.csv postgres-practice:/tmp/my_data.csv` 명령어를 통해 컨테이너에 파일을 넘겨야 한다.

```bash
Updated Rows	3142
Execute time	0.032s
Start time	Fri Aug 15 21:09:42 KST 2025
Finish time	Fri Aug 15 21:09:42 KST 2025
Query	COPY us_counties_pop_est_2019
	FROM 'C:\YourDirectory\us_counties_pop_est_2019.csv'
	WITH (FORMAT CSV, HEADER)
```

#### 가져온 데이터 검사하기

```sql
-- 모든 열과 행 조회
SELECT * FROM us_counties_pop_est_2019;

-- area_land 값이 큰 세 개의 행 조회
SELECT county_name, state_name, area_land
FROM us_counties_pop_est_2019
ORDER BY area_land DESC
LIMIT 3;

-- internal_point_lon 값이 큰 다섯 개의 행 조회
SELECT county_name, state_name, internal_point_lat, internal_point_lon
FROM us_counties_pop_est_2019
ORDER BY internal_point_lon DESC
LIMIT 5;
```


### `COPY`를 사용하여 열 하위 집합 가져오기

```sql
CREATE TABLE supervisor_salaries (
	id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
	town text,
	county text,
	supervisor text,
	start_date text,
	salary numeric(10,2),
	benefits numeric(10,2)
);

-- 오류 발생
COPY supervisor_salaries
FROM 'C:\YourDirectory\supervisor_salaries.csv'
WITH (FORMAT CSV, HEADER);

-- CSV 테이블 열 지정
COPY supervisor_salaries (town, supervisor, salary)
FROM 'C:\YourDirectory\supervisor_salaries.csv'
WITH (FORMAT CSV, HEADER);
```
- 테이블 생성 후 `COPY` 키워드를 통해 `CSV` 데이터를 가져와야 한다.
- 다만 테이블 열을 따로 지정하지 않으면 `DB` 내 테이블 열의 첫 번째 열인 `id` 값이 `CSV`에는 없으므로 오류가 발생한다.
- 그러므로 `COPY` 키워드 사용 시 `CSV` 테이블의 열도 지정해야 한다.

```bash
Updated Rows	5
Execute time	0.012s
Start time	Fri Aug 15 21:18:33 KST 2025
Finish time	Fri Aug 15 21:18:33 KST 2025
Query	COPY supervisor_salaries (town, supervisor, salary)
	FROM 'C:\YourDirectory\supervisor_salaries.csv'
	WITH (FORMAT CSV, HEADER)
```

```sql
SELECT * FROM supervisor_salaries ORDER BY id LIMIT 2;
```
- 데이터를 확인하면 다음과 같다.

|id |town      |county|supervisor|start_date|salary|benefits|
|---|----------|------|----------|----------|------|--------|
|1  |Anytown   |      |Jones     |          |67,000|        |
|2  |Bumblyburg|      |Larry     |          |74,999|        |


### `COPY`를 사용하여 행의 일부만 가져오기 

```sql
COPY supervisor_salaries (town, supervisor, salary)
FROM 'C:\YourDirectory\supervisor_salaries.csv'
WITH (FORMAT CSV, HEADER)
WHERE town = 'New Brillig';

SELECT * FROM supervisor_salaries;
```
- `WHERE` 키워드를 통해 특정 조건에 해당하는 행만 가져올 수 있다.


### 가져오는 과정에서 열에 값 추가하기
- `CSV` 파일의 카운티 열에 `Mills`라는 이름이 없는데, 데이터를 가져오는 과정에서 그 값이 필요하다는 걸 알게 된다면 어떻게 해야 할까?
- 바로 `CSV` 파일을 가져오기 전에 임시 테이블에 로드하는 것이다.
- 임시 테이블은 데이터베이스 세션을 종료하기 전까지만 존재하므로 데이터베이스를 다시 열거나 연결을 끊으면 해당 테이블은 사라진다.

```sql
CREATE TEMPORARY TABLE supervisor_salaries_temp
(LIKE supervisor_salaries INCLUDING ALL);

COPY supervisor_salaries_temp (town, supervisor, salary)
FROM 'C:\YourDirectory\supervisor_salaries.csv'
WITH (FORMAT CSV, HEADER);

INSERT INTO supervisor_salaries (town, county, supervisor, salary)
SELECT town, 'Mills', supervisor, salary
FROM supervisor_salaries_temp;

DROP TABLE supervisor_salaries_temp;
```
- 임시 테이블로 `CSV` 데이터를 가져와서, 해당 데이터를 기반으로 `count` 열의 값을 `Mills`로 바꾼 값을 원본 테이블에 삽입한다.
- 이후 임시 테이블을 삭제한다.
- 물론 직접 삭제하지 않아도 연결이 끊어질 경우 자동으로 사라지지만, 다른 `CSV` 파일을 가져와 새로운 임시 테이블을 사용하려는 경우 이렇게 삭제해주어야 한다.
- `SELECT * FROM supervisor_salaries ORDER BY id LIMIT 2;`를 통해 데이터를 확인한 결과는 다음과 같다.

|id |town      |county|supervisor|start_date|salary|benefits|
|---|----------|------|----------|----------|------|--------|
|21 |Anytown   |Mills |Jones     |          |67,000|        |
|22 |Bumblyburg|Mills |Larry     |          |74,999|        |


### `COPY`를 사용하여 데이터 내보내기
- 데이터를 내보낼 때는 `FROM` 절로 데이터를 식별하는 대신 `TO`를 사용한다.
- 전체 테이블을 내보내거나 단 몇 개의 열만 내보낼 수 있다.
- 쿼리 결과를 세밀하게 조정하여 데이터를 내보내기도 한다.

```sql
-- COPY로 전체 테이블 내보내기
COPY us_counties_pop_est_2019
TO 'C:\YourDirectory\us_counties_export.txt'
WITH (FORMAT CSV, HEADER, DELIMITER '|');

-- COPY로 테이블의 특정 열만 내보내기
COPY us_counties_pop_est_2019
	(county_name, internal_point_lat, internal_point_lon)
TO 'C:\YourDirectory\us_counties_latlon_export.txt'
WITH (FORMAT CSV, HEADER, DELIMITER '|');

-- COPY를 이용한 쿼리 결과 내보내기
COPY (
	SELECT county_name, state_name
	FROM us_counties_pop_est_2019
	WHERE county_name ILIKE '%mill%'
	 )
TO 'C:\YourDirectory\us_counties_mill_export.csv'
WITH (FORMAT CSV, HEADER);
```
- 구분 기호를 쉼표로 하지 않는다면, `csv` 파일이 아닌 `txt` 파일로 내보내는 것이 좋다.