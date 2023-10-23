---
title: "[Design-Pattern] 전략 패턴"
excerpt: "Java; 전략 패턴; Design-Pattern"
categories: 
- Java
tags:
- [Java, 전략 패턴, Design-Pattern]
published: true
toc: true
toc_sticky: true
---

<br><br>

### ✅ 정의
알고리즘군을 정의하고 각각을 캡슐화하여 교체 가능하게 만드는 디자인 패턴 <br>
실행 중에 알고리즘을 선택할 수 있음

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### ✅ 예제

```java
interface PaymentStrategy {
    void pay(int amount);
}

class CreditCardPayment implements PaymentStrategy {
    private String cardNumber;

    public CreditCardPayment(String cardNumber) {
        this.cardNumber = cardNumber;
    }

    public void pay(int amount) {
        System.out.println(amount + " paid with credit card " + cardNumber);
    }
}

class PayPalPayment implements PaymentStrategy {
    private String email;

    public PayPalPayment(String email) {
        this.email = email;
    }

    public void pay(int amount) {
        System.out.println(amount + " paid with PayPal to " + email);
    }
}
```

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### ✅ 위 코드의 특징
- 정의와 구현의 분리 (Separation of Concerns)
 알고리즘을 클라이언트로부터 분리하여, 각 알고리즘을 독립적으로 변경할 수 있도록 함 <br>
이로써 클라이언트는 알고리즘의 세부 내용에 대해 신경 쓸 필요가 없어짐
- 교체 가능한 알고리즘
전략 패턴은 알고리즘군을 정의하고 각각을 별도의 전략 클래스로 캡슐화 <br>
이로써 알고리즘을 동적으로 변경할 수 있어, <br>
결제 처리, 정렬 알고리즘, 데이터 유효성 검사, 로깅 등 서로 다른 알고리즘을 선택할 수 있음

- Open/Closed Principle 준수
개방/폐쇄 원칙(Open/Closed Principle)을 준수 <br>
새로운 전략(알고리즘)을 추가하거나 수정할 때, 기존의 코드를 수정하지 않고 확장 가능
- 클라이언트 독립성
클라이언트 코드는 구체적인 알고리즘 클래스와 느슨하게 결합
