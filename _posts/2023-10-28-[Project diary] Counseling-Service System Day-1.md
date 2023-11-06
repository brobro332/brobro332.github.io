---
title: "[Project diary] Counseling-Service System DAY-1"
excerpt: "etc.; [Project diary] Counseling-Service System DAY-1"
categories: 
- etc
tags:
- [etc]
published: true
toc: true
toc_sticky: true
---

## ✅ 목표: 왜 프로젝트를 진행하는가?

- JWT를 이용한 사용자 인증 구현: 그간 세션을 통한 사용자 인증만 구현해서 RESTful의 stateless를 지킬 수 없었음
- 리액트 경험
- PostgreSQL 경험: 스프링 부트 덕분에 개발 과정에는 크게 차이점 없을 것으로 예상
- 예약 시스템 구현
- 결제 시스템 구현
- jar 배포 및 파이프라인을 통한 CI/CD 구현: CI/CD의 경우 진행하지 못할 수도 있음
- Docker를 통한 연구: 다른 컴퓨터에서 실행 및 코드 수정 등이 가능한가? 

## ✅ DAY-1

- 프로젝트 생성
- H2 DB를 통한 개발
  - H2는 스프링 부트에서 지원하는 인메모리의 경량 데이터베이스
  - 설치가 필요 없음, 인메모리이므로 애플리케이션 종료 시 데이터 휘발
  - 실제 프로덕션 환경에서 사용하기에는 적합하지 않음: PostgreSQL로 전환 예정
  - H2 사이트에서 무설치형 zip 파일 다운받고 프로젝트 내 application.yml에 관련 설정 작성하면 끝

```yml
server:
  port: 80 # 내장 톰캣 포트번호

spring:
  # H2 Database 설정
  datasource:
    driver-class-name: org.h2.Driver
    url: 'jdbc:h2:mem:test;NON_KEYWORDS=USER'    # H2 DB 연결 주소 (In-Memory Mode) NON_KEYWORDS=USER: H2 키워드 'USER' 해제
    # url: 'jdbc:h2:~/test'                      # H2 DB 연결 주소 (Embedded Mode)
    username: '아이디'                           # H2 DB 접속 ID
    password: '패스워드'                         # H2 DB 접속 PW

  # H2 Console 설정
  h2:
    console: # H2 DB를 웹에서 관리할 수 있는 기능
      enabled: true           # H2 Console 사용 여부
      path: /h2-console       # H2 Console 접속 주소: localhost/h2-console로 접속

  # JPA 설정
  jpa:
    database-platform: org.hibernate.dialect.H2Dialect
    hibernate:
      ddl-auto: create        # DB 초기화 전략 (none, create, create-drop, update, validate)
    properties:
      hibernate:
        dialect: org.hibernate.dialect.H2Dialect
        format_sql: true      # 쿼리 로그 포맷
        show_sql: true        # 쿼리 로그 출력
```

```java
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Entity
@Table(name = "tbl_user")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    @Column(nullable = false)
    private String username;

    @Column(nullable = false)
    private String password;
}
```

## ✅ 애로사항 및 계획

큰 문제는 없었으나 H2의 키워드로 "USER"가 등록되어있어서 <br>
yml에 따로 설정을 하지 않으면 테이블명으로 사용할 경우 에러가 남 <br>
언제가 될진 모르겠지만 다음에는 JWT를 통한 사용자 인증을 구현할 예정
- JWT
  - 프론트엔드
    - 로그인 폼
    - 토큰을 받아 로컬 스토리지에 저장
    - 토큰을 통해 API 요청
  - 백엔드
    - 로그인 정보가 일치한다면 서버에서 토큰 전송
    - 토큰 만료 및 갱신 처리