---
title: ⛓️ Java Design-Pattern Ⅸ - Abstract Factory
date: 2025-08-03 12:43:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Absctract Factory` 패턴이란?
- '추상적인 공장'에서 '추상적인 부품'을 조합하여 '추상적인 제품'을 만드는 패턴이다. 
- 해당 패턴에서는 구체적인 구현보다 인터페이스 `API`에 주목한다. 
- 그리고 그 인터페이스만 사용해서 부품을 조립하고 제품을 완성하는 것이다.
- `Abstract Factory` 패턴에서도 하위 클래스 단계에서 구체적으로 구현한다. 
- 구체적인 공장에서 구체적인 부품을 조합하여 구체적인 제품을 만든다.

### 예제 프로그램
- 다음 프로그램은 계층 구조로 된 링크 페이지를 `HTML` 파일로 만드는 것이다.
![](/assets/image/Pasted%20image%2020250803141036.png)
![](/assets/image/Pasted%20image%2020250803141050.png)
- 추상적인 공장, 부품, 제품을 포함하는 `factory` 패키지와 구체적인 공장, 부품, 제품을 포함하는 `listFactory` 패키지가 있다.
- 지금까지는 `Main.java`만 컴파일하면 필요한 클래스가 모두 컴파일 되었다. 
- 하지만 이번에는 `Main.java`를 컴파일하더라도 `ListFactory.java`, `ListLink.java`, `ListTray.java`, `ListPage.java`는 컴파일 되지 않는다. 
- 왜냐면 `Main` 클래스는 `factory` 패키지만 사용하고 `listFactory` 패키지는 직접 사용하지 않기 때문이다. 
- 그래서 소스 파일은 다음과 같이 컴파일 해야 한다.

```java
javac Main.java listFactory/ListFactory.java
```


#### `Item` 클래스

```java
package factory;

public abstract class Item {
	protected String caption;
    
    public Item(String caption) {
    	this.caption = caption;
    }
    
    public abstract String makeHTML();
}
```
- `Link`, `Tray`의 상위 추상 클래스이다.
- `caption` 필드는 항목의 표제어를 나타낸다. 
- `makeHTML` 메소드는 추상 메소드이므로 하위 클래스에서 구현해야 한다.

#### `Link` 클래스

```java
package factory;

public abstract class Link extends Item {
	protected String url;
    
    public Link(String caption, String url) {
    	super(caption);
        this.url = url;
    }
}
```
- 하이퍼링크를 추상적으로 표현한 클래스이다. 
- '추상적인 부품'에 해당한다.

#### `Tray` 클래스

```java
package factory;

import java.util.ArrayList;
import java.util.List;

public abstract class Tray extends Item {
	protected List<Item> tray = new ArrayList<>();
    
    public Tray(String caption) {
    	super(caption);
    }
    
    public void add(Item item) {
    	tray.add(item);
    }
}
```
- 복수의 `Link`, `Tray`를 모아서 한 곳에 묶는 클래스이다. 
- '추상적인 부품'에 해당한다.

#### `Page` 클래스

```java
package factory;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.StandardOpenOption;
import java.util.ArrayList;
import java.util.List;

public abstract class Page {
	protected String title;
    protected String author;
    protected List<Item> contect = new ArrayList<>();
    
    public Page(String title, String author) {
    	this.title = title;
        this.author = author;
    }
    
    public void add(Item item) {
    	content.add(item);
    }
    
    public void output(String filename) {
    	try {
        	Files.writeString(
            	Path.of(filename), 
            	makeHTML(),
                StandardOpenOption.CREATE,
                StandardOpenOption.TRUNCATE_EXISTING,
                StandardOpenOption.WRITE
            );
            
            System.out.println(filename + "파일을 작성했습니다.");
        } catch (IOException e) {
        	e.printStackTrace();
        }
    }
    
    public abstract String makeHTML();
}
```
- `HTML` 페이지 전체를 추상적으로 표현한 클래스이다. 
- 즉 '추상적인 제품'에 해당한다.

#### `Factory` 클래스

```java
package factory;

public abstract class Factory {
	public static Factory getFactory(String className) {
    	Factory factory = null;
        
        try {
        	factory = (Factory) Class
            	.forName(className)
                .getDeclaredConstructor()
                .newInstance();
        } catch (ClassNotFoundException e) {
        	System.out.println(className + " 클래스가 발견되지 않았습니다.");
        } catch (Exception e) {
        	e.printStackTrace();
        }
        return factory;
    }
    
    public abstract Link createLink(String caption, String url);
    public abstract Tray createTray(String caption);
    public abstract Page createPage(String title, String author);
}
```

- 파라미터의 `className`에는 구체적인 공장의 클래스 이름을 문자열로 지정한다.
- `getFactory` 메소드 안에서는 `Class` 클래스의 `forName` 메소드를 사용하여 해당 클래스를 동적으로 가져와, `getDeclaredConstructor` 메소드로 생성자를 얻고, `newInstance` 메소드로 인스턴스를 생성하여 변수에 저장한다.
- 이렇게 클래스나 생성자 같은 요소를 컴파일러가 다루지 않고 프로그램 자신이 다루는 처리를 리플랙션 `reflection`이라고 한다.
- `getFactory` 메소드에서는 구체적인 공장의 인스턴스를 만들지만, 반환값의 타입은 추상적인 공장이다.

#### `Main` 클래스

```java
import factory.*;

