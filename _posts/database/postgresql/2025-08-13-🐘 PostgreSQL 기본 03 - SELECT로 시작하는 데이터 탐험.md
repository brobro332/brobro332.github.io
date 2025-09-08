---
title: 🐘 PostgreSQL 기본 03 - SELECT로 시작하는 데이터 탐험
date: 2025-08-13 23:11:00 +0900
categories:
  - Database
tags:
  - Database
  - PostgreSQL
---

![](/assets/image/Pasted%20image%2020250813142544.png)
> 📙 `『실용 SQL』`을 읽고 정리한 글입니다.

### 기초 `SELECT` 구문

```sql
SELECT * FROM teachers;
```
- 위 구문은 `teachers` 테이블의 모든 데이터를 조회하는 `SELECT` 구문이다.
- `*`는 와일드 카드라고 불린다.
- 와일드 카드는 어떤 값을 대체하는 데 사용되는 문자로, 특정한 무언가가 아니라, 그 값이 될 수 있는 모든것을 대표한다.
- `FROM` 키워드는 쿼리가 특정 테이블로부터 데이터를 가져오도록 지시한다.

|id |first_name|last_name|school             |hire_date |salary|
|---|----------|---------|-------------------|----------|------|
|1  |Janet     |Smith    |F.D. Roosevelt HS  |2011-10-30|36,200|
|2  |Lee       |Reynolds |F.D. Roosevelt HS  |1993-05-22|65,000|
|3  |Samuel    |Cole     |Myers Middle School|2005-08-01|43,500|
|4  |Samantha  |Bush     |Myers Middle School|2011-10-30|36,200|
|5  |Betty     |Diaz     |Myers Middle School|2005-08-30|43,500|
|6  |Kathleen  |Roush    |F.D. Roosevelt HS  |2010-10-22|38,500|

```sql
TABLE teachers;
```
- 위 명령어도 같은 결과를 반환한다.

#### 열의 하위 집합 쿼리하기

```sql
SELECT last_name, first_name, salary FROM teachers;
```
- 위 구문과 같이 쿼리가 검색할 열을 제한하면 과도한 정보를 헤쳐 보지 않아도 되므로 매우 실용적이다.
- 특히 대용량 데이터베이스라면 그 실용성은 높아진다.
- 위 구문처럼 열은 원하는 순서대로 호출할 수 있다.

|last_name|first_name|salary|
|---------|----------|------|
|Smith    |Janet     |36,200|
|Reynolds |Lee       |65,000|
|Cole     |Samuel    |43,500|
|Bush     |Samantha  |36,200|
|Diaz     |Betty     |43,500|
|Roush    |Kathleen  |38,500|

- 일반적으로 분석을 시작할 때는 데이터의 유무와 원하는 형태의 데이터인지 먼저 확인하는 것이 우선이다.
- 날짜는 연도-월-일로 표현이 되었는지, 모든 행에 값이 들어 있는지 등이다.
- 이러한 요소들은 모두 잠재적인 위험 요소이다.
- 데이터가 유실되었을 수도 있고, 작업 과정 어딘가 데이터 관리가 잘못 되었을 수도 있다.

#### `ORDER BY`로 데이터 정렬하기

```sql
-- 열 이름으로 정렬
SELECT first_name, last_name, salary
FROM teachers
ORDER BY salary DESC;

-- 열 위치에 대한 숫자값으로 정렬
SELECT first_name, last_name, salary
FROM teachers
ORDER BY 3 DESC;
```
- `SQL`에서는 `ORDER BY` 키워드와 정렬이 필요한 열, 열들이 담긴 절을 통해 결과의 순서를 정렬할 수 있다.
- 데이터는 뒤죽박죽 섞여 있을 때보다 순서대로 정렬되어 있을 때 이해하기 쉽고, 패턴을 더욱 순조롭게 드러낼 수 있다.
- `DESC`는 내림차순을 의미하며, `ASC` 키워드를 사용하거나 공백으로 두면 기본적으로 오름차순으로 정렬된다.
- `ORDER BY` 절에는 열 이름 대신 숫자를 넣을 수도 있다.
- 숫자에는 열이 반환되는 위치에 대한 값이 들어간다.

|first_name|last_name|salary|
|----------|---------|------|
|Lee       |Reynolds |65,000|
|Samuel    |Cole     |43,500|
|Betty     |Diaz     |43,500|
|Kathleen  |Roush    |38,500|
|Janet     |Smith    |36,200|
|Samantha  |Bush     |36,200|

