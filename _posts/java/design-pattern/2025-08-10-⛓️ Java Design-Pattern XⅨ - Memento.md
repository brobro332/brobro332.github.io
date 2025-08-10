---
title: ⛓️ Java Design-Pattern XⅨ - Memento
date: 2025-08-10 14:14:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Memento` 패턴이란?
- 가령 텍스트 에디터를 사용할 때 실수로 필요한 텍스트를 삭제하더라도 실행 취소 기능을 통해 삭제하기 전 상태로 텍스트를 복원할 수 있다.
- 객체지향 프로그램으로 실행 취소를 구현하려면 인스턴스가 가진 정보를 저장해 두어야 한다.
- 인스턴스를 복원하기 위해서는 인스턴스 내부 정보에 자유롭게 접근할 수 있어야 하는데, 부주의하게 접근을 허락해 버리면, 해당 클래스의 내부 구조에 의존하는 코드가 프로그램 곳곳에 흩어져 클래스를 수정하기 곤란해지고, 이를 캡슐화의 파괴라고 한다.
- 인스턴스의 상태를 나타내는 역할을 도입해, 캡슐화의 파괴에 빠지지 않고 저장과 복원을 하는 것이 `Memento` 패턴이다.
- `Memento` 패턴을 이용하면 다음과 같은 일을 수행할 수 있다.

1. 실행 취소(`Undo`)
2. 다시 실행(`Redo`)
3. 작업 이력 작성(`History`)
4. 현재 상태 저장(`Snapshot`)


### 예제 프로그램
- 다음은 '과일 모으기 주사위 게임'을 주제로 한 예제 프로그램이다.
- 규칙은 다음과 같다.

1. 이 게임은 자동으로 진행됩니다.
2. 게임 주인공이 주사위를 던져서 나온 수가 다음 상태를 결정합니다.
3. 좋은 수가 나오면 주인공의 돈이 늘어납니다.
4. 나쁜 수가 나오면 돈이 줄어듭니다.
5. 특히 좋은 수가 나오면 주인공은 과일을 받습니다.
6. 돈이 없어지면 종료합니다.

- 프로그램 안에서는 돈이 모였을 때 미래를 위해 `Memento` 클래스의 인스턴스를 만들고, 현재 상태를 저장한다.
- 저장할 것은 현재 가진 돈과 과일이다.
- 만약 계속 져서 돈이 줄어들면, 돈이 없어져서 종료되지 않도록 기존에 저장해 둔 `Memento` 인스턴스로 이전 상태를 복원한다.

![](/assets/image/Pasted%20image%2020250810153306.png)
![](/assets/image/Pasted%20image%2020250810153500.png)

#### `Memento` 클래스

```java
package game;

import java.util.ArrayList;
import java.util.List;

public class Memento {
	private int money;
	private List<String> fruits;
	
	/* 생성자(wide interface) */
	Memento(int money) {
		this.money = money;
		this.fruits = new ArrayList<>();
	}
	
    /* 소지금을 얻는다.(narrow interface) */
	public int getMoney() {
		return money;
	}
	
    /* 과일을 추가한다.(wide interface) */
	void addFruit(String fruit) {
		fruits.add(fruit);
	}
	
    /* 과일을 얻는다.(wide interface) */
	List<String> getFruits() {
		return new ArrayList<>(fruits);
	}
}
```
- `Memento`는 주인공의 상태를 나타내는 클래스이다.
- `Memento` 클래스의 생성자에는 `public`이 없다.
- 따라서 해당 클래스의 인스턴스는 아무나 만들 수 없고, 동일한 패키지에 속한 클래스에서만 사용할 수 있다.
- `addFruit()` 메서드는 과일을 추가하는 메서드로, 이 또한 패키지 외부에서 `Memento` 내부를 변경할 수 없다.

#### `Gamer` 클래스

```java
package game;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;

public class Gamer {
	private int money;
	private List<String> fruits = new ArrayList<>();
	private Random random = new Random();
	
	private static String[] fruitsName = {
		"사과", "포도", "바나나", "오렌지"
	};
	
	public Gamer(int money) {
		this.money = money;
	}
	
	public int getMoney() {
		return money;
	}
	