public class Main {
	public static void main(String[] args) {
    	if (args.length() != 2) {
        	System.out.println("Usage : java Main filename.html class.name.of.ConcreteFactory");
           	System.out.println("Example 1 : java Main list.html listFactory.ListFactory");
            System.out.println("Example 2 : java Main div.html divFactory.DivFactory");
            System.exit(0);
        }
        
        String fileName = args[0];
        String className = args[1];
        
        Factory factory = Factory.getFactory(className);
        
        Link blog1 = factory.createLink("Blog 1", "https://example.com/blog1");
        Link blog2 = factory.createLink("Blog 2", "https://example.com/blog2");
        Link blog3 = factory.createLink("Blog 3", "https://example.com/blog3");
        
        Tray blogTray = factory.createTray("Blog site");
        blogTray.add(blog1);
        blogTray.add(blog2);
        blogTray.add(blog3);
        
        Link news1 = factory.createLink("News 1", "http://example.com/news1");
        Link news2 = factory.createLink("News 2", "http://example.com/news2");
        Tray news3 = factory.createLink("News 3");
        news3.add(factory.createLink("News 3 (US)", 	"https://example.com/news3us"));
        news3.add(factory.createLink("News 3 (Korea)", 	"https://example.com/news3kr"));
        
        Tray newsTray = factory.createTray("News Site");
        newTray.add(news1);
        newTray.add(news2);
        newTray.add(news3);
        
        Page page = factory.createPage("Blog and News", "Youngjin.com");
        page.add(blogTray);
        page.add(newsTray);
		
        page.output(fileName);
	}
}
```
- 추상적인 공장을 사용해 추상적인 부품을 제조하고 추상적인 제품을 조립한다. 
추상 클래스만 모아 놓은 `factory` 패키지만 `import`하여 추상 클래스에 대한 구체적인 구현 클래스는 전혀 의존하지 않는다.
- 구체적인 공장의 클래스 이름은 다음과 같이 커맨드 라인에서 지정한다.

```java
Java Main list.html listFactory.ListFactory
```

#### `ListFactory` 클래스

```java
package listFactory;

import factory.Factory;
import factory.Link;
import factory.Page;
import factory.Tray;

public class ListFactory extends Factory {
	@Override
    public Link createLink(String caption, String url) {
    	return new ListLink(caption, url);
    }
    
    @Override
    public Tray createTray(String caption) {
    	return new ListTray(caption);
    }
    
    @Override
    public Page createPage(String title, String author) {
    	return new ListPage(title, author);
    }
}
```
- `Factory` 클래스의 추상 메소드 `createLink`, `createTray`, `createPage`를 구현한다.

#### `ListLink` 클래스

```java
package listFactory;

import factory.Link;

public class ListLink extends Link {
	public ListLink(String caption, String url) {
    	super(caption, url);
    }
    
    @Override
    public String makeHTML() {
    	return " <li><a href=\"" + url + "\">" + caption + "</a></li>\n";
    }
}
```
- `Link` 추상 클래스의 하위 클래스로, `makeHTML` 메소드를 구현한다.

#### `ListTray` 클래스

```java
package listFactory;

import factory.Tray;
import factory.Item;

public class ListTray extends Tray {
	public ListTray(String caption) {
    	super(caption);
    }
    
    @Override
    public String makeHTML() {
    	StringBuilder sb = new StringBuilder();
        
        sb.append("<li>\n");
        sb.append(caption);
        sb.append("\n<ul>\n");
        
        for (Item item : tray) {
        	sb.append(item.makeHTML());
        }
        
        sb.append("</ul>\n");
        sb.append("</li>\n");
        return sb.toString();
    }
}
```
- 개개의 `Item`을 개별 `makeHTML` 메소드를 통해 `HTML` 태그로 만든다. 
- 이 때 변수 `item`의 내용이 `ListLink`의 인스턴스인지, `ListTray`의 인스턴스인지 신경 쓸 필요가 없다. 
- `ListLink`, `ListTray`는 모두 `Item`의 하위 클래스이므로 `item` 변수의 인스턴스가 어떤 클래스의 `makeHTML` 메소드를 사용해야할 지 알고 있다.

#### `ListPage` 클래스

```java
package listFactory;

import factory.Item;
import factory.Page;

public class ListPage extends Page {
	public ListPage(String title, String author) {
    	super(title, author);
    }
    
    @Override
    public String makeHTML() {
    	StringBuilder sb = new StringBuilder();
        sb.append("<!DOCTYPE html>\n");
        sb.append("<html><head><title>");
        sb.append(title);
        sb.append("</title></head>\n");
        sb.append("<body>\n");
        
        sb.append("<h1>");
        sb.append(title);
        sb.append("</h1>\n");
        
        sb.append("<ul>\n");
        for (Item item : content) {
        	sb.append(item.makeHTML());
        }
        sb.append("</ul>\n");
        
        sb.append("<hr><address>");
        sb.append(author);
        sb.append("</address>\n");
        sb.append("</body></html>\n");
        
        return sb.toString();
    }
}
```
- `for`문에서 사용되는 `content`는 `Page` 클래스에서 상속받은 필드이다.

#### `DivFactory` 클래스

```java
package divFactory;

