---
title: "[Project diary] Quiz-Service System DAY-2"
excerpt: "etc.; [Project diary] Quiz-Service System DAY-2"
categories: 
- etc
tags:
- [etc]
published: true
toc: true
toc_sticky: true
---

## ✅ DAY-3 리액트 로그인 폼을 통해 스프링부트와 연동 확인

### 백엔드측

1. CORS 설정
- 스프링부트는 아무것도 설정하지 않으면 모든 외부 요청에 대해 CORS Block을 하기 때문에 설정 파일을 만들어주어야 한다.
- 
```java
@Configuration
public class CorsConfig {
    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**")
                        .allowedOrigins("http://localhost:3000")
                        .allowedMethods("GET", "POST", "PUT", "DELETE");
            }
        };
    }
}
```

2. 로그인 API

```java
/**
 * login: 로그인에 대한 HTTP 요청 처리 및 응답 생성
 */
@PostMapping("/login")
public ResponseEntity<JwtToken> login(@RequestBody UserReqDTO userReqDTO) {

    JwtToken token = userService.login(userReqDTO);

    return ResponseEntity.ok(token);
}

/**
 * login: 로그인에 대한 비즈니스 로직
 */
@Transactional
public JwtToken login(UserReqDTO userReqDTO) {
    Optional<User> joinedUser = userRepository.findOptionalByUsername(userReqDTO.getUsername());

    if (joinedUser.isEmpty()) {
        throw new IllegalArgumentException("해당 사용자가 존재하지 않습니다.");
    }

    UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(userReqDTO.getUsername(), userReqDTO.getPassword());
    Authentication authentication = authenticationManagerBuilder.getObject().authenticate(authenticationToken);
    JwtToken token = jwtProvider.generateJwtToken(authentication);

    // 로그인할 때마다 refreshToken 갱신
    joinedUser.get().setRefreshToken(token.getRefreshToken());

    return token;
}
```

3. 해야할 것
- 로그인할 경우 accessToken & refreshToken 갱신
- API 요청을 할 때마다 accessToken 확인하여 만료되었을 경우에는
  - refreshToken이 만료되지 않았다면 accessToken 갱신
  - 만료되었다면 로그인 요구

### 프론트엔드측
- 리액트를 따로 공부해본 적이 없어 ChatGPT를 통해서 작성함, 세부적인 부분은 수정해야할 듯
- 프론트엔드 개발자를 희망하는 것은 아니기 때문에 앞으로도 애용할 예정

```javascript
import React, { Component } from "react";
import axios from "axios";

class Login extends Component {
  // 상태 저장
  state = {
    username: "",
    password: "",
    loading: false,
    error: null,
    user: null,
  };

  // 입력 필드 값이 변경될 때 호출되는 메서드
  handleInputChange = (e) => {
    const { name, value } = e.target;
    this.setState({ [name]: value });
  };

  // 로그인 폼 제출 시 호출되는 메서드
  handleSubmit = (e) => {
    e.preventDefault();

    const { username, password } = this.state;
    this.setState({ loading: true, error: null });

    // 서버로 로그인 요청 및 응답을 비동기 처리
    axios.post("http://localhost:8080/api/v1/user/login", { username, password })
      .then((response) => {
        this.setState({
          loading: false,
          user: response.data, // assuming the server sends user data on successful login
        });
      })
      .catch((error) => {
        this.setState({
          loading: false,
          error: "로그인에 실패하였습니다. 사용자명과 비밀번호를 확인해주세요.",
        });
      });
  };

  render() {
    const { username, password, loading, error, user } = this.state;

    if (user) {
      // 로그인 성공 시 화면
      return (
        <div>
          <h2>로그인 성공</h2>
          <p>환영합니다, {user.username}!</p>
        </div>
      );
    } else {
      // 로그인 폼 화면
      return (
        <div>
          <h2>Login</h2>
          {error && <p style={{ color: 'red' }}>{error}</p>}
          <form onSubmit={this.handleSubmit}>
            <div>
              <input
                type="text"
                name="username"
                value={username}
                onChange={this.handleInputChange}
                placeholder="Username"
              />
            </div>
            <div>
              <input
                type="password"
                name="password"
                value={password}
                onChange={this.handleInputChange}
                placeholder="Password"
              />
            </div>
            <button type="submit" disabled={loading}>
              {loading ? '로그인 중...' : 'Login'}
            </button>
          </form>
        </div>
      );
    }
  }
}

export default Login;
```

### 이미지

![Image: Postman을 통한 회원가입](/assets/images/2023-11-21-[Project diary] Quiz-Service System Day-3/1.PNG)
![Image: 리액트를 통한 로그인](/assets/images/2023-11-21-[Project diary] Quiz-Service System Day-3/2.PNG)