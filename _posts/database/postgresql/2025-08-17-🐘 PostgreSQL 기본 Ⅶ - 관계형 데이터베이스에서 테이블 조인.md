---
title: 🐘 PostgreSQL 기본 Ⅶ - 관계형 데이터베이스에서 테이블 조인
date: 2025-08-17 13:10:00 +0900
categories:
  - Database
tags:
  - Database
  - PostgreSQL
---

![](/assets/image/Pasted%20image%2020250813142544.png)
> 📙 `『실용 SQL』`을 읽고 정리한 글입니다.

### `Join`을 사용하여 테이블 연결하기
- `ANSI SQL` 표준의 일부인 `JOIN`은 `ON`에 부울 값을 사용하여 데이터베이스의 한 테이블을 다른 테이블과 연결한다.
- 일반적으로 다음과 같은 형태로 작성한다.

```sql
SELECT *
FROM table_a JOIN table_b
ON table_a.key_column = table_b.foreign_key_column;
```
- 참고로 `>=`, `>`와 같은 표현식도 부울 결과 값을 반환하므로 조인 조건으로 사용할 수 있다.


### 키 열로 테이블 조인하기
#### 테이블 생성하기

```sql
CREATE TABLE departments (
	dept_id integer,
	dept text,
	city text,
	CONSTRAINT dept_key PRIMARY KEY (dept_id),
	CONSTRAINT dept_city_unique UNIQUE (dept, city)
);

CREATE TABLE employees (
	emp_id integer,
	first_name text,
	last_name text,
	salary numeric(10,2),
	dept_id integer REFERENCES departments (dept_id),
	CONSTRAINT emp_key PRIMARY KEY (emp_id)
);

INSERT INTO departments
VALUES
	(1, 'Tax', 'Atlanta'),
	(2, 'IT', 'Boston');

INSERT INTO employees
VALUES
	(1, 'Julia', 'Reyes', 115300, 1),
	(2, 'Janet', 'King', 98000, 1),
	(3, 'Arthur', 'Pappas', 72700, 2),
	(4, 'Michael', 'Taylor', 89500, 2);
```

| dept_id | dept | city    |
| ------- | ---- | ------- |
| 1       | Tax  | Atlanta |
| 2       | IT   | Boston  |

- `dept_id` 열은 테이블의 기본 키다.
- 여기서 기본 키는 테이블의 각 행을 고유하게 식별하는 열 또는 열 모음이다.
- 유효한 기본 키 열은 다음 제약 조건이 적용된다.

1. 열 또는 열 모음은 각 행에 대해 고유한 값을 가져야 한다.
2. 열 또는 열 모음에는 결측값이 없어야 한다.

- `CONSTRAINT` 키워드를 사용하여 `departments` 및 `employees` 테이블에 대한 기본 키를 정의한다.

| emp_id | first_name | last_name | salary  | dept_id |
| ------ | ---------- | --------- | ------- | ------- |
| 1      | Julia      | Reyes     | 115,300 | 1       |
| 2      | Janet      | King      | 98,000  | 1       |
| 3      | Arthur     | Pappas    | 72,700  | 2       |
| 4      | Michael    | Taylor    | 89,500  | 2       |

- `emp_id`는 기본 키, `dept_id` 열은 `departments` 테이블의 기본 키를 참조하는 외래 키이다.
- 외래 키 제약 조건을 사용하려면 참조하는 열에 해당 값이 이미 존재하고 있어야 한다.
- 이를 통해 데이터 무결성을 강화할 수 있으며, 기본 키와 달리 외래 키 열은 비어 있을 수 있으며, 중복된 값이 허용된다.
- 가령 두 테이블 값을 통해 `Julia`의 부서 `ID`는 1이고, 해당 부서는 `Atlanta`의 `Tax` 부서임을 알 수 있다.

|dept|location|first_name|last_name|salary |
|----|--------|----------|---------|-------|
|Tax |Atlanta |Julia     |Reyes    |115,300|
|Tax |Atlanta |Janet     |King     |98,000 |
|IT  |Boston  |Arthur    |Pappas   |72,700 |
|IT  |Boston  |Michael   |Taylor   |89,500 |

