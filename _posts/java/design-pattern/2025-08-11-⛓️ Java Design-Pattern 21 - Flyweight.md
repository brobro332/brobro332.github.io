---
title: ⛓️ Java Design-Pattern 21 - Flyweight
date: 2025-08-11 18:14:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Flyweight` 패턴이란?
- `Flyweight`란 복싱에서 가장 가벼운 체급인 플라이급을 나타낸다.
- 즉, 이 디자인 패턴은 객체를 가볍게 만들기 위한 것이다.
- 여기서 말하는 무게는 메모리 사용량을 의미한다.
- `Java`에서는 `new` 키워드를 통해 인스턴스를 만들 수 있다.
- 그러나 특정 클래스의 인스턴스가 많이 필요해서 `new`를 잔뜩 사용하면 메모리 사용량이 커진다.
- `Flyweight` 패턴에서 사용하는 기법을 한 줄로 요약하면, '인스턴스를 최대한 공유하고 쓸데없이 `new`하지 않는 것'이다.
- 인스턴스가 필요할 때 항상 `new` 키워드를 사용하는 것이 아니라, 이미 만들어진 인스턴스를 이용할 수 있다면 그것을 공유해서 사용하는 것이다. 


### 예제 프로그램
- 무거운 인스턴스를 만드는 클래스로 큰 문자를 표현하는 예제 프로그램이다.
- 큰 문자는 작은 문자를 모아서 만든다.
- `BigChar`는 큰 문자를 표현하는 클래스로, 메모리 소비를 줄이기 위해 해당 인스턴스를 공유하는 것이 관건이다.
- 해당 인스턴스가 이미 존재하는지 여부를 관리하도록 `java.util.HashMap`을 사용한다.

![](/assets/image/Pasted%20image%2020250811195244.png)

#### 숫자 파일

```txt
....######......
..##......##....
..##......##....
....######......
..##......##....
..##......##....
....######......
................
```
- 10 미만의 숫자와 '-' 기호에 대한 파일이 존재한다.
- 예를 들면 위 파일은 `big8.txt`이다.

#### `BigChar` 클래스

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class BigChar {
	// 문자의 이름
	private char charname;
	
	// 큰 문자를 표현하는 문자열('#' '.' '\n'으로 이루어진 열)
	private String fontdata;
	
	// 생성자
	public BigChar(char charname) {
		this.charname = charname;
		try {
			String filename = "big" + charname + ".txt";
			StringBuilder sb = new StringBuilder();
			
			for (String line: Files.readAllLines(Path.of(filename))) {
				sb.append(line);
				sb.append("\n");
			}
			
			this.fontdata = sb.toString();
		} catch (IOException e) {
			this.fontdata = charname + "?";
		}
	}
	
	// 큰 문자를 표시한다
	public void print() {
		System.out.print(fontdata);
	}
}
```
- 큰 문자를 나타내는 클래스이다.
- 생성된 문자열은 `fontdata` 필드에 저장한다.

#### `BigCharFactory` 클래스

