---
title: "[Project diary] Quiz-Service System DAY-4"
excerpt: "etc.; [Project diary] Quiz-Service System DAY-4"
categories: 
- etc
tags:
- [etc]
published: true
toc: true
toc_sticky: true
---

## ✅ DAY-4 JWT 만료된 토큰 처리를 위한 데모 API 생성

### 1️⃣ API 구현

- 퀴즈게임 생성 API 구현
만료된 토큰을 처리하기 위해서는 회원가입이나 로그인을 제외한 API 요청이 하나 있어야겠다고 판단했다.

```java
@PostMapping
public ResponseEntity<?> createGame(@RequestBody GameReqDTO gameReqDTO) {
    gameService.createGame(gameReqDTO);

    return ResponseEntity.ok("퀴즈 게임이 정상적으로 생성되었습니다.");
}

@Transactional
public void createGame(GameReqDTO gameReqDTO) {
    // 회원 조회
    User user = userService.selectUser(gameReqDTO.getUsername());

    // 퀴즈 게임 생성
    Game game = Game.builder()
            .title(gameReqDTO.getTitle())
            .description(gameReqDTO.getDescription())
            .user(user)
            .build();

    // 데이터베이스에 저장
    gameRepository.save(game);
}
```

### 2️⃣ 이미지

![Image: 만료된 토큰을 통한 퀴즈게임 생성](/assets/images/2023-11-23-[Project diary] Quiz-Service System Day-4/1.PNG)
<figcaption style="text-align: center; bold;">Image: 만료된 토큰을 통한 퀴즈게임 생성</figcaption>

회원가입을 진행하고, 만료된 토큰을 헤더에 넣어 퀴즈게임을 생성해보았다.

<br>

![Image: 예외 발생](/assets/images/2023-11-23-[Project diary] Quiz-Service System Day-4/2.PNG)
<figcaption style="text-align: center; bold;">Image: 예외 발생</figcaption>

정상적으로 예외가 발생하는 모습이다.

<br>

![Image: 정상 토큰을 통한 퀴즈게임 생성](/assets/images/2023-11-23-[Project diary] Quiz-Service System Day-4/3.PNG)
<figcaption style="text-align: center; bold;">Image: 정상 토큰을 통한 퀴즈게임 생성</figcaption>

이번에는 회원가입을 하고, 로그인을 한 후에 반환하는 토큰 값을 헤더에 넣으니 정상적으로 퀴즈게임이 생성되었다.


### 3️⃣ 계획

이제 예외 처리를 해야될 때이다. <br>
ExpiredJWTException이 발생될 경우 해당 사용자의 refreshToken을 조회한 후에, <br>
만료되었다면 로그인을 요구할 것이고, 아니라면 accessToken을 갱신해줄 것이다.