```sql
SELECT last_name, school, hire_date
FROM teachers
ORDER BY school ASC, hire_date DESC;
```
- 위 구문처럼 여러 개의 열을 기준으로 정렬할 수도 있다.
- 그러나 효과가 거의 눈에 띄지 않을 것이다.
- 데이터 요약은 결과가 특정 질문에 대답하는 것에 초점을 맞추었을 때 가장 쉽기 때문에, 더 나은 방법은 가장 중요한 열들로 쿼리를 제한하고 궁금한 것이 생길 때마다 쿼리를 실행하는 것이다.
- 결과는 다음과 같다.

|last_name|school             |hire_date |
|---------|-------------------|----------|
|Smith    |F.D. Roosevelt HS  |2011-10-30|
|Roush    |F.D. Roosevelt HS  |2010-10-22|
|Reynolds |F.D. Roosevelt HS  |1993-05-22|
|Bush     |Myers Middle School|2011-10-30|
|Diaz     |Myers Middle School|2005-08-30|
|Cole     |Myers Middle School|2005-08-01|

#### `DISTINCT`로 고유값 찾기

```sql
SELECT DISTINCT school
FROM teachers
ORDER BY school;
```
- 테이블을 다룰 때 열 안의 행이 중복 값을 갖고 있는 경우가 흔하다.
- 예를 들어 각 학교 별로 채용하는 선생님의 수가 다수이기 때문에 `teachers` 테이블의 `school` 열에는 같은 학교 이름이 여러 번 저장된다.
- 쿼리에 `DISTINCT` 키워드를 넣으면 중복을 제거하고 값을 하나씩 보여줄 수 있다.

|school             |
|-------------------|
|F.D. Roosevelt HS  |
|Myers Middle School|

- 이는 데이터 품질을 파악하는 데 도움이 되는 첫 단추이다.
- 가령 어떤 학교의 이름이 여러가지 형식으로 나타나는 경우에 철자 변형을 쉽게 찾아내고 수정할 수 있다.
- `DISTINCT`는 특히 날짜나 숫자를 다룰 때 일관성이 떨어지거나 깨진 형식을 찾는데 유용하다.
- 또 다른 예시로 날짜가 `text` 데이터 타입의 형태로 기입된 데이터셋을 받을 수도 있는데, 이는 꼭 피해야 할 행위이다.
- 가령 다음과 같은 기형적인 날짜 형태를 유발하게 된다.

| date      |
| --------- |
| 5/30/2023 |
| 6//2023   |
| 6/1/2023  |
| 6/2/2023  |

```sql
SELECT DISTINCT school, salary
FROM teachers
ORDER BY school, salary;
```
- `DISTINCT` 키워드는 여러 열에서도 동시에 작동한다.
- 열을 추가하게 될 경우 쿼리가 각각에 대한 고유한 값의 쌍을 결과로 보여준다.

|school             |salary|
|-------------------|------|
|F.D. Roosevelt HS  |36,200|
|F.D. Roosevelt HS  |38,500|
|F.D. Roosevelt HS  |65,000|
|Myers Middle School|36,200|
|Myers Middle School|43,500|

- 이 기술은 '테이블 안의 `x`마다 나올 수 있는 `y` 값으로는 뭐가 있을까?'라는 질문에 대한 답을 제공한다.

#### `WHERE`로 행 필터링하기

```sql
SELECT last_name, school, hire_date
FROM teachers
WHERE school = 'Myers Middle School';
```
- 특정 기준에 부합하는 열들에 담긴 행만 보여주는 쿼리가 필요할 때가 있다.
- 이런 작업을 수행하기 위해 `WHERE` 키워드를 사용한다.
- `WHERE` 키워드는 수학, 비교, 논리 연산을 수행하는 연산자를 이용해 만들어 낸 조건에 따라 특정 값, 값의 범위를 포함하는 행을 찾도록 한다.
- 또한 그 기준을 바탕으로 행을 제외할 수도 있다.

|last_name|school             |hire_date |
|---------|-------------------|----------|
|Cole     |Myers Middle School|2005-08-01|
|Bush     |Myers Middle School|2011-10-30|
|Diaz     |Myers Middle School|2005-08-30|

- 비교 연산자는 `=`, `!=`(`<>`), `>`, `<`, `<=`, `>=`, `BETWEEN`, `IN`, `LIKE`, `ILIKE`, `NOT` 등이 있다.
- 사용 예시는 다음과 같다.

