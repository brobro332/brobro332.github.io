---
title: "Software Development Life Cycle, SDLC"
excerpt: "Computer-Science; SDLC"
categories: 
- Computer-Science
tags:
- [Computer-Science, Software-Engineering, 정보처리기사]
published: true
toc: true
toc_sticky: true
---

<br><br>
##### **정의**<br>

소프트웨어 개발 생명주기란, <br>
소프트웨어를 어떻게 개발할 것인가에 대한 추상적인 표현으로, <br>
시스템의 요구분석부터 유지보수까지의 전 공정을 체계화한 절차를 의미한다. <br>
"개발 모델" 또는 "소프트웨어 공학 패러다임"이라고 부르기도 한다.

##### **프로세스**<br>
- 요구사항 분석
  - 요구사항을 고려하여 제품에 부합하는 요구와 조건을 결정하는 단계
  - 기능, 제약조건, 목표 등을 소프트웨어 사용자와 함께 명확히 정의하는 단계
  - 기능 요구사항, 비기능 요구사항

- 설계
  - 시스템 명세 단계에서 정의한 기능을 실제 수행할 수 있도록 <br>
수행 방법을 논리적으로 결정하는 단계
  - 시스템 구조 설계, 프로그램 설계, 사용자 인터페이스 설계

- 구현
  - 특정 프로그래밍 언어를 사용하여 실제 프로그램을 작성하는 단계
  - 프로그래밍 언어 선택, 기법, 스타일, 순서 등을 결정하는 단계
  - 인터페이스 개발, 자료 구조 개발, 오류 처리

- 테스트
  - 시스템이 정해진 요구에 만족하는지, <br>
예상과 실제 결과가 어떤 차이를 보이는지 검사하고 평가하는 단계
  - 단위 테스트
    - 사용자 요구사항에 대한 단위 모듈, 서브루틴 등을 테스트하는 단계
    - 기법 - 자료 구조 테스트, 실행 경로 테스트, 오류 처리 테스트, 인터페이스 테스트
  - 통합 테스트
    - 단위 테스트를 통과한 모듈 사이의 인터페이스로써 통합된 컴포넌트 간의 상호작용을 검증하는 단계
    - 기법 - 빅뱅 테스트, 샌드위치 테스트, 상향식 테스트, 하향식 테스트
  - 시스템 테스트
    - 통합된 단위 시스템의 기능이 시스템에서 정상적으로 수행되는지를 검증하는 단계
    기법 - 기능.비기능 요구사항 테스트
  - 인수 테스트
    - 계약상의 요구사항이 만족되었는지 확인하기 위한 테스트 단계
    - 기법 - 계약 인수, 규정 인수, 사용자 인수, 운영상의 인수, 알파.베타 테스트

- 유지보수
  - 시스템이 인수되고 설치된 후 일어나는 모든 활동
  - 예방, 완전, 교정, 적응, 유지보수

##### **종류**<br>
- 주먹구구식 개발 모델 : ```Build-Fix Model```
  - 요구사항 분석, 설계 단계 없이 일단 개발에 들어간 후 만족할 때까지 수정작업 수행
  - 크기가 매우 작은 규모의 소프트웨어 개발
  - 정해진 개발 순서가 없기 때문에 개발 계획이 정확하지 않고, <br>
  프로젝트 진행 사항 파악이 어려우며 개발 문서가 없어 개발 및 유지보수가 어려움
  - 주먹구구식 개발 모델의 단점으로 인해 <br>
  이후로 체계적인 소프트웨어 개발 생명주기 모델이 생겨나게 됨


######  폭포수 모델 : ```Waterfall Model```
  - 소프트웨어 개발 시 각 단계를 확실히 마무리 지은 후 다음 단계로 넘어가는 모델
  - 가장 오래된 모델
  - 선형 순차적 모형, 고전적 생명주기 모형
  - 경험과 성공 사례가 많이 있음
  - 단계별 정의와 산출물이 명확
  - 요구사항 변경이 어려움
    - 타당성 검토
    - 계획
    - 요구사항 분석
    - 설계
    - 구현
    - 테스트
    - 유지보수
######  프로토타이핑 모델 : ```Prototyping Model```
  - 고객이 요구한 주요 기능을 프로토타입으로 구현하여, <br>
고객의 피드백을 반영하여 소프트웨어를 만들어가는 모델
  - 프로토타입은 발주자나 개발자 모두에게 공동 참조 모델을 제공
  - 프로토타입은 구현 단계의 구현 골격
######  나선형 모델 : ```Spiral Model```
  - 시스템 개발 시 위험을 최소화하기 위해 점진적으로 완벽한 시스템으로 개발해 나가는 모델
    - 계획 및 정의
    - 위험 분석
    - 개발
    - 고객 평가
######  반복적 모델 : ```Iteration Model```
  - 구축대상을 나누어 병렬적으로 개발 후 통합하거나, <br>
  반복적으로 개발하여 점증 완성시키는 SDLC 모델
  - 사용자의 요구사항 일부분 혹은 제품 일부분을 반복적으로 개발하여 <br> 
  최종 시스템으로 완성하는 모델
######  브이 모델 : ```V-Model```
  - 브이 모델은 폭포수 모델의 변형으로 테스트 단계를 추가 확장한 모델
  - 폭포수 모델은 산출물 중심이라면, <br>
  브이 모델은 각 개발 단계를 검증하는데 초점을 두고 있음

<br>
---
<br>

<b>```참고사이트```</b>
- [https://ohju.tistory.com/167](https://ohju.tistory.com/167)