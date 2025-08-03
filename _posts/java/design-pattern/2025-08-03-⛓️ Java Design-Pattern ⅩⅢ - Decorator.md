---
title: ⛓️ Java Design-Pattern ⅩⅢ - Decorator
date: 2025-08-03 14:40:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Decorator` 패턴이란?
- 객체에 점점 장식을 더해 가는 디자인 패턴이다.


### 예제 프로그램
- 문자열 주위에 장식틀을 붙여 출력하는 프로그램이다.

![](/assets/image/Pasted%20image%2020250803144229.png)

#### `Display` 클래스

```java
public abstract class Display {
	public abstract int getColumns();
    public abstract int getRows();
    public abstract String getRowText(int row);
    
    public void show() {
    	for (int i = 0; i < getRows(); i++) {
        	System.out.println(getRowText(i));
        }
    }
}
```
- 여러 행으로 이루어진 문자열을 표시하는 추상 클래스이다.
- `getColumns`, `getRows`는 각각 문자열의 행렬 수를 가져오는 추상 메소드이다.
- `getRowText`는 지정한 행의 문자열을 가져오는 추상 메소드이다.
- `show`는 모든 행을 표시하는 메소드로, `getRows`와 `getRow-Text`라는 추상 메소드를 사용한 `Template Method` 패턴으로 되어 있다.

#### `StringDisplay` 클래스

```java
public class StringDisplay extends Display {
	private String string;
    
    public StringDisplay(String string) {
    	this.string = string;
    }
    
    @Override
    public int getColumns() {
    	return string.length();
    }
    
    @Override
    public int getRows() {
    	return 1;
    }
    
    @Override
    public String getRowText(int row) {
    	if (row != 0) {
        	throw new IndexOutOfBoundsException();
        }
        return string;
    }
}
```
- `Display` 클래스의 추상 메소드를 구현하는 하위 클래스이다.
- `string` 필드는 표시할 문자열 한 줄을 보관하며, `getColumns`는 문자열 길이를, `getRows`는 1을 반환한다.

#### `Border` 클래스

```java
public abstract class Border extends Display {
	protected Display display;
    
    protected Border(Display display) {
    	this.display = display;
    }
}
```
- `display` 필드는 이 장식틀이 감싸는 내용물을 의미한다.
- 내용물은 `Display` 클래스의 하위 클래스 인스턴스가 될 것이다.

#### `SideBorder` 클래스

```java
public class sideBorder extends Border {
	private char borderChar;
    
    public sideBorder(Display display, char ch) {
    	super(display);
        this.borderChar = ch;
    }
    
    @Override
    public int getColumns() {
    	return 1 + display.getColumns() + 1;
    }
    
    @Override
    public int getRows() {
    	return display.getRows();
    }
    
    @Override
    public String getRowText(int row) {
    	return borderChar + display.getRowText(row) + borderChar;
    }
}
```
- `SideBorder` 클래스는 `Border` 클래스를 구현한 하위 클래스로, 문자열 양옆에 `borderChar`로 장식한다.
- `display` 필드는 `Border` 클래스에서 `protected`로 선언 됐으므로 하위 클래스에서 사용할 수 있다.

#### `FullBorder` 클래스

```java
public class FullBorder extends Border {
	public FullBorder(Display display) {
    	super(display);
    }
    
    @Override
    public int getColumns() {
    	return 1 + display.getColumns() + 1;
    }
    
    @Override
    public int getRows() {
    	return 1 + display.getRows() + 1;
    }
    
    @Override
    public String getRowText(int row) {
    	if (row == 0) {
        	return "+" + makeLine('-', display.getColumns()) + "+";
        } else if (row == display.getRows() + 1) {
        	return "+" + makeLine('-', display.getColumns()) + "+";
        } else {
        	return "|" + display.getRowText(row - 1) + "|";
        }
    }
    
