---
title: "AWS Deployment Log"
excerpt: "Document to deploy a Project"
categories: 
- Development
tags:
- [Development, AWS, Deployment]
published: true
---

<br><br>

- <b>1회차</b> <br><br>
배포에는 성공하였으나, Tomcat 버전 및 여러 요소에서 문제가 발생했음

  - AWS 
    - EC2 <br>
    - EBS <br>
    - Elastic IP <br>

  - Project ​<br>
    - JDK 17 <br>
    - gradle <br>

    - tomcat10 : 버전으로 인한 문제 발생 <br>
```netstat -nlpt``` 명령어로 네트워크의 연결과 포트 상태를 출력 <br>
```tail -f catalina.out``` 명령어로 Tomcat 에러 로그 확인 <br>
```cat catalina.out``` 명령어로 전체 에러 로그를 확인하는 쪽이 테스팅하는데 더 수월했음 <br>

    - MariaDB : MariaDB Connector lib를 삭제해야함 <br> 
삭제하지 않으면 Memory Leak 문제가 발생함 <br> 
이 문제는 Tomcat 에러 로그로 확인 가능

    - application.yml : DB 정보 수정하기(Case By Case)
    - IntelliJ : Project의 build.gradle를 통해 War 세팅을 해야함<br>
    - bootWar X / war O <br>
    - ROOT.war 파일을 FileZila로 ec2 인스턴스에 넣어주어야 함
    - Context path="/"

<br>
---
<br>

- <b>2회차</b><br>

  - ​```java.lang.ClassNotFoundException: org.apache.jsp.WEB_002dINF.views``` 오류 발생 <br>
```리다이렉션 횟수가 너무 많습니다.``` -> ```JSP 404 Not Found Error``` <br>
    - jsp lib에 문제가 있나 살펴봤으나 문제 없음
    - 톰캣 에러 로그 확인하니 ```ErrorPageFilter```라는 에러 발견 <br> 
    하지만 이건 근본적인 문제가 아니었다.
    - 톰캣 디렉토리 아래의 ```/work``` 디렉토리 안의 <br> 
    ```/Catalina``` 디렉토리를 삭제해주면 정상 작동한다. <br> 
구글링해보니 work 하위 디렉토리는 캐시 관련 디렉토리인 것 같고 <br> 
재배포를 하면서 이 부분이 꼬인 것 같다. <br><br>
[https://debugblog.tistory.com/9](https://debugblog.tistory.com/9) <br>
감사하게도 관련 글이 있어서 문제를 해결할 수 있었다.

<br>
---
<br>

- <b>3회차</b> <br><br>
한 번에 성공했다. 이 이후로는 15분 내로 최신본으로 프로젝트 재배포 가능했음

  - ```IntelliJ```에서 War 파일 빌드한다.
  - War 파일을 압축 풀고서 MariaDB Connector lib 삭제 / yml 수정(생략 가능)
  - cmd 콘솔창에서 ROOT 폴더로 이동하고, ```jar cvf ROOT.war *``` 명령어 입력
  - ```MobaXterm``` 들어가서 mysql 들어가서 데이터베이스 드랍 후 생성(DB 구조가 바뀐 경우에만 수행)
  - ```shutdown.sh``` 실행 후 ROOT / ROOT.war 삭제
  - ```/work``` 폴더의 ```/Catalina``` 폴더 삭제
  - ```FileZilla```를 통해 /usr/local/tomcat10/webapps/ 경로로 ROOT.war 전송
  - ```startup.sh``` 실행 : 네트워크/포트 상태 및 톰캣 에러 로그 확인하기

​<br>
---
<br>

- <b>4회차</b> <br><br>
SSL 인증서 등록하는 것이 좀 오래 걸렸다.

  - ```가비아```에서 도메인 구입 <br>
접속할 때 URL에 포트번호를 붙여야된다는 점을 늦게 깨달아서 <br>
처음에는 DNS 등록에 문제가 있는 줄 알았다.
  - AWS에서 <br>
ACM / ROUTE 53 / 로드밸런서 / 타겟그룹을 건드려서 <br> 
인증서 발급 및 https 등록을 해주었다. <br>
  - 타겟그룹에 문제가 발견돼서 HTTP 80포트를 이용하기 위해 아파치를 다운 받았다.
  - ```iptables -A PREROUTING -t nat -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 8080``` <br> 
명령어를  ```MobaXterm```에 입력하여 80번 포트로 접속하면 <br> 
8080번 포트인 ```Tomcat```으로 리다이렉트 되도록 하였다.
  - 또한 스프링 시큐리티에서 "/"를 차단해놓으면 https 등록하는데도 문제가 있는 것 같다.
<br><br>  

<img src="/assets/images/2023-06-21-AWS Deployment Log/1.JPG">
<b>✔ 로드밸런서 이미지</b>
<br><br>  
<img src="/assets/images/2023-06-21-AWS Deployment Log/2.JPG">
<b>✔ 로드밸런서 규칙 설정 이미지</b>
<br><br><br>
<img src="/assets/images/2023-06-21-AWS Deployment Log/3.JPG">
<b>✔ 타겟그룹 이미지</b>