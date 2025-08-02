---
title: ⚡ Java의 특징과 SOLID 원칙
date: 2025-07-31 15:57:00 +0900
categories:
  - Computer-Science
tags:
  - Computer-Science
  - Java
  - SOLID
---

### 🔹 `Java`의 특징
#### ▫️ `OOP` 언어
- 모든 것이 객체로 구성되어 있어 코드 재사용과 유지보수가 쉽다.
- 현실 세계를 모델링하기 편하고, 확장성이 뛰어나다.

####  ▫️ 플랫폼 독립적
- 자바 바이트 코드(`.class` 파일)를 `JVM`에서 실행
- `JVM`(`Java Virtual Machine`)이 중간에서 바이트 코드를 실행해 주기 때문에 `OS`에 상관없이 실행 가능

#### ▫️ 자동 메모리 관리(`Garbage Collection`)
- 메모리 관리를 `JVM`이 자동으로 처리해 개발자는 메모리 해제에 신경 쓰지 않아도 된다.
- 덕분에 메모리 누수 같은 오류 가능성이 줄어든다.

#### ▫️ 멀티스레드 지원
- 여러 작업을 동시에 처리할 수 있어 성능 향상과 사용자 경험 개선에 유리하다.

#### ▫️ 풍부한 표준 라이브러리 제공
- 네트워크, 입출력, 데이터 구조, `GUI` 등 다양한 기능을 바로 쓸 수 있는 라이브러리를 갖추고 있다.

#### ▫️ 강한 타입 검사
- 컴파일 시점에 데이터 타입 오류를 잡아내 안정적인 프로그램 작성에 도움을 준다.

#### ▫️ 보안성
- `JVM`이 직접 하드웨어 접근을 제한해 안전하게 실행되며, 보안에 강점이 있다.

#### ▫️ 동적 바인딩

```java
class Animal {
    void sound() {
        System.out.println("동물이 소리를 낸다");
    }
}

class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("멍멍");
    }
}

class Cat extends Animal {
    @Override
    void sound() {
        System.out.println("야옹");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal a;
        
        a = new Dog();
        a.sound();     // 멍멍 출력
        
        a = new Cat();
        a.sound();     // 야옹 출력
    }
}
```
- 실행 시점에 메서드가 결정되어 유연한 코드 작성이 가능하고, 다형성을 지원한다.


### 🔹 `SOLID` 원칙
#### ▫️ `SRP` 단일 책임 원칙

```java
// 나쁜 예: 한 클래스가 여러 책임을 가짐
class UserManager {
    void addUser() { /* 사용자 추가 */ }
    void generateReport() { /* 보고서 생성 */ }
}

// 좋은 예: 책임 분리
class UserManager {
    void addUser() { /* 사용자 추가 */ }
}

class ReportGenerator {
    void generateReport() { /* 보고서 생성 */ }
}
```
- 클래스는 하나의 책임만 가져야 한다.
- 변경 사유도 하나 뿐이어야 함.

#### ▫️ `OCP` 개방-폐쇄 원칙

```java
interface Shape {
	double area();
}

class Rectangle implements Shape {
	double width, height;
	public double area() {
		return width * height;
	}
}

class Circle implements Shape {
	double radius;
	public double area() {
		return Math.PI * radius * radius;
	}
}

class AreaCalculator {
	double totalArea(Shape[] shapes) {
		double area = 0;
		for(Shape s : shapes) {
			area += s.area();
		}
		return area;
	}
}
```
- 소프트웨어 요소는 확장에는 열려 있어야 하고, 수정에는 닫혀 있어야 한다.
- 기능을 바꾸려면 기존 코드를 고치지 말고, 확장해서 기능 추가.

#### ▫️ `LSP` 리스코프 치환 원칙
```java
class Bird {
	void fly() { System.out.println("날다"); }
}

class Sparrow extends Bird { }

class Ostrich extends Bird {
	@Override
	void fly() {
		throw new UnsupportedOperationException("타조는 날 수 없다");
	}
}
```
- 자식 클래스는 언제나 부모 클래스의 역할을 대체할 수 있어야 한다.
- 부모 타입의 객체가 사용되는 곳에 자식 객체를 넣어도 문제 없어야 함.
- `Ostrich`가 `Bird`를 대체할 수 없으므로 위 예제는 `LSP` 위반

#### ▫️ `ISP` 인터페이스 분리 원칙
```java
interface Workable {
	void work();
}

interface Feedable {
	void eat();
}

class Human implements Workable, Feedable {
	public void work() { System.out.println("일함"); }
	public void eat() { System.out.println("식사함"); }
}

class Robot implements Workable {
	public void work() { System.out.println("작업함"); }
	// Robot은 eat() 불필요, 인터페이스 분리로 불필요한 구현 회피
}
```
- 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.
- 필요한 기능만 가진 작은 인터페이스를 여러 개 만드는 게 좋다.
- `Robot`은 `eat()` 메서드가 필요하지 않으므로 인터페이스 분리로 불필요한 구현 회피

#### ▫️ `DIP` 의존 역전 원칙
```java
interface MessageSender {
	void send(String message);
}

class EmailSender implements MessageSender {
	public void send(String message) {
		System.out.println("이메일 전송: " + message);
	}
}

class Notification {
	private MessageSender sender;
	
	public Notification(MessageSender sender) {
		this.sender = sender;
	}
	
	public void notifyUser() {
		sender.send("새 알림이 도착했습니다.");
	}
}
```
- 고수준 모듈은 저수준 모듈에 의존하면 안 된다.
- 둘 다 인터페이스 추상화에 의존해야 한다.
- 구체적인 것보다 추상적인 것에 의존해야 변경에 유리하다.
- 위 예제에서 `EmailSender` 인스턴스는 외부에서 `Notification` 생성자에 직접 전달되어야 한다.
