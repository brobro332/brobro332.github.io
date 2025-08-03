---
title: ⛓️ Java Design-Pattern Ⅷ - Builder
date: 2025-08-03 13:45:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Builder` 패턴이란?
- 구조를 가진 인스턴스를 만들어 가는 패턴을 `Builder` 패턴이라고 한다.


### 예제 프로그램
- 다음은 `Builder` 패턴을 이용해 문서를 작성하는 프로그램이다. 프로그램의 요구사항은 다음과 같다.

_"타이틀을 한 개 포함한다."  
"문자열을 몇 개 포함한다."  
"항목을 몇 개 포함한다."_

![](/assets/image/Pasted%20image%2020250803135007.png)
![](/assets/image/Pasted%20image%2020250803135023.png)

- `Builder` 추상 클래스에서는 문서를 구성하는 추상 메소드를 선언한다.
- `Director` 클래스는 그 메소드를 이용해서 구체적인 하나의 문서를 만든다.
- `TextBuilder`, `HTMLBuilder` 클래스는 `Builder` 클래스를 구체적으로 처리한다.

#### `Builder` 클래스

```java
public abstract class Builder {
	public abstract void makeTitle(String title);
    public abstract void makeString(String string);
    public abstract void makeItems(String[] items);
    public abstract void close();
}
```
- 문서를 만드는 추상 메소드들을 선언한 추상 클래스이다.

#### `Director` 클래스

```java
public class Director {
	private Builder builder;
    
    public Director(Builder builder) {
    	this.builder = builder;
    }
    
    public void construct() {
    	builder.makeTitle("Greeting");
        builder.makeString("일반적인 인사");
        builder.makeItems(
        	new String[]{
        		"How are you?",
                "Hello",
                "Hi"
        	}
        );
        builder.makeString("시간대별 인사");
        builder.makeItems(
        	new String[]{
        		"Good morning",
                "Good afternoon",
                "Good evening"
        	}
        );
        builder.close();
    }
}
```
- `Director` 클래스 생성자의 인자로 `Builder`형 필드를 갖는다. 
- 그런데 해당 클래스는 추상 클래스이므로 인스턴스를 만들 수 없기 때문에 생성자에 실제로 전달되는 것은 `Builder` 클래스의 하위 클래스인 `TextBuilder` 또는 `HTMLBuilder` 클래스의 인스턴스가 된다.
- `construct` 메소드는 문서를 만드는 메소드이다.

#### `TextBuilder` 클래스

```java
public class TextBuilder extends Builder {
	private StringBuffer sb = new StringBuffer();
    
    @Override
	public void makeTitle(String title) {
		sb.append("==================================\n");
		sb.append("[");
        sb.append(title);
        sb.append("]\n\n");
	}
    
    @Override
	public void makeString(String string) {
    	sb.append("■");
		sb.append(string);
		sb.append("\n\n");
	}
    
    @Override
	public void makeItems(String[] items) {
		for (String s : items) {
        	sb.append(" .");
            sb.append("s");
            sb.append("\n");
        }
        sb.append("\n");
	}
    
    @Override
	public void close() {
		sb.append("==================================\n");
	}
    
	public String getResult() {
		return sb.toString();
	}
}
```
- 문서를 작성하여 `String` 형태로 반환한다.

#### `HTMLBuilder` 클래스

```java
import java.io.*;

public class HTMLBuilder extends Builder {
	private String filename = "untitled.html";
	private StringBuffer sb = new StringBuffer();
    
    @Override
	public void makeTitle(String title) {
		filename = title + ".html";
		sb.append("<!DOCTYPE html>\n");
        sb.append("<html>\n");
        sb.append("<head><title>");
        sb.append(title);
        sb.append("</title></head>\n");
        sb.append("<body>\n");
        sb.append("<h1>");
        sb.append(title);
        sb.append("</h1>\n\n");
	}
    
    @Override
	public void makeString(String string) {
        sb.append("<p>");
        sb.append(string);
        sb.append("</p>");
	}
    
    @Override
	public void makeItems(String[] items) {
		sb.append("<ul>\n");
        
        for (String s : items) {
        	sb.append("<li>");
            sb.append(s);
            sb.append("</li>\n");
        }
        
        sb.append("</ul>\n\n");
	}
    
    @Override
	public void close() {
		sb.append("</body>");
        sb.append("</html>\n");
        
        try {
        	Writer writer = new FileWriter(filename);
            writer.write(sb.toString());
            writer.close();
        } catch (IOException e) {
        	e.printStackTrace();
        }
	}
    
