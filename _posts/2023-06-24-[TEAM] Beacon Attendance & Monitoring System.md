---
title: "[TEAM] Beacon Attendance & Monitoring System"
excerpt: "Project; [TEAM] Beacon Attendance & Monitoring System"
categories: 
- Project
tags:
- [Project, Team]
published: true
toc: true
toc_sticky: true
---

<br><br>

# 🎞 Monitoring-Server-Project


### **📄 Outline**

- 비콘을 통한 출결 자동화 및 현장 내 보안 모니터링
- 사용자페이지와 관리자페이지로 나뉨


### ⏱ **Period**  

- 23.03.30 ~ 22.06.19


### 🛠 **Environment**

- Java 17
- Springboot 3.0.4
- Gradle
- IntelliJ
- MariaDB


### **🙋‍♂️ 나의 역할**

#### Ⅰ. 프로젝트 관리

- 팀장으로서 프로젝트 일정 관리, 산출물 작성, 멘토링 활동진행 등 프로젝트에 대한 관리를 맡았다.


#### Ⅱ. 백엔드 영역 개발

##### **1. Firebase Cloud Messaging**

- 서버의 요청을 통해 Firebase 클라우드에서 클라이언트에게 푸시 메세지를 보내는 서비스
- 보안구역에 접근한 사용자에게 경고 메세지를 보내기 위해 FCM 서비스를 이용하였다. 
- 당시 모바일 애플리케이션이 구현되지 않은 상태여서, 단위 테스트는 서버와 앱 각각 데모 버전의 프로젝트를 만들어서 진행했다.

<img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/1.jpg" width="50%">

---

##### **2. Server Sent Events**

- HTTP 스트리밍을 통해 서버에서 클라이언트로 단방향 통신을 위한 HTML5 통신 기술
- 실시간 모니터링 기능을 구현하기 위해 단방향 통신으로 서버의 부하가 덜한 SSE 기술을 채택하였다.
- 구현 후 단위 테스트는 데모 버전의 메소드를 만들어서 진행했다.

<img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/2.JPG">

---

##### **3. 사용자 인증(로그인, 회원가입)에 대한 CRUD**
  - 쿠키 기반의 세션과 스프링 시큐리티를 통한 사용자 인증을 구현했다.
  - 상용 단계까지는 가지 않을 것이라 생각해 세션 기반으로 충분하다고 판단했다. 
  - 쿠키 탈취 등 보안 문제를 보완하기 위해 배포시에 SSL 인증을 통해 HTTPS 설정을 하였다.
  - 스프링 시큐리티를 통해 관리자만 접속 가능한 관리자페이지를 구현하였다.

---

##### **4. 관리자페이지 알림**

- JQuery와 서버의 MVC Interceptor를 통해 관리자 알림 기능을 구현했다.

<img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/3.JPG"><br><br>

---

#### Ⅲ. 프론트엔드 영역 개발
- JSP와 JQuery를 통해 프론트 영역을 구현하였다.
- Chart.js
<img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/4-1.JPG"><br><br>
<img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/4-2.png"><br><br>

---

#### Ⅳ. 배포
- Amazon Web Service의 EC2를 이용하였다.
- 가장 보편적이고, 프로젝트 기간이 짧아 사용한 만큼 결제한다는 메리트가 있어 좋았다.
- Elastic IP : 변하지 않는 고정 IP 주소를 위해 이용
- Route53 : 가비아에서 구매한 도메인 등록 및 도메인 관리
- Load Balancer : 트래픽 분산에 대한 서비스로, HTTPS 설정을 위해 이용
- SSL 인증을 통한 HTTPS 설정
- 웹의 보안을 강화하기 위해 SSL인증서를 등록하여 HTTPS 설정을 하였으며 서버와 웹 브라우저는 암호화된 데이터를 공유할 수 있게 됨

---

### **🎥 동영상**

#### 웹 사용자페이지

<video src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/5.mp4" width="800px" height="400px" controls></video>

<br/><br/>

#### 웹 관리자페이지

<video src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/6.mp4" width="800px" height="400px" controls></video>

5초 ~ 55초의 경우 서버의 스케줄러를 통해 비콘의 배터리 상태를 확인하여 SSE Emitter를 통해 관리자 클라이언트로 메세지를 보내는 과정을 기다리는 것 

---

### **💭 느낀점**

- 개발 프로젝트도, 조장도 처음이었다. 그래서 여러 면에서 많이 미숙했다.
- 지나고나니 프로젝트 계획이나 일정 관리에 있어서도 아쉬운 점이 많다.
- 코드 및 형상 관리, 협업 등 팀원들과 멘토님께 여러 부분에서 많이 배웠다.
- 삼변측량, 출결 등 백엔드 핵심 기능은 보다 실력이 좋은 팀원이 맡았는데, 많은 자극을 받아 나도 백엔드 영역 개발에 더욱 노력해야겠다는 생각이 들었다.
- 개발 프로젝트를 진행해봄으로써 JSP, JQuery, Spring boot에 대한 이해도가 늘었다.
- 사용자 인증, SSE 서버, FCM 등 서버 관련 구현 및 기술을 사용해봄으로써 좋은 경험을 얻었다.