---
title: ⛓️ Java Design-Pattern 18 - Observer
date: 2025-08-10 09:14:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Observer` 패턴이란?
- 관찰 대상의 상태가 변화하면 관찰자에게 알린다.
- 상태 변화에 따른 처리를 기술할 때 효과적이다.


### 예제 프로그램
- 다음은 수를 많이 생성하는 객체를 관찰자가 관찰하고 그 값을 표시하는 프로그램이다.
- 단, 표시하는 방법은 관찰자에 따라 다르다.
- 가령 `DigitObserver`는 값을 숫자로 표시하지만, `GraphObserver`는 값을 간단한 그래프로 표시한다.

![](/assets/image/Pasted%20image%2020250810131927.png)

#### `Observer` 인터페이스

```java
public interface Observer {
	public abstract void update(NumberGenerator generator);    
}
```
- 관찰자를 나타내는 인터페이스로, 구체적인 관찰자가 이 인터페이스를 구현하게 된다.
- `Java` 클래스 라이브러리에 등장하는 `java.util.Observer`와는 다르다.
- `update()` 메서드를 호출하는 것은 `NumberGenerator`이다.
- `NumberGenerator`가 '내용이 갱신되었어요. 표시하는 쪽도 갱신해주세요.'라고 관찰자에 전달하기 위한 메서드이다.

#### `NumberGenerator` 클래스

```java
import java.util.ArrayList;
import java.util.List;

public abstract class NumberGenerator {
	// Observer 저장
	private List<Observer> observers = new ArrayList<>();
	
	// Observer 추가
	public void addObserver(Observer observer) {
		observers.add(observer);
	}
	
	// Observer에 통지
	public void notifyObservers() {
		for (Observer o : observers) o.update(this);
	}
	
	// 수를 취득
	public abstract int getNumber();
	
	// 수를 생성
	public abstract void execute();
}
```
- 수를 생성하는 추상 클래스이다.
- 실제로 수를 생성하고 취득하는 메서드는 하위 클래스에서 구현해야 하는 추상 메서드로 구성되어 있다.
- `observers` 는 `NumberGenerator`를 관찰하는 `Observer`를 저장하는 필드이다.

#### `RandomNumberGenerator` 클래스

```java
import java.util.Random;

public class RandomNumberGenerator extends NumberGenerator {
	private Random random = new Random();
	private int number;
	
	// 수를 취득
	@Override
	public int getNumber() {
		return number;
	}
	
