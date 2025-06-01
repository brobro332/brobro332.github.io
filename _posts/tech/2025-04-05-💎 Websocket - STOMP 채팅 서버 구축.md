---
title: 💎 Websocket - STOMP 채팅 서버 구축
date: 2025-04-05 15:04:00 +0900
categories:
  - Tech
tags:
  - Tech
  - Websocket
  - STOMP
---

### `Websocket` - `STOMP` 도입 근거
- 채팅 서비스를 위해 `Websocket` 연결 및 메시지를 전송해서 `MongoDB`에 데이터를 등록해보려고 한다. 
- 채팅은 양방향 통신이므로 `SSE`는 불가하고, 효율성을 위해 `Polling` 방식 또한 배제되어 `Websocket`을 도입하게 되었다.


### 작성 코드
#### ✅ 설정 파일
```java
/**
 * 엔드포인트 설정
 * @param registry
 */
@Override
public void registerStompEndpoints(StompEndpointRegistry registry) {
	registry.addEndpoint("/ws")
		.setAllowedOriginPatterns("*");
		.withSockJS();
}
```

#### ✅ `Controller`
```java
@MessageMapping("/chat.sendSystemMessage")
public void enterUser(Message message) {
    service.sendSystemMessage(message);
}
```

#### ✅ `Service`
```java
public void sendSystemMessage(Message message) {
	MessageType type = message.getType();
	Long roomId = message.getRoomId();
	
	Message systemMessage = Message.builder()
		.type(type)
		.content(
			String.format(
				type.name().equals("ENTER") ? "[%s] %s 님이 입장했습니다." : "[%s] %s 님이 퇴장했습니다.",
				LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")),
				message.getSenderName()
			)
		)
		.senderId(message.getSenderId())
		.roomId(roomId)
		.sendAt(LocalDateTime.now())
		.build();
		
	repository.save(systemMessage);
	template.convertAndSend("/topic/room_" + roomId, systemMessage);
}
```
- 클라이언트가 메시지를 요청할 경우 `DB`에 해당 메시지를 저장하고, 해당 사용자가 구독한 `Topic`에 메시지를 전송한다.
- `SimpleMessagingTemplate`은 간단하게 `Topic`에 메시지를 전달할 수 있는 메서드를 제공한다.


### 테스트
[`Websocket Debug Tool`](https://jiangxy.github.io/websocket-debug-tool/)
- `Postman`에서는 `STOMP` 서브 프로토콜을 지원하지 않으므로 위 링크를 활용해야 한다.

![](/assets/image/Pasted%20image%2020250601150402.png)

```bash
2025-04-05T15:14:04.404+09:00 
DEBUG 23688 --- [nboundChannel-4] o.s.m.s.b.SimpleBrokerMessageHandler     
: Processing MESSAGE 
destination=/topic/room_2 
session=null 
payload={
	"senderId":1,
	"roomId":1,
	"senderName":null,
	"type":"ENTER",
	"send...(truncated)"
}
```
- 앞서 첨부한 링크에서는 `CONNECT`, `SUBSCRIBE`, `SEND` 등 요청 프레임을 작성할 필요 없이 간단하게 테스트할 수 있다.
- 실제 목적지에 제대로 `Broadcast` 되는지는 `Frontend` 영역을 구현해야 알 수 있기는 하지만 `Backend` 영역 `Logging`을 확인하는 선에서 테스트를 마쳤다. 
- `Session`이 `null` 값으로 `Logging`되고 있는데, 이건 `SimpMessagingTemplate`를 사용할 경우 `Session` 정보를 포함하지 않고 메시지를 브로커로 보내기 때문이다.
- `SockJS`를 사용할 경우 프로토콜에 `ws`가 아닌 `http`를 기재해야 한다.