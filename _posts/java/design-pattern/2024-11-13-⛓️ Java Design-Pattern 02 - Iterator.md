---
title: ⛓️ Java Design-Pattern 02 - Iterator
date: 2024-11-13 05:41:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Iterator` 패턴이란?
```java
// 반복문
for (int i = 0; i < arr.length; i++) {
	System.out.println(arr[i]);
}

// 요소
arr[0]
arr[1]
arr[2]

// ...

arr[i]

// ...

arr[arr.length - 1]
```
- `Iterator` 패턴은 반복문에서 사용되는 변수 `i`의 기능을 추상화하여 일반화한 패턴이다.
- 해당 패턴은 요소가 모여 있을 때 이를 순서대로 가리키며 전체를 검색하고 동일 처리를 반복한다.


### 예제 프로그램
![](/assets/image/Pasted%20image%2020250528231816.png)
- 위 다이어그램은 업무에 해당하는 하위 업무명을 차례대로 표시하는 프로그램을 나타낸다.
- 업무와 하위 업무는 필자가 진행하는 토이 프로젝트 도메인이다. 
- 프로젝트는 업무로 구성되어 있고, 각 업무는 하위 업무로 구성할 예정이다.

```java
public interface Iterable<E> {
	public abstract Iterator<E> iterator();
}
```
- 반복 가능한 객체다.
- `iterator()` 메서드를 통해 실제로 반복을 수행하는 `Iterator<E>` 반복자를 반환한다.

```java
public interface Iterator<E> {
	public abstract boolean hasNext();
	public abstract E next();
}
```
- 반복을 수행하는 객체다.
- `hasNext()` 메서드를 통해 다음 요소의 존재 여부를 반환한다.
- `next()` 메서드를 통해 요소를 하나 반환하고, 인덱스가 다음 요소를 가리키도록 한다.

```java
public class SubTask {
	private String name;
	private String description
	
	public SubTask(String name, String description) {
		 this.name = name;
		 this.description = description;
	}
	
	public String getName() {
		return name;
	}
	
	public String getDescription() { 
		return description;
	}
}
```
- 하위업무에 해당하는 클래스다.

```java
import java.util.Iterator;

public class Task implements Iterable<SubTask> {
	private SubTask[] subTasks;
	private int last = 0;
	
	public Task(int maxsize) {
		this.subTasks = new SubTasks[maxsize];
	}
	
	public Book getSubTaskAt(int index) {
		return subTasks[index];
	}
	
	public void appendSubTask(SubTask subTask) {
		 this.subTasks[last] = subTask;
		 last++;
	}
	
	public int getLength() {
		return last;
	}
	
