---
title: ⛓️ Java Design-Pattern 23 - Command
date: 2025-08-12 13:49:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Command` 패턴이란?
- 클래스는 자기 자신이나 다른 클래스의 메서드를 호출해서 일을 처리한다.
- 메서드를 호출한 결과는 객체에 반영되지만, 일한 이력은 어디에도 남지 않는다.
- 이럴 때 명령을 표현하는 클래스가 있으면 편리하다.
- 처리하고 싶은 일을 메서드 호출이라는 동적인 처리로 표현하지 않고, 명령을 나타내는 클래스의 인스턴스라는 하나의 객체로 표현할 수 있다.
- 이력을 관리하고 싶을 때는 해당 인스턴스의 집합을 관리하면 된다.
- 명령의 집합을 저장해두면 같은 명령을 재실행할 수도 있고, 여러 명령을 모아서 새로운 명령으로 재이용할 수도 있다.
- 디자인 패턴에서는 이러한 명령에 `Command`라는 이름을 붙였으며, `Event`라고 불리는 경우도 있다.
- 가령 마우스 클릭이나 키 입력 등의 이벤트가 일어났을 때 그 사건을 일단 인스턴스라는 객체로 만들어 두고, 발생 순서대로 대기 행렬에 나열한다.
- 그리고 나열된 이벤트를 순서대로 처리해 나간다.


### 예제 프로그램
- 다음은 간단한 그림 그리기 소프트웨어를 주제로 한 예제 프로그램이다.
- 마우스를 드래그하면 빨간색 점으로 된 선이 그려지고, `[clear]` 버튼을 누르면 모든 점이 사라진다.
- 사용자가 마우스를 드래그할 때마다 '이 위치에 점을 그리시오'라는 명령이 `DrawCommand` 클래스의 인스턴스로 생성된다.

![](/assets/image/Pasted%20image%2020250812161901.png)
![](/assets/image/Pasted%20image%2020250812161939.png)

#### `Command` 인터페이스

```java
package command;

public interface Command {
	public abstract void execute();
}
```
- 명령을 나타낸다.
- `execute()` 메서드를 실행했을 때 구체적으로 어떤 일이 일어날 지는 해당 인터페이스를 구현한 클래스가 결정한다.

#### `MacroCommand` 클래스

```java
package command;

import java.util.ArrayDeque;
import java.util.Deque;

public class MacroCommand implements Command {
	/* 명령의 배열 */
	private Deque<Command> commands = new ArrayDeque<>();
	
	/* 실행 */
	@Override
	public void execute() {
		for (Command cmd: commands) {
			cmd.execute();
		}
	}
	
	/* 추가 */
	public void append(Command cmd) {
		if (cmd == this) {
			throw new IllegalArgumentException("infinite loop caused by append");
		}
		
		commands.push(cmd);
	}
	
	/* 마지막 명령을 삭제 */
	public void undo() {
		if (!commands.isEmpty()) {
			commands.pop();
		}
	}
	
	/* 전부 삭제 */
	public void clear() {
		commands.clear();
	}
}
```
- 복수의 명령을 모은 명령을 나타내며, `Command` 인터페이스를 구현한다.
- `commands` 필드는 `java.util.Deque` 인터페이스로 여러 `Command`의 인스턴스를 모아 두기 위한 것이다.
- 또한 `Command` 인터페이스의 `execute()` 메서드를 구현하고 있는데, 각 인스턴스의 `execute()` 메서드를 호출하고 있다.
- 어쩌면 이 `for` 루프에서 실행하려는 `Command`가 새로운 `MacroCommand`의 인스턴스일 수도 있다.
- 그런 경우에도 다시 그 인스턴스의 `execute()` 메서드가 호출되므로 결국 모든 `Command`가 실행된다.
- `append()` 메서드는 `MacroCommand` 인스턴스에 새로운 `Command`의 인스턴스를 추가하는 메서드이다.
- 추가되는 `Command`의 인스턴스는 다른 `MacroCommand` 인스턴스일 수도 있는데, 실수로 자기 자신을 `push()` 해버리면 `execute()` 메서드가 영원히 끝나지 않으므로 인수를 체크한다.
- `undo()` 메서드는 마지막으로 추가한 `Command` 인스턴스를 제거한다.
- `clear()` 메서드는 모든 명령을 삭제한다.

#### `DrawCommand` 클래스

```java
package drawer;

