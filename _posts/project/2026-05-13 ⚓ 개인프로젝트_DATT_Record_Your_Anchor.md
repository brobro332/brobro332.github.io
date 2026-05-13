---
title: ⚓ 개인프로젝트_DATT_Record_Your_Anchor
date: 2026-05-13 20:33:00 +0900
categories:
  - Project
tags:
  - Project
---

>**"당신이 머문 공간을 닻(Anchor)으로 기록하세요"**
 DATT는 사용자의 위치를 기반으로 장소를 검색하고, 그곳에서의 소중한 기억을 '닻'이라는 상징적인 개념으로 기록하고 공유하는 위치 기반 서비스입니다.

* **🌐 서비스 URL:** [https://datt-prd.xyz/](https://datt-prd.xyz/) 
* **💻 GitHub 저장소:** [https://github.com/brobro332/DATT](https://github.com/brobro332/DATT)

---
## 1. 🚀 개요

- **서비스 성격**: 위치 기반 장소 기록 및 소셜 공유 플랫폼
- **핵심 가치**: 단순한 체크인을 넘어, 특정 장소에 나만의 기록을 고정(Anchor)하고 이를 타인과 공유하는 경험 제공
- **주요 기능**:
  - **실시간 장소 탐색**: 네이버/구글 맵 크롤링 기반의 최신 장소 정보 제공
  - **위치 기반 기록(Anchor)**: 지도 상의 특정 좌표에 텍스트와 사진으로 추억 기록
  - **공유 자동화**: 기록된 '닻'을 고유 ID 기반의 공유 페이지로 생성 및 전파

---
## 2. 🛠 기술스택

### **Frontend**
- **Framework**: Next.js 14 (App Router)
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **State Management**: React Hooks (useState, useMemo, useEffect)

### **Backend**
- **Main Engine**: Spring Boot 3.x / Java 17
- **Data Engine**: FastAPI / Python (Web Scraping Agent)
- **Database**: PostgreSQL with **PostGIS** (Spatial Extension)
- **ORM**: Spring Data JPA / Hibernate Spatial
- **Architecture**: Domain-Driven Design (DDD) 지향

### **Infra & DevOps**
- **Container**: Docker, Docker Compose
- **Web Server**: Nginx (Reverse Proxy)
- **Security**: Certbot (SSL/HTTPS Auto-renewal)

---
## 3. 💎 핵심 역량

### 1. 멀티 엔진 아키텍처 (Hybrid Backend Structure)
- **Challenge**: Java의 정적인 특성으로는 유연한 웹 크롤링 및 실시간 데이터 가공에 한계가 있음
- **Solution**: 비즈니스 로직과 트랜잭션 관리는 **Spring Boot**가, 대규모 웹 데이터 수집은 **FastAPI(Python)**가 담당하는 분산 구조 설계
- **Result**: **Playwright**를 활용한 에이전트 시스템을 구축하여, 고정된 DB 정보가 아닌 실시간 장소 데이터를 안정적으로 수집/제공

### 2. 확장성을 고려한 도메인 중심 설계 (DDD)
- **Challenge**: 서비스 기능 확장에 따른 코드 복잡도 증가 및 의존성 스파게티 위험
- **Solution**: `anchor`, `place`, `member` 등 핵심 도메인을 기준으로 패키지를 분리하고, 레이어드 아키텍처를 적용
- **Result**: 각 도메인의 독립성을 보장하여 유지보수성을 높였으며, 신규 기능(예: 장소 추천 로직) 추가 시 영향 범위를 최소화함

### 3. 안정적인 운영 환경 구축 (Infrastructure as Code)
- **Challenge**: 다양한 기술 스택(Java, Python, JS, DB, Proxy)의 일관된 배포 환경 필요
- **Solution**: 전체 시스템을 **Docker Compose**로 컨테이너화하여 'One-Step' 배포 환경 구축
- **Result**: **Nginx**를 API Gateway로 활용해 보안(SSL)과 트래픽 분산을 관리하며, **Certbot**을 통한 인증서 자동 갱신으로 운영 공수를 절감

---
## 4. 🏗 시스템 아키텍처

1. **Client**: Next.js 기반의 반응형 웹 인터페이스
2. **Proxy**: Nginx를 통한 SSL 종단점 처리 및 요청 라우팅
3. **Core API**: Spring Boot를 이용한 비즈니스 로직 및 공간 데이터 쿼리 수행
4. **Data Agent**: FastAPI 기반의 실시간 장소 수집 엔진
5. **Storage**: PostGIS가 적용된 PostgreSQL로 공간 데이터 정밀 저장

---
## 5. 💡 회고

### 1. 주요 문제점 분석
### 🚀 성능 및 응답 속도
- **실시간 크롤링 병목:** 검색 시마다 4개 카테고리를 실시간으로 크롤링하여 DB에 저장하므로 응답 시간이 매우 김 (특히 '인천' 등 광범위한 지역)
- **카테고리 전환 지연:** 카테고리를 변경할 때마다 발생하는 데이터 처리로 인해 사용자 경험이 저하됨
### 🖱️ UI / UX 디테일
- **검색 초기화 부재:** 검색어를 지우고 버튼을 눌러도 초기 리스트로 복귀하지 않아 새로고침이 강제됨
- **정보 탐색 피로도:** 리스트가 길게 나열되어 스크롤 압박이 심하며, 검색 외에는 필터링이나 탐색이 어려움
- **테마 유지 안 됨:** 다크모드 설정 후 새로고침 시 화이트모드로 초기화되는 현상 발생
### 📢 기획 의도 전달
- **서비스 컨셉 모호:** "보편적인 핫플을 한눈에 보고 공유한다"는 제작 의도가 사용자에게 명시적으로 전달되지 않음

---
### 2. 추후 개선 및 실행 방안
### ✅ 기술적 개선
- **배치(Batch) 시스템 도입:** 
  - 주요 지역 및 카테고리를 24시간 단위로 미리 크롤링하여 DB에 캐싱
  - 사용자 요청 시 크롤링이 아닌 **DB 조회** 방식으로 전환하여 응답 속도를 획기적으로 개선
- **프론트엔드 캐싱:** 한 번 불러온 데이터는 상태 관리 라이브러리를 통해 카테고리 전환 시 즉각 반응하도록 수정
### ✅ UX 고도화
- **검색 핸들러 수정:** 검색어가 비어 있을 경우 전체 데이터를 노출하는 로직 추가
- **다크모드 지속성:** `localStorage`를 사용하여 사용자 테마 설정을 브라우저에 저장
- **UI 구조 개편:** 
  - 긴 스크롤 대신 탭 전환이나 그리드 뷰 도입 고려
  - 서비스 목적을 알리는 온보딩 가이드나 메인 배너 추가
### ✅ 신규 기능 개발
- **평점 및 사용자 리뷰:** 단순 정보 나열에서 벗어나 신뢰도를 높이기 위한 별점/리뷰 기능 추가
- **공유 최적화:** 약속 장소를 친구와 함께 결정할 수 있도록 공유 프로세스(카카오톡 등) 고도화

---
### 3. 종합 결론

현재의 **'실시간 크롤링'** 방식을 **'사전 데이터 수집(Batch)'** 방식으로 전환하는 것이 가장 시급한 과제입니다. 기술적 성능이 확보된 후, 다크모드 유지 및 검색 초기화와 같은 디테일한 UX를 보강하여 서비스 완성도를 높일 계획입니다.