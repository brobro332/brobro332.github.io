---
title: ⛓️ Java Design-Pattern 17 - Mediator
date: 2025-08-09 19:14:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Mediator` 패턴이란?
- 가령 10명이 모여 공동 작업을 하는데 좀처럼 끝이 나질 않는다. 
- 멤버들이 서로 지시하며 작업은 혼란을 겪고 있고, 서로의 작업에 일일이 참견해서 분쟁이 일어나고 있다.
- 이때 입장이 다른 중재자가 등장해서 멤버가 모두 중재자에게만 보고하고, 중재자만 멤버에게 지시를 내리게 된다고 가정하자. 
- 이러한 프로세스를 패턴화한 것이 `Mediator` 패턴이다.


### 예제 프로그램
- 다음은 이름과 패스워드를 입력하여 로그인하는 `GUI` 애플리케이션이다. 
- 다음과 같이 사용한다.

1. 게스트 로그인 또는 사용자 로그인을 사용한다.
2. 게스트 로그인일 때는 `username`, `password`가 비활성화 된다.
3. 사용자 로그인일 때만 `username`과 `password`를 입력한다.
4. `username`에 문자가 하나도 없으면 `password`는 비활성화 된다.
5. 로그인하려면 `OK` 버튼, 그만두려면 `CANCEL` 버튼을 클릭한다.
6. `username`, `password` 모두 문자가 하나라도 들어가 있으면 `OK` 버튼이 활성화 된다.
7. `CANCEL` 버튼은 항상 활성화 되어 있다.

- 상기 로직을 각 클래스에 분산시키면 코딩 및 관리하기가 매우 힘들어진다. 
- 각각의 객체가 서로 연관되어 있어 서로가 서로를 통제하는 상황에 빠지기 때문이다.

![](/assets/image/Pasted%20image%2020250809191805.png)
![](/assets/image/Pasted%20image%2020250809191839.png)

#### `Mediator` 인터페이스

```java
public interface Mediator {
	public abstract void createColleagues();    
    public abstract void colleagueChanged();
}
```
- 중재자를 나타내는 인터페이스이다.
- `createColleagues` 메소드는 중재자가 관리할 멤버를 생성한다.
- `colleagueChanged` 메소드는 멤버가 호출하는 메소드이다. 
- 예제 프로그램에서는 라디오 버튼이나 텍스트 필드 상태가 변화했을 때 이 메소드를 호출한다.

#### `Colleague` 인터페이스

```java
public interface Colleague {
	public abstract void setMediator(Mediator mediator);
    public abstract void setColleagueEnabled(boolean enabled);
}
```
- 중재자에게 상담을 의뢰할 멤버를 나타내는 인터페이스이다.
- `setMediator` 메소드는 중재자가 자신을 중재자로 기억해달라는 의미로 호출한다. 
- 이 메소드의 파라미터로 전달된 중재자는 추후 상담이 필요할 때 사용된다.
- `setColleagueEnabled` 메소드는 중재자로부터 내려오는 지시에 해당한다. 
- 즉 중재자가 호출하는 메소드이다.

#### `ColleagueButton` 클래스

```java
import java.awt.Button;

public class ColleagueButton extends Button implements Colleague {
	private Mediator mediator;
    
    public ColleagueButton(String caption) {
    	super(caption);
    }
    
    @Override
    public void setMediator(Mediator mediator) {
    	this.mediator = mediator;
    }
    
    @Override
    public void setColleagueEnable(boolean enabled) {
    	setEnabled(enabled);
    }
}
```
- `java.awt.Button`의 하위 클래스로 `Colleague` 인터페이스를 구현하여 `LoginFrame` 클래스와 협조하며 동작한다.

#### `ColleagueTextField` 클래스

```java
import java.awt.Color;
import java.awt.TextField;
import java.awt.event.TextEvent;
import java.awt.event.TextListener;

public class ColleagueTextField extends TextField implements TextListener, Colleague {
	private Mediator mediator;
    
    public ColleagueTextField(String text, int columns) {
    	super(text, columns);
    }
    
    @Override
    public void setMediator(Mediator mediator) {
    	this.mediator = mediator;
    }
	
    @Override
    public void setColleagueEnabled(boolean enabled) {
    	setEnable(enabled);
        setBackground(enabled ? Color.white : Color.lightGray);
    }
    