import command.Command;
import java.awt.Point;

public class DrawCommand implements Command {
	/* 그리는 대상 */
	protected Drawable drawable;
	
	/* 그리는 위치 */
	private Point position;
	
	/* 생성자 */
	public DrawCommand(Drawable drawable, Point position) {
		this.drawable = drawable;
		this.position = position;
	}
	
	/* 실행 */
	@Override
	public void execute() {
		drawable.draw(position.x, position.y);
	}
}
```
- `Command` 인터페이스를 구현하여, 점을 그리는 명령을 표현한 클래스이다.
- 이 클래스에는 `drawable`, `position`이라는 두 개의 필드가 있다.
- `drawable` 필드에는 그리기를 실행할 대상을 저장한다.
- `position` 필드는 그리기를 실행할 위치를 나타낸다.
- `Point` 클래스는 `java.awt` 패키지에 정의되어 있는 클래스로 `X` 좌표와 `Y` 좌표를 갖는 2차원 평면의 위치를 나타낸다.
- 생성자에서는 두 필드를 인수로 받아, 해당 위치에 점을 그리라는 명령을 생성한다.
- `execute()` 메서드에서는 `drawable` 필드의 `draw()` 메서드를 호출한다.

#### `Drawable` 인터페이스

```java
package drawer;

public interface Drawable {
	public abstract void draw(int x, int y);
}
```
- 그리는 대상을 나타낸다.
- `draw()`는 그리는 메서드이다.

#### `DrawCanvas` 클래스

```java
package drawer;

import command.MacroCommand;

import java.awt.Canvas;
import java.awt.Color;
import java.awt.Graphics;

public class DrawCanvas extends Canvas implements Drawable {
	/* 그리는 색 */
	private Color color = Color.red;
	
	/* 그리는 점의 반지름 */
	private int radius = 6;
	
	/* 이력 */
	private MacroCommand history;
	
	/* 생성자 */
	public DrawCanvas(int width, int height, MacroCommand history) {
		setSize(width, height);
		setBackground(Color.white);
		this.history = history;
	}
	
	/* 이력 전체 다시 그리기 */
	@Override
	public void paint(Graphics g) {
		history.execute();
	}
	
	/* 그리기 */
	@Override
	public void draw(int x, int y) {
		Graphics g = getGraphics();
		g.setColor(color);
		g.fillOval(x - radius, y - radius, radius * 2, radius * 2);
	}
}
```
- `Drawable` 인터페이스를 구현하는 클래스로, `java.awt.Canvas` 클래스의 하위 클래스이다.
- 자신이 그려야 할 명령 집합은 `history` 필드에 저장된다.
- 생성자는 너비와 높이, 드로잉 내용(`history`)을 받아서 `DrawCanvas`의 인스턴스를 초기화한다.
- 이 안에서 호출하는 `setSize()`나 `setBackground()` 메서드는 각각 크기와 배경색을 지정한다.
- `paint()` 메서드는 `DrawCanvas`를 다시 그릴 필요가 있을 때, `java` 처리 시스템에서 호출되는 메서드이다.
- 해야 할 처리는 `history.execute()`를 호출하는 것 뿐이다.
- 이것만으로도 `history`에 기록된 명령 집합이 다시 실행된다.
- `draw()` 메서드는 `Drawable` 인터페이스를 구현하고자 정의된 메서드이다.
- 이 안에서 `g.setColor()` 메서드로 색상을 지정하고, `g.fillOval()` 메서드로 원을 지정한다.

#### `Main` 클래스

```java
import command.*;
import drawer.*;

import java.awt.*;
import java.awt.event.*;
import javax.swing.*;

public class Main extends JFrame implements MouseMotionListener, WindowListener {
	/* 그리기 이력 */
	private MacroCommand history = new MacroCommand();
	
	/* 그리는 영역 */
	private DrawCanvas canvas = new DrawCanvas(400, 400, history);
	
	/* 삭제 버튼 */
	private JButton clearButton = new JButton("clear");
	
