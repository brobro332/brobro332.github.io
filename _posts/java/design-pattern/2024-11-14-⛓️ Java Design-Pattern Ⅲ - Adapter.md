---
title: ⛓️ Java Design-Pattern Ⅲ - Adapter
date: 2024-11-14 05:16:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Adapter` 패턴이란?
- 직류 `12` 볼트로 동작하는 노트북을 교류 `100` 볼트 `AC` 전원에 연결할 때 우리는 `AC` 어댑터라는 장치를 사용한다.
- 제공된 것과 필요한 것 그 사이에서 `AC` 어댑터가 `Adapter` 역할을 하는 것이다.
- `Adapter` 패턴은 프로그래밍 차원에서도 이미 제공된 코드를 그대로 사용할 수 없을 때 필요한 형태로 변환하여 이용하는 경우 사용한다. 
- 그래서 `Adapter` 패턴은 `Wrapper` 패턴이라고 표현한다.
- `Adapter` 패턴은 다음 두 가지 방법으로 구현할 수 있다.
	1. 클래스 상속을 이용하여 중간 역할을 수행한다.
	2. 중간 역할을 인스턴스에 위임한다.


### 예제 프로그램
- 다음 프로그램은 업무의 세부 정보를 출력하는 뷰어를 구현한 것이다.
- 위에서 언급한 클래스를 상속하는 방법과 위임을 사용하는 방법 모두 살펴볼 것이다.

#### ✅ 클래스를 상속하는 방법

![](/assets/image/Pasted%20image%2020250528234749.png)
```java
public class TaskViewer  {
	private Task task;
	
	public TaskViewer(Task task) {
		this.task = task;
	}
	
	public void showTaskInfo() {
		System.out.println(task.getName());
	}
}
```
- `TaskViewer` 클래스는 우리에게 주어진 것이라고 가정한다.
- 해당 클래스에 존재하는 `showTaskInfo()` 메서드는 업무의 이름만 알 수 있으므로 사용자 입장에서는 얻을 수 있는 정보가 제한적이다.

```java
public interface UpgradedTaskViewer {
	public abstract void showDetailInfo();
}
```
- `UpgradedTaskViewer` 인터페이스는 우리가 필요로 하는 것이다. 

```java
public class TaskDetailViewer extends TaskViewer implements upgradedTaskViewer {
	public TaskDetailViewer(Task task) {
		super(task);
	}
	
	@Override
	public void showDetailInfo() {
		System.out.println("=============== 업무 ===============");
		
		showTaskInfo();
		System.out.println(task.getDescription());
		
		System.out.println("============= 하위업무 =============");
		
		List<SubTask> subTasks = task.getSubTasks();
		for (SubTask subTask : subTasks) {
			System.out.println(subTask.getName());
		}
	}
}
```
- `TaskDetailViewer` 클래스는 어댑터 역할을 한다.
- `TaskViewer` 클래스를 상속하는 동시에 `upgradeTaskViewer` 인터페이스를 구현한다. 
- `showDetailInfo()` 메서드는 `TaskViewer`의 `showTaskInfo()` 메서드에 살을 붙여 사용자에게 더욱 풍부한 정보를 제공한다.


```java
public class Main {
	public static void main(String[] args) {
		Task task = new Task(); // 정보가 인스턴스 필드에 초기화되어 있다고 가정
		UpgradedTaskViewer u = new TaskDetailViewer(task);
		u.showDetailInfo();
	}
}
```
- 메인 클래스에는 `TaskViewer` 클래스와 `TaskDetailViewer` 클래스의 구현은 완전히 숨겨져 있다. 
- 그러므로 메인 클래스의 수정 없이 `UpgradedTaskViewer` 인터페이스의 구현을 변경할 수 있다.

#### ✅ 위임을 상속하는 방법
![](/assets/image/Pasted%20image%2020250528235504.png)
```java
public class TaskViewer  {
	private Task task;
	
	public TaskViewer(Task task) {
		this.task = task;
	}
	
	public void showTaskInfo() {
		System.out.println(task.getName());
	}
	