- `departments` 테이블과 `employees` 테이블로 나누듯 꼭 데이터를 구성 요소로 나눠야 할까?
- 위와 같이 모든 데이터를 한 테이블에 통합하여 관리한다면 다음 문제가 있다.

1. 여러 항목의 데이터를 한 테이블에 조인할 때 필연적으로 정보를 반복해야 한다.
	- 수백만 개의 행을 다루는 경우와 같이 데이터가 늘어날 경우 중복되는 데이터로 인해 리소스를 크게 낭비하게 된다.
2. 관련 없는 데이터를 한 테이블에 넣는 것은 데이터 관리를 어렵게 만든다.
	- 부서의 이름을 변경한다면, 테이블에 각 행에 업데이트가 필요하게 된다.
	- 그런데, 일부 행만 잘못 업데이트하게 된다면 오류가 발생할 수 있다.
	- 하지만 테이블을 따로 관리한다면, 부서 테이블의 한 행만 값을 바꾸면 된다.
3. 정보가 여러 테이블에 걸쳐 정규화되었다고 해서 전체적으로 정보를 살펴보는 것이 어렵지 않다.
	- `JOIN`을 사용하면 된다.


### `JOIN`을 사용하여 여러 테이블 쿼리하기
- 쿼리에서 테이블을 조인하면 데이터베이스는 지정한 열에 대해 `ON`절의 표현식을 `true`로 반환하는 값이 있는 두 테이블의 행을 연결한다.
- 그러면 두 테이블의 열을 쿼리의 일부로 요청한 경우, 쿼리 결과에 포함되거나 조인된 테이블의 열을 사용하여 `WHERE` 절로 결과를 필터링할 수도 있다.
- `JOIN`을 사용하는 예제 코드는 다음과 같다. 

```sql
SELECT *
FROM employees JOIN departments
ON employees.dept_id = departments.dept_id
ORDER BY employees.dept_id;
```

|emp_id|first_name|last_name|salary |dept_id|dept_id|dept|city   |
|------|----------|---------|-------|-------|-------|----|-------|
|1     |Julia     |Reyes    |115,300|1      |1      |Tax |Atlanta|
|2     |Janet     |King     |98,000 |1      |1      |Tax |Atlanta|
|3     |Arthur    |Pappas   |72,700 |2      |2      |IT  |Boston |
|4     |Michael   |Taylor   |89,500 |2      |2      |IT  |Boston |

- 쿼리 실행 결과에는 두 테이블의 모든 열이 나타나기 때문에, `dept_id` 열이 두 번 등장한다.
- 이를 방지하기 위해서는 두 테이블에서 원하는 열만 검색해야 한다.


### `JOIN` 유형 이해하기
- 다음은 다양한 종류의 `JOIN`에 대한 예시이다.

1. `JOIN`
	- `INNER JOIN`으로 대체할 수 있다. 
	- 두 테이블의 조인된 열에서 일치하는 값이 있는 두 테이블의 행을 반환한다.
2. `LEFT JOIN`
	- 왼쪽 테이블의 모든 행을 반환한다.
	-  `SQL`이 오른쪽 테이블에서 일치하는 값을 가진 행을 찾으면 해당 행의 값이 포함된다.
	- 그렇지 않으면, 오른쪽 테이블의 값은 표시되지 않는다.
- `RIGHT JOIN`
	- 오른쪽 테이블의 모든 행을 반환한다.
	-  `SQL`이 왼쪽 테이블에서 일치하는 값을 가진 행을 찾으면 해당 행의 값이 포함된다.
	- 그렇지 않으면, 왼왼쪽 테이블의 값은 표시되지 않는다.
- `FULL OUTER JOIN`
	- 두 테이블에서 모든 행의 값을 반환하고, 값이 일치하는 행은 연결한다.
	- 반대 테이블에 일치하는 행이 없는 경우에는 쿼리 결과에 다른 테이블에 대한 빈 값이 포함된다.