```java
import java.util.HashMap;
import java.util.Map;

public class BigCharFactory {
	/* 이미 만든 BigChar 인스턴스를 관리 */
	private Map<String,BigChar> pool = new HashMap<>();
	
	/* Singleton 패턴 */
	private static BigCharFactory singleton = new BigCharFactory();
	
	/* 생성자 */
	private BigCharFactory() { }
	
	/* 유일한 인스턴스를 얻는다 */
	public static BigCharFactory getInstance() {
		return singleton;
	}
	
	/* BigChar 인스턴스 생성(공유) */
	public synchronized BigChar getBigChar(char charname) {
		BigChar bc = pool.get(String.valueOf(charname));
		
		if (bc == null) {
			// 여기서 BigChar 인스턴스를 생성
			bc = new BigChar(charname);
			pool.put(String.valueOf(charname), bc);
		}
		
		return bc;
	}
}
```
- `BigChar`의 인스턴스를 만드는 공장이다.
- `pool` 필드는 `BigChar`의 인스턴스를 관리한다.
- 즉, `BigCharFactory`의 `pool`에는 이미 만들어진 `BigChar` 인스턴스가 모여있다.
- 가령 문자 '3'에 대응하는 `BigChar`를 얻고 싶을 경우 "3"이라는 문자열이 키가 되고, 파일 `big3.txt`로부터 만들어지는 `BigChar` 인스턴스가 값이 된다.
- `BigCharFactory` 클래스는 `Singleton` 패턴을 사용해 구현했다.
- 해당 인스턴스는 하나만 필요하기 때문이다.
- `getBigChar()` 메서드는 `Flyweight` 패턴의 중심이 되는 메서드이다.
- 이 메서드는 주어진 인수에 해당하는 `BigChar` 인스턴스를 만든다.
- 단, 이미 같은 문자에 해당하는 인스턴스가 존재한다면, 새로 만들지는 않는다.
- `getBigChar()` 메서드가 `syncronized`로 되어 있는 이유는 멀티 쓰레드 환경에서 한 번에 하나의 쓰레드만 이 메서드를 실행하도록 강제하기 위함이다.

#### `BigString` 클래스

```java
public class BigString {
	/* '큰 문자'의 배열 */
	private BigChar[] bigchars;
	
	/* 생성자 */
	public BigString(String string) {
		BigCharFactory factory = BigCharFactory.getInstance();
		bigchars = new BigChar[string.length()];
		
		for (int i = 0; i < bigchars.length; i++) {
			bigchars[i] = factory.getBigChar(string.charAt(i));
		}
	}
	
	/* 표시 */
	public void print() {
		for (BigChar bc: bigchars) {
			bc.print();
		}
	}
}
```
- `BigChar`를 모은 큰 문자열 클래스이다.
- `bigchars` 필드는 `BigChar`의 배열이며 `BigChar`의 인스턴스를 저장한다.
- 생성자의 `for`문을 보면 `getBigChar()` 메서드를 사용해 작성되어 있다.
- 즉, `new` 키워드를 사용하지 않았기 때문에 같은 문자열에서 생성하는 `BigChar` 인스턴스는 공유된다.

#### `Main` 클래스

```java
public class Main {
	public static void main(String[] args) {
		if (args.length == 0) {
			System.out.println("Usage: java Main digits");
			System.out.println("Example: java Main 1212123");
			System.exit(0);
		}
		
		BigString bs = new BigString(args[0]);
		bs.print();
	}
}
```
- 주어진 인수를 바탕으로 문자열을 생성하고 출력한다.


### `Flyweight`  패턴의 등장인물
#### `BigChar` 클래스
- 평소처럼 다루면 프로그램이 무거워지기 때문에 공유하기 편이 나은 것을 나타내는 플라이급(`Flyweight`) 역을 맡았다.

#### `BigCharFactory` 클래스
- `Flyweight`를 만드는 공장인 플라이급 공장(`FlyweightFactory`) 역을 맡았다.
- 이 공장을 사용해 `Flyweight`를 만들면 인스턴스가 공유된다.

#### `BigString` 클래스
- `FlyweightFactory`을 사용하여 `Flyweight`를 만들어내고 이용하는 의뢰자(`Client`) 역을 맡았다.


### 책에서 제시하는 힌트
#### 여러 장소에 영향을 미친다.
- 인스턴스를 공유할 때는 여러 곳에 영향을 미친다는 점을 주의해야 한다.
- 즉, 하나의 인스턴스를 변경하는 것만으로 해당 인스턴스를 사용하는 모든 곳에 동시에 변경 사항이 반영된다.
- 가령 `BigChar` 클래스에서 '3'의 `fontdata`를 변경한다면 `BigString`에서 사용되는 '3'의 글꼴은 모두 변경된다.
- 이렇게 여러 곳에 영향을 미치는 것이 프로그램이 다루는 문제에 따라 좋은 경우도 있고 그렇지 않은 경우도 있다.
- 그러므로 `Flyweight` 역이 가질 정보는 잘 생각해서 골라야 한다.
- 정말 여러 곳에서 공유해야 할 정보만 해당 역이 갖도록 하는 것이 올바르다.
- 예제 프로그램에 색을 추가한다고 하면 해당 정보는 어떤 클래스가 갖도록 해야 할까?
- 만약 `BigChar`에 색 정보를 갖도록 한다면 `BigChar` 별로 색 정보가 공유되어 반영될 것이다.
- 가령 `BigString`에 색 정보를 갖게 한다고 하면, '세 번째 글자는 빨간색'과 같은 색 정보를 `BigString`이 갖게 하여, 같은 `BigChar` 인스턴스라도 다른 색으로 지정할 수 있다.
- 어떤 정보를 공유하고, 공유하지 않을 지 클래스 사용 목적에 달려 있다.

