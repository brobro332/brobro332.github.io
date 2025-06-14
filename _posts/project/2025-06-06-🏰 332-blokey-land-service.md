---
title: 🏰 332-blokey-land-service
date: 2025-06-06 20:33:00 +0900
categories:
  - Project
tags:
  - Project
---

### 기획 및 설계
![](/assets/image/Pasted%20image%2020250607224217.png)
- 프로젝트를 체계적으로 관리하고, 프로젝트와 팀원을 연결해주는 서비스

### 🛠 **프로젝트 환경**  
- `Java 21`, `Spring boot 3.5.0`, `Gradle`
- `PostgreSQL`, `Elasticsearch`, `JPA` , `QueryDSL` 
- `React`, `Mui`
- `Docker`, `Docker-compose`
- `OCI`, `GitHub Actions`
- `IntelliJ IDEA`

#### ✅ 패키지 구조(`v1.0.0`)
```bash
332-blokey-land-service
├── project
│   ├── domain
│   ├── controller
│   ├── dto
│   ├── mapper
│   ├── repository
│   └── service
├── task
│   ├── domain
│   ├── controller
│   ├── dto
│   ├── mapper
│   ├── repository
│   └── service
├── blokey
│   ├── domain
│   ├── controller
│   ├── dto
│   ├── mapper
│   ├── repository
│   └── service
├── offer
│   ├── domain
│   ├── controller
│   ├── dto
│   ├── mapper
│   ├── repository
│   └── service
├── milestone
│   ├── domain
│   ├── controller
│   ├── dto
│   ├── mapper
│   ├── repository
│   └── service
├── member
│   ├── domain
│   ├── controller
│   ├── dto
│   ├── mapper
│   ├── repository
│   └── service
└── common
    ├── domain
    ├── dto
    ├── exception
    ├── type
    ├── util
    └── config
```

#### ✅ 기능 명세서(`v1.0.0`)

| 기능                      | HTTP Method | Endpoint                               |
| ------------------------- | ----------- | -------------------------------------- |
| 사용자 등록               | POST        | `/api/blokeys`                         |
| 사용자 목록 조회          | GET         | `/api/blokeys`                         |
| 사용자 정보 조회          | GET         | `/api/blokeys/{blokeyId}`              |
| 사용자 정보 수정          | PATCH       | `/api/blokeys/{blokeyId}`              |
| 사용자 삭제               | DELETE      | `/api/blokeys/{blokeyId}`              |
| 프로젝트 생성             | POST        | `/api/projects`                        |
| 프로젝트 목록 조회        | GET         | `/api/projects`                        |
| 프로젝트 정보 조회        | GET         | `/api/projects/{projectId}`            |
| 프로젝트 정보 수정        | PATCH       | `/api/projects/{projectId}`            |
| 프로젝트 삭제             | DELETE      | `/api/projects/{projectId}`            |
| 프로젝트 복구             | PATCH       | `/api/projects/{projectId}:restore`    |
| 태스크 등록               | POST        | `/api/tasks`                           |
| 태스크 정보 조회          | GET         | `/api/tasks/{taskId}`                  |
| 태스크 정보 수정          | PATCH       | `/api/tasks/{taskId}`                  |
| 태스크 삭제               | DELETE      | `/api/tasks/{taskId}`                  |
| 멤버 등록                 | GET         | `/api/projects/{projectId}/members`    |
| 프로젝트별 멤버 목록 조회 | GET         | `/api/projects/{projectId}/members`    |
| 사용자별 멤버 목록 조회   | GET         | `/api/blokeys/{blokeyId}/members`      |
| 멤버 정보 수정            | PATCH       | `/api/members/{memberId}`              |
| 멤버 삭제                 | DELETE      | `/api/projects/{projectId}/members`    |
| 제안 등록                 | POST        | `/api/offers`                          |
| 제안 목록 조회            | GET         | `/api/offers`                          |
| 제안 정보 수정            | PATCH       | `/api/offers/{offerId}`                |
| 제안 삭제                 | DELETE      | `/api/offers/{offerId}`                |
| 마일스톤 등록             | POST        | `/api/projects/{projectId}/milestones` |
| 마일스톤 목록 조회        | GET         | `/api/projects/{projectId}/milestones` |
| 마일스톤 정보 수정        | PATCH       | `/api/milestones/{milestoneId}`        |
| 마일스톤 삭제             | DELETE      | `/api/milestones/{milestoneId}`        |
| 작업에 마일스톤 설정      | PATCH       | `/tasks/{taskId}/milestone`            | 
