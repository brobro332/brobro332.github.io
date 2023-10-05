---
title: "[Team] Beacon Attendance & Monitoring System"
excerpt: "Project; [Team] Beacon Attendance & Monitoring System"
categories: 
- Project
tags:
- [Project, Team]
published: true
toc: true
toc_sticky: true
---

<br><br>

## **📄 Outline**

- 비콘을 통한 출결 자동화 및 현장 내 보안 모니터링
- 사용자/관리자페이지 개발

<br>

## ⏱ **Period**  

- 23.03.30 ~ 22.06.19

<br>

## 🛠 **Environment**

- Java 17
- Springboot 3.0.4
- Gradle
- IntelliJ
- MariaDB

<br>

## **🙋‍♂️ 나의 역할**

<br>

### 1️⃣ 프로젝트 관리

- 팀장으로서 일정 관리, 보고서/산출물 작성, 멘토링 활동진행 등 프로젝트 관리

<br>

![Image: 기능 명세서](/assets/images/2023-06-24-Beacon Attendance & Monitoring System/7.PNG)
<figcaption style="text-align: center; bold;">Image: 산출물 - 기능 명세서</figcaption>

<br>

![Image: API 명세서](/assets/images/2023-06-24-Beacon Attendance & Monitoring System/8.jpg)
<figcaption style="text-align: center; bold;">Image: 산출물 - API 명세서</figcaption>

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### 2️⃣ 백엔드 영역 개발
##### 1. Firebase Cloud Messaging
- 서버의 요청으로 Firebase 클라우드에서 클라이언트로 푸시 메세지를 보내는 서비스
- 보안구역에 접근한 비인가자에게 경고 알림을 보내기 위해 FCM 서비스를 선택

##### 2. Server Sent Events

- HTTP 스트리밍을 통해 서버에서 클라이언트로 단방향 통신을 위한 HTML5 통신 기술
- 실시간 모니터링 구현을 위해 단방향 통신으로 부하가 덜한 SSE 기술을 선택

##### **3. 사용자 인증(로그인, 회원가입)에 대한 CRUD**
  - 쿠키 기반의 세션과 스프링 시큐리티를 통한 사용자 인증을 구현
  - 상용 단계까지는 가지 않을 것이라 생각해 세션 기반으로 충분하다고 판단
  - 쿠키 탈취 등 보안 문제 보완을 위해 AWS의 ACM 서비스를 통해 HTTPS 설정

##### **4. 관리자페이지 알림**

- JQuery와 서버의 MVC Interceptor를 통해 관리자 알림 기능을 구현

<br>

<div style="text-align: center;">
<img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/1.jpg" alt="Image: 단위 테스트 - FCM" style="width: 50%;">
</div>
<figcaption style="text-align: center; font-weight: bold;">Image: 단위 테스트 - FCM</figcaption>

<br>

<div style="text-align: center;">
<img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/2.JPG" alt="Image: 단위 테스트 - SSE">
</div>
<figcaption style="text-align: center; font-weight: bold;">Image: 단위 테스트 - SSE</figcaption>

<br>

<div style="text-align: center;">
<img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/3.JPG" alt="Image: 단위 테스트 - 관리자페이지 알림">
</div>
<figcaption style="text-align: center; font-weight: bold;">Image: 단위 테스트 - 관리자페이지 알림</figcaption>

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### 3️⃣ 프론트엔드 영역 개발
- JSP와 JQuery를 통해 프론트 영역을 구현
- Chart.js를 통해 데이터 시각화

<br>

<div style="text-align: center;">
<img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/4-1.JPG" alt="Image: Chart.js_1">
</div>
<figcaption style="text-align: center; font-weight: bold;">Image: Chart.js_1</figcaption>

<br>

<div style="text-align: center;">
<img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/4-2.png" alt="Image: Chart.js_2">
</div>
<figcaption style="text-align: center; font-weight: bold;">Image: Chart.js_2</figcaption>

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### 4️⃣ 배포
- Amazon Web Service를 통해 프로젝트 배포
  - EC2: 프로젝트 기간이 짧아 금전적인 부분을 고려하여 선택
  - Elastic IP: 변하지 않는 고정 IP 주소를 위해 이용
  - Route53: 가비아에서 구매한 도메인 등록 및 도메인 관리
  - Load Balancer: 트래픽 분산에 대한 서비스로, HTTPS 설정을 위해 이용
  - ACM: SSL/TLS 인증을 통한 HTTPS 설정

<br>

## 🎥 동영상

### 1️⃣ 사용자페이지

<video src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/5.mp4" width="800px" height="400px" controls></video>

<br/><br/>

### 2️⃣ 관리자페이지

<video src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/6.mp4" width="800px" height="400px" controls></video>

5초 ~ 55초: 스케줄러를 통해 배터리 잔량이 기준치 이하인 비콘이 존재할 경우 SSE Emitter를 통해 관리자 클라이언트로 메세지를 보내는 과정을 기다리는 것 

<br>

## 💭 느낀점

- 프로젝트 개발도, 조장도 처음이어서 많이 미숙했기에 프로젝트 계획이나 보고서 작성 등 프로젝트 진행에 있어 아쉬운 점이 많다.<br><br>
프로젝트 기간이 짧고 4학년 1학기에 진행한 팀 프로젝트이다보니 팀장으로서 보고서 및 산출물 작성, 멘토링 활동 및 일정을 관리하는데 어려움을 많이 겪었다. <br><br>
프로젝트를 진행하며 팀원들과 멘토님께 코드 작성 및 형상 관리, 협업 등 여러 부분을 많이 배우게 되었다. <br><br>
개발 경험이 없어서 백엔드 핵심 역할은 맡지 못했지만 사용자 인증, SSE 서버, FCM 등 세부 기능을 구현함으로써 백엔드 영역 개발에 값진 경험을 얻었다. <br><br>
아울러 서버에서 응답하는 데이터를 프론트 영역에 가져오는 과정에서 JSP, JQuery, Spring boot에 대한 이해도가 늘었다. <br>