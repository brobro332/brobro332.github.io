---
title: 💥 Javascript URL Encoding
date: 2025-03-28 23:26:00 +0900
categories:
  - Trouble-Shooting
tags:
  - Trouble-Shooting
  - Javascript
  - URL Encoding
---

### 문제 상황
#### ✅ `Frontend` 코드
```javascript
const socket = new SockJS('http://localhost:8083/ws?token=' + username);
```

#### ✅ `Backend` 코드
```java
@Override
public void registerStompEndpoints(StompEndpointRegistry registry) {
registry.addEndpoint("/ws")
	.setAllowedOriginPatterns("*")
	.setHandshakeHandler(customHandshakeHandler)
	.withSockJS();
}
```

#### ✅ `Chrome` 개발자 도구
![](/assets/image/Pasted%20image%2020250601141053.png)

![](/assets/image/Pasted%20image%2020250601141107.png)
- `WebSocket` 연결을 시도했지만 실패하고 있다.
- 처음에는 코드를 아무리 봐도 문제를 찾지 못했다.


### 문제 원인
![](/assets/image/Pasted%20image%2020250601141618.png)
- 토큰으로 전달되는 값이 `??` 문자열인데, `URL`을 통해 전달되는 공백, `=`, `&`, `?`, `#` 같은 특수 문자들은 `URL`에서 예약된 문자로 전환되므로 의도한 대로 요청이 전달되지 않는다.


### 해결 방법
```javascript
const socket = new SockJS('http://localhost:8083/ws?token=' + encodeURIComponent(username));
```
- `Frontend`에서 요청 시 전달하는 `Query String`의 `username`을 `Encoding` 해야 한다.