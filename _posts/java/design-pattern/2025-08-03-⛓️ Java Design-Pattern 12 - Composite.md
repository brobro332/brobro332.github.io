---
title: ⛓️ Java Design-Pattern 12 - Composite
date: 2025-08-03 14:35:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Composite` 패턴이란?
- `Composite`란 혼합물, 복합물이라는 뜻으로, 그릇과 내용물을 동일시하여 재귀적인 구조를 만드는 디자인 패턴이다.


### 예제 프로그램
- 다음은 파일과 디렉터리를 도식적으로 표현하는 프로그램이다.

![](/assets/image/Pasted%20image%2020250803143724.png)

- `File` 클래스는 파일을 나타내며, `Directory` 클래스는 디렉터리를 나타낸다. 
- `Entry` 클래스는 그 둘을 취합하는 형태인 디렉터리 엔트리를 나타낸다.

#### `Entry` 클래스

```java
public abstract class Entry {
	public abstract String getName();
    
    public abstract int getSize();
    
    public void printList() {
    	printList("");
    }
    
    protected abstract void printList(String prefix);
    
    @Override
    public String toString() {
    	return getName() + "(" + getSize() + ")";
    }
}
```
- 디렉터리 엔트리를 표현하는 추상 클래스이다. 
- 하위 클래스로 `File`, `Directory` 클래스가 만들어진다.
- 디렉터리 엔트리는 이름과 사이즈를 갖고 있으며, 각각을 얻는 `getter` 메소드는 하위 클래스에 구현을 맡긴다.
- `printList` 메소드는 인수 여부에 따라 오버로딩 되어 있다.

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
    protected void printList(String prefix) {
	    System.out.println(prefix + "/" + this);
    }
}
```
- `printList` 메소드처럼 객체를 출력하면 자동으로 해당 객체의 `toString()`이 호출된다.
- `Entry` 클래스에서 선언된 추상 메소드를 모두 구현했으므로 `File` 클래스는 추상 클래스가 아니다.

#### `Directory` 클래스

```java
import java.util.ArrayList;
import java.util.List;

public class Directory extends Entry {
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
    
    @Override
    protected void printList(String prefix) {
    	System.out.println(prefix + "/" + this);
        for (Entry entry : directory) {
        	entry.printList(prefix + "/" + name);
        }
    }
    
 	public Entry add(Entry entry) {
    	directory.add(entry);
        return this;
    }
}
```
- `Directory` 클래스는 사이즈를 동적으로 계산해서 구하므로 `size` 필드가 따로 없다.
- `size += entry.getSize();`를 통해 사이즈를 구하는데, `entry`가 `File` 클래스의 인스턴스이든, `Directory` 클래스의 인스턴스이든 `Entry`의 하위 클래스의 인스턴스이므로 안심하고 `getSize` 메소드를 호출할 수 있다.
- 이외에도 `add`, `printList` 등의 메소드도 `entry`가 파일인지 디렉토리인지 조사하지 않고 그릇과 내용물을 동일시하여 처리한다.

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
        
        rootDir.printList();
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
        
        rootDir.printList();
    }
}
```
- `Main` 클래스의 파일과 디렉터리 계층을 그리면 다음과 같다.

```bash
root
├── bin
│   ├── vi
│   └── latex
├── tmp
└── usr
    ├── yongjin
	│	├── diary.html
	│   └── Composite.java
    ├── gildong
	│   └── memo.txt
    └── dojun
	    ├── game.doc
	    └── junk.mail
```


### `Composite` 패턴의 등장인물
#### `File` 클래스
- 내용물을 나타내는 잎 `Leaf` 역할을 한다. 
- 이 안에는 다른 것을 넣을 수 없다.

#### `Directory` 클래스
- 잎 역할이나 복합체 역할을 넣을 수 있는 복합체 `Composite` 역할을 한다.

#### `Entry` 클래스
- 잎 역할과 복합체 역할을 동일시하기 위한 상위 클래스 `Component` 역할을 한다.

#### `Main` 클래스
- 해당 패턴의 사용자인 의뢰자 `Client` 역할을 한다.


### 책에서 제시하는 힌트
#### 복수와 단수 동일시하기
- `Composite` 패턴은 그릇과 내용물을 동일시하는 패턴인데, 이는 복수와 단수를 동일시한다고 말할 수 있다.
- 예를 들어 키보드 입력, 파일 입력, 네트워크 입력 등의 프로그램 동작 테스트를 모아서 할 때 해당 패턴을 사용할 수 있다. 
- 입력 테스트를 모아 `InputTest`라는 입력 테스트로 만들 수 있고, 동일하게 출력 테스트를 만들 수 있다. 
- 심지어 `InputOutputTest`라는 입출력 테스트까지 만들 수 있다.

#### `add`는 어디에 두어야 할까?
- 예제 프로그램과 `GoF` 책에서는 `add`, `remove`, `getChild` 등 자식을 조작하는 메소드를 복합체 역할에서 정의하였다. 
- 만약 잎 역할에서 자식을 조작하는 요청이 발생하면 오류 처리가 필요하다.
- 그렇다면 `Directory` 클래스와 `Entry` 클래스 중에서는 자식을 조작하는 메소드를 어디에 두는 것이 더 좋을까? 
- 이는 그릇과 내용물을 동일시한 결과로 얻어지는 것은 무엇인가?에 대한 대답이다.
- 결론적으로 설계자는 해당 클래스가 가져야 할 책무를 명확히 해야하며, 이러한 방향에 더 적합한 위치에 메소드를 위치시키는 것이 적절하다.

#### 재귀적 구조는 모든 장면에서 등장한다.
- 일반적으로 트리 구조로 된 데이터 구조는 `Composite` 패턴에 해당한다. 
- 문장의 글머리 기호 항목 안에 다시 항목이 포함되는 것을 그 예로 들 수 있다.