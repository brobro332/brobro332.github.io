---
title: ⛓️ Java Design-Pattern Ⅶ - Prototype
date: 2025-08-03 01:33:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Prototype` 패턴이란?
- 인스턴스를 생성할 때는 일반적으로 `new` 키워드와 함께 클래스 명을 반드시 지정해주어야 한다.
- 그런데, 다음과 같은 상황에서 클래스 이름을 지정하지 않고 인스턴스를 생성하고 싶을 때도 있다.

_"종류가 너무 많아 클래스로 정리할 수 없는 경우"  
"클래스로부터 인스턴스 생성이 어려운 경우"  
"프레임워크와 생성하는 인스턴스를 분리하고 싶은 경우"_

- 첫 번째는 취급할 오브젝트 종류가 너무 많아서 하나하나 다른 클래스로 만들면 소스 파일을 많이 작성해야 하는 경우이다.
- 두 번째는 생성하고 싶은 인스턴스가 복잡한 과정을 거쳐 만들어지는 것으로, 클래스로부터 만들기 어려운 경우이다. 
- 예를 들면 그래픽 에디터 등으로 사용자가 마우스로 그린 도형을 나타내는 인스턴스 같이 사용자 조작으로 만들어진 인스턴스가 해당될 수 있다.
- 세 번째는 인스턴스를 생성하는 프레임워크를 특정 클래스에 의존하지 않게 하고 싶은 경우이다. 
- 이 경우에는 미리 원형이 될 인스턴스를 등록해두고, 등록된 인스턴스를 복사해서 인스턴스를 생성한다.
- 이처럼 `Prototype` 패턴은 원형이 되는 인스턴스를 바탕으로 새로운 인스턴스를 만들어 내는 패턴이다. 
- `Java`에서는 복제하는 조작을 `clone`이라고 부르며, `Cloneable` 인터페이스와 함께 쓰인다.

### 예제 프로그램
- 다음 프로그램은 문자열을 테두리로 감싸서 표시하거나 밑줄을 그어 표시한다.
![](/assets/image/Pasted%20image%2020250803134126.png)
- `framework`에 포함된 `Product`, `Manager` 클래스는 인스턴스를 복제한다.
- `Manager` 클래스는 `createCopy`를 호출하지만 `Product` 인터페이스를 구현한 클래스이기만 하면 구체적으로 어느 클래스의 인스턴스를 복제할지 관여하지 않는다.
- `MessageBox`, `UnderlinePen` 클래스는 모두 `Product` 클래스를 구현한 클래스이다.


#### `Product` 인터페이스

```java
package framework;

public interface Product extends Cloneable {
	public abstract void use(String s);
    public abstract Product createCopy();
}
```
- `Clonable` 인터페이스를 상속한 `Product` 인터페이스는 `clone()`을 통해 복제를 가능하게 한다.
- `use`, `createCopy` 추상 메소드가 선언되어 있다.

#### `Manager` 클래스

```java
package framework;

import java.util.HashMap;
import java.util.Map;

public class Manager {
	private Map<String, Product> showcase = new HashMap<>();
    
    public void register(String name, Product prototype) {
    	showcase.put(name, prototype);
    }
    
    public Product create(String prototypeName) {
    	Product p = showcase.get(prototypeName);
        return p.createCopy();
    }
}
```
- `Product` 인터페이스를 통해 인스턴스를 복제하는 클래스이다.
- `Product` 인터페이스나 `Manager` 클래스 소스 코드에는 구현 클래스 명이 포함되어 있지 않으며, 그렇기에 구현 클래스에 의존하지 않는다. 
- 이는 구현 클래스와는 독립적으로 수정할 수 있음을 의미한다.
- `Manager`에는 `Product`라는 인터페이스만 포함되어 있는데, 그렇기에 이 인터페이스만이 `Manager` 클래스와 다른 클래스를 연결하는 다리가 된다.

#### `MessageBox` 클래스

```java
import framework.Product;

public class MessageBox implements Product {
	private char decochar;
    
    public MessageBox(char decochar) {
    	this.decochar = decochar;
    }
    
    @Override
    public void use(String s) {
    	int decolen = 1 + s.length() + 1;
        for (int i = 0; i < decolen; i++) {
        	System.out.print(decochar);
        }
        System.out.println();
        System.out.println(decochar + s + decochar);
    	for (int i = 0; i < decolen; i++) {
        	System.out.print(decochar);
        }
        System.out.println();
    }
    
    @Override
    public Product createCopy() {
    	Product p = null;
        try {
        	p = (Product) clone();
        } catch (CloneNotSupportedException e) {
			e.printStackTrace();
        }
        return p;
    }
}
```
- 해당 클래스의 `use` 메소드는 `decochar` 필드로 문자열을 감싼다.
- `createCopy`는 자기 자신을 복제하는 메소드이다. 
- 복제할 때는 인스턴스가 가진 필드 값도 그대로 새 인스턴스에 복사된다.
- `clone` 메소드로 복사할 수 있는 것은 `java.lang.Cloneable` 인터페이스를 구현한 클래스 뿐이다. 
- 만약 이 인터페이스를 구현하지 않는 클래스가 `clone` 메소드를 통해 복제를 시도하면 `CloneNotSupportedException` 예외가 발생할 수 있으므로 `try ... catch`문으로 예외 처리를 해주어야 한다.
- `MessageBox` 클래스는 `Cloneable` 인터페이스를 확장한 `Product` 인터페이스를 구현하고 있기 때문에 예외가 발생하지 않는다.
- `Cloneable` 인터페이스는 단순한 표시로 이용되는 마커 인터페이스일 뿐, 따로 선언된 메소드는 없다.
- `clone` 메소드는 자신의 클래스 또는 하위 클래스에서만 호출할 수 있으므로 다른 클래스의 요청으로 복제할 경우에는 `createCopy`와 같은 별도의 메소드로 `clone`을 감싸야 한다.

#### `UnderlinePen` 클래스

```java
import framework.Product;