#### `intrinsic`과 `extrinsic`
- 공유하는 정보는 `intrinsic`한 정보라고 한다.
- 그 인스턴스를 어디에 가지고 가도 어떤 상황에서도 변하지 않는 정보다.
- 가령 `BigChar`의 폰트 데이터는 `BigString`의 어디에 등장해도 변하지 않는다.
- 공유하지 않는 정보를 `extrinsic`한 정보라고 한다.
- 인스턴스 배치 장소에 따라 변경되는 정보, 상황에 따라 변화하는 정보, 상태에 의존하는 정보이다.
- 가령 `BigChar`의 인스턴스가 `BigString`의 몇 번째 문자인가 하는 정보는 `BigChar`의 위치에 따라 달라지므로 `BigChar`가 가질 수는 없다.

#### 관리되는 인스턴스는 가비지 컬렉션되지 않는다.
- `BigCharFactory`에서는 `java.util.HashMap`을 이용해서 생성한 `BigChar`의 인스턴스를 관리한다.
- 이처럼 인스턴스를 관리하는 기능을 구현했을 때, 반드시 '관리되는 인스턴스는 가비지 컬렉션되지 않는다'는 점을 의식할 필요가 있다.
- 예제 프로그램을 보면 `pool` 필드로 `BigChar` 인스턴스를 관리하고 있다.
- 실제로 `BigString` 인스턴스에서 `BigChar` 인스턴스를 참조하지 않더라도 `pool` 필드로 관리되고 있으므로 쓰레기로 간주되지 않는다.
- 그렇다는 것은 일단 한 번 만든 `BigChar` 클래스의 인스턴스는 메모리에 계속 남아있다는 말이다.
- 장시간 또는 적은 메모리로 동작하는 프로그램을 설계할 경우 이 점을 유의해야 한다.
- 인스턴스를 명시적으로 삭제할 순 없지만, 인스턴스에 대한 참조를 없앨 수는 있다.
- 가령 `HashMap`에서 해당 인스턴스를 포함한 엔트리를 삭제하면 인스턴스에 대한 참조를 없앨 수 있다.

#### 메모리 이외의 리소스
- `Flyweight` 패턴을 사용하면 메모리 사용량을 줄일 수 있다고 했다.
- 더 일반적으로 얘기하면, 인스턴스를 공유하면 인스턴스를 생성하는 데 필요한 리소스를 줄일 수 있다.
- 가령 인스턴스를 새로 만드는 횟수를 줄일 수 있고, 이를 통해 프로그램 속도를 향상시킬 수 있다.
- 파일 핸들이나 윈도우 핸들 등도 자원의 일종이다.
- `OS`에 따라서는 동시에 사용할 수 있는 파일 핸들이나 윈도우 핸들 수에 제한이 있고, 그럴 때 인스턴스를 공유해두지 않으면 이 제한에 걸려 프로그램이 동작하지 않을 위험이 있다.

#### `static Factory Method`
- 예제 프로그램의 `BigCharFactory` 클래스에는 인스턴스를 얻기 위한 `static` 메서드가 몇 개 등장했다.
- `getInstance()` 메서드는 유일한 인스턴스를 얻기 위한 것이고, `String.valueOf()` 메서드는 특정 문자에 대응하는 문자열 표현을 얻기 위한 것이다.
- 이들은 `static Factory Method`라고 할 수 있다.