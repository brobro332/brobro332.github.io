---
title: 🏰 332-blokey-land-service
date: 2025-07-15 20:33:00 +0900
categories:
  - Project
tags:
  - Project
---
![](/assets/image/Pasted%20image%2020250715201843.png)

### 개요
- 프로젝트, 태스크, 마일스톤을 통합적으로 관리할 수 있는 서비스
- 데이터를 시각화하여 프로젝트 현황을 쉽게 파악할 수 있다.


###  **개발 기간**
- `v1.0.0` (`2025-06-06 ~ 2025-06-15`): 서비스 구조 설계 및 초기 기능 구현
- `v1.0.1` (`2025-06-24 ~ 2025-07-07`): 프론트엔드 개발 및 도메인 로직 정비
- `v1.0.2` (`2025-07-08`): 개발 환경에서 웹 접속 시 무한 새로고침 버그 핫픽스
- `v1.1.0` (`2025-07-09 ~ 2025-07-15`): 테스트 환경 구축 및 기존 기능 개선 작업


### **프로젝트 환경**
- `Language`: `Java 21`, `Typescript`
- `Framework`: `Spring boot 3.5.0`
- `Database`: `PostgreSQL`
- `IDE`: `IntelliJ IDEA`
- `CSR`: `React`
- `Build-Tool`: `Gradle`
- `ORM`: `JPA`
- `Query Library`: `QueryDSL`
- `DevOps`: `Docker`, `Docker-compose`
- `Test`: `JUnit`, `JaCoCo`, `SonarQube`, `Cypress`
- `CI`: `GitHub Actions`


### 요청 · 응답 흐름
![](/assets/image/Pasted%20image%2020250715210836.png)
1. 클라이언트가 사이트에 접속하면 `Nginx`가 `React` 정적 빌드 파일을 응답한다.
2. 모든 요청은 `Sentinel-Server` `API Gateway`를 통한다. 
3. 인증 · 인가 요청은 `Sentinel-Server`에서 직접 응답한다.
4. `Blokey-Land-Service`에 대한 `API` 요청은 `Reverse Proxy`를 통해 `Blokey-Land-Service`가 응답한다.


### 인프라 구조 (예정)
![](/assets/image/Pasted%20image%2020250715212713.png)
- 아직 배포는 진행하지 않았으며, 추후 인스턴스를 나누기 위해 `Docker-compose` 단위로 분리하였다.
- `Blokey-Land` 서버에는 아직 `ElasticSearch`를 도입하지 않았다.
- 이 또한 추후 관련 기능과 함께 추가될 예정이다.


### 품질 관리 프로세스
#### ✅ 단위 테스트

```java
@DisplayName("존재하는 ID로 프로젝트를 조회하면 해당 객체를 반환해야 한다.")  
@Test  
void givenValidId_whenReadProjectByProjectId_thenReturnProject() {  
	// given  
	Long id = 1L;  
	Project project = Project.builder()  
		.id(id)  
		.title("제목")  
		.description("설명")  
		.status(ProjectStatusType.ACTIVE)  
		.isPrivate(true)  
		.estimatedStartDate(LocalDate.now())  
		.estimatedEndDate(LocalDate.now())  
		.actualStartDate(LocalDate.now())  
		.actualEndDate(LocalDate.now())  
		.build();  
		
	when(repository.findById(id)).thenReturn(Optional.of(project));  
	
	// when  
	Project found = service.findProjectByProjectId(id);  
	
	// then  
	assertEquals(project.getId(), found.getId());  
	assertEquals(project.getTitle(), found.getTitle());  
	assertEquals(project.getDescription(), found.getDescription());  
	assertEquals(project.getImageUrl(), found.getImageUrl());  
	assertEquals(project.getStatus(), found.getStatus());  
	assertEquals(project.isPrivate(), found.isPrivate());  
	assertEquals(project.getEstimatedStartDate(), found.getEstimatedStartDate());  
	assertEquals(project.getEstimatedEndDate(), found.getEstimatedEndDate());  
	assertEquals(project.getActualStartDate(), found.getActualStartDate());  
	assertEquals(project.getActualEndDate(), found.getActualEndDate());  
	
	verify(repository).findById(id);  
}
```

