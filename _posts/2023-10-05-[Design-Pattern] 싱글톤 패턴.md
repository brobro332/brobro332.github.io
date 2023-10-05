---
title: "[Design-Pattern] 싱글톤 패턴"
excerpt: "Java; 싱글톤 패턴; Design-Pattern"
categories: 
- Java
tags:
- [Java, 싱글톤 패턴, Design-Pattern]
published: true
toc: true
toc_sticky: true
---

<br><br>

### ✅ 정의
디자인 패턴 중 하나로, 특정 클래스의 인스턴스가 어플리케이션에서 단 하나만 생성되고 이에 대한 전역적인 접근 지점을 제공

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### ✅ 예제

```java
public class Singleton {
    // 1. private static 변수로 유일한 인스턴스를 갖도록 합니다.
    private static Singleton instance;

    // 2. private 생성자를 선언하여 외부에서 인스턴스를 직접 생성하지 못하게 합니다.
    private Singleton() {
    }

    // 3. 인스턴스를 제공하는 정적 메서드를 만듭니다.
    public static Singleton getInstance() {
        // 이미 인스턴스가 존재하면 그 인스턴스를 반환하고,
        // 없으면 새로운 인스턴스를 생성합니다.
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }

    // 나머지 메서드 및 속성은 이 싱글톤 클래스의 기능을 정의합니다.
    // ...
}
```

<br>
<hr style="border: 2px dashed #d3d3d3;">
<br>

### ✅ 위 코드의 특징
- instance 변수는 private 및 static으로 선언되어 클래스 레벨에서 하나의 유일한 인스턴스를 가리킴
- 생성자 Singleton()은 private으로 선언되어 외부에서 인스턴스를 직접 생성하지 못하게 함
- getInstance() 메서드는 인스턴스를 반환하며, 처음 호출될 때에만 인스턴스를 생성함
- 리소스를 효율적으로 관리하고, 객체의 생성 및 소멸을 제어할 수 있음

