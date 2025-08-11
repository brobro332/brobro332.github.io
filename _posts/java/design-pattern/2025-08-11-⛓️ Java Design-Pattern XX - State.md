---
title: ⛓️ Java Design-Pattern XX - State
date: 2025-08-11 13:14:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `State` 패턴이란?
- `Java`에서 객체 지향 프로그래밍을 할 때 프로그래밍 대상을 클래스로 표현한다.
- `State` 패턴에서는 상태를 클래스로 표현한다.
- 상태를 클래스로 표현하면 클래스를 전환함으로써 상태 변화를 나타낼 수 있으며, 새로운 상태를 추가해야 할 때 무엇을 프로그래밍 할지 명확해진다.


### 예제 프로그램
- 다음은 금고 경비 시스템을 예제 프로그램으로 만든 것이다.

- 프로그램 상의 1초를 현실 세계의 1시간으로 가정한다.
- 금고가 하나 있다.
- 금고는 경비 센터와 연결되어 있다.
- 금고에는 비상벨과 일반 통화용 전화가 연결되어 있다.
- 금고에는 시계가 설치되어 있어, 현재 시각을 감시한다.
- 주간은 `9:00 ~ 16:59`, 야간은 `17:00 ~ 23:59` 및 `0:00 ~ 8:59` 범위이다.
- 금고는 주간에만 사용할 수 있다.
- 주간에 금고를 사용하면 경비 센터에 비상벨 통보가 간다.
- 일반 통화용 전화는 언제라도 사용할 수 있다.(단, 야간은 녹음만 가능)
- 주간에 전화를 사용하면 경비 센터가 호출된다.
- 야간에 전화를 사용하면 경비 센터의 자동 응답기가 호출된다.

#### `State` 패턴을 사용하지 않는 의사 코드

```bash
경비 시스템 클래스 {
	금고 사용 시 호출되는 메서드() {
		if (주간) {
			경비 센터에 사용 기록
		} else if (야간) {
			경비 센터에 비상 상황 기록
		}
	}
	
	비상벨 사용 시 호출되는 메서드() {
		경비 센터에 비상벨 통보
	}
	
	일반 통화 시 호출되는 메서드() {
		if (주간) {
			경비 센터 호출
		} else if (야간) {
			경비 센터 자동 응답기 호출
		}
	}
}
```

#### `State` 패턴을 사용한 의사 코드

```bash
주간 상태를 표시하는 클래스 {
	금고 사용 시 호출되는 메서드() {
		경비 센터에 사용 기록
	}
	
	비상벨 사용 시 호출되는 메서드() {
		경비 센터에 비상벨 통보
	}
	
	일반 통화 시 호출되는 메서드() {
		경비 센터 호출
	}
}

야간 상태를 표시하는 클래스 {
	금고 사용 시 호출되는 메서드() {
		경비 센터에 비상 상황 기록
	}
	
	비상벨 사용 시 호출되는 메서드() {
		경비 센터에 비상벨 통보
	}
	
	일반 통화 시 호출되는 메서드() {
		경비 센터 자동 응답기 호출
	}
}
```
- `State` 패턴을 사용하지 않는 의사 코드에서는 주간, 야간이라는 상태가 각 메서드의 `if`문 부분에 등장한다.
- 반면 `State` 패턴을 사용하는 의사 코드에서는 상태가 클래스로 표현되었기 때문에, 각 메서드에는 현재 상태를 체크하는 `if`문이 등장하지 않는다.

![](/assets/image/Pasted%20image%2020250811155203.png)
![](/assets/image/Pasted%20image%2020250811155320.png)

#### `State` 인터페이스

```java
public interface State {
	public abstract void doClock(Context context, int hour);
	public abstract void doUse(Context context);
	public abstract void doAlarm(Context context);
	public abstract void doPhone(Context context);
}
```
- 금고 상태를 나타내는 인터페이스다.
- 시간이 설정되었을 때, 금고가 사용되었을 때, 비상벨이 눌렸을 때, 일반 통화를 할 때 호출된다.
- 인수로 전달되는 `Context`는 상태를 관리하는 인터페이스다.

#### `DayState` 클래스