	public void bet() {
		int dice = random.nextInt(6) + 1;
		if (dice == 1) {
			money += 100;
			System.out.println("소지금이 증가했습니다.");
		} else if (dice == 2) {
			money /= 2;
			System.out.println("소지금이 절반으로 줄었습니다.");
		} else if (dice == 6) {
			String f = getFruit();
			System.out.println("과일(" + f + ")를 받았습니다.");
			fruits.add(f);
		} else {
			System.out.println("변동 사항이 없습니다.");
		}
	}
	
	public Memento createMemento() {
		Memento m = new Memento(money);
		for (String f : fruits) {
			if (f.startsWith("맛있는 ")) m.addFruit(f);
		}
		
		return m;
	}
	
	public void restoreMemento(Memento memento) {
		this.money = memento.getMoney();
		this.fruits = memento.getFruits();
	}
	
	@Override
	public String toString() {
		return "[money = " + money + ", fruits = " + fruits + "]";
	}
	
	private String getFruit() {
		String f = fruitsName[random.nextInt(fruitsName.length)];
		if (random.nextBoolean()) {
			return "맛있는 " + f;
		} else {
			return f;
		}
	}
}
```
- 게임 주인공을 나타내는 클래스로, 소지금, 과일, 난수 생성기를 갖고 있다.
- 게임의 중심이 되는 `bet()` 메서드는 만약 주인공이 파산하지 않았다면 주사위를 던지고 그 눈에 따라 소지금과 과일 개수를 변화시킨다.
- `createMemento()` 메서드는 현재 상태를 저장하는 메서드이다.
- 해당 메서드를 통해 만들어진 `Memento` 인스턴스는 현재 게임 주인공의 상태를 나타낸다.
- 과일에 대해서는 맛있는 것만 저장하도록 한다.
- `restoreMemento()` 메서드는 반대로 실행을 취소하는 메서드이다.
- 주어진 `Memento` 인스턴스를 바탕으로 자신의 상태를 복원한다.

#### `Main` 클래스

```java
import game.Memento;
import game.game.Gamer;

