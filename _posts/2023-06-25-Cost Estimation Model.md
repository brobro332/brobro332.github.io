---
title: "Cost Estimation Model"
excerpt: "Computer-Science; Cost Estimation Model"
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

- 비용산정 모형이란, <br>
소프트웨어의 개발 규모를 소요되는 인원, 자원, 기간 등으로 확인하여 <br>
실행 가능한 계획을 수립하기 위해 <br>
필요한 비용을 예측하는 과학적이고 합리적인 활동 
- 비용산정을 통해 발주자는 소프트웨어의 합리적인 가격을 확인할 수 있고 <br>
개발자는 개발에 필요한 정당한 비용 요구 가능


##### <br> **분류**


- 하향식 산정법
  - 경험이 많은 전문가에게 의뢰하거나 여러 전문가, 조정자를 통해 산정
    - 전문가 판단
    - 델파이 기법 : 전문가의 경험적 지식을 통해 문제 해결 및 미래 예측


- 상향식 산정법
  - 세부적인 요구사항과 기능에 따라 비용을 계산하는 방식


###### LOC : ```Lines of Code```
  - 각 기능의 원시 코드 라인 수의 낙관치, 중간치, 비관치를 측정해 <br>
  예측치를 구하고 이를 통해 비용을 산정하는 방식
  - 측정이 쉽고 이해하기 쉬움
  - 예측치를 통해 생산성, 노력, 개발 기간 등 비용을 산정

###### Man Month
  - 한 사람이 1개월 동안 할 수 있는 일의 양을 기준으로 프로젝트 비용을 산정
  - (프로젝트 기간) = (Man Month)/(프로젝트 인력)
  - (man Month) = (LoC)/(프로그래머의 월간 생산성)


###### COCOMO : ```COnstructive COst MOdel```
  - 보헴이 제안한 규모에 따라 비용을 산정하는 방식
  - 비용산정 결과는 Man-Month로 산정
  - COCOMO 소프트웨어 개발 유형
    - 조직형; Organic Mode : 5만 라인 이하의 소프트웨어를 개발하는 유형
    - 반 분리형; Semi-Detached mode : 30만 라인 이하의 소프트웨어를 개발하는 유형
    - 임베디드형; Embedded Mode : 30만 라인 이상의 소프트웨어를 개발하는 유형


###### Putnam
 - 소프트웨어 개발주기의 단계별로 요구할 인력의 분포를 가정하는 방식
 - 생명주기 예측 모형이라고 함
 - Rayleigh - Norden 곡선의 노력 분포도를 기초로 함


###### FP : ```Function Point```
  - 요구 기능을 증가시키는 인자별로 가중치를 부여하고 <br>
  요인별 가중치를 합산하여 총 기능의 점수를 계산하여 비용을 산정하는 방식
  - 기능점수(FP) = 총 기능점수 * [0.65 + (0.1 + 총 영향도)]

<br>
---
<br>

```참고사이트```
- [https://ohju.tistory.com/223](https://ohju.tistory.com/223)