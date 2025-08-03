---
title: ⛓️ Java Design-Pattern Ⅺ - Strategy
date: 2025-08-03 02:30:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Strategy` 패턴이란?
- 스위치를 전환하듯 알고리즘을 바꿔서, 같은 문제를 다른 방법으로 해결하기 쉽게 만들어주는 패턴이다.


### 예제 프로그램
- 다음은 컴퓨터로 가위바위보를 하는 프로그램이다.

![](/assets/image/Pasted%20image%2020250803143222.png)

#### `Hand` 타입

```java
public enum Hand {
	ROCK("바위", 0),
    SCISSORS("가위", 1),
    PAPER("보", 2);
    
    private String name;
    private int handValue;
    
    private static Hand[] hands = {
    	ROCK, SCISSORS, PAPER
    };
    
    private Hand(String name, int handValue) {
    	this.name = name;
        this.handValue = handValue;
    }
    
    public static Hand getHand(int handValue) {
    	return Hands[handValue];
    }
    
    public boolean isStrongerThan(Hand h) {
    	return fight(h) == 1;
    }
    
    public boolean isWeakerThan(Hand h) {
    	return fight(h) == -1;
    }
    
    private int fight(Hand h) {
    	if (this == h) {
        	return 0;
        } else if ((this.handValue + 1) % 3 == h.handValue) {
        	return 1;
        } else {
        	return -1;
        }
    }
    
    @Override
    public String toString() {
    	return name;
    }
}
```
- 가위바위보의 손을 나타내는 `enum`형으로 가위, 바위, 보를 각각 `SCISSORS`, `ROCK`, `PAPER`라는 `enum` 상수로 나타낸다. 
- `getHand` 메소드를 사용하여 0, 1, 2를 파라미터로 주면 `Hand`형의 인스턴스를 얻을 수 있다. 
- 이 구조는 `Singleton` 패턴의 일종이다. 
- 또한 `getHand` 메소드는 `static Factory Method`라고 할 수 있다.
- `isStrongerThan`, `isWeakerThan`은 손의 강약을 비교하는 메소드이다. 
- 다만 실제로 손의 강약을 판단하는 메소드는 `flight`이다.

#### `Strategy` 인터페이스

```java
public interface Strategy {
	public abstract Hand nextHand();
    public abstract void study(boolean win);
}
```
- 가위바위보 전략을 위한 추상 메소드를 모은 이다.
- `nextHand`는 다음에 낼 손을 얻기 위한 메소드이다. 
- 해당 메소드를 호출하면 구현체에 해당하는 전략을 수행한다.
- `strategy`는 직전에 낸 손으로 이겼는지 졌는지를 학습하는 메소드이다.

#### `WinningStrategy` 클래스

```java
import java.util.Random;

public class WinningStrategy implements Strategy {
	private Random random;
    private boolean won = false;
    private Hand prevHand;
    
    public WinningStrategy(int seed) {
    	random = new Random(seed);
    }
    
    @Override
    public Hand nextHand() {
    	if (!won) {
        	prevHand = Hand.getHand(random.nextInt(3));
        }
        return prevHand;
    }
    
    @Override
    public void study(boolean win) {
    	won = win;
    }
}
```
- `Strategy` 인터페이스를 구현하는 클래스 중 하나이다. 
- 해당 인터페이스를 구현한다는 것은 `nextHand`, `study`라는 두 메소드를 구현하겠다는 것이다.
- 이 클래스는 이전 승부에 이길 경우, 다음에도 같은 손을 내는 전략을 취한다. 
- 만약 이전 승부에서 졌다면, 다음 손은 난수를 사용하여 결정한다.

#### `ProbStrategy` 클래스

```java
import java.util.Random;

public class ProbStrategy implements Strategy {
	private Random random;
    private int prevHandValue = 0;
    private int currentHandValue = 0;
    private int[][] history = {
    	{1, 1, 1},
        {1, 1, 1},
        {1, 1, 1}
    };
    
    public ProbStrategy(int seed) {
    	random = new Random(seed);
    }
    
    @Override
    public Hand nextHand() {
    	int bet = random.nextInt(getSum(currentHandValue));
        int handValue = 0;
        if (bet < history[currentHandValue][0]) {
        	handValue = 0;
        } else if (bet < history[currentHandValue][0] + history[currentHandValue][1]) {
        	handValue = 1;
        } else {
        	handValue = 2;
        }
        prevHandValue = currentHandValue;
        currentHandValue = handValue;
        
        return Hand.getHand(handValue);
    }
    
    private int getSum(int handValue) {
    	int sum = 0;
        for (int i = 0; i < 3; i++) {
        	sum += history[handValue][i];
        }
        
        return sum;
    }
    
    @Override
    public void study(boolean win) {
    	if (win) {
        	history[prevHandValue][currentHandValue]++;
        } else {
        	history[prevHandValue][(currentHandValue + 1) % 3]++;
            history[prevHandValue][(currentHandValue + 2) % 3]++;
        }
    }
}
```
- `ProbStrategy` 클래스는 또 하나의 구체적인 전략이다. 
- 과거의 이기고 진 전적을 활용해서 손 낼 확률을 바꾼다.
- `history` 필드는 과거의 승패를 반영한 확률 계산을 위한 표로 되어 있다. 
- 각 차원의 `index`는 다음과 같은 의미가 있다.

```java
history[직전에 낸 손][이번에 낼 손]