```java
public class DayState implements State {
	private static DayState singleton = new DayState();
	private DayState() { }
	
	public static State getInstance() {
		return singleton;
	}
	
	@Override
	public void doClock(Context context, int hour) {
		if (hour < 9 || 17 <= hour) {
			context.changeState(NightState.getInstance());
		}
	}
	
	@Override
	public void doUse(Context context) {
		context.recordLog("금고사용(주간)");
	}
	
	@Override
	public void doAlarm(Context context) {
		context.callSecurityCenter("비상벨(주간)");
	}
	
	@Override
	public void doPhone(Context context) {
		context.callSecurityCenter("일반통화(주간)");
	}
	
	@Override
	public String toString() {
		return "[주간]";
	}
}
```
- 주간 상태를 나타내는 클래스로, `State` 인터페이스에서 선언된 메서드를 구현한다.
- 상태를 나타내는 클래스는 인스턴스를 하나씩만 만든다.
- 상태가 변화할 때마다 인스턴스를 만들면 메모리와 시간이 낭비되기 때문이다.
- 그 때문에 `Singleton` 패턴을 사용한다.
- `doClock()` 메서드는 주어진 시간이 야간이면 야간 상태로 시스템을 전환한다.
- `doUse()`, `doAlarm()`, `doPhone()` 메서드는 각각 금고 사용, 비상벨, 일반 통화라는 사건에 대응한다.
- 이들 메서드 안에 현재 상태를 체크하는 `if`문은 존재하지 않는다.
- 이렇듯 `State` 패턴에서는 상태의 차이가 클래스의 차이로 나타나므로 `if`, `switch` 등 상태 별로 분기할 필요가 없다.

#### `NightState` 클래스

```java
public class NightState implements State {
	private static NightState singleton = new NightState();
	
	private NightState() { }
	
	public static State getInstance() {
		return singleton;
	}
	
	@Override
	public void doClock(Context context, int hour) {
		if (9 <= hour && hour < 17) {
			context.changeState(DayState.getInstance());
		}
	}
	
	@Override
	public void doUse(Context context) {
		context.callSecurityCenter("비상：야간 금고 사용！");
	}
	
	@Override
	public void doAlarm(Context context) {
		context.callSecurityCenter("비상벨(야간)");
	}
	
	@Override
	public void doPhone(Context context) {
		context.recordLog("야간 통화 녹음");
	}
	
	@Override
	public String toString() {
		return "[야간]";
	}
}
```
- 야간 상태를 나타내는 클래스로, `State` 인터페이스에서 선언된 메서드를 구현한다.
- 이 클래스 또한 `Singleton` 패턴을 사용한다.

#### `Context` 인터페이스

```java
public interface Context {
	public abstract void setClock(int hour);             // 시간 설정
	public abstract void changeState(State state);       // 상태 변화
	public abstract void callSecurityCenter(String msg); // 경비 센터 경비원 호출
	public abstract void recordLog(String msg);          // 경비 센터 기록
}
```
- 상태를 관리하거나 경비 센터를 호출한다.

#### `SafeFrame` 클래스

