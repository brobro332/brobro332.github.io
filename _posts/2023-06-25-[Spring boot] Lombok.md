---
title: "[Spring boot] Lombok"
excerpt: "Development; Lombok"
categories: 
- Development
tags:
- [Development, Lombok, Springboot]
published: true
toc: true
toc_sticky: true
---

<br><br>

**Lombok**
##### Lombok이란?
- 어노테이션 기반으로 코드를 자동완성 해주는 코드 다이어트 라이브러리
- Getter, Setter, Equals, ToString 등과 다양한 방면의 코드를 자동완성 시킬 수 있다.

##### 어노테이션 종류


###### @Getter, @Setter
  - 필드 또는 클래스에 어노테이션을 붙여 접근자/설정자 자동 생성

  ```java
  @Getter @Setter
  private String name;

  user.setName("홍길동");
  String userName = user.getName();
  ```


###### @NoArgsConstructor
  - parameter가 없는 디폴트 생성자를 생성
  - access : AccessLevel을 이용하여 접근 제한자를 설정
  - 기본 생성자를 public(default)로 열어두면 안전성이 심각하게 저하
  - static 변수들은 스킵
  - 필드들이 final로 생성되어 있는 경우에는 필드를 초기화 할 수 없기 때문에 <br>
  생성자를 만들 수 없고 에러가 발생
  - @NoArgsConstructor(access = AccessLevel.PROTECTED)를 사용하여 <br> 
  객체 생성시 안전성을 보장해주는것을 권장


###### @RequiredArgsConstructor
  - final 혹은 @NonNull인 변수만 parameter로 받는 생성자를 생성
  - 멤버변수가 final이고, 변수의 수가 많을 때 <br>
    일일이 생성자를 만드는 방법보다 훨씬 유용하기 때문에 <br>
    따로 생성자를 이용해서 생성하지 않고 DI를 이용해 생성할 때 사용


###### @AllArgsConstructor
  - 클래스에 존재하는 모든 필드에 대한 생성자를 자동으로 생성
  - 개발자가 Entity.java의 변수(필드) 위치를 바꿀 경우, <br>
  생성자의 Parameter의 순서도 함께 바뀌는데 <br>
  두 필드 모두 입력이 String이라 오류는 발생하지 않지만 <br>
  생성자의 필드에 의도치 않은 입력값이 들어갈 수 있다.

    ```java
    @AllArgsConstructor
    public class User{
        private String identity;
        private String password;
    }

    User user = new User("userId","userPwd");
    ```
  - 이에 @Builder 어노테이션을 이용하는 것이 좋다.


###### @Builder
  - 빌더 패턴을 사용할 수 있도록 코드를 생성
  - @Builder도 private으로 만들긴 하지만 @AllArgsConstructor를 내포
  - 해당 클래스의 다른 메소드에서 이렇게 자동으로 생성된 생성자를 사용하거나 할 때 문제 소지가 있음
  - @Builder를 Class보다는 직접 만든 생성자 혹은 static 객체 생성 메소드에 붙이는 것을 권장
  
    ```java
    public class User {
    private String password;
    private String identity;
    private String name;

    @Builder
    private UserProfile(String pwd, String id, String name) {
        this.pwd = pwd;
        this.id = id;
        this.name = name;
      }
    }
    ```

###### @ToString
  - ToString() 메소드를 생성해줌
  - 기본적으로 static을 제외한 전체 변수에 대한 ToStirng()을 생성
  - JPA 사용 시, 객체가 양방향 연관 관계일 경우, 무한순환참조 발생 가능
  - @ToString(exclude={"필드명"})을 권장
    - 비밀번호 등과 같은 민감한 정보는 exclude를 통해 제외해야함


###### @EqualsAndHashCode
  - 실무에서든 개인 프로젝트든 일단 쓰지 말기
  - @EqualsAndHashCode(of={“변수명”}) 형태로 동등성 비교에 필요한 필드를 명시하는게 좋다.

###### @Data
  - @Getter, @Setter, @ToString, @EqualsAndHashCode, @RequiredArgsConstructor 등을 자동으로 생성
  - 무분별하게 @Getter,@Setter를 사용하게 되고 @RequiredArgsConstructor등을 포함하기 때문에 지양

###### @Value
  - Immutable(불변성) 객체를 선언
  - 해당 어노테이션을 사용할 경우 setter 메소드는 사용이 불가능
  - @Value역시 @AllArgsConstructor등을 포함하기 때문에 사용하지 않는게 좋음

###### @NonNull
  - 변수나 파라미터에 선언하게 되면, 해당 값에 null이 올 수 없음
  - Lombok에서 null-check 로직을 자동으로 생성
  - Setter에 null값이 들어오면 NullPointException예외를 일으킴
  - 변수에 @NonNull이 달려있으면 <br>
  해당 변수에 값을 설정하는 메소드들에도 null-check 코드를 생성
  - Lombok이 생성한 메소드나 생성자에만 효과
  - 불필요하게 branch coverage를 증가시킴

<br>
---
<br>

```참고사이트``` 
- [https://dev-splin.github.io/spring/Spring-Lombok/](https://dev-splin.github.io/spring/Spring-Lombok/)