	public String getHtmlResult() {
		return filename;
	}
}
```
- `HTML` 파일 형태로 문서를 작성하여 `HTML` 파일의 파일명을 반환한다.

#### `Main` 클래스

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class Main {
	public static void main(String[] args) {
    	if (args.length != 1) {
        	usage();
            System.exit(0);
        }
        
        if (args[0].equals("text")) {
        	TextBuilder textBuilder = new TextBuilder();
            Director director = new Director(textBuilder);
            director.construct();
            String result = textBuilder.getTextResult();
            System.out.println(result);
        } else if (args[0].eqauls("html")) {
        	HTMLBuiler htmlBuilder = new HTMLBuilder();
            Director director = new Director(htmlBuilder);
            director.construct();
            String filename = htmlBuilder.getHtmlResult();
            System.out.println("HTML 파일" + filename + "이 작성되었습니다.");
        } else {
        	usage();
            System.exit(0);
        }
    }
    
    public static void usage() {
    	System.out.println("Usage: java Main text	 텍스트로 문서 작성");
        System.out.println("Usage: java Main html	 HTML 파일로 문서 작성");
    }
}
```
- 커맨드 라인에서 `java Main text`와 같이 `text`를 파라미터로 지정한 경우 `TextBuilder` 클래스의 인스턴스를 `Director` 클래스의 생성자에 전달한다.
- 반면 `html`을 지정한 경우에는 `HTMLBuilder` 클래스의 인스턴스를 전달한다.
- `Builder`의 메소드만 사용한다는 것은 `Director`는 실제로 동작하는 것이 `TextBuilder`인지 `HTMLBuilder`인지 의식하지 않는다.
- `Builder`는 문서 구축에 필요한 충분한 메소드를 구성하되, 텍스트나 `HTML` 파일을 작성하는 데 고유한 메소드까지 제공해서는 안 된다. 
- 의존성이 생기기 때문이다.


### `Builder` 패턴의 등장인물
#### `Builder` 클래스
- 인스턴스 생성하기 위한 인터페이스를 결정하는 건축가 `Builder` 역할을 한다. 
- 건축가 역할에는 인스턴스의 각 부분을 만드는 메소드가 준비된다.

#### `TextBuilder`, `HTMLBuilder` 클래스
- 건축가의 인터페이스를 구현하는 구체적인 건축가 `ConcreteBuilder` 역할이다. 
- 실제 인스턴스 생성으로 호출되는 메소드가 여기에 구현되며, 최종적으로 완성된 결과를 얻는 메소드가 준비된다.

#### `Director` 클래스
- 건축가의 인터페이스를 사용하여 인스턴스를 생성하는 감독관 `Director` 역할을 한다. 
- 구체적인 건축가 역할이 무엇이든 잘 작동하도록 건축가의 메소드만 사용한다.

#### `Main` 클래스
- `Builder` 패턴을 이용하는 의뢰인 `Client`이다.


### 책에서 제시하는 힌트
#### 누가 무엇을 알고 있는가?
- 객체지향 프로그래밍에서는 어느 클래스가 어느 메소드를 사용할 수 있는지가 매우 중요하므로 주의해서 프로그래밍 해야 한다.
- `Main` 클래스는 `Director` 클래스의 메소드만 호출하므로 `Builder` 클래스의 메소드를 모른다.
- `Director` 클래스는 `Builder` 클래스의 메소드를 알지만 실제로 어떤 구현체의 인스턴스를 이용하는지 모른다. 
- 그렇기 때문에 구현체를 교체할 수 있는 것이다. 
- 항상 교체 가능성을 염두에 두고 프로그래밍 해야 한다.

#### 의존성 주입
- 앞서 `Director` 클래스는 `Builder`의 어떤 구현체를 이용하는지 모른다고 했다. 
- 그런데 실제로 동작하려면 `Builder`의 구체적인 인스턴스가 필수로 필요하므로 `Director`를 생성할 때 인자로 `TextBuilder` 또는 `HTMLBuilder`를 넣어 `builder` 필드에 주입한다. 
- 이를 의존성 주입 `Dependency Injection`라고 한다.
- 의존성 주입이란 "이 인스턴스에 의존하여 동작해주세요."라는 의미를 담아 인스턴스를 건네는 것과 같다.

#### 설계 시 결정 사항
- `Builder` 클래스는 문서를 작성하기 위해 필요한 충분한 메소드를 선언해야 한다. 
- `Director` 클래스에게 주어지는 도구는 `Builder` 클래스가 제공하기 때문이다.
- 또한 미래를 완전히 예측할 수는 없지만 앞으로 늘어날 하위 클래스에도 어느정도 견딜 수 있도록 설계를 해야 한다.

#### 소스를 읽고 수정하는 방법
- 이미 만들어진 소스 코드를 수정하거나 추가할 때, 기존 소스를 읽어야 한다. 
- 그런데 추상 클래스만 살펴본다고 정보가 그다지 많이 늘어나지 않는다.
- 예제 프로그램을 예로 들면 적어도 `Director`의 소스를 읽고 `Builder` 클래스의 사용법을 이해해야 한다.
- 이후에는 `TextBuilder`, `HTMLBuilder` 클래스를 읽고 `Builder`의 추상 메소드에 어떤 동작이 기대되는지 알 수 있다. 
- 소스를 읽는 단서로 `@Override`도 도움이 된다.
- 클래스의 역할을 이해하지 못하면 실수로 `Director` 클래스가 `Builder` 클래스의 특정 구현체만을 바라보도록 하면 독립성이 손상되어 인스턴스의 교체 가능성이 무너질 수 있다.