public class Main {
	public static void main(String[] args) {
	    /* 게이머 생성 및 상태 저장 */
		Gamer gamer = new Gamer(100);
		Memento memento = gamer.createMemento();
		
		/* 게임 시작 */
		for (int i = 0; i < 100; i++) {
			System.out.println("==== " + i);
			System.out.println("상태:" + gamer);
			
			gamer.bet();
			
			System.out.println("소지금은 " + gamer.getMoney() + "원이 되었습니다.");
			
			if (gamer.getMoney() > memento.getMoney()) {
				System.out.println("많이 벌었으니 현재 상태를 저장하자 !");
				memento = gamer.createMemento();
			} else if (gamer.getMoney() < memento.getMoney()) {
				System.out.println("많이 줄었으니 이전 상태를 복원하자 !");
				gamer.restoreMemento(memento);
			}
		}
		
		try {
			Thread.sleep(1000);
		} catch (InterruptedException e) { }
		
		System.out.println();
	}
}
```
- `Main` 클래스에서는 `Gamer` 인스턴스를 만들어 게임을 진행한다.
- 운 좋게 소지금이 늘어나면 현재 상태를 저장하고, 소지금이 줄어들면 이전 상태로 복원한다.


### `Memento` 패턴의 등장인물
#### `Gamer` 클래스
- 자신의 현재 상태를 저장하고 싶을 때 스냅샷을 만들고, 이전 `Memento`를 받으면 그 시점의 상태로 되돌리는 처리를 하는  작성자(`Originator`) 역을 맡았다.

#### `Memento` 클래스
- 작성자의 내부 정보를 정리하며, 해당 정보를 누구에게나 공개하지는 않는다.
- `Memento`는 다음과 같은 두 종류의 인터페이스를 갖고 있다.

1. `wide interface` `... 넓은 인터페이스`
	- 오브젝트의 상태를 되돌리는 데 필요한 정보를 모두 얻을 수 있는 메서드 집합
	- 넓은 인터페이스는 `Memento`의 내부 상태를 드러내기 때문에 이를 사용할 수 있는 것은 `Originator` 뿐이다.
2.  `narrow interface` `... 좁은 인터페이스`
	- 외부 `Caretaker`에 보여주는 것을 의미한다.
	- 좁은 인터페이스로 할 수 있는 일에는 한계가 있어 내부 상태가 외부에 공개되는 걸 방지한다.

- 이 두 종류의 인터페이스를 구별해서 사용함으로써 객체의 캡슐화 파괴를 막을 수 있다.
- 기념품(`Memento`) 역할을 맡았다.

#### `Main` 클래스
- 작성자의 상태를 저장하고 싶을 때 작성자에 요청하고, 작성자는 기념품을 만들어 관리인에게 넘겨준다. 
- 관리인은 미래에 대비하여 그 기념품을 저장해준다.
- `Main` 클래스는 이러한 관리인(`Caretaker`) 역을 맡았다.
- 하지만 관리인은 좁은 인터페이스만 사용할 수 있으므로 `Memento` 내부 정보에 접속할 수 없고, 단지 만들어 준 `Memento`를 한 덩어리의 블랙 박스로 통째로 보관해 두기만 한다.
- `Originator`와  `Memento`는 넓은 인터페이스로 연결되어 있지만, `Caretaker`와 `Memento`는 좁은 인터페이스로 연결되어 있어 `Memento`는 `Caretaker`에 대해 정보를 은폐하고 있다.


### 책에서 제시하는 힌트
#### 두 개의 인터페이스와 액세스 제어
- 예제 프로그램에서는 `Memento` 패턴에 등장하는 두 종류의 인터페이스를 실현하고자 `Java`의 접근 제어자 기능을 사용했다.
- 가령 `Memento` 클래스에서는 `getMoney()` 메서드에만 `public`을 붙여 좁은 인터페이스를 구현했다.
- 해당 메서드는 `Caretaker` 역의 `Main` 클래스에서도 이용할 수 있다.
- `public`이 하나라도 붙어 있는데 좁다는 건 이상한 느낌이 들 수 있지만, 좁다라는 뜻은 내부 상태를 조작할 수 있는 정도가 적다는 것을 의미한다.
- 즉 할 수 있는 일은 소지금을 얻는 것과 해당 인스턴스를 변수에 저장해두는 것 뿐이다.
- `Memento` 인스턴스도 직접 생성하지 못하고, `Gamer` 클래스에 부탁하는 수 밖에 없다.
- 이와 같이 접근 제한을 이용하면 '이 클래스에는 이 메서드를 보여주지만 저 클래스에는 보여 주지 않는다'는 것을 프로그램으로 표현할 수 있다.

#### `Memento`를 몇 개 가질까?
- 배열 등을 사용하면 `Memento` 인스턴스를 여러 개 갖게 하여 다양한 시점에서의 상태를 저장해 둘 수 있다.

#### `Memento`의 유효기간은?
- 예제 프로그램처럼 메모리에서만 `Memento`를 보관할 경우 큰 문제가 없지만, 파일로 영속적으로 저장할 경우에는 `Memento`의 유효기간이 문제가 된다.
- 특정 시점의 `memento`를 저장해 놓더라도 이후 프로그램 버전이 올라가면, 파일로 저장되어 있던 `Memento`와 맞지 않을 수 있다. 

#### `Caretaker` 역할과 `Originator` 역할을 나누는 이유
- '실행 취소 기능이 필요하면 `Originator` 역에 그 기능을 만들면 될 되지 않을까'하는 의문을 가질 수 있다.
- `Caretaker`는 어느 시점에 스냅샷을 찍을지 결정하고, 언제 실행 취소를 할지 결정하고, `Memento`를 저장하는 일을 한다.
- `Originator`는 `Memento`를 만드는 일과 주어진 `Memento`를 사용하여 자신의 상태를 복원하는 일을 한다.
- 이처럼 역할을 분담하고 있기 때문에 다음과 같이 수정하고 싶을 때에도 `Originator`를 변경할 필요가 전혀 없다.

1. 여러 단계의 실행 취소를 할 수 있게 변경하고 싶다.
2. 실행 취소 뿐만 아니라, 현재 상태를 파일에 저장하고 싶다.