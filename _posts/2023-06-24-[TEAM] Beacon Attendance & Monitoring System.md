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
##### **📄 개요**
- 프로젝트명
  - monitoring-server

- 개발 기간
  - 23.03.30 ~ 22.06.19

- 개발환경
  - Java 17
  - Springboot 3.0.4
  - Gradle
  - IntelliJ
  - MariaDB

- 주요 기능
  - 비콘을 통한 출결 자동화 및 현장 내 보안 모니터링

##### **🙋‍♂️ 나의 역할**
###### 프로젝트 관리
  - 팀장으로서 프로젝트 일정, 진행 등 프로젝트 관리를 맡았다.
  - 학교 주관으로, 멘토님과 함께 하는 프로젝트라 <br>
  물품 구입 신청, 보고서 작성 및 제출 등 멘토링 활동에 대한 관리도 필요했다.<br><br>

###### 백엔드 영역
  - <b>FCM</b>
    - 서버의 요청을 통해 Firebase 클라우드에서 클라이언트에게 푸시 메세지를 보내는 서비스
    - 보안구역에 접근한 사용자(모바일 클라이언트)에게 경고 푸시알림을 보내기 위해 <br>
    Firebase Cloud Messaging 서비스를 이용하였다. 
    - 구현 후 단위 테스트는 모바일 애플리케이션이 구현되지 않은 상태여서, 
    개인적으로 서버와 모바일 부분 각각 데모 버전의 프로젝트를 만들어서 진행했다.<br><br>
    <img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/1.JPG" width="300px" height="300px"><br><br>
  - <b>SSE</b>
    - HTTP 스트리밍을 통해 서버에서 클라이언트로 단방향 통신을 위한 HTML5 통신 기술
    - 실시간 모니터링 기능을 구현하기 위해서는 웹소켓과 SSE 기술 중 하나를 선택해야했는데, <br>
    단방향 통신으로 서버의 부하가 덜한 SSE 기술을 채택하였다.
    - 구현 후 단위 테스트는 데모 버전의 메소드를 만들어서 진행했다.
    - 멘토님께 SSE보다 기능구현이 편리한 SignalR이라는 라이브러리가 있음을 알게 됨<br><br>
    <img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/2.JPG"><br><br>
  - <b>사용자 인증(로그인, 회원가입)에 대한 CRUD</b>
    - 스프링 시큐리티, 쿠키 기반의 세션을 통한 사용자 인증을 구현했다.
    - 상용 단계까지는 가지 않을 것이라 생각해 세션 기반으로 충분하다고 판단했다. 
  - <b>관리자페이지 알림</b>
    - 관리자 페이지 footer.jsp 파일에 js파일을 include하여 <br>
    window 객체가 load되는 이벤트가 발생할 경우, <br>
    서버단의 메소드를 통해 관리자의 알림을 조회하게끔 하였다. <br>
    그리고 MVC Interceptor를 통해 해당 Model을 <br>
    현재 관리자가 접속해있는 페이지로 넘겨줄 수 있게끔 하였으며,<br>
    실시간 갱신은 되지 않지만 페이지를 이동할 때마다 갱신이 된다.
    모든 관리자페이지에서 알림을 확인 가능하다.<br><br>
    <img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/3.JPG"><br><br>

###### 프론트엔드 영역
  - JSP와 자바스크립트를 통해 프론트 영역을 구현하였다.
  - JSP와 Thymeleaf 중 고민을 하였으나 <br> 
  이번 프로젝트는 프론트엔드보다는 백엔드 영역 기능 구현이 중요하다고 판단하여 <br>
  프론트엔드 영역은 조금이라도 신경을 덜 쓰기 위해 구현에 익숙한 JSP를 채택하였다. 
  - Chart.js
    - 웹 사용자페이지
  <img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/4-1.JPG"><br><br>
    - 웹 관리자페이지
  <img src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/4-2.PNG"><br><br>
    - JPA를 통한 Select 메소드 등 백엔드 영역과 프론트 영역이 혼재돼있으나 <br> 
    Chart.js는 자바스크립트 관련 서비스이기에 프론트엔드 영역으로 구분하였다.

###### 배포
  - Amazon Web Service의 EC2를 이용하였다.
    - 가장 보편적이고, 프로젝트 기간이 짧아 사용한 만큼 결제한다는 메리트가 있어 좋았다.
    - Elastic IP : EC2의 public IPv4 주소는 서버가 중지, 중단, 일시정지 될 경우 <br>
    IP가 유동적으로 변해버리는데, 변하지 않는 고정 IP 주소를 위해 이용했음
    - Route53 : 가비아에서 구매한 도메인 등록 및 도메인 관리를 위함
    - Load Balancer : 트래픽 분산에 대한 서비스로, HTTPS 설정을 위해 이용함
    - SSL 인증을 통한 HTTPS 설정
      - HTTP 프로토콜은 암호화되어있지 않은 데이터를 전송하기 때문에 웹의 보안을 <br>
      강화하기 위해 SSL인증서를 등록하여 HTTPS 설정을 하였으며, <br>
      서버와 웹 브라우저는 암호화된 데이터를 공유할 수 있게 됨

##### **🎥 동영상**
- 웹 사용자페이지
  <video src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/5.MP4" width="800px" height="400px" controls></video>

<br>

- 웹 관리자페이지
  <video src="/assets/images/2023-06-24-Beacon Attendance & Monitoring System/6.MP4" width="800px" height="400px" controls></video>

<br>
---
<br>

##### **💭 느낀점**
프로젝트도, 조장도 처음이었다. 여러 면에서 많이 미숙했으며, <br>
지나고나니 프로젝트 계획이나 일정 관리에 있어서도 아쉬운 점이 많다. <br>
나 포함 세 명의 팀원과 멘토님으로 구성돼있었는데 <br> 
코드 및 형상 관리, 협업 등 팀원들과 멘토님께 여러 부분에서 많이 배웠다. <br>
삼변측량, 출결 등 백엔드 핵심 기능은 보다 실력이 좋은 팀원이 맡았는데, <br> 
많은 자극을 받아 나도 더욱 노력해야겠다는 생각이 들었다.