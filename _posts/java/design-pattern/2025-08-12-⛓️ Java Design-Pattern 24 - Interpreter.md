---
title: ⛓️ Java Design-Pattern 24 - Interpreter
date: 2025-08-12 20:49:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Interpreter` 패턴이란?
- 프로그램이 해결하려는 문제를 간단한 미니 언어로 구현하는 패턴이다.
- 즉, 구체적인 문제를 미니 언어로 작성된 미니 프로그램으로 만드는 것이다.
- 미니 프로그램은 그 자체로는 동작하지 않기 때문에, `Java` 언어로 통역하는 역할을 하는 프로그램을 만들어 둔다.
- 통역 프로그램은 미니 언어를 이해하고 해석하여 프로그램을 실행한다.
- 이 통역 프로그램 자체를 인터프리터라고 부른다.
- 해결해야 할 문제가 변경됐을 때는 `Java` 언어로 작성된 프로그램이 아닌 미니 프로그램 쪽 코드를 변경한다.


### 미니 언어
#### 미니 언어의 명령
- 미니 언어를 설명하기 위해 무선으로 자동차를 조종하는 언어를 생각해보자.
- 자동차 조종이라지만 할 수 있는 일은 다음 세 가지 뿐이다.

1. `go`: 앞으로 1미터 이동한다.
2. `right`: 오른쪽으로 돈다.
3. `left`: 왼쪽으로 돈다.

- 이것만으로는 시시하니 반복 명령도 추가하자.
- `repeat`: 반복한다.

#### 미니 프로그램의 예

```bash
program go end
```
- 위와 같이 프로그램의 시작과 끝을 알 수 있도록 `program`과 `end` 단어로 명령을 감싼다.

```bash
program go right right go end
```
- 상기 코드는 앞으로 갔다가 다시 뒤로 돌아서 오는 미니 프로그램이다.

```bash
program go right go right go right go right end
program repeat 4 go right end end
```
- 상기 코드는 정사각형을 그리고 돌아오는 미니 프로그램이다.

```bash
program repeat 4 repeat 3 go right go left end right end end
```
- 상기 코드는 마름모를 그리고 돌아오는 미니 프로그램이다.

#### 미니 언어의 문법

```bash
<program> ::= program <command List>
<command list> ::= <command>* end
<command> ::= <repeat command> | <primitive command>
<repeat command> ::= repeat <number> <command list>
<primitive command> ::= go | right | left
```
- `BNC` 표기법을 사용했다.
- `Backus-Naur Form` 또는 `Backus Normal Form`의 줄임말로 언어의 문법을 표기할 때 많이 사용된다.

```bash
<program> ::= program <command list>
```
- 프로그램이라는 `<program>`을 정의한다.
- 키워드 뒤로 명령 목록 `<command list>`가 이어진 것으로 정의한다.
- `::=`는 좌변이 정의되는 대상이고, 우변이 정의되는 내용이다.

```bash
<command list> ::= <command>* end
```
- 여기서는 명령 목록 `<command list>`를 정의한다.
- `<command list>`는 `<command>`가 0개 이상 반복된 후 `end` 단어가 오는 것으로 정의한다.
- `*`는 직전의 명령어가 0회 이상 반복됨을 의미한다.

```bash
<command> ::= <repeat command> | <primitive command>
```
- 이번에는 명령 `<command>`를 정의한다.
- `<command>`는  `<repeat command>` 또는 `<primitive command>` 중 하나로 정의된다.

```bash
<repeat command> ::= repeat <number> <command list>
```
- 다음으로 반복 명령 `<repeat command>`를 정의한다.
- `repeat`이라는 명령어 뒤에 반복 횟수 `<number>`가 오고 `<command list>`가 뒤따르는 것으로 정의한다.
- 명령 목록 `<command list>`는 이미 위에서 정의되었다.
- `<command list>`의 정의에는 `<command>`가 사용되고, `<command>`의 정의에서는  `<repeat command>`가 사용되고, `<repeat command>` 정의에서는 `<command list>`가 사용되는 이러한 형태를 재귀적인 정의라고 한다.

```bash
<primitive command> ::= go | right | left
```
- 기본 명령 `<primitive command>`을 정의한다.

#### 터미널 익스프레션과 논터미널 익스프레션
- `go`, `left`, `right` 처럼 더 전개되지 않는 표현을 터미널 익스프레션이라고 한다.
- 버스나 열차의 종착역을 터미널이라고 하는 것과 비슷하다.
- 또한 `program`이나 `repeat` 명령어처럼 다시 더 전개되는 표현을 논터미널 익스프레션이라고 한다.


### 예제 프로그램
- 다음은 미니 언어를 구문 해석하는 예제 프로그램이다.

![](/assets/image/Pasted%20image%2020250813133102.png)

- 단순한 문자열인 미니 프로그램을 분해하여 각 부분이 어떤 구조로 되어 있는지를 해석하는 것이 구문 해석이다.
- 가령 다음 미니 프로그램이 주어졌다고 하자.

```bash
program repeat 4 go right end end
```

![](/assets/image/Pasted%20image%2020250812211112.png)
- 위 이미지와 같은 구문 트리 구조를 메모리 상에 만들어 내는 처리가 구문 해석이다.

#### `Node` 클래스

```java
public abstract class Node {
	public abstract void parse(Context context) throws ParseException;
}
```
- 구문 트리의 각 노드를 구성하는 최상위 클래스이다.
- 해당 클래스에는 추상 메서드 `parse()`만 선언되어 있다.
- 이는 구문 해석 처리를 위한 메서드이다.
- 인수로 전달되는 `Context`는 상황을 나타내는데, 이후에 등장한다.
- 이 메서드에는 구문 해석을 하다가 오류가 났을 때 `ParseException` 예외를 던진다.

#### `ProgramNode` 클래스

```java
/* <program> ::= program <command list> */
public class ProgramNode extends Node {
	private Node commandListNode;
	