	/* 생성자 */
	public Main(String title) {
		super(title);
		
		this.addWindowListener(this);
		canvas.addMouseMotionListener(this);
		clearButton.addActionListener(e -> {
			history.clear();
			canvas.repaint();
		});
		
		Box buttonBox = new Box(BoxLayout.X_AXIS);
		buttonBox.add(clearButton);
		Box mainBox = new Box(BoxLayout.Y_AXIS);
		mainBox.add(buttonBox);
		mainBox.add(canvas);
		getContentPane().add(mainBox);
		
		pack();
		setVisible(true);
	}
	
	/* MouseMotionListener용 */
	@Override
	public void mouseMoved(MouseEvent e) { }
	
	@Override
	public void mouseDragged(MouseEvent e) {
		Command cmd = new DrawCommand(canvas, e.getPoint());
		history.append(cmd);
		cmd.execute();
	}
	
	/* WindowListener용 */
	@Override
	public void windowClosing(WindowEvent e) {
		System.exit(0);
	}
	
	@Override public void windowActivated(WindowEvent e) {}
	@Override public void windowClosed(WindowEvent e) {}
	@Override public void windowDeactivated(WindowEvent e) {}
	@Override public void windowDeiconified(WindowEvent e) {}
	@Override public void windowIconified(WindowEvent e) {}
	@Override public void windowOpened(WindowEvent e) {}
	
	public static void main(String[] args) {
		new Main("Command Pattern Sample");
	}
}
```
- 예제 프로그램을 실행하기 위한 클래스다.
- `history` 필드는 그리기 이력을 저장한다.
- 이는 나중에 `DrawCanvas` 인스턴스에 전달하는 것과 동일하다.
- 즉 그리기 이력은 `Main`의 인스턴스와 `DrawCanvas`에서 공유된다고 볼 수 있다.
- `canvas` 필드는 그리는 영역이다.
- `clearButton` 필드는 그린 점을 지우는 삭제 버튼이고, `JButton` 클래스는 `javax.swing` 패키지 클래스로 버튼을 표현한 것이다.
- 생성자에서는 마우스 클릭 등의 이벤트를 받는 리스너를 설정하고 컴포넌트를 배치하고 있다.
- 컴포넌트 레이아웃은 다음과 같다. 

![](/assets/image/Pasted%20image%2020250812160812.png)

- `clearButton.addActionListener()` 메서드를 호출하는 곳에서 람다식을 이용해 그리기 이력을 지운 후에 다시 그리기 처리를 설정했다.
- `mouseMoved()`, `mouseDragged()` 메서드는 `MouseMotionListener` 인터페이스를 구현한 것이다.
- 여기서 마우스를 드래그할 때 (`mouseDragged`)에 이 위치에 점을 그리라는 명령을 만들었다.
- 만든 명령은 `history.append(cmd)`로 실행 이력에 추가한 후 `cmd.execute()`로 즉시 실행하고 있다.
- `window...`로 시작하는 메서드 그룹은 `WindowListener` 인터페이스를 구현하기 위한 것이다.
- 여기에서는 종료 처리만 구현했다.
- `main()` 메서드에서는 `Main` 클래스의 인스턴스를 만들어 실행하고 있다.


### `Command` 패턴의 등장인물
#### `Command` 인터페이스
- 명령의 인터페이스를 정의하는 명령(`Command`) 역을 맡았다.

#### `MacroCommand`, `DrawCommand` 클래스
- `Command` 역의 인터페이스를 구현하는 구체적인 명령(`ConcreteCommand`) 역을 맡았다.

#### `DrawCanvas` 클래스
- `Command`가 명령을 실행할 때 대상이 된다.
- 명령의 수신자로 부르는 수신자(`Receiver`) 역을 맡았다.

#### `Main` 클래스
- `ConcreateCommand`를 생성하고 그 때 `Receiver`를 할당하는 의뢰자(`Client`) 역을 맡았다.
- `Main` 클래스는 마우스의 드래그에 맞추어 `DrawCommand` 인스턴스를 생성한다.

#### `Main`, `DrawCanvas` 클래스
- `Command` 인터페이스를 호출하여 명령 실행을 시작하는 호출자(`Invoker`) 역을 맡았다.
- 이 두 클래스는 `Command` 인터페이스의 `execute()` 메서드를 호출한다.


### 책에서 제시하는 힌트
#### 명령이 가져야 하는 정보는?
- 명령이 어느 정도의 정보를 가질 지는 목적에 따라 달라진다.
- `DrawCommand` 클래스에는 그리는 점의 위치 정보만 갖고 있을 뿐, 점의 크기나  색상, 모양 등의 정보는 없다.
- 또한 이벤트 발생 시각 정보를 갖고 있다면 단순한 그리기가 아니라 마우스 동작의 완급까지 재현할 수 있다.
- `DrawCommand` 클래스에는 그리는 대상을 나타내는 `drawable` 필드도 있다.
- 예제 프로그램에서 `DrawCanvas` 인스턴스는 하나 뿐이고, 모든 그리기는 그곳에서 이루어지기 때문에, 이 `drawable` 필드가 크게 의미가 없다.
- 그러나 그리는 대상이 여러 개 존재하는 프로그램일 경우 이런 필드가 도움이 된다.
- `ConcreateCommand` 역 자신이 `Receiver` 역을 알고 있어서 `ConcreateCommand` 역을 누가 관리하고 가지고 있든 언제든지 `execute()` 할 수 있기 때문이다.

#### 이력의 저장
- 예제 프로그램에서는 그리기 이력을 `MacroCommand` 인스턴스의 `history`에 저장하고 있다.
- 이 인스턴스는 지금까지 그린 정보를 모두 가지고 있으며, 파일로 잘 저장해두면 그리기 이력이 보존된다.

#### 어댑터
- 예제 프로그램의 `Main` 클래스에는 두 개의 인터페이스를 구현했는데, 인터페이스의 메서드 중에 실제로 사용하는 것은 그 일부이다.
- 가령  `MouseMotionListener` 메서드 중에서 사용하는 것은 `mouseDragged()` 메서드 뿐이다.
- 또 하나 예를 들면, `WindowListener`에서는 7개의 메서드 중 `windowClosing()` 메서드만 사용한다.
- 프로그래밍을 간결하게 하기 위해 어댑터 클래스들이 `java.awt.event` 패키지에 준비되어 있다.
- `MouseMotionListener` 인터페이스에는 `MouseMotionAdapter` 클래스, `WindowListener` 인터페이스에는 `WindowAdapter` 클래스가 준비되어 있다.
- 이러한 어댑터는 `Adapter` 패턴의 한 예이다.
- `MouseMotionAdapter` 인터페이스로 예를 들어 보면, 이 클래스는 `MouseMotionListener` 인터페이스가 요구하는 메서드를 모두 구현하지만, 그 내용은 모두 비어있다.
- 따라서 `MouseMotionAdapter`의 하위 클래스를 만들고 필요한 메서드만 구현하면 목적을 달성할 수 있다.
- 특히 `Java`의 익명 클래스를 조합해서 어댑터를 사용하면 한층 더 프로그램을 **스마트**하게 작성할 수 있다.

##### `MouseMotionListener`를 사용하는 경우

```java
public class Main extends JFrame implements MouseMotionListener, WindowListener {
	// ...
	