- `CROSS JOIN`
	- 두 테이블에서 가능한 모든 조합을 반환한다.

- `JOIN`을 살펴보기 위한 두 테이블을 다음과 같이 생성하자.

```sql
CREATE TABLE district_2020 (
id integer CONSTRAINT id_key_2020 PRIMARY KEY,
school_2020 text
);

CREATE TABLE district_2035 (
id integer CONSTRAINT id_key_2035 PRIMARY KEY,
school_2035 text
);

INSERT INTO district_2020 VALUES
(1, 'Oak Street School'),
(2, 'Roosevelt High School'),
(5, 'Dover Middle School'),
(6, 'Webutuck High School');

INSERT INTO district_2035 VALUES
(1, 'Oak Street School'),
(2, 'Roosevelt High School'),
(3, 'Morrison Elementary'),
(4, 'Chase Magnet Academy'),
(6, 'Webutuck High School');
```

#### `JOIN`(`INNER JOIN`)

```sql
SELECT *
FROM district_2020 JOIN district_2035
ON district_2020.id = district_2035.id
ORDER BY district_2020.id;
```
- 위 코드와 같이 `JOIN`에 사용한 열에서 일치하는 행을 반환하려면 `JOIN` 또는 `INNER JOIN` 키워드를 사용한다.
- 두 테이블에 동시에 존재하는 학교 `ID`가 세 개이므로 쿼리는 해당 `ID`를 가진 세 개의 행만 반환한다.
- 또한 `JOIN` 키워드 왼쪽에 적은 테이블의 열이 결과 테이블에서 앞에 표시된다는 점을 유의해야 한다.
- `JOIN` 키워드는 일반적으로 잘 구조화되고, 유지 관리가 잘 된 데이터셋에서 두 테이블 모두에 존재하는 행만 찾아야 하는 경우 사용한다.

#### `JOIN`에서 `USING` 사용하기

```sql
SELECT *
FROM district_2020 JOIN district_2035
USING (id)
ORDER BY district_2020.id;
```
- `JOIN`의 `ON` 절에 사용하는 열 이름이 동일한 경우 `USING` 키워드를 사용해 중복 출력을 줄이고, 쿼리를 줄일 수 있다.
- 둘 이상의 열을 결합하는 경우 쉼표로 구분한다.
- 결과는 다음과 같다.

| id  | school_2020           | school_2035           |
| --- | --------------------- | --------------------- |
| 1   | Oak Street School     | Oak Street School     |
| 2   | Roosevelt High School | Roosevelt High School |
| 6   | Webutuck High School  | Webutuck High School  |

#### `LEFT JOIN`과 `RIGHT JOIN`
- `JOIN`과 달리 각각 한 테이블의 모든 행을 반환하며, 다른 테이블에 일치하는 값이 있는 행이 존재한다면, 결과에 해당 행의 값을 포함한다.

```sql
-- LEFT JOIN
SELECT *
FROM district_2020 LEFT JOIN district_2035
ON district_2020.id = district_2035.id
ORDER BY district_2020.id;

-- RIGHT JOIN
SELECT *
FROM district_2020 RIGHT JOIN district_2035
ON district_2020.id = district_2035.id
ORDER BY district_2035.id;
```

| id  | school_2020           | id  | school_2035           |
| --- | --------------------- | --- | --------------------- |
| 1   | Oak Street School     | 1   | Oak Street School     |
| 2   | Roosevelt High School | 2   | Roosevelt High School |
| 5   | Dover Middle School   |     |                       |
| 6   | Webutuck High School  | 6   | Webutuck High School  |

- `JOIN`의 왼쪽에 있는 행 4개가 모두 표시되고, 우측 테이블에는 `id`가 5인 행이 없으므로 값을 비워 표시한다.

|id |school_2020          |id |school_2035          |
|---|---------------------|---|---------------------|
|1  |Oak Street School    |1  |Oak Street School    |
|2  |Roosevelt High School|2  |Roosevelt High School|
|   |                     |3  |Morrison Elementary  |
|   |                     |4  |Chase Magnet Academy |
|6  |Webutuck High School |6  |Webutuck High School |

