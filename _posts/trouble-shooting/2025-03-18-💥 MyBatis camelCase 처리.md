---
title: 💥 MyBatis CamelCase 처리
date: 2025-03-18 23:52:00 +0900
categories:
  - Trouble-Shooting
tags:
  - Trouble-Shooting
  - MyBatis
---

### 문제 상황
![](/assets/image/Pasted%20image%2020250601134446.png)
- 코드와 `Query` 모두 분명 제대로 작성되어 있는데 등록자와 수정일자에 잘못된 값이 매핑 되었다.
- `Query`에 대한 응답 `DTO`를 `Logging`해 보니 다음과 같았다.

```bash
[
	NoticeResponseDto(
		writerEmail='제목'
		, writerName='내용'
		, title='제목'
		, content=내용
		, createdDate='2025-03-18'
		, updatedDate='김진형'
	)
]
```


### 문제 원인
```kotlin
@Schema(description = "공지사항 목록 조회 응답 DTO")
class NoticeResponseDto (
	@Schema(description = "작성자 이메일")
	val writerEmail: String,
	
	@Schema(description = "작성자 이름")
	val writerName: String,
	
	@Schema(description = "제목")
	val title: String,
	
	@Schema(description = "내용")
	val content: String,
	
	@Schema(description = "등록일자")
	val createdDate: String,
	
	@Schema(description = "수정일자")
	val updatedDate: String
)
```
- `MyBatis`는 기본적으로 매핑 시 `snake_case` ↔ `camelCase` 자동 매핑을 지원하지 않기 때문에, 일치하지 않을 경우 `resultMap`을 따로 지정해야 한다.


### 해결 방법
#### ✅ 생성자 기반 매핑
```xml
<resultMap id="NoticeResponseMap" type="com.example.dto.NoticeResponseDto">
    <constructor>
        <arg column="writer_email" javaType="String"/>
        <arg column="writer_name" javaType="String"/>
        <arg column="title" javaType="String"/>
        <arg column="content" javaType="String"/>
        <arg column="created_date" javaType="String"/>
        <arg column="updated_date" javaType="String"/>
    </constructor>
</resultMap>
```
- 생성자 파라미터 순서가 `<constructor>`의 `<arg>` 순서와 반드시 일치해야 한다.

#### ✅ `application.properties` 자동 매핑 설정
```bash
mybatis.configuration.map-underscore-to-camel-case=true
```
- `MyBatis`는 위와 같이 `CamelCase` 설정을 해주어야 한다고 한다.
- 이 방법은 모든 필드에 `@Setter`가 설정되어 있어야 한다.


### 회고
- 해결 방법은 간단하지만, 컴파일이나 예외가 터지지 않는 문제가 오히려 더 해결하기 어려운 것 같다.