---
title: 🐘 PostgreSQL 기본 Ⅻ - 날짜와 시간을 사용한 작업
date: 2025-08-21 12:10:00 +0900
categories:
  - Database
tags:
  - Database
  - PostgreSQL
---

![](/assets/image/Pasted%20image%2020250813142544.png)
> 📙 `『실용 SQL』`을 읽고 정리한 글입니다.

### 날짜 및 시간에 대한 데이터 타입과 함수 이해하기
- 다음은 날짜 및 시간 관련 데이터 타입이다.

1. `timestamp`
	- 날짜와 시간을 기록한다.
	- 표준 시간대를 포함해 시간을 저장하고 싶다면 `with time zone` 키워드를 추가해야 한다.
	- `timestamp with time zone` 형식은 `SQL` 표준의 일부이며, `PostgreSQL`에는 그와 동일한 데이터 타입인 `timestamptz`가 있다.
	- 표준 시간대는 `UTC` 오프셋, 영역/위치 지정자, 또는 표준 약어라는 세 가지 형식으로 지정할 수 있다.
	- 시간대가 없는 시간을 `timestamptz`열에 제공할 경우 데이터베이스는 서버의 기본 설정을 사용하여 시간대 정보를 추가한다.
2. `date`
	- 날짜만 기록하며, `SQL` 표준의 일부다.
	- `with time zone` 키워드를 사용하면 여러 날짜 형식을 사용할 수 있다.
	- 날짜를 표시하려면 `ISO 8601` 국제 표준 형식이자 `PostgreSQL` 기본 출력인 `YYYY-MM-DD` 포맷 사용이 권장된다.
	- `ISO` 형식을 사용하면 데이터를 국제적으로 공유할 때 혼선을 방지할 수 있다.
3. `time`
	- 시간만 기록하며, `SQL` 표준의 일부다.
	- `with time zone` 키워드를 사용하면 열에서 시간대를 인식하지만, 날짜가 없으면 시간대는 의미가 없다.
	- 이 점을 고려하면 `with time zone` 키워드나 `timestapmtz`는 사용하지 않는 것이 좋다.
	- `ISO 8601` 형식은 `HH:MM:SS`이고 시간, 분, 초를 나타낸다.
4. `interval`
	- `quantity unit` 형식으로 표현된 시간 단위를 나타내는 값을 보유한다.
	- `12 days`나 `8 hours`와 같이 기간만 기록한다.
	- `SQL` 표준의 일부지만, `PostgreSQL` 전용 구문은 더 많은 옵션을 제공한다.

- 세 데이터 타입 `date`, `time`, `timestamp with time zone`은 `datetime` 타입이라고 부르며, 그 값을 `datetimes`라고 한다.
- `interval` 타입의 값은 `intervals`라고 한다.


### 날짜와 시간 조작하기
- 날짜 및 시간 데이터 타입, 구문, 함수 등은 데이터베이스에 따라 `SQL` 표준에서 벗어나는 경우가 많으므로 주의해야 한다.

#### 타임스탬프 값의 구성 요소 추출하기

```sql
SELECT
	date_part('year', '2022-12-01 18:37:12 EST'::timestamptz) AS year,
	date_part('month', '2022-12-01 18:37:12 EST'::timestamptz) AS month,
	date_part('day', '2022-12-01 18:37:12 EST'::timestamptz) AS day,
	date_part('hour', '2022-12-01 18:37:12 EST'::timestamptz) AS hour,
	date_part('minute', '2022-12-01 18:37:12 EST'::timestamptz) AS minute,
	date_part('seconds', '2022-12-01 18:37:12 EST'::timestamptz) AS seconds,
	date_part('timezone_hour', '2022-12-01 18:37:12 EST'::timestamptz) AS tz,
	date_part('week', '2022-12-01 18:37:12 EST'::timestamptz) AS week,
	date_part('quarter', '2022-12-01 18:37:12 EST'::timestamptz) AS quarter,
	date_part('epoch', '2022-12-01 18:37:12 EST'::timestamptz) AS epoch;
```
- 위 명령문은 `date_part()`를 사용하여 `timestamp` 값의 구성 요소를 추출하는 쿼리다.
- 해당 함수의 첫 번째 인자에는 날짜 또는 시간 부분을 나타내는 텍스트가 들어가고, 두 번째 인자에는 `date`, `time`, `timestamp` 값이 들어간다.
- 결과는 다음과 같다.