	@Override
	public void parse(Context context) throws ParseException {
		context.skipToken("program");
		commandListNode = new CommandListNode();
		commandListNode.parse(context);
	}
	
	@Override
	public String toString() {
		return "[program " + commandListNode + "]";
	}
}
```
- `<program>`을 나타내는 `programNode` 클래스다.
- 이 클래스에는 `Node`형 `commandListNode` 필드가 있다.
- 이 필드는 자신의 뒤에 이어지는 `<command list`에 대응하는 노드를 저장하기 위함이다.
- `ProgramNode`의 `parse()` 메서드의 첫 라인에서는 `"program"`이라는 단어를 건너 뛴다.
- 구문 해석을 할 때 처리 단위를 토큰이라고 한다.
- 좀 더 자세히 말하자면, 어휘 분석은 문자로부터 토큰을 만들고, 구문 해석은 토큰으로부터 구문 트리를 만든다.
- `BNF`를 보면 그 뒤로 `<command list>`가 이어진다.
- `<command list>`에 대응하는 `CommandListNode` 인스턴스를 생성하고, 그 인스턴스의 `parse()` 메서드를 호출한다.
- `<command list>`가 어떠한 내용으로 되어 있는지는 `ProgramNode` 메서드에는 기술되어 있지 않다.
- `ProgramNode`에 기술하는 것은 어디까지나 다음과 같이 `BNF`에서 보이는 범위에 한정된다.

```bash
<program> ::= program <command list>
```

#### `CommandListNode` 클래스

```java
import java.util.ArrayList;
import java.util.List;

/* <command list> ::= <command>* end */
public class CommandListNode extends Node {
	private List<Node> list = new ArrayList<>();
	
	@Override
	public void parse(Context context) throws ParseException {
		while (true) {
			if (context.currentToken() == null) {
				throw new ParseException("Error: Missing 'end'");
			} else if (context.currentToken().equals("end")) {
				context.skipToken("end");
				break;
			} else {
				Node commandNode = new CommandNode();
				commandNode.parse(context);
				list.add(commandNode);
			}
		}
	}
	
