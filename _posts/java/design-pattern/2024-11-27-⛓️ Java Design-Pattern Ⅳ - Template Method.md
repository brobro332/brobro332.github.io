---
title: ⛓️ Java Design-Pattern Ⅳ - Template Method
date: 2024-11-27 03:47:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Template Method` 패턴이란?
- 상위 클래스에 뼈대로써 템플릿이 될 추상 메소드를 정의하고, 하위 메소드에서 그것을 구체적으로 구현하는 패턴이다.
- 어느 하위 클래스에서 구현을 달리 하더라도 상위 클래스의 템플릿에 따라 처리의 큰 흐름은 정해져 있다.
- 실제 템플릿에 비유하면, 템플릿 구멍을 통해 어떤 형태의 문자인지 알 수 있지만 어떤 도구를 사용하느냐에 따라 색깔, 또는 구체적인 모양이 달라진다.


### 예제 프로그램
![](/assets/image/Pasted%20image%2020250529000902.png)
- 문자열과 문자열 리스트를 출력하는 간단한 프로그램이다.

```java
public abstract class AbstractDisplay {
	public abstract void open();
	public abstract void print();
	public abstract void close();
	
	public final void display() {
		open();
		print();
		close();
	}
}
```
- `AstractDisplay` 클래스는 `display()` 메서드만 구현되어 있으며 나머지 세 개의 메서드는 추상 메서드로 정의되어 있다.
- 추상 메서드가 위에서 설명한 템플릿이다.
- 세 개의 추상 메서드는 하위 클래스를 확인하지 않는 한 어떤 동작을 하는지 알 수 없다.

```java
public class OneLineDisplay extends AbstractDisplay {
	private String str;
	
	public OneLineDisplay(String str) {
		this.str = str;
	}
	
	@Override
	public void open() {
		System.out.println("<<");
	}
	
	@Override
	public void print() {
		System.out.println(str);
	}
	
	@Override
	public void close() {
		System.out.println(">>");
	}
}
```
- `OneLineDisplay` 클래스는 추상 클래스를 상속 받아 한 줄의 메시지를 출력한다.

```java
public class MultiLineDisplay extends AbstractDisplay {
	private List<String> lines;
	
	public MultiLineDisplay(List<String> lines) {
		this.lines = lines;
	}
	
	@Override
	public void open() {
		System.out.println("=====================");
	}
	
	@Override
	public void print() {
		for (String str : lines) {
			System.out.println(str);
		}
	}
	
	@Override
	public void close() {
		System.out.println("=====================");
	}
}
```
- `MultiLineDisplay` 클래스는 추상 클래스를 상속 받아 여러 줄의 메시지를 출력한다.

```java
public class Main {
	public static void main(String[] args) {
		AbstractDisplay d1 = new OneLineDisplay("이슈에 코멘트가 달렸습니다.");
		
		List<String> strList = new ArrayList<>(Arrays.asList({"안녕하세요, CO-WORKING입니다.", "회원가입이 완료되었습니다!", "감사합니다."}));
		AbstractDisplay d2 = new MultiLineDisplay(strList);
		
		d1.display();
		d2.display();
	}
}
```
- 추상 클래스 변수에 구현체 인스턴스를 주입하여 메시지를 출력하는 메인 클래스이다.


### 체크 포인트
#### ✅ 로직 공통화
- 상위 클래스의 템플릿 메서드에 알고리즘이 기술되어 있으므로 하위 클래스에서는 알고리즘을 일일이 기술할 필요가 없다.
- 가령 해당 패턴을 사용하지 않고 구현 클래스를 여러 개 만들어 버린다면 버그를 발견할 경우 모든 클래스를 수정해야 한다. 
- 그러면 또 테스트해야 할 범위가 넓어진다. 
- 하지만 해당 패턴을 사용하면, 템플릿 메서드만 수정하면 된다.

#### ✅ 하위 클래스를 상위 클래스와 동일시한다.
- `instanceof` 등으로 하위 클래스의 종류를 특정하지 않아도 프로그램이 동작한다.
- 상위 클래스형 변수에 하위 클래스의 어떤 것을 대입해도 제대로 동작할 수 있게 하는 원칙을 `LSP`라고 한다. 
- `The Liskov Substitution Principle`의 준말이다.

#### ✅ 상위 클래스에서 하위 클래스로 요청
- 클래스 계층에 관해 학습할 때 다음과 같이 하위 클래스 관점에서 생각할 수 있다.
> "상위 클래스에서 정의된 메소드를 하위 클래스에서 이용할 수 있다."
> "하위 클래스에 약간의 메소드를 기술하는 것만으로 새로운 기능을 추가할 수 있다."
> "하위 클래스에서 메소드를 오버라이드하면 동작을 변경할 수 있다."
- 상위 클래스 관점에서는 다음과 같이 생각할 수 있다.
>"하위 클래스에서 그 메소드를 구현하기를 기대한다."
>"하위 클래스에 해당 메소드 구현을 요청한다."
- 하위 클래스에는 상위 클래스의 추상 메소드를 구현할 책임이 있는데, 이를 `Subclass Responsibility`라고 한다.

#### ✅ 추상 클래스의 의의
- 추상 클래스는 인스턴스를 만들 수 없다. 
- `Template Method` 패턴을 이해하면 추상 클래스 단계에서 처리 흐름을 형성하는 것이 중요하다는 점을 알 수 있다.

#### ✅ 상위 클래스와 하위 클래스의 협조
- 상위 클래스의 소스 코드가 없으면 하위 클래스 구현이 어려울 수 있다.
- 상위 클래스에서 선언된 추상 클래스를 하위 클래스에서 구현할 때는 그 메서드가 어떤 시점에 호출되는지 이해해야 한다.
- 상위 클래스에 기술하는 내용이 많아지면 하위 클래스를 작성하기 편해지지만 하위 클래스의 자유도는 떨어진다.
- 반면 상위 클래스에 기술하는 내용이 적어지면 하위 클래스를 작성하기 어려워지고, 각각의 하위 클래스에서 처리 기술이 중복될 수 있다.
- `Template Method` 패턴에서는 처리 내용의 뼈대는 상위 클래스에 기술하고, 구체적인 내용은 하위 클래스에 기술한다.
- 그런데 어떤 처리를 상위 클래스에 두고 어떤 처리를 하위 클래스에 둘 지를 규정한 매뉴얼이 있는 것은 아니고, 프로그램을 설계하는 사람의 몫이다.