	@Override
	public Iterator<SubTask> iterator {
		return new TaskIterator(this);
	} 
}
```
- 업무에 해당하는 클래스다.
- 생성자의 인수를 통해 해당 업무의 최대 하위 업무 개수가 정해진다.
- 필드 접근제한자를 `private`로 선언하여 외부에서 접근하지 못하도록 방지한다.
- `getSubTaskAt(int index)` 메서드를 통해 현재 인덱스가 어떤 하위 업무를 가리키는 지 알 수 있다.
- `appendSubTask(SubTask subTask)` 메서드를 통해 하위 업무를 추가할 수 있다.
- `getLength()` 메서드를 통해 현재 하위 업무의 개수를 알 수 있다.
- `iterator()` 메서드를 통해 하위 업무를 차례대로 순회하며 처리할 수 있는 `TaskIterator()` 클래스의 인스턴스를 생성한다.

```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public class TaskIterator implements Iterator<Book> {
	private Task task;
	private int index;
	
	public TaskIterator(Task task) {
		this.task = task;
		this.index = 0;
	}
	
	@Override
	public boolean hasNext() {
		if (index < task.getLength()) {
			return true;
		} else {
			return false;
		}
	}
	
	@Override
	public SubTask next() {
		if (!hasNext) {
			throw new NoSuchElementException();
		}
		SubTask subTask = task.getSubTaskAt(index);
		index++;
		return subTask;
	}
}
```
- 업무 반복자에 해당하는 클래스다.
- `Task` 필드는 현재 대상으로 하는 업무이며, `index` 필드는 가리키고 있는 하위 업무의 순서를 의미한다.

```java
public class Main {
	public static void main(String[] args) {
		Task task = new Task(3);
		task.appendSubTask(new SubTask("설계", "프로젝트 설계를 의미한다."));
		task.appendSubTask(new SubTask("구현", "설계대로 구현한다."));
		task.appendSubTask(new SubTask("테스트", "구현한 부분을 테스트한다."));
		
		// 명시적 사용
		Iterator<SubTask> it = task.iterator();
		while (it.hasNext) {
			SubTask subTask = it.next();
			System.out.println(subTask.getName());
			System.out.println(subTask.getDescription());
		}
		
		// 확장 for문 사용
		/*
		for (SubTask subTask : Task) {
			System.out.println(subTask.getName());
			System.out.println(subTask.getDescription());
		}
		*/
	}
}
```
- 프로그램의 메인 클래스이다.
- 명시적 사용 코드와 `for-each`문을 사용한 코드는 완전히 같은 동작을 수행한다.
- 일반적으로 자바의 `for-each`문은 `Iterator` 인터페이스를 구현한 클래스의 인스턴스의 경우, 내부적으로 `Iterator`를 사용하여 처리한다. 


### 체크 포인트
#### ✅ 어떻게 구현하든 `Iterator`를 사용할 수 있다.
```java
Iterator<SubTask> it = task.iterator();
while (it.hasNext) {
	SubTask subTask = it.next();
	System.out.println(subTask.getName());
	System.out.println(subTask.getDescription());
}
```
- `Iterator` 패턴을 사용함으로써 구현과 분리하여 반복할 수 있다.
- 상기 코드에서 `while`문은 업무 클래스 구현에 의존하지 않는다.
- 업무 인스턴스의 `subTasks` 필드를 배열에서 `java.util.ArrayList`로 변경한다고 하자. 
- 업무 클래스를 어떻게 변경하든 `iterator()` 메서드를 갖고 있고, `hasNext()`와 `next()`가 바르게 구현된 올바른 `Iterator<SubTask>`을 반환하면 `while`문은 변경하지 않아도 동작한다. 
- 이러한 의존성 분리는 코드 재사용을 촉진한다.

#### ✅ 추상 클래스와 인터페이스를 사용하자.
- 구체적인 클래스만 사용하면 클래스 사이의 결합이 강해져 부품으로 재사용하기 어렵다. 
- 결합을 약화하고 재사용하기 쉽게 하고자 추상 클래스나 인터페이스를 도입해야 한다.

#### ✅ 반복자의 집합체 의존
- `TaskIterator` 클래스는 업무 클래스가 어떻게 구현되어 있는지 알고 있었기 때문에 `getSubTaskAt(int index)` 메서드를 호출하여 다음 하위 업무를 가져올 수 있었다.
- 이는 업무 클래스의 구현을 바꾼다면 `TaskIterator` 클래스를 수정해야 된다는 말이 된다.
- `Iterable<E>` 클래스와 `Iterator<E>` 클래스가 짝을 이루듯 `Task` 클래스와 `TaskIterator` 클래스도 짝을 이룬다.
- 사실 예제 코드의 메서드 명은 혼동하기 쉽다.  
	- `next()` 메서드는 현재 처리하는 요소를 반환하는지, 다음 요소를 반환하는지 모호하다.
	- 정확히 표현하면 `CurrentElementAndAdvanceToNextPosition()` 메서드가 맞다.

#### ✅ 복수의 반복자
- 현재 어디까지 조사했는지 기억하는 구조를 집합체 역할 외부에 두는 것이 `Iterator` 패턴의 특징 중 하나다.
- 이런 특징에 따라 하나의 구체적인 집합체 역할에 대해 여러 개의 구체적인 반복자 역할을 만들 수 있다.

#### ✅ `deleteIterator()` 메서드는 필요 없다.
- `Java`에서 사용되지 않는 인스턴스는 `Garbage-collection`에 의해 자동으로 삭제된다.


### 회고
- `Java`의 `for-each`문 배후에서는 우리가 작성한 `Iterator` 패턴이 사용된다는 점을 기억해야겠다.