|year |month|day|hour|minute|seconds|tz |week|quarter|epoch        |
|-----|-----|---|----|------|-------|---|----|-------|-------------|
|2,022|12   |2  |8   |37    |12     |9  |48  |4      |1,669,937,832|

- `tz` 열은 `UTC`(협정세계시)로부터 시간 차이 또는 오프셋을 보고한다.
- 가령 위 결과는 `UTC`보다 9시간 이른 시간대를 지정한다.

> 시간대에서 `UTC` 오프셋을 유도할 수는 있지만 그 반대의 경우는 불가능하다.
> 각 `UTC` 오프셋은 여러 명명된 시간대와 표준 및 일광 절약 시간 변형을 참조할 수 있다.

- `week` 열은 2022년 12월 2일이 해당 연도의 48번째 주에 해당함을 보여준다.
- `quarter` 열은  해당 날짜가 올해 4분기에 속함을 알 수 있다.
- `epoch` 열은 컴퓨터 시스템과 프로그래밍 언어에서 사용되는 측정 값을 나타내는데, `UTC` 0인 1970년 1월 1일 오전 12시 이전 또는 이후로 경과된 시간을 초 단위로 보여준다.

> 유닉스 시간에 주의하자.
> `PostgreSQL`의 `date_part()`는 `double precision` 타입으로 유닉스 시간을 반환한다.
> `double precision` 타입은 부동 소수점이라 계산 시 오차가 발생하기도 한다.
> 또한 유닉스 시간을 사용하면 2038년 문제도 조심해야 한다.
> 일부 컴퓨터 시스템에서 특정 시간이 지나면 오버플로가 발생하는 오류를 2038년 문제라고 부른다.

- `PostgreSQL`은 `date_part()` 함수와 동일한 방식으로 `datetimes`를 구문 분석하는 표준 `SQL` `extract()` 함수도 지원하지만 두 가지 이유로 `date_part()`가 권장된다.

1. 이름 자체만으로 역할을 상기시킨다.
2. `extract()`는 여러 데이터베이스에서 널리 지원되지 않는다.

- 그럼에도 `extract()`를 사용해야 한다면 `extract(text from value)` 형식을 취한다.
- 타임스탬프에서 연도를 가져오기 위해서는 `extract('year' from '2022-12-01 18:37:12 EST'::timestamptz)` 와 같이 쿼리한다.

#### 타임스탬프 구성 요소에서 날짜 시간 값 만들기
- 연도, 월, 일이 별도의 열에 존재하는 데이터셋을 발견하는 것은 드문 일이 아니다.
- 이러한 구성 요소에서 `PostgreSQL` 함수를 사용하여 `datetime` 값을 생성할 수 있다.

```sql
-- 날짜 만들기
SELECT make_date(2022, 2, 22);

-- 시간 만들기
SELECT make_time(18, 4, 30.3);

-- 시간대가 적용된 timestamp 만들기
SELECT make_timestamptz(2022, 2, 22, 18, 4, 30.3, 'Europe/Lisbon');
```
- 이 세 함수에서는 `integer` 타입의 변수를 입력으로 사용하지만, 다음 두 가지 예외가 있다.

1. 초는 소수로 된 초 단위를 제공할 수 있기 때문에, `double precision` 타입의 숫자로 지정해야 한다.
2. 시간대는 `text` 타입의 문자열로 지정해야 한다.

#### 현재 날짜 및 시간 검색하기
- 행을 업데이트할 때 쿼리의 일부로 현재 날짜 또는 시간을 기록해야 하는 경우에 표준 `SQL`도 이에 대한 함수를 제공한다.

```sql
SELECT
	current_timestamp,
	localtimestamp,
	current_date,
	current_time,
	localtime,
	now();
```

1. `current_timestamp`
	- 시간대를 포함한 현재 타임스탬프를 반환한다.
	- `PostgreSQL` 전용 단축 버전은 `now()`이다.
2. `localtimestamp`
	- 시간대를 포함하지 않은 현재 타임스탬프를 반환한다.
	- 시간대가 없는 타임스탬프는 의미가 없으니 권장되지 않는다.
3. `current_date`: 날짜를 반환한다.
4. `current_time`: 시간대를 포함한 현재 시간을 반환한다.
5. `localtime`: 시간대를 포함하지 않은 현재 시간을 반환한다.