```sql
-- Janet 이름을 가진 선생님 찾기
SELECT first_name, last_name, school
FROM teachers
WHERE first_name = 'Janet';

-- F.D. Roosevelt HS를 제외한 모든 학교 이름 출력하기
SELECT school
FROM teachers
WHERE school <> 'F.D. Roosevelt HS';

-- 2000년 1월 1일 이전에 고용된 선생님 출력하기
SELECT first_name, last_name, hire_date
FROM teachers
WHERE hire_date < '2000-01-01';
  
-- 연봉이 $43,500 이상인 선생님 찾기
SELECT first_name, last_name, salary
FROM teachers
WHERE salary >= 43500;

-- 연봉이 $40,000~$65,000인 선생님 찾기
SELECT first_name, last_name, school, salary
FROM teachers
WHERE salary BETWEEN 40000 AND 65000;

SELECT first_name, last_name, school, salary
FROM teachers
WHERE salary >= 40000 AND salary <= 65000;
```
- `BETWEEN`을 사용할 때는 이중 계산을 주의해야 한다.
- 예를 들어 `BETWEEN 10 AND 20`을 한 이후 `BETWEEN 20 AND 30`을 실행하면 두 쿼리 결과 모두에서 20을 값으로 갖는 행이 나타난다.
- `BETWEEN`보다 명시적인 초과, 미만 연산자를 사용하면 이중 계산을 방지할 수 있다.

#### `WHERE`에 `LIKE`와 `ILIKE` 사용하기
- 두 연산자는 지정된 패턴에 맞는 다양한 문자를 검색하기 때문에, 정확한 철자를 모르거나 잘못 작성한 단어를 찾을 때 편리하다.
- 가령 다음 기호를 사용하여 일치시킬 패턴을 지정한다.
- 두 기호는 혼합해서 사용할 수 있다.

1. 퍼센트 기호(`%`): 문자 한 개 또는 여러 개와 매칭하는 와일드 카드
2. 언더바(`_`): 문자 한 개와 매칭하는 와일드 카드

- 예를 들어 `baker`라는 단어를 찾고자 한다면 다음과 같이 매칭할 수 있다.
```bash
LIKE 'b%'
LIKE '%ak%'
LIKE '_aker'
LIKE 'ba_er'
```

- `ANSI SQL` 표준인 `LIKE` 연산자는 대소문자를 구분하는 반면, `PostgreSQL`에서만 적용되는 연산자인 `ILIKE` 연산자는 대소문자를 구분하지 않는다.

```sql
SELECT first_name
FROM teachers
WHERE first_name LIKE 'sam%';

SELECT first_name
FROM teachers
WHERE first_name ILIKE 'sam%';
```
- 가령 첫 번째 `WHERE` 절에서는 대소문자를 구분하기 때문에 0개의 결과가 나오지만, 두 번째 `WHERE` 절에서는 테이블에서 `Samuel`과 `Samantha`라는 결괏값을 보여준다.
- 사람의 이름이나 장소, 제품 또는 고유 명사를 기입한 작업자가 모든 데이터를 일관되게 적어 두기는 쉽지 않기 때문에 `ILIKE`와 와일드 카드를 활용하면 좋다.
- 다만 `LIKE`와 `ILIKE`는 패턴을 검색하므로, 데이터베이스가 커질 수록 검색 성능이 떨어질 수 있다.
- 이 문제는 인덱스를 통해 해결할 수 있다.

#### `AND`와 `OR`로 연산자 조건 결합하기

```sql
SELECT *
FROM teachers
WHERE school = 'Myers Middle School'
	  AND salary < 40000;

SELECT *
FROM teachers
WHERE last_name = 'Cole'
	  OR last_name = 'Bush';

SELECT *
FROM teachers
WHERE school = 'F.D. Roosevelt HS'
	  AND (salary < 38000 OR salary > 40000);
```
- 만약 한 절에서 괄호 없이 `OR`와 `AND`를 모두 사용할 경우 데이터베이스는 `AND` 조건을 먼저 평가한 후 `OR` 조건을 평가한다는 점을 주의해야 한다.

#### 활용

```sql
SELECT first_name, last_name, school, hire_date, salary
FROM teachers
WHERE school LIKE '%Roos%'
ORDER BY hire_date DESC;
```
- `WHERE` 절과 `ORDER BY` 정렬을 포함한 `SELECT` 문이다.
- 위 코드는 `Roosevelt High School`의 선생님을 가장 최근에 고용된 순서대로 보여준다.
- 결과는 다음과 같다.

|first_name|last_name|school           |hire_date |salary|
|----------|---------|-----------------|----------|------|
|Janet     |Smith    |F.D. Roosevelt HS|2011-10-30|36,200|
|Kathleen  |Roush    |F.D. Roosevelt HS|2010-10-22|38,500|
|Lee       |Reynolds |F.D. Roosevelt HS|1993-05-22|65,000|

- 쿼리 결과를 통해 특정 학교의 선생님 명단에 대한 채용 기간과 연봉 수준의 연관성을 확인할 수 있다.