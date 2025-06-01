---
title: 💎 Redis Caching
date: 2025-04-06 01:08:00 +0900
categories:
  - Tech
tags:
  - Tech
  - Redis
---

### `Redis` 도입 근거
- 여러 서비스에서 자주 조회하는 데이터에 대해 `Caching` 처리가 필요했다.


### `Redis` `Value` 처리 방식
#### ✅ 문자열 방식
```bash
"user:test@example.com" => 
"{\"email\":\"test@example.com\", \"name\":\"홍길동\", \"service\":\"helpdesk\"}"
```
- `JSON` 또는 직렬화 된 객체 `Value`를 하나의 문자열로 처리한다.
- 단순하고 빠르며, 한 번에 저장하고 조회를 하는 것이 가능하다.
- 하지만 내부 필드별 접근을 하려면 파싱 처리를 추가적으로 해야 하고 정렬, 조건별 필터링이 어렵다.

#### ✅ 해시 방식
```bash
HMSET user:test@example.com 
  email       test@example.com 
  name        홍길동 
  service     helpdesk
```
- 필드 단위로 접근이 가능하기 때문에 특정 필드 기준으로 정렬도 가능함
- 많은 유저 데이터를 관리할 때 메모리 효율 면에서 유리하다.
- 하나의 필드만 수정하거나 조회 가능하다.
- 전체 데이터를 직렬화, 역직렬화 하려면 따로 `Mapping` 작업이 필요한데, 이 작업은 `RedisTemplate`이 자동으로 처리해 준다.


### 작성 코드
#### ✅ 설정 파일
```java
@Configuration
@EnableCaching
public class RedisConfig {
	@Bean
	public RedisConnectionFactory redisConnectionFactory() {
		return new LettuceConnectionFactory();
	}
	
	@Bean
	public RedisTemplate<String, Object> redisTemplate() {
		RedisTemplate<String, Object> template = new RedisTemplate<>();
		template.setConnectionFactory(redisConnectionFactory());
		
		template.setKeySerializer(new StringRedisSerializer());
		template.setValueSerializer(new GenericJackson2JsonRedisSerializer());
		
		return template;
	}
}
```
- 해당 `Template`은 `Key`, `Value`의 직렬화와 역직렬화, 외부 시스템으로의 데이터 전송을 담당한다.
- `Redis Server`와 연결을 관리하는 `LettuceConnectionFactory` 구현체를 `Connection Factory`로 설정하였다.

#### ✅ `Controller`
```java
@PostMapping
public ResponseEntity<Void> saveUser(@RequestBody @Validated UserDto user) {
	service.setUser(user);
	return ResponseEntity.ok().build();
}

@GetMapping("/all")
public ResponseEntity<Page<UserDto>> getAllUserPage(
	@PageableDefault(
		size = 10, sort = "service", direction = Sort.Direction.DESC
	) Pageable pageable
) {
	Page<UserDto> allUserListPage = service.getAllUserListPage(pageable);
	return ResponseEntity.ok(allUserListPage);
}
```

#### ✅ `Service`
```java
@Service
@RequiredArgsConstructor
public class UserService {
	private final RedisTemplate<String, Object> template;
	private static final String USER_KEY_PREFIX = "user:";
	
	public void setUser(UserDto dto) {
		String key = "user:" + dto.getEmail();
		
		template.opsForHash().put(key, "email", dto.getEmail());
		template.opsForHash().put(key, "name", dto.getName());
		template.opsForHash().put(key, "description", dto.getDescription());
		template.opsForHash().put(key, "phoneNumber", dto.getPhoneNumber());
		template.opsForHash().put(key, "service", dto.getService());
		
		template.opsForSet().add("user:keys", key);
	}
	
	public Page<UserDto> getAllUserListPage(Pageable pageable) {
		// 1. 모든 키 목록 가져오기
		Set<Object> keySet = template.opsForSet().members("user:keys");
		
		// 2. 가져온 키 목록이 비었다면 빈 페이지 반환
		if (keySet == null || keySet.isEmpty()) return Page.empty(pageable);
		
		// 3. 정렬 처리
		List<String> allKeys = keySet.stream()
			.map(Object::toString)
			.map(key -> Map.entry(key, template.opsForHash().get(key, "service")))
			.sorted(
				Comparator.comparing(
					entry -> (String) entry.getValue(),
					Comparator.nullsLast(String::compareTo)
				)
			)
			.map(Map.Entry::getKey)
			.toList();
			
		// 4. 페이징 처리
		int start = (int) pageable.getOffset();
		int end = Math.min(start + pageable.getPageSize(), allKeys.size());
		List<String> pagedKeys = allKeys.subList(start, end);
		
		// 5. 반환 사용자 목록 생성
		List<UserDto> userList = pagedKeys.stream()
			.map(key -> template.opsForHash().entries(key))
			.map(map -> {
				UserDto dto = new UserDto();
				dto.setEmail((String) map.get("email"));
				dto.setName((String) map.get("name"));
				dto.setDescription((String) map.get("description"));
				dto.setPhoneNumber((String) map.get("phoneNumber"));
				dto.setService((String) map.get("service"));
				return dto;
			})
			.toList();
			
		// 6. 반환
		return new PageImpl<>(userList, pageable, allKeys.size());
	}
}
```
- 등록 메서드에서 `opsForHash()` 메서드를 내부적으로 살펴보면 다음과 같다.