- 이러한 함수들은 쿼리가 시작할 때 시간을 기록하므로 쿼리 실행 시간과는 관계가 없다.
- 만약 쿼리 실행 중 시계가 변경되는 방식을 날짜와 시간에 반영하고 싶다면 `clock_timestamp()` 함수를 사용하여 시간 경과에 따라 기록할 수 있다.
- 해당 함수는 대용량 쿼리를 느리게 만들고 시스템 제한이 적용될 수 있으므로 주의해야 한다.

```sql
CREATE TABLE current_time_example (
	time_id integer GENERATED ALWAYS AS IDENTITY,
	current_timestamp_col timestamptz,
	clock_timestamp_col timestamptz
);

INSERT INTO current_time_example
		(current_timestamp_col, clock_timestamp_col)
	(SELECT current_timestamp,
		clock_timestamp()
	 FROM generate_series(1,1000));

SELECT * FROM current_time_example;
```
- 위 `INSERT` 명령문의 첫 번째 열은 시작 시간을 기록하는 `current_timestamp`를 사용했다.
- 두 번째 열에는 `clock_timestamp()`을 통해 각 행의 삽입 시간을 기록했다.
- `PostgreSQL` 전용 구문인 `generate_series()`를 통해 1000개의 행을 삽입했다.


### 시간대 다루기
#### 시간대 설정 찾기

```sql
SHOW timezone;
SELECT current_setting('timezone');
```
- 위 두 명령어 중 하나를 사용하면 현재 시간대를 알 수 있다.

> `SHOW ALL;` 명령어를 사용하면 `PostgreSQL` 서버의 모든 매개 변수 설정을 알 수 있다.

```sql
SELECT make_timestamptz(2022, 2, 22, 18, 4, 30.3, current_setting('timezone'));
```
- 두 문장 모두 동일한 정보를 제공하지만, 다른 함수에 대한 입력으로는 `current_setting()`을 사용하는 게 좋다.

```sql
SELECT * FROM pg_timezone_abbrevs ORDER BY abbrev;
SELECT * FROM pg_timezone_names ORDER BY name;

SELECT * FROM pg_timezone_names
WHERE name LIKE 'Europe%'
ORDER BY name;
```
- 위 두 명령문은 시간대 약어와 이름을 출력하는 쿼리다.
- 마지막 쿼리를 통해 약어를 필터링 할 수도 있다.
- 실행 결과는 다음과 같다.

|name            |abbrev|utc_offset|is_dst|
|----------------|------|----------|------|
|Europe/Amsterdam|CEST  |02:00:00  |true  |
|Europe/Andorra  |CEST  |02:00:00  |true  |
|Europe/Astrakhan|+04   |04:00:00  |false |
|Europe/Athens   |EEST  |03:00:00  |true  |

#### 시간대 설정하기
- `PostgreSQL`을 설치할 때 서버의 기본 시간대는 `postgresql.conf`에서 매개 변수로 설정되었다.
- 해당 파일을 변경하면 다른 사용자나 응용 프로그램에 의도하지 않은 결과가 발생할 수 있으므로 주의해야 하며, 이번 장에서는 세션 별로 시간대를 설정하는 방법을 알아볼 것이다.

```sql
SET TIME ZONE 'America/Los_Angeles';

CREATE TABLE time_zone_test (
	test_date timestamptz
);

INSERT INTO time_zone_test VALUES ('2023-01-01 4:00');

SELECT test_date
FROM time_zone_test;

SET TIME ZONE 'America/Indiana/Petersburg';

SELECT test_date
FROM time_zone_test;

SELECT test_date AT TIME ZONE 'Asia/Seoul'
FROM time_zone_test;
```

|test_date                    |
|-----------------------------|
|2023-01-01 21:00:00.000 +0900|

- 위 결과는 `America/Los_Angeles`, `America/Indiana/Petersburg`의 결과다.
- `UTC`보다 9시간이 빠르다는 의미다.

|timezone               |
|-----------------------|
|2023-01-01 21:00:00.000|

- 마지막 쿼리의 결과는 위와 같으며, 다음과 같은 사실을 알 수 있다.

