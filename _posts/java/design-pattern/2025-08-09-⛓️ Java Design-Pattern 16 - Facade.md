---
title: ⛓️ Java Design-Pattern 16 - Facade
date: 2025-08-09 18:55:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Facade` 패턴이란?
- 해당 패턴은 복잡하게 얽혀서 너저분한 세부 내용을 정리하여 높은 수준의 인터페이스 `API`를 제공한다.
- 즉 시스템 외부에 간단한 인터페이스 `API`를 보여주고, 시스템 내부의 각 클래스의 역할과 의존 관계를 고려하여 올바른 순서로 클래스를 사용한다.


### 예제 프로그램
- 사용자의 웹 페이지를 작성하는 프로그램이다. 
- 이메일 주소에서 이름을 구하는 `Database` 클래스, `HTML` 파일을 작성하는 `HtmlWriter` 클래스, `Facade` 역할로서 높은 수준의 인터페이스 `API`를 제공하는 `PageMaker`로 구성된다.

![](/assets/image/Pasted%20image%2020250809190424.png)

#### `Database` 클래스

```java
package pagemaker;

import java.io.FileReader;
import java.io.IOException;
import java.util.Properties;

public class Database {
	private Database() {
    
    }
    
    public static Properties getProperties(String dbname) throws IOException {
    	String filename = dbname + ".txt";
        Properties prop = new Properties();
        prop.load(new FileReader(filename));
        
        return prop;
    }
}

/* maildata.txt */
// name@example.com=Kim dojun
// ...
```
- 해당 클래스는 `Properties`의 인스턴스를 만들지 않고 `getProperties` `static` 메소드를 통해 인스턴스를 얻는다.

#### `HtmlWriter` 클래스

```java
package pagemaker;

import java.io.Writer;
import java.io.IOException;

public class HtmlWriter {
	private Writer writer;
    
    public HtmlWriter(Writer writer) {
    	this.writer = writer;
    }
    
    public void title(String title) throws IOException {
    	writer.write("<!DOCTYPE html>");
        writer.write("<html>");
        writer.write("<header>");
        writer.write("<title>" + title + "</title>");
        writer.write("</header>");
        writer.write("<body>");
        writer.write("\n");
        writer.write("<h1>" + title + "</h1>");
        writer.write("\n");
    }
    
    public void paragraph(String msg) throws IOException {
    	writer.write("<p>" + msg + "</p>");
        writer.write("\n");
    }
    
    public void link(String href, String caption) throws IOException {
    	paragraph("<a href=\"" + href + "\">" + caption + "</a>");
    }
    
    public void mailto(String mailAddr, String userName) throws IOException {
    	link("mailto:" + mailAddr, userName);
    }
    
    public void close() throws IOException {
    	writer.write("</body>");
        writer.write("</html>");
        writer.write("\n");
        
        writer.close();
    }
}
```
- 인스턴스 생성 시 `Writer`를 주고 그 `Writer`에 `HTML`을 출력한다.
- 제목, 단락, 링크, 메일 주소의 링크를 출력하는 메소드와 출력을 종료하는 메소드를 제공한다.
- 해당 클래스에는 `title` 메소드를 가장 먼저 출력해야 하는 제약 조건이 있으며, 창구가 되는 `pageMaker` 클래스에서 해당 제약을 지키도록 작성되어 있다.

#### `PageMaker` 클래스

```java
package pagemaker;

import java.io.FileWriter;
import java.io.IOException;
import java.util.Properties;
 
public class PageMaker {
	private PageMaker() {
    
    }
    