- `RIGHT JOIN`도 마찬가지다.
- `JOIN`과 마찬가지로 조건을 만족하면 `ON` 대신 `USING`을 사용할 수 있다.
- 다음과 같은 몇 가지 상황에서 `LEFT JOIN`과 `RIGHT JOIN`을 사용하게 된다.

1. 쿼리 결과에 한 테이블의 모든 행이 포함되기를 원하는 경우
2. 테이블 중 하나에서 결측값을 모두 찾으려는 경우, 예를 들자면 서로 다른 두 기간을 나타내는 항목에 대한 데이터를 비교하는 경우
3. 조인된 테이블의 일부 행에 일치하는 값이 없을 경우

#### `FULL OUTER JOIN`

```sql
SELECT *
FROM district_2020 FULL OUTER JOIN district_2035
ON district_2020.id = district_2035.id
ORDER BY district_2020.id;
```

|id |school_2020          |id |school_2035          |
|---|---------------------|---|---------------------|
|1  |Oak Street School    |1  |Oak Street School    |
|2  |Roosevelt High School|2  |Roosevelt High School|
|5  |Dover Middle School  |   |                     |
|6  |Webutuck High School |6  |Webutuck High School |
|   |                     |4  |Chase Magnet Academy |
|   |                     |3  |Morrison Elementary  |

- 일치 여부에 관계 없이 두 테이블의 모든 행을 보기 위해 사용한다.

#### `CROSS JOIN`

```sql
-- 일반적인 사용
SELECT *
FROM district_2020 CROSS JOIN district_2035
ORDER BY district_2020.id, district_2035.id;

-- 쉼표(,) 대체
SELECT *
FROM district_2020, district_2035
ORDER BY district_2020.id, district_2035.id;

-- JOIN ... ON true 대체
SELECT *
FROM district_2020 JOIN district_2035 ON true
ORDER BY district_2020.id, district_2035.id;
```
- 왼쪽 테이블과 오른쪽 테이블의 각 행을 정렬하여 가능한 모든 행 조합을 나타낸다.
- 규모가 큰 테이블에서는 `CROSS JOIN`을 피하는 것이 좋다.
- 가령 각각 25만 개의 행을 가진 두 테이블을 `CROSS JOIN`한다면 625억 행의 결과 집합을 생성한다.


### `NULL`을 사용하여 결측값이 있는 행 찾기
- 테이블을 조인할 때마다 한 테이블의 키 값이 다른 테이블에 나타나는지, 누락된 값은 없는지 조사해야 한다.
- 어떤 이유에서라도 불일치는 생기기 마련이고, 어떤 데이터는 시간이 지남에 따라 변경되었을 수도 있다.
- 예를 들어 새제품 테이블에는 이전 제품 테이블에는 없는 코드가 포함되었을 수 있다.
- 행이 많지 않다면 데이터를 훑어볼 때 누락된 데이터가 있는 행을 쉽게 찾을 수 있지만, 큰 테이블을 다루는 경우 일치하지 않는 모든 행을 표시하는 필터링 전략이 필요하다.
- 이를 위해 `NULL` 키워드를 사용할 것이다.

```sql
SELECT *
FROM district_2020 LEFT JOIN district_2035
ON district_2020.id = district_2035.id
WHERE district_2035.id IS NULL;
```

|id |school_2020        |id |school_2035|
|---|-------------------|---|-----------|
|5  |Dover Middle School|   |           |

- 결과는 왼쪽 테이블의 행 중 오른쪽 테이블과 일치하지 않는 값만 표시한다.
- 이런 조인을 일반적으로 안티 조인이라고 부른다.
- 오른쪽 테이블의 행 중 왼쪽 테이블과 일치하지 않는 값을 표시하려면 `RIGHT JOIN`으로 바꾸고, `WHERE`절을 `district_2020.id IS NULL`로 바꾸면 된다.


### 세 가지 유형의 테이블 관계 이해하기
- 테이블 관계의 세 가지 유형은 다음과 같다.