- `JUnit`을 통해 `TDD` 단위 테스트를 수행한다.
- `Mockito` 프레임워크를 사용하여 레포지토리의 메서드 호출 시 특정 값을 반환하도록 하여 `DB` 연결 없이 테스트를 진행할 수 있다.
- 통합 테스트를 수행하면 좋지만, 개발 및 빌드 과정에서 시간 비용이 크므로 서비스 계층은 단위 테스트로 충족하였다.

#### ✅ 통합 테스트

```java
@Testcontainers  
public abstract class ContainerBaseTest {  
	private static final PostgreSQLContainer<?> POSTGRES_CONTAINER;  
	
	static {  
		Dotenv dotenv = Dotenv.configure()  
			.ignoreIfMissing()  
			.load();  
			
		dotenv.entries().forEach(entry ->  
			System.setProperty(entry.getKey(), entry.getValue())  
		);  
		
		POSTGRES_CONTAINER = new PostgreSQLContainer<>("postgres:15-alpine");  
		POSTGRES_CONTAINER.start();  
	}  
	
	@DynamicPropertySource  
	static void overrideProps(DynamicPropertyRegistry registry) {  
		String originalJdbcUrl = POSTGRES_CONTAINER.getJdbcUrl();  
		String p6spyJdbcUrl = originalJdbcUrl.replace("jdbc:postgresql:", "jdbc:p6spy:postgresql:");  
		registry.add("spring.datasource.url", () -> p6spyJdbcUrl);  
		registry.add("spring.datasource.username", POSTGRES_CONTAINER::getUsername);  
		registry.add("spring.datasource.password", POSTGRES_CONTAINER::getPassword);  
		registry.add("spring.datasource.driver-class-name", () -> "com.p6spy.engine.spy.P6SpyDriver");  
		registry.add("file.upload-dir", () -> "DEFAULT");  
		registry.add("server.port", () -> "8081");  
		registry.add("server.address", () -> "0.0.0.0");  
	}  
}
```
- 통합 테스트는 `Testcontainers`를 사용하여 가상의 `DB`를 사용할 수 있도록 추상 클래스를 작성했다.

```java
@Test  
@DisplayName("사용자 ID로 프로젝트 및 태스크 목록 조회 시 프로젝트 목록과 해당 프로젝트의 태스크 목록이 함께 반환된다.")  
void givenBlokeyId_whenFindProjectsWithTasksByBlokeyId_thenReturnsProjectsWithTasks() { 
    // given  
    taskRepository.save(Task.builder()  
        .title("제목 1")  
        .description("설명 1")  
        .project(project1)  
        .milestone(null)  
        .assignee(blokeyId)  
        .estimatedStartDate(LocalDate.now())  
        .estimatedEndDate(LocalDate.now())  
        .actualStartDate(LocalDate.now())  
        .actualEndDate(LocalDate.now())  
        .build()  
    );  
    
    // when  
    List<Project> result = repository.findProjectsWithTasksByBlokeyId(blokeyId);  
    
    // then  
    assertThat(result).hasSize(2);  
    assertThat(result.getFirst().getTitle()).isEqualTo("제목 1");  
    assertThat(result.getFirst().getTasks()).isNotNull();  
}
```
- 레포지토리 메서드로 매핑되는 쿼리를 테스트하기 위해 통합 테스트를 진행했다.

#### ✅ 테스트 커버리지 모니터링
![](/assets/image/Pasted%20image%2020250715214507.png)
- `JaCoCo`를 사용하여 테스트 커버리지를 개발 환경에서 모니터링 했다.
- 테스트 불필요 코드까지 커버리지 범위에 포함되면 오히려 개발 진행에 방해가 되기 때문에, 대상 코드는 `Service`, `Repository` 클래스로 한정하였다.

#### ✅ `E2E` 테스트
![](/assets/image/Pasted%20image%2020250715215110.png)
- 프론트엔드의 핵심 프로세스를 `ts` 파일로 작성하여 테스트를 진행했다.
- 가령 `Blokey-Land`의 핵심은 간트차트 확인이므로 초기에 주요 데이터를 저장하는 프로세스를 작성해보았다.

#### ✅ `CI` 통합을 통한 품질 관리