1. `timestamptz`는 절대적 시각을 보장한다.
2. 타임존 변경은 데이터를 변경하는 것이 아니라, 출력 및 변환 시점에서의 표현 방식일 뿐이다.
3. 같은 행을 보더라도 세션 타임존에 따라 달리 보일 수 있다.


### 날짜 및 시간을 활용하여 계산하기

```sql
SELECT '1929-09-30'::date - '1929-09-27'::date;
SELECT '1929-09-30'::date + '5 years'::interval;
```
- 위 명령문과 같이 `datetime` 및 `interval` 타입에 대해 간단한 산술을 수행할 수 있다.
- 첫 번째 쿼리는 두 날짜가 정확히 3일 떨어져 있음을 알 수 있다.
- 두 번째 쿼리는 `1934-09-30`이라는 타임스탬프 값을 반환한다.

#### 뉴욕시 택시 데이터에서 패턴 찾기

```sql
CREATE TABLE nyc_yellow_taxi_trips (
	trip_id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
	vendor_id text NOT NULL,
	tpep_pickup_datetime timestamptz NOT NULL,
	tpep_dropoff_datetime timestamptz NOT NULL,
	passenger_count integer NOT NULL,
	trip_distance numeric(8,2) NOT NULL,
	pickup_longitude numeric(18,15) NOT NULL,
	pickup_latitude numeric(18,15) NOT NULL,
	rate_code_id text NOT NULL,
	store_and_fwd_flag text NOT NULL,
	dropoff_longitude numeric(18,15) NOT NULL,
	dropoff_latitude numeric(18,15) NOT NULL,
	payment_type text NOT NULL,
	fare_amount numeric(9,2) NOT NULL,
	extra numeric(9,2) NOT NULL,
	mta_tax numeric(5,2) NOT NULL,
	tip_amount numeric(9,2) NOT NULL,
	tolls_amount numeric(9,2) NOT NULL,
	improvement_surcharge numeric(9,2) NOT NULL,
	total_amount numeric(9,2) NOT NULL
);

COPY nyc_yellow_taxi_trips (
	vendor_id,
	tpep_pickup_datetime,
	tpep_dropoff_datetime,
	passenger_count,
	trip_distance,
	pickup_longitude,
	pickup_latitude,
	rate_code_id,
	store_and_fwd_flag,
	dropoff_longitude,
	dropoff_latitude,
	payment_type,
	fare_amount,
	extra,
	mta_tax,
	tip_amount,
	tolls_amount,
	improvement_surcharge,
	total_amount
   )
FROM 'C:\YourDirectory\nyc_yellow_taxi_trips.csv'
WITH (FORMAT CSV, HEADER);

CREATE INDEX tpep_pickup_idx
ON nyc_yellow_taxi_trips (tpep_pickup_datetime);

SELECT count(*) FROM nyc_yellow_taxi_trips;
```
- 위 명령문은 테이블 생성 및 뉴욕시의 노란색 택시 데이터를 가져오는 쿼리다.

##### 하루 중 가장 바쁜 시간대

```sql
SELECT
	date_part('hour', tpep_pickup_datetime) AS trip_hour,
	count(*)
FROM nyc_yellow_taxi_trips
GROUP BY trip_hour
ORDER BY trip_hour;
```
- 시간 별 택시 승차 횟수를 계산하는 쿼리를 통해 하루 중 가장 바쁜 시간대를 알아보자.

|trip_hour|count |
|---------|------|
|0        |17,383|
|1        |18,031|
|2        |17,998|
|3        |19,125|
|4        |18,053|
|5        |15,069|
|6        |18,513|
|7        |22,689|
|8        |23,190|
|9        |23,098|
|10       |24,106|
|11       |22,554|
|12       |17,765|
|13       |8,182 |
|14       |5,003 |
|15       |3,070 |
|16       |2,275 |
|17       |2,229 |
|18       |3,925 |
|19       |10,825|
|20       |18,287|
|21       |21,062|
|22       |18,975|
|23       |17,367|

##### 엑셀에서 시각화하기 위해 `CSV`로 내보내기

```sql
COPY
	(SELECT
		date_part('hour', tpep_pickup_datetime) AS trip_hour,
		count(*)
	FROM nyc_yellow_taxi_trips
	GROUP BY trip_hour
	ORDER BY trip_hour
	)
TO 'C:\YourDirectory\hourly_taxi_pickups.csv'
WITH (FORMAT CSV, HEADER);
```
- 데이터를 엑셀로 불러온 뒤 선 그래프를 생성하면 해당 날짜의 패턴이 더욱 명확하다.

