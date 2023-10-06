---
title: "[Design-Pattern] 팩토리 패턴"
excerpt: "Java; 팩토리 패턴; Design-Pattern"
categories: 
- Java
tags:
- [Java, 팩토리 패턴, Design-Pattern]
published: true
toc: true
toc_sticky: true
---

<br><br>

### ✅ 정의
객체 생성을 추상화하고 객체를 생성하기 위한 인터페이스를 제공하는 디자인 패턴 <br>
팩토리 패턴은 객체 생성과 관련된 많은 복잡성을 다루고, 코드의 재사용성과 유지보수성을 향상시키는 데 도움이 됨

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### ✅ 예제

```java
// Product 인터페이스
interface Product {
    void display();
}

// ConcreteProduct1 클래스
class ConcreteProduct1 implements Product {
    @Override
    public void display() {
        System.out.println("ConcreteProduct1");
    }
}

// ConcreteProduct2 클래스
class ConcreteProduct2 implements Product {
    @Override
    public void display() {
        System.out.println("ConcreteProduct2");
    }
}

// Creator 클래스 (추상 팩토리)
abstract class Creator {
    // 팩토리 메서드
    public abstract Product factoryMethod();
}

// ConcreteCreator1 클래스
class ConcreteCreator1 extends Creator {
    @Override
    public Product factoryMethod() {
        return new ConcreteProduct1();
    }
}

// ConcreteCreator2 클래스
class ConcreteCreator2 extends Creator {
    @Override
    public Product factoryMethod() {
        return new ConcreteProduct2();
    }
}

public class FactoryPatternExample {
    public static void main(String[] args) {
        // 팩토리 생성
        Creator creator1 = new ConcreteCreator1();
        Creator creator2 = new ConcreteCreator2();

        // 객체 생성 및 사용
        Product product1 = creator1.factoryMethod();
        product1.display();

        Product product2 = creator2.factoryMethod();
        product2.display();
    }
}
```

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### ✅ 위 코드의 특징
- 객체 생성 로직의 캡슐화
객체를 생성하는 복잡한 로직을 캡슐화하고, 클라이언트 코드에서 해당 로직의 세부 사항을 모르도록 만들고자 할 때 팩토리 패턴을 사용
- 객체 생성의 유연성과 확장성
새로운 객체 타입을 추가하거나 기존 객체 타입을 변경하기 쉽도록 함 <br>
즉, 클라이언트 코드를 수정하지 않고도 새로운 객체를 사용할 수 있음
- 객체의 인스턴스화 위치의 중앙 집중화
객체의 생성을 중앙 집중화하고 추적하기 쉽도록 함
- 의존성 주입 (Dependency Injection)
DI는 객체가 필요로 하는 의존성을 외부에서 주입받는 방식으로 객체 간의 결합도를 낮추고 테스트 가능한 코드를 작성
- 싱글톤 객체 생성
팩토리 메서드가 항상 같은 인스턴스를 반환하도록 구현할 수 있으며, 이를 통해 애플리케이션에서 유일한 인스턴스를 유지할 수 있음
- 복잡한 객체 구성
객체가 여러 하위 객체를 조합하여 생성되어야 하는 복잡한 경우, 팩토리 패턴을 사용하여 이러한 객체 구성을 추상화하고 단순화
