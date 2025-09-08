---
title: ⛓️ Java Design-Pattern 22 - Proxy
date: 2025-08-11 20:49:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Proxy` 패턴이란?
- `Proxy`란 대리인이라는 뜻으로, 일을 해야 할 본인을 대신하는 사람이다.
- 객체지향 프로그램에서는 본인도, 대리인도 객체가 된다.
- 바빠서 그 일을 할 수 없는 본인 객체를 대신해서 대리인 객체가 어느 정도 일을 처리한다.


### 예제 프로그램
- 이번 예제 프로그램은 '이름 붙인 프린터'이다.
- `Main` 클래스는 `PrinterProxy` 인스턴스를 생성하여 이름을 붙이고 그 이름을 표시한다.
- 이름 설정이나 취득에 대해서는 진짜 `Printer` 인스턴스는 생성되지 않았다.
- 실제로 프린터하는 단계가 되어서야 `PrinterProxy` 클래스는 `Printer` 인스턴스를 생성한다.
- `Printer` 클래스와 `PrinterProxy` 클래스를 동일시하기 위해 `Printable`이라는 인터페이스를 정의했다.
- 여기서는 `Printer` 인스턴스 생성에 오랜 시간이 걸린다는 전제로 프로그램을 작성한다.
- 물론 예제 프로그램일 뿐이므로 5초 정도의 시간을 벌 뿐이다.

![](/assets/image/Pasted%20image%2020250811211450.png)
![](/assets/image/Pasted%20image%2020250811211531.png)

#### `Printer` 클래스

```java
public class Printer implements Printable {
	private String name; // 이름
	
	/* 생성자 */
	public Printer() {
		heavyJob("Printer 인스턴스 생성 중");
	}
	
	/* 생성자(이름 지정) */
	public Printer(String name) {
		this.name = name;
		heavyJob("Printer 인스턴스(" + name + ") 생성 중");
	}
	
	/* 이름 설정 */
	@Override
	public void setPrinterName(String name) {
		this.name = name;
	}
	
	/* 이름 취득 */
	@Override
	public String getPrinterName() {
		return name;
	}
	
	/* 이름 붙여서 표시 */
	@Override
	public void print(String string) {
		System.out.println("=== " + name + " ===");
		System.out.println(string);
	}
	
	/* 무거운 작업이라고 가정 */
	private void heavyJob(String msg) {
		System.out.print(msg);
		
		for (int i = 0; i < 5; i++) {
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e) { }
			System.out.print(".");
		}
		
		System.out.println("완료");
	}
}
```
- 앞서 말한 것처럼 가짜 무거운 작업인 `heavyJob()` 메서드를 갖고 있다.
- `Proxy` 패턴의 핵심은 `Printer` 클래스가 아닌 `PrinterProxy` 클래스에 있다.

#### `Printable` 인터페이스

```java
public interface Printable {
	public abstract void setPrinterName(String name);
	public abstract String getPrinterName();
	public abstract void print(String string);
}
```
- `Printer` 클래스와 `PrinterProxy` 클래스는 동일시하기 위한 것이다.
- 각각 이름 설정, 이름 취득, 프린트 아웃을 위한 메서드이다.
 
#### `PrinterProxy` 클래스

```java
public class PrinterProxy implements Printable {
	private String name;  // 이름
	private Printer real; // 본인
	
	/* 생성자 */
	public PrinterProxy() {
		this.name = "No Name";
		this.real = null;
	}
	
	/* 생성자(이름 지정) */
	public PrinterProxy(String name) {
		this.name = name;
		this.real = null;
	}
	
	/* 이름 설정 */
	@Override
	public synchronized void setPrinterName(String name) {
		if (real != null) {
			// '본인'에게도 설정한다
			real.setPrinterName(name);
		}
		this.name = name;
	}
	
	/* 이름 취득 */
	@Override
	public String getPrinterName() {
		return name;
	}
	
