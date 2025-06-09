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
- `IntelliJ`

#### ✅ 패키지 구조
```bash
332-blokey-land-service
├── project
│   ├── domain
│   │   ├── Project.java
│   ├── controller
│   │   └── ProjectController.java
│   ├── mapper
│   │   └── ProjectMapper.java
│   ├── repository
│   │   └── ProjectRepository.java
│   ├── service
│   │   └── ProjectService.java
│   └── dto
│       └── ProjectDto.java
│
├── task
│   ├── domain
│   │   └── Task.java
│   ├── controller
│   │   └── TaskController.java
│   ├── mapper
│   │   └── TaskMapper.java
│   ├── repository
│   │   └── TaskRepository.java
│   ├── service
│   │   └── TaskService.java
│   └── dto
│       └── TaskDto.java
│
├── kanban
│   ├── domain
│   │   ├── KanbanColumn.java
│   │   └── KanbanTask.java
│   ├── controller
│   │   └── KanbanController.java
│   ├── repository
│   │   └── kanbanRepository.java
│   ├── service
│   │   └── kanbanService.java
│   └── dto
│       └── kanbanDto.java
│
├── user
│   ├── domain
│   │   └── User.java
│   ├── controller
│   │   └── UserController.java
│   ├── dto
│   │   ├── UserReqCreateDto.java
│   │   ├── UserReqUpdateDto.java
│   │   └── UserRespDto.java
│   ├── mapper
│   │   └── UserMapper.java
│   ├── repository
│   │   └── UserRepository.java
│   └── service
│       └── UserService.java
│
└── common
	├── domain
	├── dto
    ├── exception
    ├── type
    ├── util
    └── config
```

#### ✅ 기능 명세서

| 기능명             | 설명                             | HTTP Method | Endpoint                                             |
| ------------------ | -------------------------------- | ----------- | ---------------------------------------------------- |
| 사용자 회원가입    | 사용자 등록 요청                 | POST        | `/api/users`                                         |
| 사용자 목록 조회   | 조건에 따른 사용자 리스트 조회   | GET         | `/api/users`                                         |
| 사용자 정보 조회   | 특정 사용자 정보 조회            | GET         | `/api/users/{userId}`                                |
| 사용자 정보 수정   | 사용자 정보 수정                 | PATCH       | `/api/users/{userId}`                                |
| 사용자 탈퇴        | 사용자 삭제 요청                 | DELETE      | `/api/users/{userId}`                                |
| 프로젝트 생성      | 새 프로젝트를 생성               | POST        | `/api/projects`                                      |
| 프로젝트 목록 조회 | 프로젝트 목록 조회               | GET         | `/api/projects`                                      |
| 프로젝트 정보 조회 | 특정 프로젝트 정보 조회          | GET         | `/api/projects/{projectId}`                          |
| 프로젝트 정보 수정 | 프로젝트 정보 수정               | PATCH       | `/api/projects/{projectId}`                          |
| 프로젝트 삭제      | 프로젝트 삭제                    | DELETE      | `/api/projects/{projectId}`                          |
| 태스크 등록        | 태스크 등록                      | POST        | `/api/tasks`                                         |
| 태스크 정보 조회   | 태스크 정보 조회                 | GET         | `/api/tasks/{taskId}`                                |
| 태스크 정보 수정   | 태스크 정보 수정                 | PATCH       | `/api/tasks/{taskId}`                                |
| 태스크 삭제        | 태스크 삭제                      | DELETE      | `/api/tasks/{taskId}`                                | 
| 멤버 목록 조회     | 해당 프로젝트의 멤버 리스트 조회 | GET         | `/api/projects/{projectId}/members`                  |
| 마일스톤 생성      | 마일스톤(일정 단위) 생성         | POST        | `/api/projects/{projectId}/milestones`               |
| 마일스톤 수정      | 마일스톤 정보 수정               | PUT         | `/api/projects/{projectId}/milestones/{milestoneId}` |
| 마일스톤 삭제      | 마일스톤 삭제                    | DELETE      | `/api/projects/{projectId}/milestones/{milestoneId}` |
| 칸반 컬럼 생성     | 칸반 보드에 컬럼 추가            | POST        | `/api/projects/{projectId}/kanban-columns`           |
| 칸반 컬럼 수정     | 컬럼 이름 수정                   | PUT         | `/api/kanban-columns/{columnId}`                     |
| 칸반 컬럼 삭제     | 컬럼 삭제                        | DELETE      | `/api/kanban-columns/{columnId}`                     |
| 칸반 태스크 생성   | 칸반 컬럼에 태스크 추가          | POST        | `/api/kanban-columns/{columnId}/tasks`               |
| 칸반 태스크 이동   | 드래그앤드롭으로 컬럼/순서 이동  | PATCH       | `/api/kanban-tasks/{taskId}/order`                   |
| 칸반 태스크 수정   | 태스크 제목, 설명 등 수정        | PUT         | `/api/kanban-tasks/{taskId}`                         |
| 칸반 태스크 삭제   | 태스크 삭제                      | DELETE      | `/api/kanban-tasks/{taskId}`                         |