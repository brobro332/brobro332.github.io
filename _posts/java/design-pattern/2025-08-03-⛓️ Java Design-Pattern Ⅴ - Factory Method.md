---
title: ⛓️ Java Design-Pattern Ⅴ - Factory Method
date: 2025-08-03 12:47:00 +0900
categories:
  - Java
tags:
  - Java
  - Design-Pattern
---

![](/assets/image/Pasted%20image%2020250528230454.png)
> 📗 `『JAVA 언어로 배우는 디자인 패턴 : 쉽게 배우는 GoF의 23가지 디자인 패턴』`를 읽고 정리한 글입니다.

### `Factory Method` 패턴이란?
- 인스턴스 생성 방법을 상위 클래스에서 결정하되, 실제 인스턴스는 하위 클래스에서 생성한다. 
- 즉 인스턴스를 생성하는 공장을 `Template Method` 패턴으로 구성한 것이다.


### 예제 프로그램
![](/assets/image/Pasted%20image%2020250803130339.png)
- `framework` 패키지에 포함된 `Product`, `Factory` 클래스는 인스턴스를 생성하는 뼈대 역할을 한다.
- `idcard` 패키지에 포함된 `IDCard`, `IDCardFactory` 클래스는 구체적인 내용을 구현하는 역할을 한다.
- `Main` 클래스는 동작 테스트를 위한 클래스이다.

#### `Product` 클래스

```java
package framework;

public abstract class Product {
	public abstract void use();
}
```
- 이 프레임워크에서는 무엇이든 `use`할 수 있는 것을 제품으로 규정한다.

#### `Factory` 클래스

```java
package framework;

public abstract class Factory {
	public final Product create(String owner) {
    	Product p = createProduct(owner);
        registerProduct(p);
        return p;
    }
    
    protected abstract Product createProduct(String owner);
    protected abstract void registerProduct(String product);
}
```
- `Template Method` 패턴을 사용하여 `create` 메소드를 통해 `Product` 인스턴스를 생성한다. 
- `create` 메소드 내 `createProduct`, `registerProduct` 메소드는 추상 메소드로 선언되어 있다.
- 이처럼 `Factory Method` 패턴은 인스턴스를 생성할 때 `Template Method` 패턴을 사용한다.

#### `IDCard` 클래스

```java
package idcard;

import framework.Product;

public class IDCard extends Product {
	private String owner;
    
    IDCard(String owner) {
    	System.out.println(owner + "의 카드를 만듭니다.");
        this.owner = owner;
    }
    
    @Override
    public void use() {
    	System.out.println(this + "을 사용합니다.");
    }
    
    @Override
    public String toString() {
    	return "[IDCard:" + owner + "]"
    }
    
    public String getOwner() {
    	return owner;
    }
}
```
- `Product` 클래스의 하위 클래스로서 정의된 클래스이다.

#### `IDCardFactory` 클래스

```java
package idcard;

import framework.Factory;
import framework.Product;
import java.util.ArrayList;
import java.util.List;

public class IDCardFactory extends Factory {
    private List<String> owners = new ArrayList<>();
    
    @Override
    protected Product createProduct(String owner) {
        return new IDCard(owner);
    }
    
    @Override
    protected void registerProduct(Product product) {
        if (product instanceof IDCard) {
            IDCard card = (IDCard) product;
            owners.add(card.getOwner());
        }
    }
    
    public List<String> getOwners() {
        return owners;
    }
}
```

#### `Main` 클래스

```java
import framework.Factory;
import framework.Product;
import idcard.IDCardFactory;

public class Main {
	public static void main(String args[]) {
    	Factory factory = new IDCardFactory();
        Product card1 = factory.create("김아무개");
        Product card2 = factory.create("박아무개");
        Product card3 = factory.create("최아무개");
        
        card1.use();
        card2.use();
        card3.use();
    }
}
```
- 실제로 `IDCardFactory` 인스턴스를 만들어 사용하는 동작 테스트를 위한 클래스이다.


### `Factory Method` 패턴의 등장인물
#### `Product` 클래스
- 프레임워크에 해당되며, 제품역할로, 이 패턴으로 생성되는 인스턴스가 가져야 할 인터페이스 `API`를 결정하는 추상 클래스이다.

