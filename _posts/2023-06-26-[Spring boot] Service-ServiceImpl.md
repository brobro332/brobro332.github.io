---
title: "[Spring boot] Service-ServiceImpl"
excerpt: "Development; Service-ServiceImpl"
categories: 
- Development
tags:
- [Development, Service-ServiceImpl, Springboot]
published: true
toc: true
toc_sticky: true
---

<br><br>

##### 서론
JWT를 이용한 사용자 인증을 구현하며 Service layer가 <br>
Service(Interface)와 ServiceImpl(구현체) 구조로 나뉜 포스팅을 참조했다. <br>
개발하다가 구글링을 할 경우 이런 구조를 자주 봤는데, <br>
실제로 내 프로젝트에도 적용을 해보았고, <br>
왜 이런 구조를 사용하는지 알아보는 시간을 가졌다. 
<br><br>


##### Interface를 왜 쓰는걸까?
- 객체지향(OCP) 특징 중 하나인 다형성 때문
- 다형성이란 하나의 자료형에 여러가지 객체를 대입하여 <br> 
다양한 결과를 얻어내는 성질


##### 추상화
  - 인터페이스와 구현체를 분리하는 것을 추상화로 볼 수도 있다.
  - 관습적인 추상화를 통해 얻을 수 있는 장점
    - 구현체 클래스를 변경하거나 확장하더라도, <br>
    이를 호출하는 클라이언트 코드(Controller Class)에는 영향을 주지 않음
    - 예시로 정률 할인(%), 정액 할인(원)으로 Impl 구현체를 따로 만들 수 있음


##### 문제상황
- 실질적으로는 1:N 관계가 아닌 1:1의 관계를 형성하며 인터페이스를 사용중임
- 대부분의 프로젝트의 코드를 보면 Interface 와 구현체 클래스는 1:1로 구성되어, <br>
인터페이스를 갈아끼우는 상황이 많지 않음


##### 구현체가 하나라면 인터페이스가 필요없을까?
- 구현체가 하나로 충분하다면 당장은 필요없다.
- 기획 정책에 유연하게 대응하려면 필요할 수 있다.
- 프로젝트 진행 중 기획 정책은 얼마든지 변경될 수 있음

<br>
---
<br>

```참고사이트``` 
- [https://junior-datalist.tistory.com/243](https://junior-datalist.tistory.com/243)