1. 일대일 관계
	- 두 테이블에서 `ID`가 같은 행이 하나씩만 존재하는 경우를 말한다.
2. 일대다 관계
	- 한 테이블의 키 값이 다른 테이블의 여러 열과 매칭되는 경우를 말한다.
	- 가령 제조업체 테이블과 자동차 테이블을 그 예시로 들 수 있다.
3. 다대다 관계
	- 한 테이블의 여러 항목이 다른 테이블의 여러 항목과 매칭되는 경우이다.


### 조인에서 특정 열 선택하기

```sql
-- 오류 발생
SELECT id
FROM district_2020 LEFT JOIN district_2035
ON district_2020.id = district_2035.id;

-- 별칭 미사용
SELECT district_2020.id,
	district_2020.school_2020,
	district_2035.school_2035
FROM district_2020 LEFT JOIN district_2035
ON district_2020.id = district_2035.id
ORDER BY district_2020.id;

-- 별칭 사용
SELECT d20.id,
	d20.school_2020,
	d35.school_2035
FROM district_2020 AS d20 LEFT JOIN district_2035 AS d35
ON d20.id = d35.id
ORDER BY d20.id;
```
- 특정 열을 선택하려면 `SELECT` 키워드 이후 원하는 열 이름을 나열한다.
- 첫 번째 쿼리에서 오류가 발생하는 이유는 `id`가 속하는 테이블을 지정하지 않아 어떤 테이블의 `id`를 말하는 건지 명확하지 않기 때문이다.
- 오류를 수정하려면 `district_2020.id`와 같이 테이블 이름을 추가해야 한다.
- `AS` 키워드를 통해 코드를 단축할 수 있으며, 별칭을 사용하더라도 `AS` 키워드는 생략해도 된다.


### 여러 테이블 조인하기

```sql
CREATE TABLE district_2020_enrollment (
	id integer,
	enrollment integer
);

CREATE TABLE district_2020_grades (
	id integer,
	grades varchar(10)
);

INSERT INTO district_2020_enrollment
VALUES
	(1, 360),
	(2, 1001),
	(5, 450),
	(6, 927);

INSERT INTO district_2020_grades
VALUES
	(1, 'K-3'),
	(2, '9-12'),
	(5, '6-8'),
	(6, '9-12');
```
- 여러 테이블을 조인하기 위해 우선 예제 테이블을 생성하자.

```sql
SELECT d20.id,
	d20.school_2020,
	en.enrollment,
	gr.grades
FROM district_2020 AS d20 JOIN district_2020_enrollment AS en
	ON d20.id = en.id
JOIN district_2020_grades AS gr
	ON d20.id = gr.id
ORDER BY d20.id;
```

|id |school_2020          |enrollment|grades|
|---|---------------------|----------|------|
|1  |Oak Street School    |360       |K-3   |
|2  |Roosevelt High School|1,001     |9-12  |
|5  |Dover Middle School  |450       |6-8   |
|6  |Webutuck High School |927       |9-12  |

- 필요한 경우 추가 조인을 사용하여 쿼리에 더 많은 테이블을 조회할 수 있다.


### 집합 연산자로 쿼리 결과 결합하기
- 어떤 인스턴스는 조인 결과처럼 다양한 테이블의 열이 나란히 반환되지 않고 하나의 결과로 출력되어 데이터를 재정렬해야 한다.
- 이렇게 데이터를 재조작하는 방법으로는 `ANSI SQL` 표준인 집합 연산자 `UNION`, `INTERSECT`, `EXCEPT`가 있다.

1. `UNION`
	- 두 쿼리가 주어지면, 두 번째 쿼리의 행을 첫 번째 쿼리에 추가하고 중복을 제거하여 결합된 고유 행 집합을 생성한다.
	- 구문을 `UNION ALL`로 수정하면 중복을 포함한 모든 행이 반환된다.
2. `INTERSECT`
	- 두 쿼리에 모두 존재하는 행만 반환하고, 중복을 제거한다.
3. `EXCEPT`
	- 첫 번째 쿼리에는 있지만, 두 번째 쿼리에는 없는 행을 반환한다.
	- 중복이 제거된다.

