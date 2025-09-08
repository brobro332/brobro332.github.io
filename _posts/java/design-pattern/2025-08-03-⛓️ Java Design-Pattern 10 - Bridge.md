---
title: ⛓️ Java Design-Pattern 10 - Bridge
date: 2025-08-03 14:20:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Bridge` 패턴이란?
- 기능의 클래스 계층과 구현의 클래스 계층 사이에서 다리 역할을 하는 패턴

#### 기능의 클래스 계층
- 어떤 클래스 `Something`이 있다고 할 때 새로운 기능을 추가하고 싶을 때는 하위 클래스 `SomethingGood`를 만든다. 또 새로운 기능을 추가한다고 할 때는 `SomethingGoot` 하위 클래스로 `SomethingBetter` 클래스를 만들게 될 것이다.
- 이렇게 새로운 기능을 추가하고 싶을 때 클래스 계층에서 목적과 가까운 클래스를 찾아 새로운 기능을 추가한 하위 클래스를 만드는데, 이러한 계층을 기능의 클래스 계층이라고 한다.
- 일반적으로 클래스 계층을 너무 깊게 하지 않는 편이 좋다.

#### 구현의 클래스 계층
- 상위 클래스를 추상 메소드로 인터페이스 `API`를 규정한다.
- 하위 클래스를 구현 메소드로 그 인터페이스를 구현한다.
- 이러한 클래스 계층을 구현의 클래스 계층이라고 한다.

#### 클래스 계층의 혼재와 분리
- 이처럼 우리가 하위 클래스를 만들 때는 기능을 추가하려고 하는지, 구현하려고 하는지 확인해야 한다.
- 두 클래스 계층이 혼재되어 있으면 계층을 복잡하게 만들어 예측을 어렵게 할 수 있다.
- 그래서 두 계층을 독립된 클래스 계층으로 나눈다. 
- 다만 분리만 하면 그냥 흩어지기 때문에 두 클래스 계층 사이에 다리 하나를 두어야 한다. 
- 가장 처음 언급했듯 `Bridge` 패턴은 두 계층 사이에 다리를 놓아주는 역할을 한다.

### 예제 프로그램
- 다음 프로그램은 무언가를 표시하기 위한 프로그램이다.

![](/assets/image/Pasted%20image%2020250803142627.png)

#### `Display` 클래스

```java
public class Display {
	private DisplayImpl impl;
    
    public Display(DisplayImpl impl) {
    	this.impl = impl;
    }
    
    public void open() {
    	impl.rawOpen();
    }
    
    public void print() {
    	impl.rawPrint();
    }
    
    public void close() {
    	impl.rawClose();
    }
    
    public final void display() {
    	open();
        print();
        close();
    }
}
```
- `impl` 필드는 `Display` 클래스의 구현을 나타내는 필드이다. 
- 이 필드가 두 클래스 계층의 다리가 된다.
- `open`, `print`, `close` 세 메소드는 `Display` 클래스에서 제공하는 인터페이스이다. 
- `open`은 표시의 전처리이다. 
- `print`는 표시 그 자체이다. 
- `close`는 표시의 후처리이다.
- `display` 메소드는 `open`, `print`, `close`라는 `Display`의 인터페이스를 이용해 '표시한다'는 처리를 수행한다.

#### `CountDisplay` 클래스

```java
public class CountDisplay extends Display {
	public CountDisplay(DisplayImpl impl) {
    	super(impl);
    }
    
    public void multiDisplay(int times) {
    	open();
        for (int i = 0; i < times; i++) {
        	print();
        }
        close();
    }
}
```
- `Display` 기능에 지정 횟수만큼 표시하는 새로운 기능을 추가하여 `CountDisplay` 하위 클래스를 만들었다.

#### `DisplayImpl` 클래스

```java
public abstract class DisplayImpl {
	public abstract void rawOpen();
    public abstract void rawPrint();
    public abstract void rawClose();
}
```
- 구현의 클래스 계층 최상위에 위치하는 추상 클래스이다.
- 하위 클래스들이 이 추상 메소드들을 구현하게 된다.

#### `StringDisplayImpl` 클래스

```java
public class StringDisplayImpl extends DisplayImpl {
	private String string;
    private int width;
    
