---
title: ⛓️ Java Design-Pattern 15 - Chain of Responsibility
date: 2025-08-09 18:15:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Chain of Responsibility` 패턴이란?
- 여러 객체를 사슬처럼 연쇄적으로 묶고, 객체 사슬을 차례대로 돌면서 원하는 객체를 결정하는 패턴이다.
- 즉, 어떤 객체에게 요청을 처리할 수 있으면 처리하고, 처리할 수 없을 때는 다른 객체에게 넘긴다.


### 예제 프로그램
- 트러블이 발생했을 때 누군가가 해결해야 하는 상황을 다음 예제 프로그램에서 확인할 수 있다.

![](/assets/image/Pasted%20image%2020250809185835.png)

#### `Trouble` 클래스

```java
public class Trouble {
	private int number;
    
    public Trouble(int number) {
        this.number = number;
    }
    
    public int getNumber() {
        return number;
    }
    
    @Override
    public String toString() {
        return "[Trouble " + number + "]";
    }
}
```
- 발생한 트러블을 표현하는 클래스이다.

#### `Support` 클래스

```java
public abstract class Support {
	private String name;
    private Support next;
    
    public Support(String name) {
    	this.name = name;
        this.next = null;
    }
    
    public Support setNext(Support next) {
    	this.next = next;
        return next;
    }
    
    public void support(Trouble trouble) {
    	if (resolve(trouble)) {
        	done(trouble);
        } else if (next != null) {
        	next.support(trouble);
        } else {
        	fail(trouble);
        }
    }
    
    @Override
    public String toString() {
    	return "[" + name + "]";
    }
    
	protected abstract boolean resolve(Trouble trouble);
    
    protected void done(Trouble trouble) {
    	System.out.println(trouble + " is resolved by " + this + ".");
    }
    
    protected void fail(Trouble trouble) {
    	System.out.println(trouble + " cannot be resolved.");
    }
}	
```
- 문제 해결 사슬을 만들기 위한 추상 클래스이다.
- `resolve` 메소드는 하위 클래스에서 구현해야 하는 추상 메소드이다.

#### `NoSupport` 클래스

```java
public class NoSupport extends Support {
	public NoSupport(String name) {
    	super(name);
    }
    
    @Override
    protected boolean resolve(Trouble trouble) {
    	return false;
    }
}
```
- `Support` 클래스의 하위 클래스로, `resolve` 메소드는 항상 `false`를 반환하여 어떤 문제도 해결하지 않는 클래스이다.

#### `LimitSupport` 클래스

```java
public class LimitSupport extends Support {
	private int limit;
    
    public LimitSupport(String name, int limit) {
    	super(name);
        this.limit = limit;
    }
    
    @Override
    protected boolean resolve(Trouble trouble) {
    	if (trouble.getNumber() < limit) {
        	return true;
        } else {
        	return false;
        }
    }
}
```
- `Support` 클래스의 하위 클래스로, `limit` 미만의 문제를 해결하는 클래스이다. 
- 본 예제 프로그램은 `resolve` 메소드에서 `true`를 반환할 뿐이지만, 실제로는 이곳에서 문제를 해결해주어야 한다.

#### `OddSupport` 클래스

```java
public class OddSupport extends Support {
	public OddSupport(String name) {
    	super(name);
    }
    
    @Override
    protected boolean resolve(Trouble trouble) {
    	if (trouble.getNumber() % 2 == 1) {
        	return true;
        } else {
        	return false;
        }
    }	
}
```
- `Support` 클래스의 하위 클래스로, 홀수 문제를 해결한다.

#### `SpecialSupport` 클래스

```java
public class SpecialSupport extends Support {
	private int number;
    
    public SpecialSupport(String name, int number) {
    	super(name);
        this.number = number;
    }
    