```java
import java.awt.BorderLayout;
import java.awt.Button;
import java.awt.Color;
import java.awt.Frame;
import java.awt.Panel;
import java.awt.TextArea;
import java.awt.TextField;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class SafeFrame extends Frame implements ActionListener, Context {
	private TextField textClock = new TextField(60);      // 현재 시간 표시
	private TextArea textScreen = new TextArea(10, 60);   // 경비 센터 출력
	private Button buttonUse = new Button("금고 사용");    // 금고 사용 버튼
	private Button buttonAlarm = new Button("비상벨");     // 비상벨 버튼
	private Button buttonPhone = new Button("일반 통화");  // 일반 통화 버튼
	private Button buttonExit = new Button("종료");       // 종료 버튼
	private State state = DayState.getInstance();        // 현재 상태
	
	/* 생성자 */
	public SafeFrame(String title) {
		super(title);
		setBackground(Color.lightGray);
		setLayout(new BorderLayout());
		
		// textClock 배치
		add(textClock, BorderLayout.NORTH);
		textClock.setEditable(false);
		
		// textScreen 배치
		add(textScreen, BorderLayout.CENTER);
		textScreen.setEditable(false);
		
		// 패널에 버튼 저장
		Panel panel = new Panel();
		panel.add(buttonUse);
		panel.add(buttonAlarm);
		panel.add(buttonPhone);
		panel.add(buttonExit);
		
		// 그 패널을 배치
		add(panel, BorderLayout.SOUTH);
		
		// 표시
		pack();
		setVisible(true);
		
		// 리스너 설정
		buttonUse.addActionListener(this);
		buttonAlarm.addActionListener(this);
		buttonPhone.addActionListener(this);
		buttonExit.addActionListener(this);
	}
	
	/* 버튼이 눌리면 여기로 온다 */
	@Override
	public void actionPerformed(ActionEvent e) {
		System.out.println(e.toString());
		if (e.getSource() == buttonUse) {           // 금고 사용 버튼
			state.doUse(this);
		} else if (e.getSource() == buttonAlarm) {  // 비상벨 버튼
			state.doAlarm(this);
		} else if (e.getSource() == buttonPhone) {  // 일반 통화 버튼  
			state.doPhone(this);
		} else if (e.getSource() == buttonExit) {   // 종료 버튼
			System.exit(0);
		} else {
			System.out.println("?");
		}
	}
	
	/* 시간 설정 */
	@Override
	public void setClock(int hour) {
		String clockstring = String.format("현재 시간은 %02d:00", hour);
		System.out.println(clockstring);
		textClock.setText(clockstring);
		state.doClock(this, hour);
	}
	
	/* 상태 변화 */
	@Override
	public void changeState(State state) {
		System.out.println(this.state + "에서" + state + "으로 상태가 변화했습니다.");
		this.state = state;
	}
	
	/* 경비 센터 경비원 호출 */
	@Override
	public void callSecurityCenter(String msg) {
		textScreen.append("call! " + msg + "\n");
	}
	
	/* 경비 센터 기록 */
	@Override
	public void recordLog(String msg) {
		textScreen.append("record ... " + msg + "\n");
	}
}
```
- `Context` 인터페이스를 구현하여 `GUI`를 통해 금고 경비 시스템을 실현한다.
- `SafeFrame` 클래스의 필드는 화면에 표시되는 텍스트 필드나 텍스트 영역, 버튼 등의 부품이다.
- 단 `state` 필드는 화면 상 부품이 아닌 금고의 현재 상태를 나타낸다.
- 생성자에서는 다음과 같은 처리를 한다.

1. 배경색 설정
2. 레이아웃 관리자 설정
3. 부품 배치
4. 리스너 설정

- 각 버튼의 `addActionListener()` 메서드를 호출하여 리스너를 설정한다.
- 이 때 해당 메서드의 인수로 버튼을 눌렀을 때 호출될 인스턴스를 결정한다.
- 또한 해당 인스턴스는 `ActionListener` 인터페이스를 구현해야 한다.
- 버튼을 눌렀을 때 리스너가 호출되는 구조는 `Observer` 패턴과 유사하다.
- `ActionPerformed()` 메서드는 버튼을 눌렀을 때 호출되는 메서드이다.
- 이 메서드 명은 `java.awt.event.ActionListener` 인터페이스에서 정해진 이름이므로 함부로 수정해서는 안된다.
- 이 클래스에 `if`문이 등장하지만, 버튼 종류에 대응하는 것이지 현재 상태에 대응하는 것이 아님을 주의해야 한다.
- 가령 금고 사용 버튼을 누르면 `state.doUse(this)` 메서드가 실행된다.
- `State` 패턴을 사용하기 때문에 현재 시각이나 현재 금고 상태를 조사하는 분기 처리가 불필요하다.
- `setClock()` 메서드는 시간을 표시하고, 시간에 대한 상태를 설정한다.
- `changeState()` 메서드는 `DayState` 또는 `NightState` 클래스에서 호출된다.
- `callSecurityCenter()` 메서드는 경비 센터 호출을 나타내며 `recordLog` 메서드는 경비 센터의 기록을 나타낸다.

#### `Main` 클래스