// 예시
history[0][0] == 3
history[0][1] == 5
history[0][2] == 7
```
가령 위의 예시와 같은 값이 있다면, `nextHand` 메소드를 호출했을 때 난수가 3 미만이면 바위, 3 이상 8 미만이면 가위, 8 이상 15 미만이면 보를 반환한다.

#### `Player` 클래스

```java
public class Player {
	private String name;
    private Strategy strategy;
    private int winCount;
    private int loseCount;
    private int gameCount;
    
    public Player(String name, Strategy strategy) {
    	this.name = name;
        this.strategy = strategy;
    }
    
    public Hand nextHand() {
    	return strategy.nextHand();
    }
    
    public void win() {
    	strategy.study(true);
        winCount++;
        gameCount++;
    }
    
    public void lose() {
    	strategy.study(false);
        loseCount++;
        gameCount++;
    }
    
    public void even() {
    	gameCount++;
    }
    
    @Override
    public String toString() {
    	return "[" 
        	+ name 
            + ":" 
            + gameCount 
            + " games, " 
            + winCount 
            + " win, " 
            + loseCount 
            + " lose" 
            + "]";
    }
}
```
- 가위바위보하는 사람을 표현한 클래스이다. 
- 주어진 `name`과 `strategy`로 인스턴스를 만든다.
- `nextHand`는 다음 손을 얻는 메소드인데, 실제로 다음 손은 인스턴스의 `strategy`가 결정한다. 
- 즉 `Stretegy`에게 처리를 위임하고 있다.
- 이기거나, 질 경우 `strategy` 필드를 통해 `study` 메소드를 호출한다. 
- `XXCount` 필드는 플레이어의 승수를 기록한다.

#### `Main` 클래스

```java
public class Main {
	public static void main(String[] args) {
    	if (args.length != 2) {
        	System.out.println("Usage : java Main randomseed1 randomseed2");
            System.out.println("Example : java Main 314 15");
            System.exit(0);
        }
        
        int seed1 = Integer.parseInt(args[0]);
        int seed2 = Integer.parseInt(args[1]);
        
        Player player1 = new Player("KIM", new WinningStrategy(seed1));
        Player player2 = new Player("LEE", new ProbStrategy(seed2));
        
        for (int i = 0; i < 10000; i++) {
        	Hand nextHand1 = player1.nextHand();
            Hand nextHand2 = player2.nextHand();
        
        	if (nextHand1.isStrongerThan(nextHand2)) {
            	System.out.println("Winner : " + player1);
                player1.win();
                player2.lose();
            } else if (nextHand2.isStrongerThan(nextHand1)) {
            	System.out.println("Winner : " + player2);
                player1.lose();
                player2.win();
            } else {
            	System.out.println("Even...");
                player1.even();
                player2.even();
            }
        }
        
        System.out.println("Total result : ");
        System.out.println(player1);
        System.out.println(player2);
    }
}
```
- 실제 두 명의 플레이어가 가위바위보를 진행하는 클래스이다.


### `Strategy` 패턴의 등장인물
#### `Strategy` 인터페이스
- 전략 `Strategy` 역할을 맡아 전략을 이용하기 위한 인터페이스를 결정한다.

#### `WinningStrategy`, `ProbStrategy` 클래스
- 전략 역할의 인터페이스를 실제로 구현하는 구체적인 전략 `ConcreteStrategy` 역할을 수행한다. 
- 여기서 알고리즘 등 실제 전략을 프로그래밍한다.

#### `Player` 클래스
- 전략 역할을 이용하는 문맥 `Context` 역할을 수행한다. 
- 구체적인 전략 인스턴스를 갖고 있다가 필요에 따라서 이용한다.


### 책에서 제시하는 힌트
#### 일부러 `Strategy` 역할을 만들 필요가 있을까?
- 가령 알고리즘을 개량해서 더 빠르게 만들고 싶다고 할 때, 해당 패턴을 사용하면 인터페이스의 수정 없이 전략 구현체만 수정하면 된다.
- 또한 위임이라는 약한 결합을 사용하므로 다양한 알고리즘 구현체를 용이하게 전환할 수 있다.

#### 실행 중 교체도 가능
- 해당 패턴을 사용하면 프로그램 동작 중에도 전략 구현체를 전환할 수 있다. 
- 예를 들어 메모리가 적은 환경과 메모리가 많은 환경에서 다른 전략을 사용하게 할 수도 있다.

#### 다양한 난수 생성기
##### `java.util.Random`
- 일반적으로 사용되며 선형합동법을 사용한다. 
- `Thread Safe`하다는 특징이 있지만, `ThreadLocalRandom` 쪽이 더 고성능이고, 보안 용도로 사용해서는 안 된다.

##### `java.util.concurrent.ThreadLocalRandom`
- 멀티스레드 환경에서도 고성능이며, 다른 스레드의 영향을 받지 않는다. 
- 마찬가지로 보안 용도로 사용해서는 안 된다.

##### `java.security.SecureRandom`
- 암호학적으로 강한 난수 생성기로, 보안 용도로 사용된다. 
- `Thread Safe`하다.