#### `Factory` 클래스
- 프레임워크에 해당되며, 제품 역할을 생성하는 작성자 `Creator` 역할을 한다. 
- 실제로 생성할 `ConcreteProduct` 역할에는 아는 바가 없다.
- 예제 프로그램에서는 `new` 대신 `createProduct` 메소드를 통해 인스턴스를 생성하는데, 이를 통해 구체적인 클래스 명에 의존하지 않을 수 있게 된다.

##### `IDCard` 클래스
- 구체적인 제품 `ConcreteProduct` 역할을 맡아 제품 역할의 구체적인 구현을 수행한다.

##### `IDCardFactory` 클래스
- 구체적인 작성자 `ConcreteCreator` 역할을 맡아 작성자 역할의 구체적인 구현을 수행한다.


### 책에서 제시하는 힌트

#### 프레임워크와 구체적인 내용
- 여기서 같은 프레임 워크를 사용하여 `IDCard`가 아닌 `TV`를 만든다고 가정해보자. 
- 이 경우 `framework` 패키지를 `import`하는 별개의 `television` 패키지를 만들게 된다.
- 그런데, `framework` 패키지 내 클래스들은 수정할 필요가 없다. 
- 왜냐하면 `framework` 패키지 내에서는 `idcard` 패키지를 `import` 한 적이 없다. 
- 즉 해당 패턴에서 프레임워크는 구체적인 내용에 의존하지 않는다는 것을 알 수 있다.

#### 인스턴스 생성: 메소드 구현 방법
- 인스턴스 생성 메소드를 기술하는 방법은 다음 두 가지로 생각할 수 있다.

```java
/* 1. 추상 메소드로 기술 */
abstract class Factory {
	public abstract Product createProduct(String name);
	
    // ...
}

/* 2. 디폴트 구현을 준비 */
class Factory {
	public Product createProduct(String name) {
    	return new Product(name);
    }
}
```
- 추상 메소드로 기술하는 경우 예제 프로그램에서 사용한 방법으로, 추상 메소드로 기술할 경우 구현되어 있지 않더라도 컴파일할 때 검출된다는 장점이 있다.
- 디폴트 구현을 준비하는 경우 하위 클래스에서 구현하지 않더라도 디폴트 구현이 사용된다는 장점이 있다. 
- 다만, `Product` 클래스에 대해 직접 `new`를 실행하여 인스턴스를 생성하므로 `Product` 클래스를 추상 클래스로 둘 수 없다.

#### 패턴 이용과 개발자 간의 의사소통
- 하나의 클래스만 읽어서는 동작을 잘 이해할 수 없다. 
- 상위 클래스에서 동작의 뼈대를 이해한 후 거기에서 사용되는 추상 메소드가 무엇인지 확인하고, 다시 그 추상 메소드를 실제로 구현하는 클래스의 소스 코드를 살펴보아야 한다.
- 일반적으로 디자인 패턴을 사용해 어떤 클래스 군을 설계할 경우, 설계자가 보수자에게 의도한 디자인 패턴이 무엇인지 잘 전달할 필요가 있다. 
- 그렇지 않으면 설계자의 의도에서 벗어난 수정이 가해질 수 있다.
- 주석이나 개발 문서에 실제로 사용되는 디자인 패턴의 명칭과 의도를 기술하여 두는 것도 좋은 방법이다.

#### `static Factory Method`

```java
/* 1. java.security.SecureRandom */
SecureRandom random = SecureRandom.getInstance("NativePRNG");

/* 2. java.util.List */
List<String> list = List.of("Alice", "Bob");

/* 3. java.util.Arrays */
String[] arr = {"Alice", "Bob"};
List<String> list = Arrays.asList(arr);

/* 4. java.lang.String */
String string = String.valueOf('A');

/* 5. java.time.Instant */
Instant instant = Instant.now();
```
- 인스턴스 생성을 위한 `static` 메소드 전반을 `Factory Method`라고 부르는 경우가 존재한다. 
- 이것은 `GoF`의 `Factory Method` 패턴과는 다르지만 `Java`에서 인스턴스를 생성할 때 자주 사용되는 기법이다.
- `Java API` 래퍼런스에서도 인스턴스 생성을 위한 클래스 메소드를 `static Factory Method`로 표현하기도 하므로 `Java API` 레퍼런스를 읽을 때는 참조하는 클래스에 `static Factory Method`가 제공되는지 주목해야 한다.
- `create`, `newInstance`, `getInstance` 등의 이름이 자주 사용되지만, 그 밖의 이름이 사용되는 경우도 있다.