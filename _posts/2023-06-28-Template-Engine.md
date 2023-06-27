---
title: "Template-Engine"
excerpt: "Development; Template-Engine"
categories: 
- Development
tags:
- [Development, Template-Engine, Springboot]
published: true
toc: true
toc_sticky: true
---

<br><br>

##### 정의
템플릿 엔진이란, <br>
템플릿 양식과 입력 자료를 합성하여 결과 문서를 출력하는 소프트웨어를 말한다. <br>
그 중 웹 템플릿 엔진은 브라우저에서 출력되는 문서를 위한 소프트웨어이다. <br>
고정적으로 사용될 부분을 템플릿을 미리 작성해 놓고 <br>
동적으로 변경될 데이터 부분만 필요시 결합하여 화면(문서)을 완성한다.<br><br>


##### 분류
- 서버 사이드 템플릿 엔진
  - 서버에서 데이터와 미리 정의된 템플릿으로 HTML을 완성하여 클라이언트에게 전달
  - Freemarker, Thymeleaf, Handlebars(Handlebars.java), JSP 등
- 클라이언트 사이드 템플릿 엔진
  - RESTful 서버를 구성할 경우 필수적
  - 데이터와 템플릿을 합쳐 클라이언트에서 HTML을 완성
  - 서버는 api 콜에대한 응답데이터만 제공하면서 화면 랜더링은 클라이언트가 담당하는 구조가 가능
  - React, Vue 등


##### 클라이언트 사이드 템플릿 엔진의 필요성
- 계속해서 페이지를 이동하면서(서버에 데이터를 요청하면서) 화면이 변경되는 것이 아니라, <br>
단일 화면에 특정 이벤트에 따라 화면이 변경되어야 하는 경우 javascript로 html을 변경해야 한다.
- 조작해야할 코드량이 많아지면 관리가 어려우므로 <br>
직접 javascript로 DOM을 제어하는 대신에 클라이언트 사이드 템플릿 엔진을 이용하면 좋다.

<br>
---
<br>

```참고사이트``` 
- [https://yangbox.tistory.com/47](https://yangbox.tistory.com/47)