    @Override
    public void textValueChanged(TextEvent e) {
    	mediator.colleagueChanged();
    }
}
```
- `java.awt.TextField`의 하위 클래스이고 `Colleague`, `java.awt.event.TextListener` 인터페이스를 구현한다.
- `texetValueChanged` 메소드는 `TextListener` 인터페이스를 위한 메소드로, 텍스트 내용에 변경이 있으면 `AWT` 프레임워크에서 호출된다. 
- 예제 프로그램에서 해당 메소드는 중재자의 `colleagueChanged` 메소드를 호출하고 있다.

#### `ColleagueCheckBox` 클래스

```java
import java.awt.Checkbox;
import java.awt.CheckboxGroup;
import java.awt.event.ItemEvent;
import java.awt.event.ItemListener;

public class ColleagueCheckbox extends Checkbox implements ItemListener, Colleague {
	private Mediator mediator;
    
    public ColleagueCheckbox(String caption, CheckboxGroup group, boolean state) {
    	super(caption, group, state);
    }
    
    @Override
    public void setMediator(Mediator mediator) {
    	this.mediator = mediator;
    }
     
    @Override
    public void setColleagueEnabled(boolean enabled) {
    	setEnabled(enabled);
    }
     
    @Override
    public void itemStateChanged(ItemEvent e) {
    	mediator.colleagueChanged();
    }
}
```

- `java.awt.Checkbox` 클래스의 하위 클래스다. 
- 예제 프로그램에서는 체크박스가 아닌 라디오 버튼으로 사용된다.
- 이 클래스는 라디오 버튼의 상태 변화를 `itemStateChanged` 메소드로 파악하기 위해 `java.awt.event.ItemListener` 인터페이스도 구현한다.

#### `LoginFrame` 클래스

```java
import java.awt.CheckboxGroup;
import java.awt.Color;
import java.awt.Frame;
import java.awt.GridLayout;
import java.awt.Label;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class LoginFrame extends Frame implements ActionListener, Mediator {
	private ColleagueCheckbox checkGuest;
    private ColleagueCheckbox checkLogin;
    private ColleagueTextField textUser;
    private ColleagueTextField textPass;
    private ColleagueButton buttonOk;
    private ColleagueButton buttonCancel;
    
    public LoginFrame(String title) {
    	super(title);
        
        setBackground(Color.lightGray);
        
        setLayout(new GridLayout(4, 2));
        
        createColleagues();
        
        add(checkGuest);
        add(checkLogin);
        add(new Label("Username:"));
        add(textUser);
        add(new Label("Password:"));
        add(textPass);
        add(buttonOk);
        add(buttonCancel);
        
        colleagueChanged();
        
        pack();
        setVisible(true);
    }
    
    @Override
    public void createColleagues() {
    	// checkbox
        CheckboxGroup g = new CheckboxGroup();
        checkGuest = new ColleagueCheckbox("Guest", g, true);
        checkLogin = new ColleagueCheckbox("Login", g, false);
        
        // textField
        textUser = new ColleagueTextField("", 10);
        textPass = new ColleagueTextField("", 10);
        
        textPass.setEchoChar('*');
        
        // button
        buttonOk = new ColleagueButton("OK");
        buttonCancel = new ColleagueButton("Cancel");
        
        // mediator
        checkGuest.setMediator(this);
        checkLogin.setMediator(this);
        textUser.setMediator(this);
        textPass.setMediator(this);
        buttonOk.setMediator(this);
        buttonCancel.setMediator(this);
        
        // listener
        checkGuest.addItemListener(checkGuest);
        checkLogin.addItemListener(checkLogin);
        textUser.addTextListener(textUser);
        textPass.addTextListener(testPass);
        buttonOk.addActionListener(this);
        buttonCancel.addActionListener(this);
    }
    
    @Override
    public void colleagueChanged() {
    	if (checkGuest.getState()) {
        	textUser.setColleagueEnabled(false);
            textPass.setColleagueEnabled(false);
            buttonOk.setColleagueEnabled(true);
        } else {
        	textUser.setColleagueEnabled(true);
            userPassChanged();
        }
    }
    
    private void userPassChanged() {
    	if (textUser.getText().length() > 0) {
        	textPass.setColleagueEnabled(true);
        	if (textPass.getText().length() > 0) {
            	buttonOk.setColleagueEnabled(true);
            } else {
            	buttonOk.setColleagueEnabled(false);
            }
        } else {
        	textPass.setColleagueEnabled(false);
            buttonOk.setColleagueEnabled(false);
        }
    }
    
    @Override
    public void actionPerformed(ActionEvent e) {
    	System.out.println(e.toString());
        System.exit(0);
    }
}
```
- 중재자 역할에 해당한다. 
- `java.awt.Frame`의 하위 클래스이고 `Mediator` 인터페이스를 구현한다.
- 생성자에서는 배경색 설정, 레이아웃 설정, `colleague` 생성 및 배치, 초기 상태 설정, 표시 등을 처리하고 있다.
- `createColleague` 메소드는 대화상자에 필요한 `colleague`를 생성하고 필드에 저장한다. 
- 또 각 `colleague`에 대해 `setMediator`를 호출해 중재자임을 알린다. 
- 또한 각 `listener` 설정도 수행하여 `AWT` 프레임워크에서 적절하게 호출될 수 있도록 한다.
- 이 프로그램에서 가장 중요한 메소드는 `colleagueChanged` 메소드이다. 
- 이 메소드에서 표시의 활성/비활성화를 적절한 조건에 처리한다. 
- 즉, 모든 `colleague`의 상태 변화가 `colleagueChanged` 메소드로 집결된다.

#### `Main` 클래스

```java
public class Main {
	static public void main(String[] args) {
    	new LoginFrame("Mediator Sample");
    }
}
```
- `LoginFrame`의 인스턴스를 생성한다. 
- `main` 메소드가 끝나더라도 `LoginFrame`의 인스턴스는 `AWT` 프레임워크에서 유지된다.

### `Mediator` 패턴의 등장인물
#### `Mediator` 인터페이스
- `colleague`와 통신하고 조정하는 인터페이스 `API`를 정의하는 중재자 `Mediator` 역할을 한다.

#### `LoginFrame` 클래스
- 중재자의 인터페이스를 구현해 실제로 조정하는 구체적인 중재자 `CreateMediator` 역할을 한다.

#### `Colleague` 인터페이스
- 중재자와 통신하는 인터페이스 `API`를 정의하는 동료 `Colleague` 역할을 한다.

#### `ColleagueButton`, `ColleagueTextField`, `ColleagueCheckbox` 클래스
- 동료 역할의 인터페이스를 구현하는 구체적인 동료 `ConcreteColleague` 역할을 한다.


### 책에서 제시하는 힌트
#### 분산이 오히려 화를 부를 때
- 예제 프로그램의 `LoginFrame` 클래스에 있는 `colleagueChanged` 메소드는 다소 복잡하다. 
- 사양이 변경되면 결국 버그가 생길텐데, 그건 문제가 되지 않는다. 
- 가령 버그가 생기더라도 표시 활성/비활성에 대한 로직은 이곳에만 존재하므로 여기만 디버깅 및 수정하면 된다. 
- 만약 로직이 분산되어 있다면 굉장히 골치 아프게 된다.
- 객체 지향에서는 한 곳으로 집중하는 것을 피하고 처리를 분산하는 경우가 많다. 
- 즉 문제를 분할해서 처리하는 것인데, 이번 예제 프로그램 같은 경우 각 클래스로 처리를 분산하는 것은 현명하지 않다. 
- 각 클래스로 분산할 것은 분산하고 집중할 것은 집중하지 않으면 클래스의 분산이 오히려 화를 부르게 된다.

#### 통신 경로의 증가
- 인스턴스가 2개일 때는 통신 경로가 `A -> B`, `B -> A`로 2개, 인스턴스가 3개일 때는 6개, 인스턴스가 4개일 때는 12개로 늘어나고, 이렇게 같은 입장의 인스턴스가 증가할수록 서로 통신하게 되면 프로그램이 복잡해진다.
- 인스턴스 수가 적을 때는 문제가 되지 않지만 인스턴스를 늘려가다보면 어딘가에서 문제가 터지게 된다. 
- 이러한 통신 경로의 가파른 증가를 억제하는 것이 `Mediator` 패턴이다.

#### 재사용할 수 있는 것은 무엇인가?
- 구체적인 동료 역할은 재사용하기 쉽지만, 구체적인 중재자 역할은 재사용하기 어렵다. 
- 예를 들어 로그인 상자와는 별도의 대화상자를 만든다고 하면 `ColleagueButton`, `ColleagueTextField`, `ColleagueCheckbox` 클래스는 새로운 대화상자에서 재사용할 수 있다.
- 하지만 코드 중 애플리케이션에 대한 의존성이 높은 부분을 구체적인 중재자 역할인 `LoginFrame`에 모두 넣어버렸기 때문에 해당 클래스를 다른 대화상자를 개발하는데 재사용하기는 어렵다.

#### `GUI` 프로그래밍 할 때 주의할 점
- 예제 프로그램에서는 패턴을 설명할 목적으로 `AWT`를 다소 번거롭게 구현한 점이 있다.
- 또 새로운 `GUI` 툴킷이 여러 개 있으며 각각 구현 방식이 다르다는 점을 주의하자.