![](/assets/image/Pasted%20image%2020250821221135.png)

- 물론 며칠 또는 몇 달에 걸친 데이터를 더 깊이 분석해야 해당 데이터를 일반화할 수 있다.
- `date_part()` 함수로 요일을 추출하여 평일과 주말의 승차량을 비교할 수도 있고, 일기 예보를 확인하여 날씨에 따른 승차량을 비교할 수도 있다.

##### 택시 승차 후 이동 시간은 언제 가장 긴가요?
- 답을 찾는 한 가지 방법은 각 시간의 중간 이동 시간을 계산하는 것이다.
- 중앙 값은 정렬된 값 세트의 중간 값으로, 평균과는 다르게 세트 안의 매우 작거나 큰 값이 결과를 왜곡하지 않기 때문에 비교할 때는 중앙 값이 평균보다 더 정확하다.

```sql
SELECT
	date_part('hour', tpep_pickup_datetime) AS trip_hour,
	percentile_cont(.5)
		WITHIN GROUP (ORDER BY
			tpep_dropoff_datetime - tpep_pickup_datetime) AS median_trip
FROM nyc_yellow_taxi_trips
GROUP BY trip_hour
ORDER BY trip_hour;
```
- `percentile_cont()`로 중앙 값을 계산하기 위해 `tpep_dropoff_datetime - tpep_pickup_datetime`를 계산하였고, 그 값을 중앙 값 계산의 정렬 기준으로 삼았다.
- 결과는 다음과 같다.

|trip_hour|median_trip|
|---------|-----------|
|0        |00:14:20   |
|1        |00:14:49   |
|2        |00:15:00   |
|3        |00:14:35   |
|4        |00:14:43   |
|5        |00:14:42   |
|6        |00:14:15   |
|7        |00:13:19   |
|8        |00:12:25   |
|9        |00:11:46   |
|10       |00:11:54   |
|11       |00:11:37   |
|12       |00:11:14   |
|13       |00:10:04   |
|14       |00:09:27   |
|15       |00:08:59   |
|16       |00:09:57   |
|17       |00:10:06   |
|18       |00:07:37   |
|19       |00:07:54   |
|20       |00:10:23   |
|21       |00:12:28   |
|22       |00:13:11   |
|23       |00:13:46   |

#### `Amtrak` 데이터에서 패턴 찾기
##### 기차 이동 시간 계산하기

```sql
CREATE TABLE train_rides (
	trip_id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
	segment text NOT NULL,
	departure timestamptz NOT NULL,
	arrival timestamptz NOT NULL
);

INSERT INTO train_rides (segment, departure, arrival)
VALUES
	('Chicago to New York', '2020-11-13 21:30 CST', '2020-11-14 18:23 EST'),
	('New York to New Orleans', '2020-11-15 14:15 EST', '2020-11-16 19:32 CST'),
	('New Orleans to Los Angeles', '2020-11-17 13:45 CST', '2020-11-18 9:00 PST'),
	('Los Angeles to San Francisco', '2020-11-19 10:10 PST', '2020-11-19 21:24 PST'),
	('San Francisco to Denver', '2020-11-20 9:10 PST', '2020-11-21 18:38 MST'),
	('Denver to Chicago', '2020-11-22 19:10 MST', '2020-11-23 14:50 CST');

SELECT * FROM train_rides;
```
- 기차 이동 데이터에 대한 테이블과 행을 추가하였고, 결과는 다음과 같다.

|trip_id|segment                     |departure                    |arrival                      |
|-------|----------------------------|-----------------------------|-----------------------------|
|1      |Chicago to New York         |2020-11-14 12:30:00.000 +0900|2020-11-15 08:23:00.000 +0900|
|2      |New York to New Orleans     |2020-11-16 04:15:00.000 +0900|2020-11-17 10:32:00.000 +0900|
|3      |New Orleans to Los Angeles  |2020-11-18 04:45:00.000 +0900|2020-11-19 02:00:00.000 +0900|
|4      |Los Angeles to San Francisco|2020-11-20 03:10:00.000 +0900|2020-11-20 14:24:00.000 +0900|
|5      |San Francisco to Denver     |2020-11-21 02:10:00.000 +0900|2020-11-22 10:38:00.000 +0900|
|6      |Denver to Chicago           |2020-11-23 11:10:00.000 +0900|2020-11-24 05:50:00.000 +0900|