```java
/* RedisTemplate.class */
private final HashOperations<K, ?, ?> hashOps = new DefaultHashOperations(this);

public <HK, HV> HashOperations<K, HK, HV> opsForHash() {
    return this.hashOps;
}

/* DefaultHashOperations.class */
public void put(K key, HK hashKey, HV value) {
    byte[] rawKey = this.rawKey(key);
    byte[] rawHashKey = this.rawHashKey(hashKey);
    byte[] rawHashValue = this.rawHashValue(value);
    this.execute((connection) -> {
        connection.hSet(rawKey, rawHashKey, rawHashValue);
        return null;
    });
}
```
- `RedisTemplate`이 `Key`, `HashKey`, `HashValue`를 내부적으로 직렬화 한다.
- 직렬화 한 값을 `execute()` 메서드를 통해 `Redis`로 전송하는 것이다.

```java
// 1. 모든 키 목록 가져오기
Set<Object> keySet = template.opsForSet().members("user:keys");

// 2. 가져온 키 목록이 비었다면 빈 페이지 반환
if (keySet == null || keySet.isEmpty()) return Page.empty(pageable);

// 3. 정렬 처리
List<String> allKeys = keySet.stream()
	.map(Object::toString)
	.map(key -> Map.entry(key, template.opsForHash().get(key, "service")))
	
// ...
```
- 위 코드는 모든 `Key` 목록을 가져와 정렬 처리를 하는 코드의 일부분이다.
- `1`번에서 `Set<Object>`로 `Key` 목록을 선언 및 초기화했기 때문에 `.map(Object::toString)` 메서드를 통해 `Stream` 내부에서 `Object`를 `String`으로 변환한다.
- `.map(key -> Map.entry(key, template.opsForHash().get(key, "service")))` 라인을 보면 `Stream` 내부에서 `Key`와 `HashKey`를 통해 값을 추출하는 부분임을 알 수 있다.
- 내부 코드를 보면 다음과 같다.

```java
/* RedisTemplate.class */
private final HashOperations<K, ?, ?> hashOps = new DefaultHashOperations(this);

public <HK, HV> HashOperations<K, HK, HV> opsForHash() {
	return this.hashOps;
}

/* DefaultHashOperations.class */
public HV get(K key, Object hashKey) {
	byte[] rawKey = this.rawKey(key);
	byte[] rawHashKey = this.rawHashKey(hashKey);
	byte[] rawHashValue = (byte[])this.execute((connection) -> connection.hGet(rawKey, rawHashKey));
	return (HV)(rawHashValue != null ? this.deserializeHashValue(rawHashValue) : null);
}
```
- `return (HV)(rawHashValue != null ? this.deserializeHashValue(rawHashValue) : null);` 이 부분이 역직렬화가 일어나는 라인이다.
- `rowHashValue`는 `Redis`에서 가져온 `byte[]` 타입의 데이터로, `Generic` 타입인 `HV`로 바꾸려면 역직렬화가 필요하다.


### 페이지 직렬화 경고
```bash
Serializing PageImpl instances as-is is not supported, meaning that there is no guarantee about the stability of the resulting JSON structure!
	For a stable JSON structure, please use Spring Data's PagedModel (globally via @EnableSpringDataWebSupport(pageSerializationMode = VIA_DTO))
	or Spring HATEOAS and Spring Data's PagedResourcesAssembler as documented in https://docs.spring.io/spring-data/commons/reference/repositories/core-extensions.html#core.web.pageables.
```
- `Spring`에서 `PageImpl` 객체를 직접 `JSON`으로 직렬화 할 경우 위 경고가 뜬다.
- 설정 파일에 다음 `Annotation`을 추가하면 된다.

```java
@EnableSpringDataWebSupport(pageSerializationMode = EnableSpringDataWebSupport.PageSerializationMode.VIA_DTO)
```
- `PageImpl`을 자동으로 `DTO` 구조로 직렬화 해서 안정적인 `JSON` 데이터를 만들어 준다.


### 회고
- 멋쟁이사자처럼 백엔드 부트캠프 플러스 4기 강사님께 "내부 코드를 까보는 것이 좋다"는 내용을 배웠는데, 이번에 직접 실습해보니 로직을 이해하는 측면에서 필요한 작업임을 실감했다.