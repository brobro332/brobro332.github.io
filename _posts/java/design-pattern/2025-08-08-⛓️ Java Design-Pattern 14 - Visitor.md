---
title: ⛓️ Java Design-Pattern 14 - Visitor
date: 2025-08-08 22:15:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Visitor` 패턴이란?
- 데이터 구조와 처리를 분리한다. 
- 즉 데이터 구조 안을 돌아다니는 방문자를 준비하여 처리를 맡긴다.
- 새로운 처리를 추가하고 싶을 때는 새로운 방문자를 만든다.
- 데이터 구조에서는 문을 두드리는 방문자를 받는다.


### 예제 프로그램
- 다음은 파일과 디렉터리로 구성된 데이터 구조 안을 방문자가 돌아다니며 파일 목록을 표시하는 프로그램이다.

![](/assets/image/Pasted%20image%2020250808222048.png)

#### `Visitor` 클래스

```java
public abstract class Visitor {
	public abstract void visit(File file);
    public abstract void visit(Directory directory);
}
```
- 방문자를 나타내는 추상 클래스이다. 
- 이 방문자는 방문하는 곳의 데이터구조에 의존한다. 여기서는 `File`과 `Directory`에 해당한다.
- `visit` 메소드가 오버로드 되어 있다.

#### `Element` 인터페이스

```java
public interface Element {
	public abstract void accept(Visitor v);
}
```
- 방문자를 받아들이는 인터페이스이다.

#### `Entry` 클래스

```java
public abstract class Entry implements Element {
	public abstract String getName();
    public abstract int getSize();
    
    @Override
    public String toString() {
    	return getName() + " (" + getSize() + ")";
    }
}
```
- `Element` 인터페이스를 구현하는 추상 클래스이다. 
- 실제로 이 클래스를 구체화하는 것은 `File` 또는 `Directory` 클래스이다.

#### `File` 클래스

```java
public class File extends Entry {
	private String name;
	private int size;
    
    public File(String name, int size) {
    	this.name = name;
        this.size = size;
    }
    
    @Override
    public String getName() {
    	return name;
    }
    
    @Override
    public int getSize() {
    	return size;
    }
    
    @Override
    public void accept(Visitor v) {
    	v.visit(this);
    }
}
```
- `accept` 메소드는 방문자의 `visit` 메소드를 호출함으로써 방문한 `File` 인스턴스를 방문자에게 알려준다.

#### `Directory` 클래스

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class Directory extends Entry implements Iterable<Entry> {
	private String name;
    private List<Entry> directory = new ArrayList<>();

	public Directory(String name) {
    	this.name = name;
    }
	
    @Override
    public String getName() {
    	return name;
    }
    
    @Override
    public int getSize() {
    	int size = 0;
        for (Entry entry : directory) {
        	size += entry.getSize();
        }
        return size;
    }
    
    public Entry add(Entry entry) {
    	directory.add(entry);
        return this;
    }
    
    @Override
    public Iterator<Entry> iterator() {
    	return directory.iterator();
    }
    
    @Override
    public void accept(Visitor v) {
    	v.visit(this)
    }
}
```
- `iterator` 메소드는 디렉터리에 포함된 디렉터리 엔트리(파일, 디렉터리) 목록을 얻기 위한 `Iterator<Entry>`를 반환한다.
- `accept` 메소드는 방문자의 `visit` 메소드를 호출함으로써 방문한 `Directory` 인스턴스를 방문자에게 알려준다.

#### `ListVisitor` 클래스

```java
public class ListVisitor extends Visitor {
	private String currentDir = "";
    
    @Override
    public void visit(File file) {
    	System.out.println(currentDir + "/" + file);
    }
    
    @Override
    public void visit(Directory directory) {
    	System.out.println(currentDir + "/" + directory);
        String saveDir = currentDir;
        currentDir = currentDir + "/" + directory.getName();
        for (Entry entry : directory) {
        	entry.accept(this);
        }
        currentDir = saveDir;
    }
}
```
- `Visitor` 클래스를 구체화한 하위 클래스이다. 
- 즉 실제 방문자 역할을 수행한다.
- `currentDir` 필드에는 현재 바라보는 디렉터리 이름을 저장한다.
- `visit` 메소드는 각 인스턴스에 해야 할 처리를 기술하였다.
- 결국 `accept` 메소드와 `visit` 메소드는 서로를 호출하게 된다. 
- 보통 재귀적 호출은 자기 자신을 호출하는 반면, 이는 상당히 복잡한 재귀적 메소드 호출이다.

#### `Main` 클래스

```java
public class Main {
	public static void main(String[] args) {
    	System.out.println("Making root entries...");
        Directory rootDir = new Directory("root");
        Directory binDir = new Directory("bin");
        Directory tmpDir = new Directory("tmp");
        Directory usrDir = new Directory("usr");
        
        rootDir.add(binDir);
        rootDir.add(tmpDir);
        rootDir.add(usrDir);
        
        binDir.add(new File("vi", 10000));
        binDir.add(new File("latex", 20000));
    	
        rootDir.accept(new ListVisitor());
        
        System.out.println();
        
        System.out.println("Making user entries...");
        Directory youngjin = new Directory("youngjin");
        Directory gildong = new Directory("gildong");
        Directory dojun = new Directory("dojun");
        
        usrDir.add(youngjin);
        usrDir.add(gildong);
        usrDir.add(dojun);
        
        youngjin.add(new File("diary.html", 100));
        youngjin.add(new File("Composite.java", 200));
        gildong.add(new File("memo.txt", 300));
		dojun.add(new File("game.doc", 400));
        dojun.add(new File("junk.mail", 500));
        
        rootDir.accept(new ListVisitor());
    }
}
```
- `Composite` 패턴의 `Main` 클래스와 거의 동일하다. 
- 단 `Composite` 패턴에서는 `printList`라는 메소드로 디렉터리를 출력하는 반면, 해당 패턴에서는 방문자가 디렉터리를 출력한다. 
- 디렉터리 출력도 데이터 구조 내의 각 요소에 대한 처리이기 때문이다.