    public StringDisplayImpl(String string) {
    	this.string = string;
        this.width = width;
    }
    
    @Override
    public void rawOpen() {
    	printLine();
    }
    
    @Override
    public void rawPrint() {
    	System.out.println("|" + string + "|");
    }
    
    @Override
    public void rawClose() {
    	printLine();
    }
    
    private void printLine() {
    	System.out.print("+");
        for (int i = 0; i < width; i++) {
        	System.out.print("-");
        }
        System.out.print("+");
    }
}
```
- `DisplayImpl` 클래스를 구현한 클래스이다.

#### `Main` 클래스

```java
public class Main {
	public static void main(String[] args) {
    	Display d1 = new Display(new StringDisplayImpl("Hello, Korea."));
        CountDisplay d2 = new Display(new StringDisplayImpl("Hello, World."));
        CountDisplay d3 = new CountDisplay(new StringDisplayImpl("Hello, Universe."));
        
        d1.display();
        d2.display();
        d3.display();
        
        d3.multiDisplay(5);
    }
}
```

### `Bridge` 패턴의 등장인물
#### `Display` 클래스
- 기능의 클래스 계층의 최상위 클래스이다. 
- 추상화 `Abstraction` 추상화 역할을 맡아 구현자 역할의 메소드를 사용하는 기본 기능만 기술된 클래스이다.

#### `CountDisplay` 클래스
- 개선된 추상화 `RefinedAbstraction` 역할을 맡았으며 추상화 역할에 기능을 추가했다.

#### `DisplayImpl` 클래스
- 구현의 클래스 계층의 최상위 클래스이다. 
- 구현자 `Implementor` 역할을 맡아 추상화 역할의 인터페이스 `API`를 구현하기 위한 메소드를 규정한다.

#### `StringDisplayImpl` 클래스
- 구체적인 구현자 `ConcreteImplementor` 역할을 맡아 구현자 역할의 인터페이스를 구체적으로 구현한다.


### 책에서 제시하는 힌트
#### 분리해두면 확장이 편리해진다.
- `Bridge` 패턴의 특징은 각 클래스 계층을 분리하는 것이다. 
- 계층을 분리해 두면 추후 각각의 클래스 계층을 독립적으로 확장할 수 있다.
- 기능을 추가하려면 기능의 클래스 계층에 클래스를 추가한다. 
- 새롭게 추가한 기능은 모든 구현 계층에서 이용할 수 있게 된다.

#### 상속은 강한 결합이고 위임은 약한 결합이다.
- 상속은 클래스를 확장하는 편리한 방법이지만, 클래스 간의 연결을 강하게 고정시킨다. 
- 이 관계는 소스 코드를 수정하지 않는 한 바꿀 수 없다.
- 예제 프로그램에서는 다음과 같이 `Display` 클래스 안에서 위임을 사용한다.

_"open을 실행할 때에는 impl.rawOpen()을 호출한다."  
"print을 실행할 때에는 impl.rawPrint()을 호출한다."  
"close을 실행할 때에는 impl.rawClose()을 호출한다."_

- `Display`의 메소드를 호출하였더니 `impl`에게 모두 떠넘긴다. 
- 이것이 위임이다.
- `Main` 클래스에서 `Display`의 인스턴스를 만들 때 생성자에 구체적인 구현자 역할의 인스턴스를 인수로 넘겨 의존성 주입을 하면 된다. 
- 전환할 때 수정한 것은 `Main` 클래스 뿐이고, `Display`나 `DisplayImpl` 등은 수정할 필요가 없다.