	@Override
	public String toString() {
		return list.toString();
	}
}
```
- `<command>`가 0회 이상 반복하는 형태를 갖고자 `java.util.List<Node>`형 필드를 가지고 있다.
- `parse()` 메서드는 다음과 같다.

1. 현재 주목하고 있는 토큰 `context.curruntToken()` 값이 `null`이면 더는 남은 토큰이 없다는 말이다. 이 경우 `parse()`는 `end` 명령어가 없다는 메시지를 붙여 예외를 던진다.
2. 현재 주목하고 있는 토큰이 `end`면 반복문을 탈출한다.
3. 현재 주목하고 있는 토큰이 `end`가 아니면 그것은 `<command>`라는 의미로, `CommandNode` 인스턴스를 만들어 `parse()` 한다. 이후 인스턴스를 `list` 필드에 추가한다.

- 여기서도 `BNF`로 기술된 범위에서만 처리하는 것을 알 수 있다.
- 이렇게 하면 프로그램에 실수가 줄어든다.
- 무심코 '이렇게 하면 속도가 더 빨라지지 않을까?'라는 유혹에 사로잡혀 더 자세한 구조까지 읽는 처리를 한다면 생각하지 못한 버그를 만들어 낼 수 있다.
- `interpreter` 패턴은 원래 미니 언어라는 간접적인 처리 방법을 이용하므로 잔재주로 효율을 꾀하는 것은 그다지 현명한 방법이 아니다.

#### `CommandNode` 클래스

```java
/* <command> ::= <repeat command> | <primitive command> */
public class CommandNode extends Node {
	private Node node;
	
	@Override
	public void parse(Context context) throws ParseException {
		if (context.currentToken().equals("repeat")) {
			node = new RepeatCommandNode();
			node.parse(context);
		} else {
			node = new PrimitiveCommandNode();
			node.parse(context);
		}
	}
	
	@Override
	public String toString() {
		return node.toString();
	}
}
```
- `Node`형 필드 `node`는 `<repeat command>`에 대응하는 `RepeatCommandNode` 또는 `<primitive command>`에 대응하는 `PrimitiveCommandNode` 클래스의 인스턴스를 저장하기 위해 사용된다.

#### `RepeatCommandNode` 클래스

```java
/* <repeat command> ::= repeat <number> <command list> */
public class RepeatCommandNode extends Node {
	private int number;
	private Node commandListNode;
	
	@Override
	public void parse(Context context) throws ParseException {
		context.skipToken("repeat");
		number = context.currentNumber();
		context.nextToken();
		commandListNode = new CommandListNode();
		commandListNode.parse(context);
	}
	
	@Override
	public String toString() {
		return "[repeat " + number + " " + commandListNode + "]";
	}
}
```
- `<number>` 부분은 `number` 필드, `<command list>` 부분은 `commandListNode` 필드에 저장된다.
- `parse()` 메서드는 다음과 같이 재귀성을 띈다.

1. `RepeatCommandNode`의 `parse()` 메서드 안에서는 `CommandListNode`의 인스턴스를 만들어 `parse()` 메서드를 호출한다.
2. `CommandListNode`의 `parse()` 메서드 안에서는 `CommandNode`의 인스턴스를 만들어 `parse()` 메서드를 호출한다.
3. `CommandNode`의 `parse()` 메서드 안에서는 `RepeatCommandNode`의 인스턴스를 만들어 `parse()` 메서드를 호출한다.

- 위의 재귀 처리는 언젠가 `PrimitiveCommandNode`를 만나기 전까지 반복된다.
- `PrimitiveCommandNode`가 바로 터미널 익스프레션이다.
- 재귀적인 취급에 익숙하지 않다면, 왠지 무한 루프가 될 것 같지만, 그것은 착각이다.
- 터미널 익스프레션에 영원히 도달하지 않는다면 그 정의는 잘못된 것이다.

#### `PrimitiveCommandNode` 클래스

```java
/* <primitive command> ::= go | right | left */
public class PrimitiveCommandNode extends Node {
	private String name;
	
	@Override
	public void parse(Context context) throws ParseException {
		name = context.currentToken();
		if (name == null) {
			throw new ParseException("Error: Missing <primitive command>");
		} else if (!name.equals("go") && !name.equals("right") && !name.equals("left")) {
			throw new ParseException("Error: Unknown <primitive command>: '" + name + "'");
		}
		context.skipToken(name);
	}
	
	@Override
	public String toString() {
		return name;
	}
}
```
- 이 클래스의 `parse()`에서는 다른 `parse()` 메서드를 호출하지 않는다.

#### `Context` 클래스

```java
import java.util.*;

public class Context {
	private String[] tokens;
	private String lastToken;
	private int index;
	
	public Context(String text) {
		this.tokens = text.split("\\s+");
		this.index = 0;
		nextToken();
	}
	
	public String nextToken() {
		if (index < tokens.length) {
			lastToken = tokens[index++];
		} else {
			lastToken = null;
		}
		
		return lastToken;
	}
	
	public String currentToken() {
		return lastToken;
	}
	