    public static void makeWelcomePage(String mailAddr, String fileName) {
    	try {
        	Properties mailProp = Database.getProperties("maildata");
            String userName = mailProp.getProperty(mailAddr);
            HtmlWriter writer = new HtmlWriter(new FileWriter(fileName));
            writer.title(userName + "'s web page");
            writer.paragraph("welcome to " + userName + "'s web page!");
            writer.paragraph("Nice to meet you!");
            writer.mailto(mailAddr, userName);
            writer.close();
            
            System.out.println(fileName + " is created for " + mailAddr + " (" + userName + ")");
        } catch (IOException e) {
        	e.printStackTrace();
        }
    }
}
```
- `Database` 클래스와 `HtmlWriter` 클래스를 조합하여 지정한 사용자의 웹 페이지를 만든다.
- 이 클래스에서 정의된 `public` 메소드는 `makeWelcomePage` 뿐이다. 
- 이 메소드에 이메일 주소와 출력 파일 이름만 파라미터로 넘겨주면 웹 페이지가 만들어 진다. 
- `HtmlWriter` 클래스의 메소드를 호출하는 복잡한 부분은 `PageMaker` 클래스가 도맡고, 외부에는 `makeWelcomePage` 메소드만 보여주는 것이다.
- 즉 해당 클래스가 단순한 창구가 되는 것이다.

#### `Main` 클래스

```java
import pagemaker.PageMaker;

public class Main {
	public static void main(String[] args) {
    	PageMaker.makeWelcomePage("name@example.com", "welcome.txt");
    }
}
```
- 위에서 언급했듯 `makeWelcomePage` 메소드만 실행하면 `PageMaker` 클래스가 복잡한 로직을 도맡아 수행한다.


### `Facade` 패턴의 등장인물
#### `PageMaker` 클래스
- 높은 수준의 단순 인터페이스 `API`를 제공하는 단순 창구 역할을 한다. 
- 즉 `Facade` 정면 역할을 맡아 수행한다.

#### `Database`, `HtmlWriter` 클래스
- 정면 역할 외의 많은 클래스들은 각자의 일을 수행한다. 
- 수 많은 클래스들에서 `Facade`를 호출하는 경우는 없다.

#### `Main` 클래스
- 해당 패턴을 이용하는 `Client` 의뢰인 역할을 한다.


### 책에서 제공하는 힌트
#### `Facade`의 역할은 무엇인가?
- 정면 역할은 복잡한 것을 단순하게 보여준다. 
- 즉 프로그래머로 하여금 내부 동작을 의식하지 않아도 되게끔 해준다.
- 해당 패턴의 핵심은 인터페이스 `API` 수를 줄이는 것이다. 
- 클래스와 메소드가 많이 보이면 프로그래머는 어떤 것을 사용할 지 망설이게 되고, 호출 순서도 주의해야 한다. 
- 인터페이스 `API`의 수가 적다는 것은 외부와의 결합이 느슨하다고도 할 수 있다. 
- 결합이 느슨해질수록 부품 재사용이 쉬워진다.
- 클래스를 설계할 때는 어떤 메소드를 `public`으로 할 지 잘 생각해야 한다. 
- 클래스를 외부에 많이 노출시킬수록 내부를 수정하기 힘들어진다.

#### 재귀적인 `Facade` 패턴의 적용
- 정면 역할을 맡은 클래스를 모아 집합을 만들어 새로운 정면 역할을 만들 수 있다. 
- 즉 `Facade` 패턴을 재귀적으로 적용하는 것이다.
- 많은 클래스와 패키지를 가진 매우 큰 프로젝트의 경우, 요소요소에 해당 패턴을 적용하면 시스템이 더 편리해진다.

#### 프로그래머가 `Facade`를 만들지 않는 이유
- 복잡한 내부를 숙지한 프로그래머는 해당 패턴을 이용하지 않을 수 있다. 
- 오히려 무의식 중에 피하기도 한다.
- 숙련된 프로그래머의 머릿속에는 시스템 내용이 모두 들어 있어 다른 프로그래머들에게 '아는 척'을 할 수 있기 때문이기도 하다.
- 어떤 프로그래머가 '이 클래스를 호출하기 전에 이쪽을 먼저 호출해야지'라고 말한다면 해당 패턴을 도입할 필요가 있음을 시사한다.
- 명확하게 언어로 표현할 수 있는 노하우는 프로그래머의 머릿속이 아니라 코드로 표현해두어야 한다.