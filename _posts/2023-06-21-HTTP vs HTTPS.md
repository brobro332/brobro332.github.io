---
title: "HTTP vs HTTPS"
excerpt: "What's the difference between HTTP and HTTPS"
categories: 
- Computer-Science
tags:
- [Computer-Science, Network]
published: true
toc: true
toc_sticky: true
---

<br><br>
##### **HTTP란?**<br>
- Hyper Text Transfer Protocol의 두문자어 <br> 
- HTTP는 웹에서 이루어지는 모든 데이터 교환의 기초 <br>
- 초기에는 HTML과 같은 하이퍼미디어 문서를 주로 전송했지만, <br>
최근에는 Plain text, JSON, XML 등 <br>
다양한 형태의 정보도 전송하는 애플리케이션 레이어 프로토콜이다. <br>
- 초기에는 웹 브라우저와 웹 서버 간의 커뮤니케이션을 위해 디자인되었지만 <br>
최근에는 모바일 애플리케이션 및 IoT 등과의 커뮤니케이션과 같이 <br>
다른 목적으로도 사용되고 있다. <br> 
- HTTP는 클라이언트가 요청을 생성하기 위한 연결을 연 다음 <br>
응답을 받을 때까지 대기하는 전통적인 클라이언트-서버 모델을 따른다. <br>
- HTTP는 무상태 프로토콜이며, 이는 서버가 두 요청 간에 어떠한 상태나 데이터를 <br>
유지하지 않음을 의미한다. <br> 
(상태를 유지하기 위한 노력으로 Cookie와 Session을 사용한다.) <br>
- 일반적으로 안정적인 TCP/IP 레이어를 기반으로 사용하는 응용 프로토콜이다. <br>
- 클라이언트(웹 브라우저, 모바일 등)가 브라우저를 통해서 <br>
어떠한 서비스를 URI를 통해 서버에 요청(Request)하면 <br> 
서버에서는 해당 요청에 대한 결과를 응답(Response)하는 형태로 동작한다. <br>
<br><br>

##### **HTTPS란?**<br>
- 하이퍼텍스트 전송 프로토콜 보안(HTTPS)은 <br> 
웹 브라우저와 웹 사이트 간에 데이터를 전송하는 데 <br> 
사용되는 기본 프로토콜인 HTTP의 보안 버전 <br>
- HTTPS는 암호화 프로토콜을 사용하여 통신을 암호화 <br>
- 이 프로토콜은 이전에는 보안 소켓 계층(SSL)으로 알려졌지만, <br>
전송 계층 보안(TLS)이라고 불림 <br>
이 프로토콜은 비대칭 공개 키 인프라로 알려진 것을 사용하여 통신을 보호 <br>
이 유형의 보안 시스템에서는 <br>
두 개의 서로 다른 키를 사용하여 두 당사자 간의 통신을 암호화
  - <b>```개인 키```</b> - 웹 사이트 소유자가 관리하며, 비공개로 유지 <br>
  이 키는 웹 서버에 있으며 공개 키로 암호화된 정보를 해독하는 데 사용 <br>
  - <b>```공개 키```</b> - 안전한 방식으로 서버와 상호 작용하고자 하는 <br>
모든 사람이 사용할 수 있음 <br>
공개 키로 암호화된 정보는 개인 키로만 해독할 수 있음 <br>
<br><br>

---
<br>

##### **요약** <br>

<i>HTTPS의 정의를 살펴보면 HTTP와 HTTPS의 차이점을 알 수 있다. <br> 
요약하자면, 서버와 웹 브라우저간의 공유 데이터를 암호화하여 <br> 
HTTP의 보안을 강화하기 위해 생긴 프로토콜이 HTTPS임을 알 수 있다.</i>

<br>
---
<br>


<b>```참고사이트```</b> 
- [https://www.cloudflare.com/ko-kr/learning/ssl/what-is-https/](https://www.cloudflare.com/ko-kr/learning/ssl/what-is-https/)
- [https://surprisecomputer.tistory.com/54](https://surprisecomputer.tistory.com/54)