import factory.Factory;
import factory.Link;
import factory.Page;
import factory.Tray;

public class ListFactory extends Factory {
	@Override
    public Link createLink(String caption, String url) {
    	return new DivLink(caption, url);
    }
    
    @Override
    public Tray createTray(String caption) {
    	return new DivTray(caption);
    }
    
    @Override
    public Page createPage(String title, String author) {
    	return new DivPage(title, author);
    }
}
```
- `divXX` 클래스는 `div`, `style` 태그를 사용해 디자인한 `HTML` 문서를 만드는 구현 클래스이다.
- `Factory` 클래스의 추상 메소드 `createLink`, `createTray`, `createPage`를 구현한다.

#### `DivLink` 클래스

```java
package divFactory;

import factory.Link;

public class DivLink extends Link {
	public DivLink(String caption, String url) {
    	super(caption, url);
    }
    
    @Override
    public String makeHTML() {
    	return "<div class=\"LINK\"><a href=\"" + url + "\">" + caption + "</a></div>\n";
    }
}
```
- `Link` 추상 클래스의 하위 클래스로, `makeHTML` 메소드를 구현한다.

#### `DivTray` 클래스

```java
package divFactory;

import factory.Tray;
import factory.Item;

public class DivTray extends Tray {
	public DivTray(String caption) {
    	super(caption);
    }
    
    @Override
    public String makeHTML() {
    	StringBuilder sb = new StringBuilder();
        
        sb.append("<p><b>");
        sb.append(caption);
        sb.append("</b></p>\n");
        sb.append("<div class=\"TRAY>\">");
        
        for (Item item : tray) {
        	sb.append(item.makeHTML());
        }
        
        sb.append("</div>\n");
        
        return sb.toString();
    }
}
```

#### `DivPage` 클래스

```java
package divFactory;

import factory.Item;
import factory.Page;

public class DivPage extends Page {
	public DivPage(String title, String author) {
    	super(title, author);
    }
    
    @Override
    public String makeHTML() {
    	StringBuilder sb = new StringBuilder();
        sb.append("<!DOCTYPE html>\n");
        sb.append("<html><head><title>");
        sb.append(title);
        sb.append("</title><style>\n");
        
        sb.append("</style></head><body>\n");
        
        sb.append("<h1>");
        sb.append(title);
        sb.append("</h1>\n");
        
        for (Item item : content) {
        	sb.append(item.makeHTML());
        }
        
        sb.append("<hr><address>");
        sb.append(author);
        sb.append("</address>\n");
        sb.append("</body></html>\n");
        
        return sb.toString();
    }
}
```
- `for`문에서 사용되는 `content`는 `Page` 클래스에서 상속받은 필드이다.


### `Abstract Factory` 패턴의 등장인물
#### `Link`, `Tray`, `Page` 클래스
- 추상적인 제품 `AbstractProduct` 역할을 맡아 추상적인 부품이나 제품의 인터페이스 `API`를 결정한다.

#### `Factory` 클래스
- 추상적인 공장 `Abstract Factory` 역할을 맡아 추상적인 제품 역할의 인스턴스를 만들기 위한 인터페이스 `API`를 결정한다.

#### `Main` 클래스
- 추상적인 제품이나 추상적인 공장 역할의 인터페이스 `API`만 사용해 작업한다. 
- 구체적인 제품이나 구체적인 공장에 대해서는 모른다.

#### `ListLink` ... `ListPage`, `DivLink` ... `DivPage` 클래스
- 구체적인 제품 `ConcreteProduct` 역할을 맡아 추상적인 제품 역할의 인터페이스를 구현한다.

#### `ListFactory`, `DivFactory` 클래스
- 구체적인 공장 `ConcreteFactory` 역할을 맡아 추상적인 공장 역할의 인터페이스를 구현한다.


### 책에서 제시하는 힌트
#### 구체적인 공장을 새로 추가하는 건 간단하다.
- 예제 프로그램처럼 해야할 일은 추상 클래스를 구현하는 하위 클래스를 만들고 클래스 내 추상 메소드를 구현하는 일 뿐이다.
- 아무리 구체적인 공장을 추가하더라도 추상적인 공장이나 `Main` 부분을 수정할 필요는 전혀 없다.

#### 부품을 새로 추가하는 건 어렵다.
- 가령 예제 프로그램의 추상적인 공장에 `Picture`라는 부품을 추가한다고 하면 이미 존재하는 모든 구체적인 공장 전부를 `Picture`에 대응하도록 수정해야 한다.

#### 인스턴스를 만드는 다양한 방법
- `new` 예약어
- `clone` 메소드
- `newInstance` 메소드: `Class`의 인스턴스를 바탕으로 그 `Class`가 나타내는 클래스의 인스턴스를 만들 수 있다.