	public void getTask() {
		return this.task;
	}
}
```
- `TaskViewer` 클래스는 상속을 사용한 방법과 동일하다.
- 구현 방법이 바뀌더라도 우리가 필요한 건 바뀌지 않기 때문이다. 

```java
public abstract class UpgradedTaskViewer {
	public abstract void showDetailInfo();
}
```
- `UpgradedTaskViewer` 클래스는 인터페이스에서 추상 클래스로 바뀌었다.

```java
public class TaskDetailViewer extends UpgradedTaskViewer {
	private TaskViewer taskViewer;
	
	public TaskDetailViewer(Task task) {
		this.taskViewer = new TaskViewer(task);
	}
	
	@Override
	public void showDetailInfo() {
		System.out.println("=============== 업무 ===============");
		
		taskViewer.showTaskInfo();
		System.out.println(taskViewer.getTask().getDescription());
		
		System.out.println("============= 하위업무 =============");
		
		List<SubTask> subTasks = taskViewer.getTask().getSubTasks();
		for (SubTask subTask : subTasks) {
			System.out.println(subTask.getName());
		}
	}
}
```
- `TaskDetailViewer` 클래스는 어댑터 역할을 한다.
- `TaskViewer` 클래스를 필드로 가지며 해당 필드에 모든 걸 위임한다.


```java
public class Main {
	public static void main(String[] args) {
		Task task = new Task(); // 정보가 인스턴스 필드에 초기화되어 있다고 가정
		UpgradedTaskViewer u = new TaskDetailViewer(task);
		u.showDetailInfo();
	}
}
```
- 메인 클래스도 두 방법이 동일하다.


### 체크 포인트
#### ✅ 어떤 경우에 사용할까?
- 프로그래밍은 늘 백지상태에서 시작하는 것은 아니다. 
- 이미 존재하는 클래스를 이용하는 경우도 흔하다.
- `Adapter` 패턴은 기존 클래스를 감싸 필요한 클래스를 만들어 내는 패턴이다.
- 버그가 발생하더라도 기존 클래스에는 버그가 없는 것을 알고 있으므로 `Adapter` 역할의 클래스를 중점적으로 살펴보면 되기 때문에 프로그램 검사가 매우 편해진다.

#### ✅ 단순히 기존 클래스를 수정하면 되지 않나?
- 만약 이미 만들어진 클래스가 있고 새로운 `API`에 맞춘다고 가정하면 우리들은 기존 클래스를 만져서 수정하고 만다.
- 하지만 그렇게 하면 동작 테스트가 이미 끝났음에도 손을 댔으니 다시 테스트해야 한다.
- `Adapter` 패턴의 목적은 기존 클래스를 전혀 수정하지 않고 새로운 `API`를 개발하는 것이다. 
- 또한 기존 클래스의 소스 코드가 반드시 필요한 것은 아니고 기존 클래스의 사양만 알면 새로운 클래스를 만들 수 있다.

#### ✅ 버전 업그레이드와 호환성
- 소프트웨어는 버전 업그레이드가 필요하다. 
- 하지만 이때 구버전과의 호환성이 문제가 된다.
- `Adapter` 패턴은 신버전과 구버전을 공존시키고 유지보수까지 편하게 할 수 있도록 도와준다.

#### ✅ 동 떨어진 클래스
- `Adapter` 역할과 `Target` 역할의 기능이 너무 동 떨어진 경우에는 `Adapter` 패턴을 사용할 수 없다.

#### ✅ 상속과 위임, 어느 쪽을 사용해야 할까?
- 일반적으로 상속을 사용하는 것보다 위임을 사용하는 편이 문제가 적다. 
- 그 이유는 상위 클래스의 내부 동작을 자세히 모르면 상속을 효과적으로 사용하기 어려운 경우가 많기 때문이다.


### 회고
- 사실 예제 프로그램이 책의 내용과는 다르다. 
- 책에 있는 코드를 그저 타이핑할 때보다 이 편이 더욱 이해가 잘 되는 것 같다.