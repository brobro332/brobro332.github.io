---
title: "[Design-Pattern] 옵저버 패턴"
excerpt: "Java; 옵저버 패턴; Design-Pattern"
categories: 
- Java
tags:
- [Java, 옵저버 패턴, Design-Pattern]
published: true
toc: true
toc_sticky: true
---

<br><br>

### ✅ 정의
객체 사이에 일대다(1:N) 의존성을 정의하는 디자인 패턴 <br>
어떤 객체의 상태가 변경될 때, 그 객체에 의존하는 다른 객체들에게 자동으로 알림을 보내는 메커니즘을 제공 <br>
객체 간의 느슨한 결합(loose coupling)을 유지하면서 상호 작용

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### ✅ 예제

```java
import java.util.ArrayList;
import java.util.List;

// 주제(Subject) 인터페이스
interface Subject {
    void registerObserver(Observer observer);
    void removeObserver(Observer observer);
    void notifyObservers();
}

// 구체적인 주제 클래스
class ConcreteSubject implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private int state;

    @Override
    public void registerObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(state);
        }
    }

    // 주제의 상태 변경 메서드
    public void setState(int newState) {
        state = newState;
        notifyObservers(); // 상태가 변경되면 옵저버들에게 알림을 보냄
    }
}

// 옵저버 인터페이스
interface Observer {
    void update(int state);
}

// 구체적인 옵저버 클래스
class ConcreteObserver implements Observer {
    private int observerState;

    @Override
    public void update(int state) {
        observerState = state;
        System.out.println("옵저버 상태 업데이트: " + observerState);
    }
}

public class ObserverPatternExample {
    public static void main(String[] args) {
        ConcreteSubject subject = new ConcreteSubject();

        ConcreteObserver observer1 = new ConcreteObserver();
        ConcreteObserver observer2 = new ConcreteObserver();

        subject.registerObserver(observer1);
        subject.registerObserver(observer2);

        // 주제의 상태 변경
        subject.setState(10);
        subject.setState(20);
    }
}
```

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### ✅ 위 코드의 특징
- 느슨한 결합 (Loose Coupling)
주제(Subject)와 옵저버(Observer) 간의 관계를 느슨하게 결합 <br>
객체는 옵저버 객체의 존재를 알지만, 옵저버 객체는 주제 객체의 존재와 동작 방식을 몰라도 됨
- 이벤트 기반 (Event-Driven)
이벤트 기반 프로그래밍에 적합 <br>
주제 객체의 상태가 변경될 때 옵저버 객체들에게 이벤트(알림)를 발생시켜 처리하도록 함
- 구조적 분리
옵저버를 구조적으로 분리하여 각각의 역할을 명확하게 정의 <br>
이로써 각 객체는 단일 책임 원칙(Single Responsibility Principle)을 준수하며, 코드의 가독성과 유지보수성이 향상
- 확장성
새로운 옵저버를 추가하기 쉬움
- Java에서 내장 지원
Java 언어와 라이브러리는 옵저버 패턴을 구현하기 위한 클래스와 인터페이스를 제공 <br>
java.util.Observer와 java.util.Observable 클래스를 사용하여 옵저버 패턴을 쉽게 구현