### `Visitor`와 `Element`의 상호 호흡

![](/assets/image/Pasted%20image%2020250808222422.png)
- `File`, `Directory` 인스턴스에 대해서는 `accept` 메소드가 호출된다.
- `accept` 메소드는 각 인스턴스에서 한 번만 호출된다.
- `ListVisitor`의 인스턴스에 대해서는 `visit` 메소드가 호출된다.
- `visit` 메소드를 처리하는 것은 하나의 `ListVisitor` 클래스의 인스턴스이다.
- 방문자 `ListVisitor`에게 `visit` 메소드 처리가 집중되는 모습을 이해해야 한다.


### `Visit` 패턴의 등장인물
#### `Visitor` 클래스
- 데이터 구조의 구체적인 클래스 별로 `visit` 추상 메소드를 선언하는 추상 클래스이다.
- 방문자 `Visitor` 역할을 맡는다.

#### `ListVisitor` 클래스
- 구체적인 방문자 `ConcreteVisitor` 역할을 맡아 방문자의 인터페이스 `API`를 구현한다.
- `ListVisitor`에서 `currentDir` 필드 값이 변화한 것처럼 `visit` 메소드를 처리하는 중에 구체적인 방문자 역할의 내부 상태가 변화하기도 한다.

#### `Element` 인터페이스
- 요소 `Element` 역할을 맡아 방문자가 방문할 곳을 나타내며, 방문자를 받아들이는 `accept` 메소드를 선언한다.

#### `File`, `Director` 클래스
- `Element`의 인터페이스 `API`를 구현하는 구체적인 요소 `ConcreteElement` 역할을 맡았다.

#### `Directory` 클래스
- 해당 클래스는 구체적인 요소 역할과 함께 오브젝트 구조 `ObjectStructure` 역할을 맡았다. 
- 즉 1인 2역을 맡았다.
- 구체적인 방문자가 각각의 요소를 취급할 수 있는 메소드를 갖추고 있다. 
- 예제 프로그램에서는 `iterator` 메소드를 통해 요소 집합을 다룬다.


### 책에서 제시하는 힌트
#### 왜 이렇게 복잡한 일을 하는가?
- `element`는 `visitor`를 `accept` 하고, `visitor`는 `element`를 `visit` 한다. 
- 이것을 더블 디스패치라고 한다.
- `Visitor` 패턴의 목적은 처리를 데이터 구조와 분리하는 것이다. 
- 데이터 구조는 요소를 집합으로 정의하거나 요소 사이를 연결해주는 역할을 하는데, 그 처리는 방문자 `API`와 구체적인 방문자를 두어 `File`, `Directory` 클래스의 부품으로서의 독립성을 높일 수 있다.
- 가령 처리 내용을 `File`, `Directory` 클래스의 메소드로 프로그램을 작성해버릴 경우 새로운 처리를 추가하고 싶다면 `File`, `Directory` 클래스를 수정해야 한다.

#### `The Open-Closed Principle` : 확장에 대해서는 열고, 수정에 대해서는 닫는다.
- `OCP` 원칙에 의하면 클래스를 설계할 때는 특별한 이유가 없는 한 확장을 허용해야 한다.
- 동시에 확장할 때마다 기존 클래스를 수정할 필요가 없게 해야 한다.
- 클래스에 대한 요구는 기능을 확장하는 쪽으로 빈번하게 변화한다. 
- 그렇기에 기능을 확장할 수 없으면 곤란하고, 이미 만들어 테스트까지 마친 클래스를 수정하면 소프트웨어의 품질을 떨어뜨릴 위험이 있다.
- 궁극적으로는 `OCP` 원칙이 디자인 패턴, 객체 지향의 목적이다.

#### 역할 추가
- 구체적인 방문자 역할 추가는 쉽다. 
- 구체적인 처리는 구체적인 방문자 역할에 맡기고, 그 처리를 위해 구체적인 요소 역할을 수정할 필요가 전혀 없기 때문이다.
- 반면 구체적인 요소 역할 추가는 어렵다. 
- 가령 `Entry` 클래스의 하위 클래스로 `Device` 클래스를 만든다고 하면 `Visitor` 클래스의 `visit(Device device)` 메소드를 새로 만들어야 하고, `Visitor` 클래스의 하위 클래스에 모두 `visit(Device device)` 메소드를 구현해야 한다.

#### 방문자가 처리하려면 무엇이 필요한가?
- 방문자는 데이터 구조에서 필요한 정보를 취득하여 동작한다. 
- 필요한 정보를 얻지 못하면 방문자가 제대로 일을 할 수 없다. 
- 반면 공개하지 말아야 할 정보까지 공개해 버리면, 미래의 데이터 구조를 변경하기가 어려워진다.
- 가령 예제 프로그램에서는 `visit(Directory directory)` 안에서 각각의 디렉터리 엔트리에 대해 `accept` 메소드를 실행하기 위해 `iterator` 메소드를 제공해야 한다.