	public Main(String title) {
		// ... 
		
		canvas.addMouseMotionListener(this);
		
		// ...
	}
	
	public void mouseMoved(MouseEvent e) { }
	public void mouseDragged(MouseEvent e) {
		Command cmd = new DrawCommand(canvas, e.getPoint());
		history.append(cmd);
		cmd.execute();
	}
	
	// ...
}
```

##### `MouseMotionAdapter`를 사용하는 경우

```java
public class Main extends JFrame implements WindowListener {
	// ...
	
	public Main(String title) {
		// ... 
		
		canvas.addMouseMotionListener(new MouseMotionAdapter() {
			public void mouseDragged(MouseEvent e) {
				Command cmd = new DrawCommand(canvas, e.getPoint());
				history.append(cmd);
				cmd.execute();
			}
		});
		
		// ...
	}
	
	// ...
}
```
- 익명 클래스 구문은 익숙하지 않으면 읽기 어렵지만, 주의 깊게 살펴보면 다음과 같은 사실을 알 수 있다.

1. `new MouseMotionAdapter()`는 마치 인스턴스를 만드는 식과 비슷하다.
2. 뒤에 이어지는 `{...}`는 메서드 정의와 비슷하다.

- `MouseMotionAdapter` 클래스의 하위 클래스를 만들어 인스턴스를 생성한다.
- 오버라이드할 메서드만 구현하면, 나머지는 아무것도 쓸 필요가 없으므로 용이하다.