public class UnderlinePen implements Product {
	private char ulchar;
    
    public UnderlinePen(char ulchar) {
    	this.ulchar = ulchar;
    }
    
    @Override
    public void use(String s) {
    	int ulen = s.length();
        for (int i = 0; i < ulen; i++) {
        	System.out.print(ulchar);
        }
        System.out.println();
    }
    
    @Override
    public Product createCopy() {
    	Product p = null;
        try {
        	p = (Product) clone();
        } catch (CloneNotSupportedException e) {
			e.printStackTrace();
        }
        return p;
    }
}
```
- `MessageBox` 클래스와 거의 같은 동작을 하는데, `ulchar` 필드가 밑줄로 사용될 뿐이다.

#### `Main` 클래스

```java
import framework.Manager;
import framework.Product;

public class Main {
	// 선언 및 초기화
	Manager manager = new Manager();
    UnderlinePen upen = new UnderlinePen('-');
    MessageBox mbox = new MessageBox('*');
    MessageBox sbox = new MessageBox('/');
    
    // 등록
    manager.register("strong message", upen);
    manager.register("warning box", mbox);
    manager.register("slash box", sbox);
    
    Product p1 = manager.create("strong message");
    p1.use("Hello, world!");
    
    Product p2 = manager.create("warning box");
    p2.use("Hello, world!");
    
    Product p3 = manager.create("slash box");
    p3.use("Hello, world!");
}
```
- `Manager` 인스턴스를 만들고 `Product` 구현체를 등록하여 사용하는 동작 테스트 클래스이다.

### `Prototype` 패턴의 등장인물
#### `Product` 인터페이스
- 인스턴스를 복사하여 새로운 인스턴스를 만들기 위한 메소드를 결정하는 원형 `Prototype` 역할을 한다.

#### `MessageBox`, `UnderlinePen` 클래스
- 인스턴스를 복사하여 새로운 인스턴스를 만드는 구체적인 원형 역할을 한다.

#### `Manager` 클래스
- `Product` 인터페이스의 메소드를 이용해 새로운 인스턴스를 만드는 이용자 `Client` 역할을 한다.


### 책에서 제시하는 힌트
#### 클래스 이름은 속박인가?
- 소스 코드 안에 클래스 이름을 쓰는게 왜 문제가 될까? 
- 여기서 객체지향 프로그래밍의 목표 중 하나가 '부품으로서의 재사용'이라는 점을 다시 한 번 상기할 필요가 있다.
- 소스 코드 안에 클래스 이름이 있으면 해당 클래스와 분리해서 재사용할 수 없게 된다. 
- 물론 소스 코드 내 클래스 이름을 수정하면 되지만 여기서 재사용이란 소스 코드의 수정은 배제한다. 
- 즉 `.java` 파일 없이 `.class` 파일만으로 그 클래스를 재사용할 수 있는지가 중요하다.
- 밀접하게 결합해야 하는 클래스의 이름이 소스 내에서 사용되는 건 당연하지만 부품으로 독립시켜야 하는 클래스 이름이 소스 내 있는 것은 문제가 된다.

#### `clone` 메소드와 `java.lang.Cloneable` 인터페이스
- `java.lang` 패키지는 암묵적으로 `import` 되어 있어 간단히 `Cloneable`로 쓸 수 있다.
- `clone` 메소드는 `java.lang.Object` 클래스에 정의되어 있다. 
- `Object` 클래스는 `Java` 클래스 계층의 최상위 클래스이므로 모든 클래스에서 `clone` 메소드를 상속하게 된다.
- `clone` 메소드는 얕은 복사를 한다. 
- `clone` 메소드의 동작은 필드 내용을 그대로 복사하는 것이다. 
- 그러나 필드가 가리키는 인스턴스의 내용까지는 고려하지 않는다. 
- 가령 필드에 배열이 있다고 하면 그 배열에 대한 참조만 복사되고 배열 요소 하나하나가 복사되지는 않는다.
- 이러한 필드 대 필드의 복사를 얕은 복사라고 한다.
- 만약 얕은 복사로 충분하지 않다면 `clone` 메소드를 오버라이드해서 `super.clone()`으로 상위 클래스의 `clone` 메소드를 호출하면 된다.

#### `clone`은 사용하기 어렵다.
- `java.lang.Object` 클래스의 `clone` 메소드가 `protected`로 지정되어 있어 상속 관계가 없는 클래스의 `clone` 메소드를 호출하기는 어렵다.
- 실제 인스턴스를 복제하는 클래스를 설계하는 경우 `clone` 메커니즘에 의존하지 않고 복사 생성자나 복사 팩토리를 사용하는 편이 좋다고 한다.