---
title: 💥 Spring Security와 CORS 문제
date: 2025-06-29 21:31:00 +0900
categories:
  - Trouble-Shooting
tags:
  - Trouble-Shooting
  - CORS
  - Spring-Security
---

### 문제 상황
- 프론트엔드 작업을 하는데, `Gateway` 서버에 `CorsWebFilter`를 구현했음에도 `CORS` 관련 문제로 특정 서비스에 로그인이 불가능 했다.
- 그 과정에서 `Gateway` 서버와 서비스에 해당하는 백엔드 서버에 `CORS` 처리를 각각 해보았지만 문제를 해결할 수 없었다.


### 문제 원인

- `Spring Security`에 설정한 커스텀 필터에서 `OPTIONS` 요청에 대한 인가 여부를 검사하고 있었다.
- 로그인 같은 `API`는 인증이 안 되어 있는 사용자도 요청을 하기 때문에 `OPTIONS` 요청이 허용되어야 한다
- 그 외에도 `Content-Type`이 `application/json`이거나 메서드가 `PUT`, `PATCH`, `DELETE`인 경우, `Authorization` 헤더가 포함된 경우 브라우저는 `OPTIONS` 요청을 보내 특정 도메인의 해당 메서드를 허용할 것인지 여부를 확인한다.


### 해결 방법
```java
private boolean isOptionsRequest(ServerHttpRequest request) {  
    return request.getMethod() == HttpMethod.OPTIONS;  
}
```
- 커스텀 필터에서 위 메서드를 로직에 추가하였다.

### 회고
- 브라우저가 `API` 요청에 대해 우선적으로 `OPTIONS` 메서드를 통해 `PRE-FLIGHT` 요청을 한다는 사실을 알게 되었다.
- 그 외에도 `Gateway` 서버에서 `corsWebFilter`를 구현하면 각 서비스의 백엔드 서버에서는 `CORS`를 처리하지 않아도 된다는 사실을 알게 되었다.
- 참고로 서버는 `PRE-FLIGHT` 요청과 실제 요청에 모두 `CORS` 헤더를 붙여서 응답해야 한다.