```yml
- name: Build and analyze with SonarCloud
  working-directory: spring-boot-app
  env:
    SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
  run: ./gradlew clean build jacocoTestReport sonarqube --info
```
- `GitHub Actions` 워크플로우에 위와 같이 `SonarQube` 분석 `Step`을 추가하였다.

![](/assets/image/Pasted%20image%2020250715220707.png)
- 품질 게이트를 설정하여 `CI`가 성공적으로 진행되면, `SonarQube` 클라우드 서비스는 `PR`에 코드를 `Push`할 경우 새로운 코드에 대한 이슈, 코드 커버리지를 분석하여 코멘트를 남긴다.


### 어려웠던 점
#### ✅ 벡엔드 테이블 설계 및 구현
- 테이블을 설계할 때 `N+1` 등 예상치 못한 문제를 예방하기 위해 가급적 연관 관계를 갖지 않도록 하였다.
- 그러나 간트차트를 렌더링 할 때는 프론트엔드에서 프로젝트를 조회하고, 해당 목록을 순회하며 태스크를 조회하다보니 네트워크 요청을 `N+1`번이나 하게 되었다.
- 해당 문제를 해결하기 위해 프로젝트와 태스크는 양방향 연관관계를 갖도록 수정하였다.
- 서버단에서 `DB`에 `N+1`번 요청을 하지 않도록 패치 조인을 사용하여 조회하였다. 

#### ✅ 프론트엔드 상태 관리
- 가령 `A` → `B`→ `C` 순서로 컴포넌트를 호출하고 `A`의 상태를 `C`에서 조작해야 할 경우 `B`에서 그 흐름이 끊기면 컴파일을 통해 문제를 디버깅 할 수 없어 문제를 해결하는데 오랜 시간이 걸렸다.
- 상태를 `props`로만 전달하는 구조는 깊어지면 관리가 어려워 진다는 점을 알게 되었다.
- 추후 `Context`, `Redux` 같은 패턴을 적극적으로 도입할 예정이다.

#### ✅ `Testcontainers` 컨테이너를 공유하지 못하고 재생성하는 문제 

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
- 처음에는 `@Container` `Annotation`을 사용하여 위와 같은 클래스를 상속받아 해당 컨테이너의 라이프 사이클을 관리했다.
- 그런데 `@Testcontainers` 컨테이너를 공유하지 못하고 해당 클래스를 상속 받는 자식 클래스 개수 만큼 컨테이너 재생성을 시도하는 문제가 발생했다.
- 컨테이너를 자식 클래스 개수 만큼 재생성하면, 리소스도 크고 실제로 컨테이너 생성 시간 때문에 `DB` 연결 문제로 테스트도 실패했다.
- `@Container` `Annotation`을 없애고, 싱글톤 방식으로 컨테이너를 관리하며 수동으로 컨테이너를 생성하여 문제를 해결하였다.


### 향후 계획
#### ✅ 프로젝트와 멤버 매칭 기능 추가
- 프로젝트와 멤버 매칭 기능은 `Blokey-Land` 서비스의 핵심 가치 중 하나로, 사용자 친화적인 경험을 제공하기 위해 기획 단계부터 중요한 요소로 고려되었다.
- 이를 위해 프로젝트 및 사용자 도메인에 스킬(`skill`), 포지션(`position`) 필드를 추가하고, `ElasticSearch`를 도입하여 프로젝트와 멤버 간 추천 및 매칭 기능을 개발하고자 한다.

#### ✅ 기존 기능 최적화
- 아직 양방향으로 연관 관계를 전환해야 할 도메인 관계가 남아있다.
- 아울러 도메인 연관 관계 전환에 따른 프론트엔드의 화면 구성과 벡엔드의 쿼리를 최적화할 예정이다.

#### ✅ 디자인
- 초기에는 사용자에게 친근한 인상을 주기 위해 `Blokey-Land`라는 네이밍과 감성적인 디자인을 적용했으나, 사이트에 처음 방문하는 사용자가 서비스 목적을 직관적으로 이해하기 어려울 것 같다는 생각이 들었다.
- 보다 전문적이고 모던한 느낌의 디자인으로 리디자인을 진행하여 서비스의 목적과 기능을 명확히 전달하고자 한다.


### `GitHub Link`
- https://github.com/brobro332/332-blokey-land-service