#### `UNION`과 `UNION ALL`

```sql
-- UNION
SELECT * FROM district_2020

UNION

SELECT * FROM district_2035
ORDER BY id;

-- UNION ALL
SELECT * FROM district_2020

UNION ALL

SELECT * FROM district_2035
ORDER BY id;

-- UNION 쿼리 커스터마이징
SELECT '2020' AS year,
	school_2020 AS school
FROM district_2020

UNION ALL

SELECT '2035' AS year,
	school_2035
FROM district_2035
ORDER BY school, year;
```
- 중복을 제거하려면 `UNION`, 중복을 포함하려면 `UNION ALL`, 또한 쿼리를 커스터마이징하여 합치는 과정에서 열을 추가할 수도 있다.

#### `INTERSECT`와 `EXCEPT`

```sql
SELECT * FROM district_2020
INTERSECT
SELECT * FROM district_2035
ORDER BY id;

SELECT * FROM district_2020
EXCEPT
SELECT * FROM district_2035
ORDER BY id;
```

|id |school_2020          |
|---|---------------------|
|1  |Oak Street School    |
|2  |Roosevelt High School|
|6  |Webutuck High School |

- `INTERSECT` 키워드는 두 테이블에 있는 공통 데이터만 출력한다.

|id |school_2020        |
|---|-------------------|
|5  |Dover Middle School|

- `EXCEPT` 키워드는 첫 번째 쿼리에 있지만, 두 번째 쿼리에는 없는 행만을 반환하고, 중복을 제거한다.
- 이러한 키워드 들은 데이터를 검사할 수 있는 충분한 기능을 제공한다.


### 조인된 테이블 열에서 수학 계산 수행하기

```sql
CREATE TABLE us_counties_pop_est_2010 (
	state_fips text,
	county_fips text,
	region smallint, 
	state_name text,
	county_name text,
	estimates_base_2010 integer,
	CONSTRAINT counties_2010_key PRIMARY KEY (state_fips, county_fips)
);

COPY us_counties_pop_est_2010
FROM 'C:\YourDirectory\us_counties_pop_est_2010.csv'
WITH (FORMAT CSV, HEADER);

SELECT c2019.county_name,
	c2019.state_name,
	c2019.pop_est_2019 AS pop_2019,
	c2010.estimates_base_2010 AS pop_2010,
	c2019.pop_est_2019 - c2010.estimates_base_2010 AS raw_change,
	round( (c2019.pop_est_2019::numeric - c2010.estimates_base_2010)
		/ c2010.estimates_base_2010 * 100, 1 ) AS pct_change
FROM us_counties_pop_est_2019 AS c2019
	JOIN us_counties_pop_est_2010 AS c2010
ON c2019.state_fips = c2010.state_fips
	AND c2019.county_fips = c2010.county_fips
ORDER BY pct_change DESC;
```
- 수학 함수는 조인된 테이블에서도 사용할 수 있다.

|county_name       |state_name    |pop_2019 |pop_2010|raw_change|pct_change|
|------------------|--------------|---------|--------|----------|----------|
|McKenzie County   |North Dakota  |15,024   |6,359   |8,665     |136.3     |
|Loving County     |Texas         |169      |82      |87        |106.1     |
|Williams County   |North Dakota  |37,589   |22,399  |15,190    |67.8      |
|Hays County       |Texas         |230,191  |157,103 |73,088    |46.5      |
|Wasatch County    |Utah          |34,091   |23,525  |10,566    |44.9      |
|Comal County      |Texas         |156,209  |108,520 |47,689    |43.9      |
|Trousdale County  |Tennessee     |11,284   |7,864   |3,420     |43.5      |

- `raw_change` 열은 2019년 추정치에서 2010년 추정치를 뺀 값이다.
- `pct_change` 열은 2010년과 2019년 사이의 변화율을 계산한 값이다.
- 두 테이블에서 주 코드와 카운티 코드의 조합이 고유한 카운티를 나타내기 때문에, `AND` 논리 연산자를 통해 두 조건을 결합한다.