    @Override
    protected boolean resolve(Trouble trouble) {
    	if (trouble.getNumber() == number) {
        	return true;
        } else {
        	return false;
        }
    }	
}
```
- `Support` 클래스의 하위 클래스로 지정된 번호에 한 해 문제를 해결하는 클래스이다.

#### `Main` 클래스

```java
public class Main {
	public static void main(String[] args) {
    	Support alice = new NoSupport("Alice");
    	Support bob = new LimitSupport("Bob", 100);
    	Support charlie = new SpecialSupport("Charlie", 429);
    	Support diana = new LimitSupport("Diana", 200);
    	Support elmo = new OddSupport("Elmo");
    	Support fred = new LimitSupport("Fred", 300);
    }
    
    alice
    	.setNext(bob)
    	.setNext(charle)
        .setNext(diana)
        .setNext(elmo)
        .setNext(fred)
        
    for (int i = 0; i < 500; i += 33) {
    	alice.support(new Trouble(i));
    }
}
```
- 당연하게도 실행 결과에 `Alice`는 출력되지 않는다. 
- 모든 문제를 다른 사람에게 떠넘기기 때문이다.


### `Chain of Responsibility` 패턴의 등장인물
#### `Support` 클래스
- 요구를 처리하는 인터페이스 `API`를 정의하는 처리자 `Handler` 역할을 한다.

#### `NoSupport`, `LimitSupport`, `OddSupport`, `SpecialSupport` 클래스
- 요구를 구체적으로 처리하는 구체적인 처리자 `ConcreteHandler` 역할을 한다.

#### `Main` 클래스
- 첫 번째 구체적인 처리자에게 요구를 하는 요구자 `Client` 역할을 한다.


### 책에서 제시하는 힌트
#### 요구자와 구체적인 처리자를 유연하게 연결한다.
- 요구자가 첫 번째 구체적인 처리자에게 요구만 하면, 나머지는 그 요구가 문제 해결 사슬을 흘러다니다가 적절한 구체적인 처리자에게 처리된다.
- 만약 이 패턴을 사용하지 않는다면 '이 요구는 이 사람이 처리해야 한다'는 정보를 누군가가 중앙집권적으로 가지고 있어야 한다. 
- 또한 그런 정보를 요구하는 사람이 갖고 있는 것은 부품으로서의 독립성이 훼손되기 때문에 적절치 않다.

#### 동적으로 사슬 형태를 바꾼다.
- 예제 프로그램에서는 지원팀은 항상 고정된 순서대로 되어 있다. 
- 그러나 요구를 처리하는 구체적인 처리자 역할 객체의 관계가 동적으로 변화하는 상황도 생각할 수 있다.
- `GUI` 애플리케이션에서는 사용자가 앱 화면상에 컴포넌트를 자유롭게 추가할 수 있는 경우가 있는데, 이 때 해당 패턴이 효과적으로 작동한다.

#### 자기 일에 집중할 수 있다.
- 구체적인 처리자 역할은 자신이 할 수 있는 일에 집중하고, 이외의 일을 해결하기 위해 필요 없는 코드를 자신이 안고 갈 필요가 없어진다. 
- 문제를 해결하지 못 할 경우, 그저 다른 구체적인 처리자에게 문제를 넘기면 된다.
- 해당 패턴을 사용하지 않을 경우 관리자 한 명이 누가 요구를 처리할 지 모두 결정하는 방법을 취한다. 
- 혹은 'A가 안되면 B에게, 그래도 안되면 C에게' 이런 식으로 일의 할당까지 개개의 구체적인 처리자 역할에 부담시키는 방법을 취하게 된다.

#### 떠넘기기로 처리가 지연되지 않을까?
- 실제로 해당 패턴을 사용하면 누가 요구를 처리할지 정해져 있는 프로그램과 비교하면 처리가 지연되는 것이 맞다.
- 하지만 이것은 무엇을 우선으로 할지 트레이드오프 문제이다.
- 요구와 처리자의 관계가 고정적이고 처리속도가 매우 중요한 경우에는 해당 패턴을 사용하지 않는 것이 효과적일 수 있다.