	/* 표시 */
	@Override
	public void print(String string) {
		realize();
		real.print(string);
	}
	
	/* 본인 생성 */
	private synchronized void realize() {
		if (real == null) {
			real = new Printer(name);
		}
	}
}
```
- `Printer` 클래스의 대리인 역할을 하며, `Printable` 인터페이스를 구현한다.
- `name` 필드는 이름을 저장하고 `real` 필드는 본인을 저장한다.
- `setPrinterName()` 메서드는 새로 이름을 설정한다.
- `real`이 `null`이면 단순히 이름을 설정하고, 아니라면 본인에 대해서도 그 이름을 설정한다.
- `getPrinterName()` 메서드는 자신의 `name` 필드 값을 반환한다.
- `print()` 메서드는 대리인이 할 수 있는 범위를 넘어서는 처리이기 때문에, `realize()` 메서드를 호출하여 본인을 생성한다.
- `real` 필드에는 본인이 저장되어 있기 때문에 `real.print()` 메서드를 호출한다.
- 이를 위임이라고 한다.
- `setPrinterName()`, `getPrintName()` 메서드를 여러 번 호출해도 `Printer` 인스턴스는 생성되지 않고, `Printer` 인스턴스 본인이 정말로 필요할 때 생성된다.
- `realize()` 메서드는 단순히 `real` 필드가 `null` 이면 `new` 키워드를 통해 생성하고, 아니라면 아무 일도 하지 않는다.
- 기억해두어야 하는 것은 `Printer` 클래스는 `PrinterProxy`의 존재를 모른다는 점이다.
- 자신이 `PrinterProxy`를 경유해서 호출되는지, 직접 호출되는지 `Printer` 클래스는 모른다.
- 반면 `PrinterProxy` 클래스는 `Printer` 클래스를 알고 있다.
- 즉 `PrinterProxy` 클래스는 `Printer` 클래스와 고정적으로 결합된 부품인 것이다.
- 또한 멀티 쓰레드 환경을 고려해 `setPrinterName()`, `realize()` 메서드는 `synchronized`로 선언되어 있다.

#### `Main` 클래스

```java
public class Main {
	public static void main(String[] args) {
		Printable p = new PrinterProxy("Alice");
		System.out.println("이름은 현재 " + p.getPrinterName() + "입니다.");
		p.setPrinterName("Bob");
		System.out.println("이름은 현재 " + p.getPrinterName() + "입니다.");
		p.print("Hello, world.");
	}
}
```
- `PrinterProxy` 클래스를 경유해서 `Printer` 클래스를 이용하는 클래스이다.
- `Printer` 인스턴스는 `print()` 메서드를 호출한 후에 생성된다.


### `Proxy`  패턴의 등장인물
#### `Printable` 인터페이스
- `Proxy`와 `RealSubject`를 동일시하기 위한 인터페이스를 정의하는 본인(`Subject`) 역을 맡았다.
- `Client`는 `Proxy`와 `RealSubject`를 구별할 필요가 없다.

#### `PrinterProxy` 클래스
- `Client`의 요청을 최대한 처리하고, 정말로 `RealSubject`가 필요할 때 `RealSubject`를 생성하는 대리인(`Proxy`) 역을 맡았다.
- `Subject`에 정의된 인터페이스를 구현한다.

#### `Printer` 클래스
- `Proxy`만으로 감당할 수 없을 때 생성되는 실제 본인(`RealSubject`) 역을 맡았다.
- 이 역할도 `Subject`에 정의된 인터페이스를 구현한다.

#### `Main` 클래스
- `Proxy` 패턴을 이용하는 `Client` 역을 맡았다.


### 책에서 제시하는 힌트
#### 대리인을 사용해 속도 올리기
- 예제 프로그램에서는 `Proxy`를 사용함으로써 실제로 `print()` 메서드를 호출할 때까지 무거운 처리를 최대한 늦출 수 있었다.
- 초기화에 시간이 걸리는 기능이 많은 대규모 시스템을 생각해보면 와 닿을 것이다.
- 가령 시작 시점에 이용하지 않는 기능까지 전부 초기화해버리면 애플리케이션 시작 시간이 길어진다.
- 실제로 그 기능을 사용하는 단계가 되었을 때 초기화하는 편이 그 사용자의 스트레스를 덜어준다.

#### 대리인과 본인을 나눌 필요가 있을까?
- `PrinterProxy` 클래스와 `Printer` 클래스를 나누지 않고 `Printer` 클래스 안에 처음부터 지연 평가 기능을 넣어 둘 수도 있다.
- 그러나 분리를 통해 프로그램이 부품화되면 개별적으로 수정할 수 있다.
- `PrinterProxy` 클래스의 구현을 바꾸면 `Printable` 인터페이스의 무엇을 대리인이 처리하고 무엇을 위임할지 변경 할 수 있다.
- 그런 변경을 하더라도 `Printer` 클래스는 수정할 필요가 전혀 없다.
- 지연 평가를 전혀 하지 않으려면 `Main` 클래스에서 `PrinterProxy` 클래스가 아닌 `Printer` 클래스의 인스턴스를 `new` 키워드로 생성하면 된다.
- `PrinterProxy` 클래스는 `Proxy` 역이라는 기능을 표현하고 있기 때문에, 그 기능을 사용할지 안 할지는 `PrinterProxy`를 사용할지 사용하지 않을지로 결정된다.

#### 대리와 위임
- 대리인 혼자 처리할 수 있는 일은 직접 처리한다.
- 하지만 그렇지 않으면 본인에게 위임한다.
- 원래 현실 세계에서는 본인이 대리인에게 책임을 맡기는데, 디자인 패턴에서는 반대로 되어 있다.

#### 투과적이란?
- `Printer`와 `PrinterProxy`는 같은 `Printable` 인터페이스를 구현하기 때문에, `Main` 클래스에서 호출하는 곳이 어떤 클래스이든 상관하지 않는다.
- `PrinterProxy`를 중간에 두어도, `Printer`를 직접 이용해도 문제 없이 사용할 수 있다.
- 이러한 특성을 투과적이라고 한다.

#### `HTTP` 프록시
- 프록시라고 하면 `HTTP` 프록시를 떠올리는 사람이 있을 수도 있다.
- `HTTP` 프록시는 `HTTP` 서버와 `HTTP` 클라이언트 사이에서 웹 페이지 캐싱 등을 하는 소프트웨어이다.
- 캐싱 또한 `Proxy` 패턴을 적용해서 생각할 수 있다.
- 웹 브라우저가 있는 웹 페이지를 표시할 때 원격지에 있는 웹 서버에 접속해서 그 페이지를 가져오는 것이 아니라, `HTTP` 프록시가 캐싱해 놓은 페이지를 대신 가져온다.
- 최신 정보가 필요할 때나 웹 페이지의 유효 기간이 지났을 때 비로소 웹 서버로 웹 페이지를 가지러 간다.
- 여기서는 웹 브라우저가 `Client`, `HTTP` 프록시가 `Proxy`, 그리고 웹 서버가 `RealSubject`에 해당한다.

#### 다양한 `Proxy`
##### 가상 프록시(`Virtual Proxy`)
- 이 장에서 소개한 `Proxy` 패턴
- 실제로 인스턴스가 필요한 시점에서 생성 및 초기화한다.

##### 원격 프록시(`Remote Proxy`)
- `RealSubject` 역이 네트워크 저편에 있더라도 마치 바로 옆에 있는 것처럼 메서드를 호출할 수 있다.
- `Java RMI` 등이 여기에 해당된다.

##### 보호 프록시(`Access Proxy`)
- `Access Proxy`는 `RealSubject` 역의 기능에 대해 접근 제한을 설정한다.
- 지정된 사용자라면 메서드 호출을 허가하지만, 나머지는 오류가 되도록 처리하는 프록시이다.