```java
public class Main {
	public static void main(String[] args) {
		SafeFrame frame = new SafeFrame("State Sample");
		while (true) {
			for (int hour = 0; hour < 24; hour++) {
				frame.setClock(hour); // 시간 설정
				try {
					Thread.sleep(1000);
				} catch (InterruptedException e) { }
			}
		}
	}
}
```
- `SafeFrame` 인스턴스를 하나 만들고, 그 인스턴스에 대해 정기적으로 시간을 설정한다.
- 시간 설정은 1초마다 이뤄지는데, 프로그램 안에서는 1시간에 해당한다.


### `State` 패턴의 등장인물
#### `State` 인터페이스
- 상태마다 다르게 동작하는 인터페이스를 정의한다.
- 이 인터페이스는 상태에 의존한 메서드 모음이 된다.
- 상태를 나타내는 상태(`State`) 역을 맡았다.

#### `DayState`, `NightState` 클래스
- 구체적인 각각의 상태를 나타낸다.
- `State`에서 정의된 인터페이스를 구현하는 구체적인 상태(`ConcreteState`) 역을 맡았다.

#### `Context` 인터페이스, `SafeFrame` 클래스
- `State` 패턴 사용자에게 필요한 인터페이스를 정의하는 문맥(`Context`) 역을 맡았다.
- `Context` 인터페이스가 인터페이스를 정의하고 `SafeFrame` 클래스가 `ConcreteState`를 가지는 부분을 맡았다.


### 책에서 제시하는 힌트
#### 분할해서 통치하라.
- 크고 복잡한 문제를 그대로 해결하려 해선 안된다.
- 작은 문제로 나누고, 그래도 힘들다면 더 작게 나눠야 한다.
- 간단히 말하면, 분할해서 통치하라는 말은 까다로운 문제를 하나 푸는 대신 작고 쉬운 문제를 많이 풀라는 말이다.
- `State` 패턴에서는 상태를 클래스로 표현하는데, 각각의 구체적인 상태를 클래스로 표현해서 문제를 분할한 것이다.
- 상태가 많아질수록 해당 패턴은 빛을 발한다.
- 앞서 소개한 의사 코드에서, `State` 패턴을 사용하지 않을 경우 현재 상태에 대응한 조건 분기가 발생하는 것을 알 수 있다.
- 상태가 많아질수록 이 조건 분기도 늘어나기 때문에 프로그램의 복잡성이 증가한다.

#### 상태에 의존한 처리
- `SafeFrame` 클래스의 `setClock()` 메서드는 `Main` 클래스에서 호출된다.
- 해당 메서드를 통해 시간 설정을 지시하고 `setClock()` 메서드 내부에서는 다음과 같이 `state`에 위임한다.

```java
state.doClock(this, hour);
```
- 즉, 시간 설정을 현재 상태에 의존하는 처리로 다룬다.
- `doClock`뿐만 아니라 `State` 인터페이스에 정의된 메서드는 모두 상태에 따라 동작이 달라지는 처리다.
- `State` 패턴에서는 상태에 의존하는 처리를 프로그램으로 다음과 같이 표현한다.

1. 추상 메서드로 선언하고 인터페이스로 한다.
2. 구상 메서드로 구현하고 개별 클래스로 한다.