    private String makeLine(char ch, int count) {
    	StringBuilder line = new StringBuilder();
        
        for (int i = 0; i < count; i++) {
        	line.append(ch);
        }
        return line.toString();
    }
}
```
- `SideBorder` 클래스와 마찬가지로 `Border`의 하위 클래스 중 하나이며, 고정된 장식 문자로 상하좌우를 장식한다.
- `makeLine`은 클래스 외부에서 사용할 수 없도록 `private`로 되어 있다.

#### `Main` 클래스

```java
public class Main {
	public static void main(String[] args) {
		Display d1 = new StringDisplay("Hello, world.");
        Display d2 = new SideBorder(d1, '#');
        Display d3 = new StringDisplay(d2);
        
        d1.show();
        d2.show();
        d3.show();
        
        Display d4 = new SideBorder(
        	new FullBorder(
            	new FullBorder(
                	new SideBorder(
                    	new FullBorder(
                        	new StringDisplay("Hello, world.")
                        ), '*'
                    )
                )
            ), '/'
        );
        
        d4.show();
	}
}
```
- 동작 테스트용 `Main` 클래스이다.

### `Decorator` 패턴의 등장인물
#### `Display` 클래스
- 기능을 추가할 때 핵심이 되는 `Component` 역할이다. 
- 장식하기 전의 인터페이스 `API`에 해당한다.

#### `StringDisplay` 클래스
- `Component`의 인터페이스를 구현하는 `Concrete Component` 역할에 해당한다.

#### `Border` 클래스
- `Component`와 동일한 인터페이스를 가지며 장식자 `Decorator` 역할을 한다. 
- 또한 `Component`를 필드로 가지고 있는데, 자신이 장식할 대상을 아는 것과 같다.

#### `SideBorder`, `FullBorder` 클래스
- 구체적인 장식자 `ConcreteDecorator` 역할을 맡았다.


### 책에서 제시하는 힌트
#### 투과적 인터페이스 `API`
- 해당 패턴에서는 장식틀과 내용물을 동일시 한다. 
- `Border` 클래스가 `Display` 클래스의 하위 클래스로 되어 있는 부분에서 그 동일시가 표현되어 있다. 
- 즉 `Border` 클래스 및 그 하위 클래스들은 `Display` 클래스와 같은 인터페이스를 갖는다.
- 이 때 장식틀을 사용해 내용물을 감싸고 인터페이스는 전혀 가려지지 않는다. 
- `getColumns`, `getRows`, `getRowText`, `show` 메소드는 가려지지 않고 다른 클래스에서 볼 수 있다.

#### 위임을 사용한다.
- 장식틀에 대한 메소드 호출은 그 내용물로 위임된다. 
- 예제 프로그램에서는 `SideBorder`의 `getColumns` 메소드에서 `display.getColumns`를 호출하고, `getRows` 메소드에서 `display.getRows`를 호출한다. 
- 즉 내용물을 수정하지 않고 기능을 추가할 수도 있다.
- 또한 위임을 통해 클래스 사이를 동적으로 결합하는데, 그러므로 프레임워크의 소스를 변경하지 않고 객체의 관계를 변경한 새로운 객체를 만들 수 있다.

#### `java.io` 패키지와 `Decorator` 패턴

```java
// 1. 파일에서 데이터를 읽어 들임
Reader reader = new FileReader("datafile.txt");

// 2. 파일에서 데이터를 읽어 들일 때 버퍼링
Reader reader = new BufferedReader(
	new FileReader("datafile.txt")
);

// 3. 2번에 행 번호 관리를 추가
Reader reader = new LineNumberReader(	
	new BufferedReader(
		new FileReader("datafile.txt")
	)
);

// 4. 3번에 파일을 읽어 들이는 대신 네트워크로부터 읽어 들임
Socket socket = new Socket(hostName, portNumber);

Reader reader = new LineNumberReader(	
	new BufferedReader(
		new InputStreamReader(
        	socket.getInputStream();
        )
	)
);
```
- `java.io` 외에도 `java.swing.border` 패키지에서도 `Decorator` 패턴이 등장한다.

#### `java.nio.file.Files`
- `java.io` 패키지에서 해당 패턴을 사용하지만 자주 사용하는 기능은 일일이 여러 클래스를 조합하지 않아도 관련 메소드를 제공하고 있다.

#### 상속과 위임의 동일시

```java
/* 1-1. 상속 - 하위 클래스와 상위 클래스 동일시 */
class Parent {
	void parentMethod() {
    	// ...
    }
}

class Child extends Parent {
	// ...
    
    void childMethod() {
    	// ...
    }
}

/* 1-2. 상속 - 클래스간 대입 */
Parent obj = new Child();
obj.parentMethod(); // 캐스트 없이 대입 가능

Parent obj = new Child();
((Child) obj).childMethod(); // 캐스트 필요

/* 2-1. 위임 - 약한 연결 */
class Rose {
	Violet obj = /* ... */;
    
    void method() {
    	obj.method();
    }
}

class Violet {
	void method() {
    	// ... 
    }
}

/* 2-2-1. 위임 - 추상 클래스 강한 연결 */
abstract class Flower {
	abstract void method();
}

class Rose extends Flower {
	Violet obj = /* ... */;
    
    @Override
    void method() {
    	obj.method();
    }
}

class Violet extends Flower {
	@Override
    void method() {
    	// ... 
    }
}

/* 2-2-2. 위임 - 인터페이스 강한 연결 */
interface Flower {
	abstract void method();
}

class Rose implements Flower {
	Violet obj = /* ... */;
    
    @Override
    void method() {
    	obj.method();
    }
}

class Violet implements Flower() {
	@Override
    void method() {
    	// ... 
    }
}
```
- 하위 클래스는 상위 클래스 변수에 바로 대입할 수 있다. 
- 반면 상위 클래스를 하위 클래스에 대입하려면 캐스트가 필요하다.
- 위임을 사용해 인터페이스가 투과적으로 되어 있을 때는 자신과 위임한 곳을 동일시 할 수 있다.