---
title: ⛓️ Java Design-Pattern Ⅵ - Singleton
date: 2025-08-03 13:25:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Singleton` 패턴이란?
- 프로그램을 실행하면 보통 많은 인스턴스가 생성된다. 예를 들어 `java.lang.String` 클래스의 경우 문자열 1개에 인스턴스 1개가 생성되므로 문자열이 1000개 등장하는 프로그램이라면 인스턴스도 1000개가 만들어진다.
- 하지만 다음과 같은 경우 어떻게 해야 할까?

 _"지정한 클래스의 인스턴스가 반드시 1개만 존재한다는 것을 보증하고 싶을 때"  
"인스턴스가 하나만 존재한다는 것을 프로그램 상에서 표현하고 싶을 때"_

- 이때 인스턴스가 하나만 존재하는 것을 보증하는 패턴을 `Singleton` 패턴이라고 부른다.

### 예제 프로그램
![](/assets/image/Pasted%20image%2020250803132625.png)
- `Singleton`은 `static` 필드로 정의된다. 
- 클래스 변수라고도 한다.
- 해당 필드는 `Singleton` 클래스의 인스턴스에서 초기화하며, 초기화는 클래스를 로드할 때 한 번만 실행된다.
- 생성자는 `private`로 되어 있기 때문에 외부에서 생성자 호출을 금지한다. 
- 만약 외부에서 생성자를 호출할 경우 컴파일할 때 에러가 발생한다.
- 그런데 다음과 같은 의문이 생길 수 있다.

_"처음부터 프로그래머가 주의해서 외부에서 생성자 호출을 하지 않으면 되지 않나?"  
"애초에 싱글톤 클래스를 만들 필요 없이 프로그래머가 인스턴스를 한 번만 생성하면 되는 것 아닌가?"_

- 해당 패턴은 프로그래머가 어떤 실수를 하더라도 인스턴스가 하나만 생성되는 것을 보증하는 패턴이므로 위의 의문들은 의미가 없다.
- `getInstance`는 `Singleton` 클래스의 유일한 인스턴스를 얻는 메소드이다. 
- 사실 해당 메소드는 `static Factory Method`의 일종이다. 
- 이름은 달리 해도 되지만, 유일한 인스턴스를 얻을 방법이 무언가 필요하다.

#### `Main` 클래스

```java
public class Main {
	public static void main(String[] args) {
    	System.out.println("Start");
        
        Singleton obj1 = Singleton.getInstance();
        Singleton obj2 = Singleton.getInstance();
		
        if (obj1 == obj2) {
        	System.out.println("obj1과 obj2는 같은 주소를 참조합니다.");
        } else {
        	System.out.println("obj1과 obj2는 서로 다른 주소를 참조합니다.");
        }
        
        System.out.println("End");
	}
}
```
- `Main` 클래스를 실행하면 `obj1`와 `obj2`는 같은 인스턴스임을 알 수 있다.

### `Singleton` 패턴의 등장인물
#### `Singleton` 클래스
- `Singleton` 역할이다. 
- 해당 역할은 유일한 인스턴스를 얻기 위한 `static` 메소드를 갖고 있으며, 항상 같은 인스턴스를 반환한다.


### 책에서 제시하는 힌트
#### 왜 제한할까?
- 인스턴스가 여러 개 존재하면 인스턴스가 서로 영향을 미쳐 의도치 않은 버그를 만들어 낼 수 있다. 
- 하지만 인스턴스가 하나뿐이라는 보장이 있다면 그 전제 조건 하에서 프로그래밍을 할 수 있다. 
- 전제 조건을 두고 프로그래밍하는 것은 사이드 이펙트를 제어하는데 효과적이다.

#### 유일한 인스턴스는 언제 생성되는가?
- 예제 프로그램에서는 `getInstance` 메소드를 호출할 때 `Singleton` 클래스가 로드되며 해당 클래스가 초기화된다. 
- 이 때 `static` 필드가 초기화되며 유일한 인스턴스가 생성된다.

#### `Enum`을 이용한 `Singleton`

```java
enum Singleton {
	INSTANCE;
    
    public void hello() {
    	System.out.println("hello is called");
    }
}
```
- `enum`의 요소는 상수로서 인스턴스의 유일성을 보증받는다. 
- 그러므로 요소를 하나만 가지는 `enum`을 이용하여 상기 코드와 같이 `Singleton` 패턴을 구현할 수 있다.
- 다음 코드와 같이 유일한 인스턴스에 액세스하여 메소드를 호출할 수 있다.

```java
Singleton.INSTANCE.hello();
```