#### 상태 전환은 누가 관리해야 하는가?
- 예제 프로그램에서는 `Context` 역을 맡은 `SafeFrame` 클래스가 실제로 상태를 전환하는 `changeState()` 메서드를 구현했다.
- 해당 메서드를 실제로 호출하는 것은 `DayState` 또는 `NightState` 클래스이다.
- 즉, 예제 프로그램에서는 상태 전환을 상태에 의존하는 동작으로 생각한다.
- 이 방식에는 장단점이 각각 존재한다.
- 장점은 다른 상태로 전환하는 시점에 대한 정보가 한 클래스 내에 있다는 점이다.
- 예를 들어 `DayState` 클래스가 언제 다른 상태로 전환하는 지 알고 싶다면 단순히 해당 클래스의 코드를 읽으면 된다.
- 단점은 한 구체적인 상태 역이 다른 구체적인 상태 역을 알아야 한다는 점이다.
- 가령 `DayState` 클래스에서는 `doClock()` 메서드 안에서 `NightState` 인스턴스를 사용한다.
- 즉, 나중에 `NightState` 클래스를 삭제하게 된다면 `DayState` 클래스도 수정해야 한다.
- 이러한 점은 클래스 사이의 의존 관계를 깊게 만들어 버린다.
- 예제 프로그램과 같은 방식을 그만두고 모든 상태 전환을 `Context` 역의 `SafeFrame` 클래스에 맡길 수도 있다.
- 그렇게 한다면 개별 구체적인 상태 역의 독립성이 좋아져서 전체 프로그램의 전망이 향상되는 경우가 있다.
- 또는 `State` 패턴 대신 상태 테이블을 사용하여 설계할 수도 있다.
- 테이블은 입력과 내부 상태를 바탕으로 출력과 다음 내부 상태를 얻을 수 있는 알림표가 된다.
- 상태 변경이 고정된 규칙에 바탕을 둔 경우에는 이처럼 테이블을 사용하는 프로그램도 효과적이라고 할 수 있다.
- 상태 수가 많은 경우에는 수작업을 포기하고, 프로그램을 자동으로 생성하는 다른 프로그램을 사용하는 방법도 있다.

#### 자기 모순에 빠지지 않는다.
- `State` 패턴을 사용하지 않고 시스템 상태가 여러 변숫값의 집합으로 표현되어 있다고 가정해보자.
- 이때 변숫값 사이의 자기 모순이나 불일치가 없어야 한다.
- `State` 패턴에서는 상태를 클래스로 표현하고, 현재 상태를 표현하는 변수는 단 하나이다.
- 그렇기 때문에 해당 변수가 시스템의 상태를 확실히 결정하므로 자기 모순을 내포한 상태라는 것이 존재하지 않게 된다.

#### 새로운 상태를 추가하는 것은 간단하다.
- 예제 프로그램으로 예를 들면, `State` 인터페이스를 구현한 `XxxState` 클래스를 만들고, 필요한 메서드를 구현하면 된다.
- 물론 상태 전환 부분은 다른 구체적인 상태 역과의 접점이 되므로 주의해서 구현해야 한다.
- 반면 완성된 `State` 패턴에 새로운 상태 의존 처리를 구현하는 것은 어렵다.
- `State` 역의 인터페이스에 메서드를 추가하는 것을 의미하는데, 모든 구체적인 상태 역에 처리를 추가해야 하기 때문이다.
- 물론 메서드를 추가하지 않으면 컴파일할 때 메서드가 구현되지 않았음을 오류로 알려주기 때문에 누락될 위험은 없다.
- 만일 `State` 패턴을 사용하지 않는다면, 상태를 판단하는 것은 `if`문 안의 조건식이기 때문에, 오류를 컴파일할 때 검출하기 어렵다.
- 참고로 고려하지 않은 상태를 검출하면 오류로 처리하면 되기 때문에 실행할 때 검출하는 것은 어렵지 않다. 

#### 두 얼굴을 가진 인터페이스
- `SafeFrame` 클래스에 등장한 다음 두 코드를 살펴보자.

```java
/* 생성자 안 */
buttonUse.addActionListner(this);

/* actionPerformed 안 */
state.doUse(this);
```
- 예제 프로그램에서는 `SafeFrame` 인스턴스가 하나만 생성되므로 `this`가 동일하지만, `addActionListner()`에 전달될 때와 `doUse()`에 전달될 때는 조금 차이가 있다.
- `addActionListner()`에 전달될 때는 `ActionListener` 인터페이스를 구현한 클래스의 인스턴스로서 다루어진다.
- 인수로 전달된 것이 `SafeFrame` 인스턴스인지 여부는 중요하지 않다.
- `doUse()`에 전달될 때는 동일한 인스턴스가 `Context` 인터페이스를 구현한 클래스의 인스턴스로 다루어진다.
- `doUse()` 메서드 안에서는 `Context` 인터페이스에서 정의된 메서드 범위에서 인수가 이용된다.
- 동일한 하나의 인스턴스에 여러 얼굴이 있다는 점을 기억하자.