```sql
SELECT segment,
	to_char(departure, 'YYYY-MM-DD HH12:MI a.m. TZ') AS departure,
	arrival - departure AS segment_duration
FROM train_rides;
```
- 위 명령문은 각 이동 구간의 길이를 계산하는 쿼리다.
- 이 쿼리는 여행 구간, 출발 시간, 여행 기간을 나열한다.
- `departure` 열의 `to_char()` 함수는 타임스탬프 값 `YYYY-MM-DD HH12:MI a.m. TZ` 형식의 문자열로 변환한다.
- `HH12` 부분은 24시간 군사 시간이 아닌 12시간 시계를 사용하도록 지정한다.
- `a.m.` 부분은 마침표로 구분된 소문자를 이용하여 오전 또는 오후 시간을 표시하도록 지정하고, `TZ` 부분은 시간대를 나타낸다.
- 마지막으로 `arrive`에서 `departure`를 빼서 `segment_time` 구간을 확인한다.
- 결과는 다음과 같다.

|segment                     |departure                |segment_duration|
|----------------------------|-------------------------|----------------|
|Chicago to New York         |2020-11-14 12:30 p.m. KST|19:53:00        |
|New York to New Orleans     |2020-11-16 04:15 a.m. KST|1 day 06:17:00  |
|New Orleans to Los Angeles  |2020-11-18 04:45 a.m. KST|21:15:00        |
|Los Angeles to San Francisco|2020-11-20 03:10 a.m. KST|11:14:00        |
|San Francisco to Denver     |2020-11-21 02:10 a.m. KST|1 day 08:28:00  |
|Denver to Chicago           |2020-11-23 11:10 a.m. KST|18:40:00        |

- 위 결과와 같이 한 타임스탬프에서 다른 타임스탬프를 빼면 `interval` 데이터 타입이 생성된다.

##### 누적 이동 시간 계산하기

```sql
SELECT segment,
	arrival - departure AS segment_duration,
	sum(arrival - departure) OVER (ORDER BY trip_id) AS cume_duration
FROM train_rides;
```
- `OVER (ORDER BY ...)` 구문을 통해 누적 합을 쿼리했다.

|segment                     |segment_duration|cume_duration  |
|----------------------------|----------------|---------------|
|Chicago to New York         |19:53:00        |19:53:00       |
|New York to New Orleans     |1 day 06:17:00  |1 day 26:10:00 |
|New Orleans to Los Angeles  |21:15:00        |1 day 47:25:00 |
|Los Angeles to San Francisco|11:14:00        |1 day 58:39:00 |
|San Francisco to Denver     |1 day 08:28:00  |2 days 67:07:00|
|Denver to Chicago           |18:40:00        |2 days 85:47:00|

- 전체 이동 시간을 계산하였는데, 결과 누계는 정확하지만 유용하지 않은 형식으로 출력되었음을 알 수 있다.
- 이는 `PostgreSQL`이 일 부분에 대한 합계와 시간 부분에 대한 합계를 따로 생성함을 알 수 있다.
- 이러한 제한을 우회하기 위해 다음과 같이 쿼리할 수 있다.

```sql
SELECT segment,
	arrival - departure AS segment_duration,
	justify_interval(sum(arrival - departure)
		OVER (ORDER BY trip_id)) AS cume_duration
FROM train_rides;
```

|segment                     |segment_duration|cume_duration  |
|----------------------------|----------------|---------------|
|Chicago to New York         |19:53:00        |19:53:00       |
|New York to New Orleans     |1 day 06:17:00  |2 days 02:10:00|
|New Orleans to Los Angeles  |21:15:00        |2 days 23:25:00|
|Los Angeles to San Francisco|11:14:00        |3 days 10:39:00|
|San Francisco to Denver     |1 day 08:28:00  |4 days 19:07:00|
|Denver to Chicago           |18:40:00        |5 days 13:47:00|

- `justify_interval()` 함수는 24시간은 일로, 30일은 월로 변환하도록 간격 계산의 출력을 표준화한다.
- 이를 통해 출력이 더욱 이해하기 쉬워졌다.