    // 수를 생성
	@Override
	public void execute() {
		for (int i = 0; i < 20; i++) {
			number = random.nextInt(50);
			notifyObservers();
		}
	}
}
```
- `NumberGenerator`의 하위 클래스로 난수를 생성한다.
- `execute()` 메서드는 난수를 20개 생성하고, 그 때마다 관찰자에게 통지한다.

#### `DigitObserver` 클래스

```java
public class DigitObserver implements Observer {
	@Override
	public void update(NumberGenerator generator) {
		System.out.println("DigitObserver:" + generator.getNumber());
		
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) { }
	}
}
```
- 구체적인 관찰자 클래스로, 관찰한 수를 숫자로 표시한다.

#### `GraphObserver` 클래스

```java
public class GraphObserver implements Observer {
	@Override
	public void update(GraphObserver generator) {
		System.out.println("GraphObserver:");
		
		int count = generator.getNumber();
		for (int i = 0; i < count; i++) System.out.println("*");
		
		System.out.println("");
		
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) { }
	}
}
```
- 이 클래스는 관찰한 수를 간이 그래프로 표시한다.

#### `Main` 클래스

```java
public class Main {
	public static void main(String[] args) {
		NumberGenerator generator = new RandomNumberGenerator();
		Observer observer1 = new DigitObserver();
		Observer observer2 = new GraphObserver();
		
		generator.addObserver(observer1);
		generator.addObserver(observer2);
		
		generator.execute();
	}
}
```
- 수 생성기와 관찰자를 생성 및 등록 한 후  `generator.execute()` 메서드를 사용하여 수를 생성한다.


### `Observer` 패턴의 등장인물
#### `NumberGenerator` 클래스
- 관찰자를 등록 및 삭제하고 현재 상태를 가져오는 메서드를 갖고 있으며, 관찰 대상자(`Subject`) 역을 맡았다.

#### `RandomNumberGenerator` 클래스
- 상태가 변경되면 관찰자에게 알리는 구체적인 관찰 대상자(`ConcreteSubject`) 역을 맡았다.

#### `Observer` 인터페이스
- 관찰 대상자로부터 '상태가 변화했어요'라고 전달 받는 관찰자(`Observer`) 역할을 맡았다.
- 참고로 전달받기 위한 메서드가 바로 `update()`이다.

#### `DigitObserver`, `GraphObserver` 클래스
- `update()` 메서드가 호출되면 그 안에서 관찰 대상자의 현재 상태를 취득하는 구체적인 관찰자(`ConcreteObserver`) 역할을 맡았다.


### 책에서 제시하는 힌트
#### 여기에도 교환 가능성이 등장한다.
- 디자인 패턴의 목적 중 하나는 클래스를 재사용 가능한 부품으로 만드는 것이다.
- `Observer` 패턴에서는 상태를 가진 `ConcreteSubject`와 상태 변화를 통보 받는 `ConcreteObserver`가 있다.
- 그리고 그 둘을 연결하는 것이 인터페이스로서의 `Subject`와 `Observer`다.
- 가령 `RandomNumberGenerator`는 자신을 관찰하는 관찰자가 어떤 클래스의 인스턴스인지 모른다.
- 다만 `observers` 필드에 저장된 인스턴스가 `Observer` 인터페이스를 구현하고, `update` 메서드를 가지고 있다는 것 정도는 알고 있다.
- `DigitObserver` 클래스 또한 자신이 관찰하는 대상이 어떤 클래스의 인스턴스인지 신경쓰지 않는다.
- 다만 `RandomNumberGenerator`의 하위 클래스이고, `getNumber` 메서드를 갖고 있다는 것은 알고 있다.

1. 추상 클래스나 인터페이스를 사용하여 구상 클래스로부터 추상 클래스를 분리한다.
2. 인수로 인스턴스를 전달할 때나 필드로 인스턴스를 저장할 때는 구상 클래스형으로 하지 않고 추상 클래스나 인터페이스형으로 해 둔다.

- 위 전제를 지키는 한 구상 클래스 부분을 쉽게 교환할 수 있다.

#### `Observer`의 순서
- `Subject` 역에는 여러 `Observer`가 등록되어 있는데, 일반적으로 `ConcreteObserver` 역의 클래스를 설계할 때는 `update()` 메서드가 호출되는 순서가 바뀌어도 문제가 생기지 않도록 해야 한다.
- 애초에 각 클래스의 독립성이 제대로 유지되면, 의존성의 혼란은 일어나지 않는다.

#### `Observer`의 행위가 `Subject`에 영향을 미칠 때
- `Observer` 패턴에서는 `Subject`가 `update()` 메서드를 호출하는 계기가 다른 클래스로부터 오기도 한다.
- 예를 들어 `GUI`에서 사용자가 버튼을 누르는 이벤트를 계기로 `update()` 메서드가 호출되는 경우가 자주 있다.
- 동시에 `Subject`가 `update()` 메서드를 호출하는 계기가 해당 `Observer`인 경우도 있는데, 이런 경우 조심해서 설계하지 않으면 메서드 호출이 영원히 반복될 수 있다.
- `Subject` 상태 변화 → `Observer`로 통지 `Observer`가 `Subject`의 메서드 호출 → `Subject` 상태 변화 → `...`
- 그렇기 때문에 현재 통지 처리 중인지를 `Observer`가 판단하거나, 통지하는 타이밍을 `Subject`가 고려하는 등의 대책이 필요하다.

#### 갱신을 위한 힌트 정보 다루기
- `RandomNumberGenerator`는 `update()` 메서드를 사용하여 '갱신됐어요'라고 `Observer`에게 통지한다.
- `update()` 메서드의 인수로 주어지는 것은 `RandomNumberGenerator` 인스턴스 뿐이기 때문에, `Observer`는 `getNumber()` 메서드를 호출하여 필요로 하는 값을 얻어야 한다.
- 상황에 따라 다음과 같이 적절하게 힌트 정보를 넘길 수 있다.

1. `void update(NumberGenerator generator);` `... (현행)`
2. `void update(NumberGenerator generator, int number);` `... (2)`
3. `void update(int number);` `... (3)`

- `(2)`부터는 `Subject`에 더해 힌트 정보를 넘겨 준다.
- 이로써 `Observer`는 `Subject`로부터 정보를 얻어 오는 수고를 덜 수 있다.
- 다만 이러한 힌트 정보를 준다는 것은 `Subject`가 `Observer`의 처리 내용을 의식하고 있다는 것이다.
- 어느 정도의 힌트 정보를 전달할 것인지는 프로그램의 복잡성을 잘 고려해서 판단해야 한다.
- `(3)`의 경우 하나의 `Observer`가 여러 `Subject`를 관찰하는 경우에는 넘어온 숫자가 어느 `Subject`의 정보인지 모르기 때문에 부적절하다.

#### 관찰하기보다 전달 받기를 기다린다.
- 관찰자가 능동적으로 관찰하는 것이 아니라, 관찰 대상자의 통보를 수동적으로 기다린다는 점에서 `Publish-Subscribe` 패턴으로 불리기도 한다.
- `publish`와 `subscribe`라는 표현이 더 실체에 맞을지도 모른다.

#### `Model` / `View` / `Controller`(`MVC`)
- `MVC` 구조에서 `Model`과 `View`의 관계는 `Observer` 패턴의 `Subject`와 `Observer` 관계에 대응한다. 
- `Model`의 경우 표시 형식에 의존하지 않는 내부 모델을 조작한다는 점에서, `View`의 경우 어떻게 보여 줄지 관리하는 점에서 그러하다. 
- 일반적으로 하나의 `Model`에 여러 `View`가 대응한다.