	public void skipToken(String token) throws ParseException {
		if (currentToken() == null) {
			throw new ParseException("Error: '" + token + "' is expected, but no more token is found.");
		} else if (!token.equals(currentToken())) {
			throw new ParseException("Error: '" + token + "' is expected, but '" + currentToken() + "' is found.");
		}
		nextToken();
	}
	
	public int currentNumber() throws ParseException {
		if (currentToken() == null) {
			throw new ParseException("Error: No more token.");
		}
		
		int number = 0;
		
		try {
			number = Integer.parseInt(currentToken());
		} catch (NumberFormatException e) {
			throw new ParseException("Error: " + e);
		}
		
		return number;
	}
}
```
- 구문 해석을 위해 필요한 메서드를 제공한다.

1. `nextToken()`: 다음 토큰을 얻는다.
2. `currentToken()`: 현재 토큰을 얻는다.
3. `skipToken()`: 현재 토큰을 체크하고서, 다음 토큰을 얻는다.
4. `currentToken()`: 현재 토큰을 수치로 얻는다.

- 주어진 문자열로부터 공백 문자가 한 개 이상 연속된 것을 구분하여 토큰 배열을 작성한다.
- `text.split("\\s+")` 라인이 이에 해당한다.

#### `ParseException` 클래스

```java
public class ParseException extends Exception {
	public ParseException(String msg) {
		super(msg);
	}
}
```
- 단순히 예외 처리를 위한 클래스이다.

#### `Main` 클래스

```java
import java.nio.file.Files;
import java.nio.file.Path;

public class Main {
	public static void main(String[] args) {
		try {
			for (String text: Files.readAllLines(Path.of("program.txt"))) {
				System.out.println("text = \"" + text + "\"");
				Node node = new ProgramNode();
				node.parse(new Context(text));
				System.out.println("node = " + node);
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```
- `Main`은 `program.txt`라는 파일을 읽어 한줄한줄 미니 프로그램으로 생각하고 구문을 분석하여 결과를 문자열로 표시한다.
- `program.txt` 파일 내용은 다음과 같다.

```txt
program end
program go end
program go right go right go right go right end
program repeat 4 go right end end
program repeat 4 repeat 3 go right go left end right end end
```


### `Interpreter` 패턴의 등장인물
#### `Node` 클래스
- 구문 트리의 노드 인터페이스를 정의하는 추상 표현(`AbstractExpression`) 역을 맡았다.

#### `PrimitiveCommandNode` 클래스
- `BNF`의 터미널 익스프레션에 대응하는 종단 표현(`TerminalExpression`) 역을 맡았다.

#### `ProgramNode`, `CommandNode`, `RepeatCommandNode`, `CommandListNode` 클래스
-  `BNF`의 논터미널 익스프레션에 대응하는 비종단 표현(`NonterminalExpression`) 역을 맡았다.

#### `Context` 클래스
- 인터프리터가 구문 해석을 하기 위한 정보를 제공하는 문맥(`Context`) 역을 맡았다.

#### `Main` 클래스
- 구문 트리를 조립하기 위해 `TerminalExpression`와 `NonterminalExpression`를 호출하는 의뢰자(`Client`) 역을 맡았다.


### 책에서 제시하는 힌트
#### 그 외에 어떤 미니 언어가 있을까?
- 이 장에서는 무선으로 자동차를 조종하는 미니 언어를 학습했다.
- 몇 가지 예를 들어보자.

1. 정규 표현: `raining & (dogs | cats) *` → `raining` 뒤에 `cat` 또는 `dog`이 0번 이상 반복
2. 검색 구문: `site: example.com "검색어1" AND "검색어2"` → `example.com`에서 검색어1과 검색어2에 완전히 일치하는 검색을 표현
3. 일괄 처리 언어: 기본적인 명령이 몇 개 준비되어 있고, 그 명령을 순서대로 실행하거나 반복하는 언어도 `Interpreter` 패턴으로 처리할 수 있다. 이 장의 무선 조종 제어도 일괄 처리 언어의 일종이라고 할 수 있다.

#### 건너뛸 것인가 읽을 것인가?
- 인터프리터를 만들 때 자주 일어나는 것이 토큰을 한 개 더 읽거나 못 읽는 버그이다.
- 각 논터미널에 대응하는 메서드를 쓸 때는 항상 이 메서드에 왔을 때 어디까지 토큰을 읽었는지, 이 메서드에서 나올 때 어디까지 토큰을 읽어야 하는지 신경 써야 한다.