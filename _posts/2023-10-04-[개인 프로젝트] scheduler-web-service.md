---
title: "개인 프로젝트: scheduler-web-service"
excerpt: "Project; Personal; scheduler-service"
categories: 
- Project
tags:
- [Project, Personal]
published: true
toc: true
toc_sticky: true
---

<br><br>

## **📄 Outline**

- 프로젝트 플래너 작성을 지원하는 웹 서비스

<br>

## **⏱ Period**  

- 2023.06.24 ~ 2023.10.01

<br>

## **🛠 Environment**

- Java 17
- Springboot 3.0.8
- Gradle
- IntelliJ
- MariaDB
- JSP
- JQuery

<br>

## **📄 특징**

- 프로젝트 플래너 작성 관리
- 커뮤니티
- 프로젝트 플래너 및 커뮤니티 게시글 작성 시 서버의 파일시스템 내 이미지 파일 관리 
- 배치 프로그램과 스케줄러를 통한 장기간 미접속 사용자 일괄 처리

<br>

## **🔧 핵심 기술**

- Scheduler
- Spring Batch 5.0
- Spring Security
- HTML 요소 동적 생성 및 JQuery 이벤트 핸들링

<br>

## **📂 프로젝트 구조도**
```bash
Scheduler-Project
├── global
│   ├── Spring Security
│   ├── Spring Batch
│   ├── FCM
│   ├── Interceptor
│   ├── Mail
│   └── Manage Image File
├── user
│   ├── Login, SignUp
│   ├── User CRUD
│   ├── Profile Image CRUD
│   ├── Navbar Alert
│   ├── OAuth 2.0(Kakao, Naver) Login + SignUp
│   └── Validation
├── community
│   ├── Post CRUD
│   ├── comment CRUD
│   ├── reply CRUD
│   └── view_cnt Logic based on Cookie
└── scheduler
    ├── Project Planner CRUD
    ├── Task CRUD
    ├── Daily Task Log CRUD 
    └── Sub Task CRUD
``` 

<br>

## **📌 ERD**
<div style="text-align: center;">
<img src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/ERD.png" alt="Image: ERD">
</div>
<figcaption style="text-align: center; font-weight: bold;">💎 Image: ERD</figcaption>

<br>

## **🎬 기능별 이미지 및 동영상**


### 1️⃣ 사용자 인증

#### 1. 회원가입
<video src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/회원가입.mp4" width="800px" height="400px" controls></video>
<figcaption style="text-align: center; font-weight: bold;">🎥 Video: 회원가입</figcaption>

<br>

#### 2. 회원정보 변경
<video src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/회원정보변경.mp4" width="800px" height="400px" controls></video>
<figcaption style="text-align: center; font-weight: bold;">🎥 Video: 회원정보 변경</figcaption>


<br>

#### 3. OAuth 2.0 로그인
<video src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/OAuth 2.0 로그인.mp4" width="800px" height="400px" controls></video>
<figcaption style="text-align: center; font-weight: bold;">🎥 Video: OAuth 2.0 로그인</figcaption>

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### 2️⃣ 커뮤니티
<video src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/커뮤니티.mp4" width="800px" height="400px" controls></video>
<figcaption style="text-align: center; font-weight: bold;">🎥 Video: 게시글 및 댓글 CRUD</figcaption>

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### 3️⃣ 프로젝트 플래너

#### 1. CRUD
<video src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/프로젝트 플래너.mp4" width="800px" height="400px" controls></video>
<figcaption style="text-align: center; font-weight: bold;">🎥 Video: 프로젝트 플래너 CRUD</figcaption>

<br>

#### 2. 스케줄러

<div style="text-align: center;">
<img src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/플래너 스케줄러.PNG" alt="Image: 프로젝트 플래너">
</div>
<figcaption style="text-align: center; font-weight: bold;">💎 Image: 프로젝트 플래너</figcaption>

<br>

<video src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/플래너 스케줄러.mp4" width="800px" height="400px" controls></video>
<figcaption style="text-align: center; font-weight: bold;">🎥 Video: 마감일이 다가온 플래너 작성자에게 FCM 메세지 전송</figcaption>

<br>

### 4️⃣ 배치 프로그램
<div style="text-align: center;">
<img src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/배치_스텝.PNG" alt="Image: 배치_스텝">
</div>
<figcaption style="text-align: center; font-weight: bold;">💎 Image: 배치_스텝: 단위 테스트를 위해 미접속 1일차 회원을 대상으로 조회</figcaption>

<br>

<div style="text-align: center;">
<img src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/배치_데이터베이스.PNG" alt="Image: 배치_데이터베이스">
</div>
<figcaption style="text-align: center; font-weight: bold;">💎 Image: 배치_데이터베이스: 대상 회원</figcaption>

<br>

<div style="text-align: center;">
<img src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/배치_프로세서.PNG" alt="Image: 배치_프로세서">
</div>
<figcaption style="text-align: center; font-weight: bold;">💎 Image: 배치_프로세서: 조회한 회원에게 메일 전송</figcaption>

<br>

<video src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/배치 프로그램.mp4" width="800px" height="400px" controls></video>
<figcaption style="text-align: center; font-weight: bold;">🎥 Video: 배치 프로그램</figcaption>

<br>

<div style="text-align: center;">
<img src="/assets/images/2023-10-04-[개인 프로젝트] scheduler-service/배치_이메일.PNG" alt="Image: 배치_이메일">
</div>
<figcaption style="text-align: center; font-weight: bold;">💎 Image: 배치_이메일: 배치_스텝에서 조회된 회원에게 메일 전송</figcaption>

<br>

## 💭 느낀점

- 이번 프로젝트는 사용자가 이미지를 업로드할 경우 데이터 정합성을 지키고, 임시 폴더에 이미지 파일이 누적되지 않도록 특히 신경을 썼다. <br><br>
3월 ~ 6월에 진행한 팀 프로젝트와 기술 스택은 거의 동일하지만 구현 부분에서 많이 개선을 하려고 노력했다. <br><br>
예를 들어 서버 영역에서는 일괄 처리를 하기 위해 배치 프로그램을 구현해보고 JSP, JQuery 등 클라이언트 영역에서는 동적 요소의 이벤트를 핸들링 하는 것에 많은 고민을 했다. <br><br>
배치 프로그램의 경우 Spring Batch 5.0에 대해 참고할 만한 자료가 적어서 어려움을 겪었으며 이번에도 템플릿 엔진을 JSP로 구현하다보니, 스크립트문이 비대해질수록 결합도가 높아진다는 단점을 실감했다. <br><br>
AWS EC2를 통해 배포를 하였으나, SSL/TLS 인증은 생략하고 파일 시스템이나 OAuth 2.0 관련 기능이 정상적으로 동작하는지 확인하는데 그쳤으며 FCM이나 배치 프로그램은 로컬 환경에서 주로 테스트를 하였다.

<br>

## 🙋‍♂️ GitHub
[https://github.com/brobro332/scheduler-service](https://github.com/brobro332/scheduler-service)