---
title: "PostgreSQL"
excerpt: "Development; PostgreSQL"
categories: 
- Development
tags:
- [Development, PostgreSQL, Deployment]
published: true
toc: true
toc_sticky: true
---

<br><br>

## 🟥 특징

- 오픈 소스
- 강력한 데이터 모델
복잡한 데이터 모델을 다룰 수 있으며, 다양한 데이터 유형과 관계를 지원
- ACID 호환
ACID(원자성, 일관성, 고립성, 지속성) 트랜잭션을 지원
- 확장 가능성
다양한 플러그인과 확장 모듈을 통해 기능을 확장할 수 있음
- 고급 쿼리 및 최적화
복잡한 쿼리와 다양한 최적화 기술을 지원하며, 고급 기능인 Window 함수, 공간 데이터 지원, 그래프 데이터베이스 기능 등을 제공

<br>

이 외에도 특징이 많지만, 사실 크게 와닿지는 않는다. MySQL과의 차이점을 알아보자.

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

## 🟧 MySQL과의 차이점

### 1️⃣ 데이터 모델

- `**PostgreSQL**`
복잡한 데이터 모델을 지원하며, JSON, hstore, 배열 및 사용자 정의 데이터 유형을 포함한 다양한 데이터 유형을 처리할 수 있다. 또한, 고급 기능인 Window 함수, 공간 데이터 처리 및 그래프 데이터베이스를 지원한다.
- `**MySQL**`
간단한 데이터 모델을 가지고 있으며, JSON 데이터 유형을 지원하는 등 다양한 데이터 유형을 처리할 수 있지만 PostgreSQL만큼 다양한 유형을 처리할 수는 없다.

### 2️⃣ SQL 호환성

- `**PostgreSQL**`
ANSI SQL 표준을 엄격하게 준수하며, 고급 SQL 기능을 다루기에 적합하다.
- `**MySQL**`
SQL 표준을 준수하긴 하지만 일부 비표준 SQL 문법과 기능을 가질 수 있으며, 이는 사용자에게 주의가 필요하다.

### 3️⃣ 트랜잭션 및 ACID 지원

- `**PostgreSQL**`
ACID 트랜잭션을 완전히 지원하며, 데이터 일관성과 안전성을 보장한다.
- `**MySQL**`
ACID 트랜잭션을 지원하지만, 스토리지 엔진에 따라서 ACID 준수 여부가 달라질 수 있다.

### 4️⃣ 성능 및 확장성

- `**PostgreSQL**`
대규모 데이터베이스 및 복잡한 쿼리에 대한 성능을 제공하며, 고급 최적화 기능과 고려적인 인덱싱을 가지고 있다.
- `**MySQL**`
빠른 읽기 작업 및 단순한 쿼리에 대한 좋은 성능을 제공하지만, PostgreSQL만큼 복잡한 쿼리와 대규모 데이터베이스에 대한 성능이 우수하지는 않을 수 있다.

### 5️⃣ 라이선스

- `**PostgreSQL**`
PostgreSQL 라이선스(기술적으로 MIT와 유사한 라이선스)에 따라 배포되며, 수정하고 재배포할 수 있다.
- `**MySQL**`
GNU General Public License (GPL) 라이선스를 따르는데, 이는 MySQL을 상업적으로 사용하려면 GPL에 따라서 자체 소스 코드를 공개해야 할 수도 있음을 의미한다. 그러나 MySQL의 상용 버전과 더 편리한 라이선스도 제공되고 있다.

### 6️⃣ 커뮤니티와 생태계

- `**PostgreSQL**`
활발한 커뮤니티와 다양한 확장 모듈을 가지고 있으며, 대부분의 추가 기능은 오픈 소스로 제공된다.
- `**MySQL**`
MySQL도 큰 커뮤니티를 가지고 있지만, Oracle Corporation이 보유하고 있어 조금 더 중앙 집중적인 제어가 있을 수 있다.

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

## 🟨 사용법

- 추후 추가될 예정