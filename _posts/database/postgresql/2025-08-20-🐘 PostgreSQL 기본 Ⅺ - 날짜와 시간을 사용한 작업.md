---
title: 🐘 PostgreSQL 기본 Ⅺ - 날짜와 시간을 사용한 작업
date: 2025-08-20 12:10:00 +0900
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

