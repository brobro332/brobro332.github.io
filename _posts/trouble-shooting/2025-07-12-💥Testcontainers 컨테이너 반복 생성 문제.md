---
title: 💥 Testcontainers 컨테이너 반복 생성 문제
date: 2025-07-12 22:36:00 +0900
categories:
  - Trouble-Shooting
tags:
  - Trouble-Shooting
  - Test
  - Testcontainers
---

### 문제 상황
- `Testcontainers`를 통해 테스트 용도의 `PostgreSQL` 컨테이너를 띄우고자 추상 클래스를 생성했다.
- 하나의 컨테이너로 모든 자식 클래스들의 테스트를 수행할 것이라 예상했지만 그렇지 않았다.
- 실제로는 자식 클래스 별로 컨테이너가 새로 생성되었으며, 해당 컨테이너에 연결 또한 실패했다.


### 문제 원인

```java
@Testcontainers 
public abstract class ContainerBaseTest { 
	@Container 
	static PostgreSQLContainer<?> postgresContainer = new PostgreSQLContainer<>("postgres:15-alpine"); 
	
	@DynamicPropertySource 
	static void overrideProps(DynamicPropertyRegistry registry) { 
		registry.add("spring.datasource.url", postgresContainer::getJdbcUrl);
		registry.add("spring.datasource.username", postgresContainer::getUsername);
		registry.add("spring.datasource.password", postgresContainer::getPassword);
		registry.add("file.upload-dir", () -> "/test-uploads"); 
	} 
}
```
- `@Container` `Annotation`의 동작 방식이 문제가 되었다.
- `Testcontainers`는 `@Container`가 붙은 필드를 발견하면 해당 컨테이너의 라이프 사이클을 자동으로 관리한다.
- 일반적으로 `static` 필드에 `@Container`를 붙이면 해당 `Container`는 테스트 클래스 전체에서 한 번만 시작되고 종료된다.
- 문제는 이 static 필드가 추상 클래스에 정의되어 있을 때 발생한다. 
- `Testcontainers`의 `JUnit 5` 통합 테스트는 추상 클래스에 정의된 `static` `@Container` 필드를 처리할 때, 이를 상속 받는 각 구체적인 테스트 클래스마다 별도의 `Container` 인스턴스를 시작하도록 동작한다.
- 이는 `Testcontainers`가 각 테스트 클래스 컨텍스트 내에서 `static` 필드를 재정의하는 방식으로 처리하기 때문이다.


### 해결 방법

```java
@Testcontainers  
public abstract class ContainerBaseTest {  
	private static final PostgreSQLContainer<?> POSTGRES_CONTAINER;  
	
	@BeforeAll  
	static void init() {  
		Dotenv dotenv = Dotenv.load();  
		dotenv.entries().forEach(entry ->  
			System.setProperty(entry.getKey(), entry.getValue())  
		);  
	}  
	
	static {  
		POSTGRES_CONTAINER = new PostgreSQLContainer<>("postgres:15-alpine");  
		POSTGRES_CONTAINER.start();  
	}  
	
	@DynamicPropertySource  
	static void overrideProps(DynamicPropertyRegistry registry) {  
		registry.add("spring.datasource.url", POSTGRES_CONTAINER::getJdbcUrl);  
		registry.add("spring.datasource.username", POSTGRES_CONTAINER::getUsername);  
		registry.add("spring.datasource.password", POSTGRES_CONTAINER::getPassword);  
		registry.add("file.upload-dir", () -> "/test-uploads");  
	}  
}
```
- `@Container` `Annotation`을 사용하지 않고 `static` 블록에서 `Container`를 수동으로 시작하는 싱글톤 `Container`를 구성하는 것이다.
- `ContainerBaseTest` 클래스가 `JVM`에 로드될 때 `static` 블록이 단 한 번만 실행되어 컨테이너가 한
  번만 생성된다. 
- 이후 `ContainerBaseTest`를 상속 받는 모든 테스트 클래스는 이 이미 시작된 단일 컨테이너 인스턴스를 공유하게 된다.


### 회고
- `@Container` `Annotation`을 사용하여 특정 컨테이너의 라이프 사이클을 자동으로 관리하게 한다는 개념을 알더라도 구체적인 내부 동작을 알아야 의도한 대로 환경을 구축할 수 있다는 사실을 체감했다.