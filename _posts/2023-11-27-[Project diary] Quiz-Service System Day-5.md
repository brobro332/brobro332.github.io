---
title: "[Project diary] Quiz-Service System DAY-5"
excerpt: "etc.; [Project diary] Quiz-Service System DAY-5"
categories: 
- etc
tags:
- [etc]
published: true
toc: true
toc_sticky: true
---

## ✅ DAY-5 AccessToken의 유효성 검증하여 처리

### 1️⃣ doFilterInternal 수정
- AccessToken이 유효하다면 인증 허가
- AccessToken이 유효하지 않다면
  - 클라이언트 요청 RefreshToken이 유효한지 검증 및 해당 사용자의 데이터베이스에 저장되어있는 RefreshToken 값을 비교
  - 유효하지 않거나 데이터베이스 토큰 값과 일치한다면
    - API 요청에 대한 로직을 처리 후 응답 반환
    - 헤더에 새로운 AccessToken 응답 

```java
    /**
     * doFilterInternal: 요청과 응답을 가로채서 사용자의 인증 및 권한 부여
     */
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        String accessToken = jwtProvider.resolveAccessToken(request);
        String refreshToken = jwtProvider.resolveRefreshToken(request);

        // accessToken이 유효한지 확인
        if (accessToken != null) {
            // 유효하다면
            if (jwtProvider.validateToken(accessToken)) {
                Authentication authentication = jwtProvider.getAuthentication(accessToken);
                SecurityContextHolder.getContext().setAuthentication(authentication);
            }
            // accessToken이 만료되고 refreshToken이 존재한다면
            else if (!jwtProvider.validateToken(accessToken) && refreshToken != null) {
                // 재발급 후, 컨텍스트에 다시 넣기
                boolean validateRefreshToken = jwtProvider.validateToken(refreshToken); // refreshToken 검증

                // 데이터베이스의 refreshToken과 비교
                UserDetails user = getUserDetailsByUsername(jwtProvider.parseClaims(refreshToken).getSubject());
                boolean compareRefreshToken = jwtProvider.compareRefreshToken(user.getUsername(), refreshToken);

                // refreshToken이 만료되지 않았고, 데이터베이스의 값과 동일하다면
                if (validateRefreshToken && compareRefreshToken) {

                    /// 새로운 accessToken 발급
                    String newAccessToken = jwtProvider.generateAccessToken(user);
                    // 새로운 accessToken 응답
                    response.addHeader("Authorization", "Bearer " + newAccessToken);
                    /// 컨텍스트에 넣기
                    Authentication authentication = jwtProvider.getAuthentication(newAccessToken);
                    SecurityContextHolder.getContext().setAuthentication(authentication);
                }
            }
        }
        filterChain.doFilter(request, response);
    }
```

### 2️⃣ 이미지

![Image: 만료된 AccessToken을 통한 퀴즈게임 생성](/assets/images/2023-11-27-[Project diary] Quiz-Service System Day-5/1.PNG)
<figcaption style="text-align: center; bold;">Image: 만료된 AccessToken을 통한 퀴즈게임 생성</figcaption>

회원가입과 로그인을 진행하고, 만료된 AccessToken과 정상적인 RefreshToken을 헤더에 넣어 퀴즈게임을 생성해보았다. <br>
서버 콘솔에는 io.jsonwebtoken.ExpiredJwtException 예외가 출력된다. <br>
이후 새로운 AccessToken을 헤더에 포함시켜 응답하며 컨텍스트에 인증을 부여한 후 정상적으로 퀴즈 게임을 생성한다.


### 3️⃣ 계획

이제는 퀴즈 도메인에 대한 개발을 하지 않을까싶다. <br>
카훗! 웹 페이지를 모티브로 하고 있는데, 카훗이랑은 조금 다른 실시간 퀴즈 웹 서비스를 개발해보고 싶다. <br>
예를 들면 기업에서 자사 홈페이지에서 코딩테스트를 주관하듯 만들고 싶은데, <br>
어디까지나 계획일 뿐이다. <br>
도메인에 대한 개발을 마치면 광고를 추가하여, 광고 제거를 결제 시스템을 통해 제공할 것이고, <br>
예약도 구현해보고 싶은데 이것도 일단은 계획까진 없고 생각만 하고 있다. 
