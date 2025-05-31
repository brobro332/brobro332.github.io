---
title: 🗂️ P6Spy SQL Logging
date: 2025-03-06 00:45:00 +0900
categories:
  - Lib
tags:
  - Lib
  - P6Spy
---

### `P6Spy` 도입 근거
- 코드에서 `DB` 접근 시 `SQL`문이 예상대로 실행되는지 확인하기 위함
	1. `WHERE` 조건에 예상한 값이 들어갔는지
	2. `Lazy Loading`이 예상치 않게 여러 번 발생했는지(`N+1`)
	3. 특정 조회가 너무 느린 건 아닌지


### 의존성 주입
```bash
/* p6spy */
implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.9.0'
```
- `Spring Boot` `3.0.0` 이상은 `1.9.0` 이후 버전을 사용해야 한다. 

```bash
2025-03-06T00:08:48.284+09:00  INFO 8720 --- [nio-8080-exec-6] p6spy                                    : #1741187328284 | took 0ms | statement | connection 19| url jdbc:oracle:thin:@localhost:1521:xe
select m1_0.member_email,m1_0.member_name,m1_0.member_description,m1_0.created_at,m1_0.modified_at,w1_0.leader_email from tbl_workspace w1_0 join tbl_member_workspace mw1_0 on w1_0.workspace_id=mw1_0.workspace_id join tbl_member m1_0 on m1_0.member_email=mw1_0.member_id where m1_0.member_del_flag=?
select m1_0.member_email,m1_0.member_name,m1_0.member_description,m1_0.created_at,m1_0.modified_at,w1_0.leader_email from tbl_workspace w1_0 join tbl_member_workspace mw1_0 on w1_0.workspace_id=mw1_0.workspace_id join tbl_member m1_0 on m1_0.member_email=mw1_0.member_id where m1_0.member_del_flag='0';
```
- 기본 `Hibernate` `Logging`은 파라미터를 `?`로 숨긴다.
- `P6Spy`를 추가하면 파라미터 값이 노출된 채로 `SQL`문이 `Logging`된다.
- 한 줄로 표현되어 보기에 불편한데, 다행스럽게도 `P6Spy`는 `Query Formatting`을 지원한다. 


### `Query Formatting`
```java
@Configuration
public class P6SpyConfig implements MessageFormattingStrategy {
	@PostConstruct
	public void setLogMessageFormat() {
		P6SpyOptions.getActiveInstance().setLogMessageFormat(this.getClass().getName());
	}
	
	@Override
	public String formatMessage(int connectionId, String now, long elapsed, String category, String prepared, String sql, String url) {
		return String.format("[%s] | %d ms | %s", category, elapsed, formatSql(category, sql));
	}
	
	private String formatSql(String category, String sql) {
		if (sql != null && !sql.trim().isEmpty() && Category.STATEMENT.getName().equals(category)) {
			String trimmedSQL = sql.trim().toLowerCase(Locale.ROOT);
			if (trimmedSQL.startsWith("create") || trimmedSQL.startsWith("alter") || trimmedSQL.startsWith("comment")) {
				return FormatStyle.DDL.getFormatter().format(sql);
			} else {
				return FormatStyle.BASIC.getFormatter().format(sql);
			}
		}
		return sql;
	}
}
```
- `@PostConstruct`는 `@Configuration`에 의해 의존성 주입이 완료된 후 실행할 메서드에 선언한다.
- `P6Spy` 옵션에서 활성화된 `Instance`를 꺼내서 `Formatting`을 `Overriding`하는 코드

```bash
2025-03-06T01:01:07.304+09:00  INFO 23432 --- [nio-8080-exec-8] p6spy                                    : [statement] | 1 ms | 
	select
		m1_0.member_email,
		m1_0.member_name,
		m1_0.member_description,
		m1_0.created_at,
		m1_0.modified_at,
		w1_0.leader_email 
	from
		tbl_workspace w1_0 
	join
		tbl_member_workspace mw1_0 
			on w1_0.workspace_id=mw1_0.workspace_id 
	join
		tbl_member m1_0 
			on m1_0.member_email=mw1_0.member_id 
	where
		m1_0.member_del_flag='0'
```
- `DB` 조회하는 특정 메서드의 `SQL`문을 `Logging`해 보면 위와 같이 출력 된다.


### 회고
- `logback`은 `resultSet`을 `Table` 형식으로 `Logging` 할 수 있어서 굉장히 편했던 기억이 있다.
- `P6Spy`을 이번에 도입해보니 `SQL`문을 확인하는 기능 밖에 없어서 `Logback` 보다는 불편한데, 그래도 기능이 제한된 만큼 가볍다는 장점이 있어서 `Trade-off`를 고려하여 선택해야겠다고 생각했다.