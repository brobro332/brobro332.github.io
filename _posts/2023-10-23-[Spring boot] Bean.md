---
title: "[Spring boot] Bean"
excerpt: "Development; Bean"
categories: 
- Development
tags:
- [Development, Bean, Springboot]
published: true
toc: true
toc_sticky: true
---

<br><br>

**Bean**
##### Bean이란?
- 제어의 역전(IoC)
객체의 생성 및 제어권을 사용자가 아닌 스프링에게 맡기는 것
- 스프링(Spring) 컨테이너가 관리하는 자바 객체를 빈(Bean)이라 함

##### 빈(Bean)을 스프링 컨테이너에 등록하는 방법

###### 컴포넌트 스캔과 자동 의존관계 설정
- 클래스에 @Component, @Controller, @Service, @Repository 어노테이션이 있으면 스프링 빈으로 자동 등록됨
- 생성자에 @Autowired가 있으면 스프링이 연관된 객체를 스프링 컨테이너에 찾아서 넣어줌
객체 의존관계를 외부에서 넣어주는 것을 '의존성 주입(DI)'라 함

```java
@Controller
public class MemberController {

    private final MemberService memberService;

	@Autowired
    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }
}
```

가령 위 코드처럼 MemberController에만 의존관계를 등록한다면 에러가 남 <br>
스프링 컨테이너에 memberService가 Bean으로 등록해줘야 함 <br>
스프링 컨테이너에 Bean을 등록할 때, 스프링 프레임워크 기본으로 싱글톤으로 등록됨

###### 자바 코드로 직접 스프링 빈 등록하기(Configuration)
- @Configuration과 @Bean 애노테이션을 이용해 스프링 빈을 등록
- @Configuration을 이용하면 스프링 프로젝터에서 Configuration 역할을 하는 Class를 지정할 수 있음

<br><br>

- XML로 설정하는 방식도 있지만 최근에는 잘 사용하지 않음
- DI에는 필드 주입, setter 주입, 생성자 주입 이렇게 3가지 방법이 있지만 의존관계가 실행중에 동적으로 변하는 경우는 거의 없으므로 생성자 주입을 권장
- 실무에서는 주로 정형화된 컨트롤러, 서비스, 리포지토리 같은 코드는 컴포넌트 스캔을 사용
- Autowired 를 통한 DI는 new 등으로 내가 직접 생성한 객체에서는 동작하지 않음.

<br>
---
<br>

```참고사이트``` 
- [https://velog.io/@falling_star3/Spring-Boot-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88bean%EA%B3%BC-%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84](https://velog.io/@falling_star3/Spring-Boot-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88bean%EA%B3%BC-%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84)
