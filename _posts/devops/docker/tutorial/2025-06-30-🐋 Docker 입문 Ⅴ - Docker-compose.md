---
title: 🐋 Docker 입문 Ⅴ - Docker-compose
date: 2025-06-30 22:44:00 +0900
categories:
  - Docker
tags:
  - Docker
  - 입문
---

![](/assets/image/Pasted%20image%2020250529002434.png)
> 📘 `『그림과 실습으로 배우는 `Docker` & 쿠버네티스』`를 읽고 정리한 글입니다.

### `Docker-compose`
- 시스템 구축과 관련된 명령어를 하나의 텍스트 파일에 기재하여 명령어 한번에 시스템 전체를 관리한다.
- `yaml` 포맷으로 기재한 정의 파일을 이용해 전체 시스템을 일괄적으로 실행, 종료, 삭제한다.
- `up` 명령어를 통해 정의 파일에 기재된 내용대로 이미지를 내려받고 `Container`를 생성 및 실행한다.
- `down` 명령어를 통해 `Container`와 네트워크를 정지 및 삭제한다. 
- 이때 볼륨과 이미지는 삭제되지 않는다.
- `stop` 명령어를 통해 `Container`와 네트워크 삭제 없이 종료만 한다.
- `Docker-compose`는 `docker run` 명령어를 여러 개 모아 놓은 것과 같다.
- `Dockerfile`은 이미지를 만들기 위한 것으로 네트워크나 `Volume`은 만들 수 없다.  
- `docker-compose.yml` 파일은 한 폴더에 하나만 있을 수 있다.


### `Docker-compose` 파일을 작성하는 법
```bash
docker run --name 컨테이너명 -dit --net=네트워크명 -p 8085:80 -e WORDPRESS_DB_HOST=데이터베이스_호스트 -e WORDPRESS_DB_NAME=데이터베이스명 -e WORDPRESS_DB_USER=유저명 -e WORDPRESS_DB_PASSWORD=패스워드 wordpress
```
- 상기 `run` 명령을 실행하기 위해서는 `docker-compose.yml` 파일을 다음과 같이 작성해야 한다.

```yml
version: "3"

service:
  컨테이너명:
    depends_on:
      - 데이터베이스_컨테이너
    image: wordpress
    networks:
      - 네트워크명
    ports:
      - 8085:80
    restart: always
    environment:
      WORDPRESS_DB_HOST: 데이터베이스_컨테이너
      WORDPRESS_DB_NAME: 데이터베이스명
      WORDPRESS_DB_USER: 유저명
      WORDPRESS_DB_PASSWORD: 패스워드
```
- `yaml` 형식에서는 공백에 따라 의미가 달라진다.
- `depends_on`은 다른 서비스에 대한 의존 관계를 나타낸다. 
- 예를 들어 위 설정에서는 `mysql01` `Container`를 먼저 생성한 다음 `wordpress` 컨테이너를 만든다.
- 그러나 `mysql01`가 온전히 통신할 수 있는 상태임을 보장하지는 않는다.
- `restart`는 재시작 여부를 설정한다.
- `Docker-compose`로 만든 `Container`라도 도커 엔진을 통해 명령을 내릴 수 있다. 그렇다면 나중에 `Docker Engine`을 통해 명령을 내리면 `Docker-compose` 파일까지 반영이 될까?
- 반영되지 않는다. 
- 그러므로 `Docker Engine`을 통해 `Container` 이름을 바꾸었다면, 나중에 `Docker-compose`를 통해 정지, 삭제하려고 해도 `Docker-compose` 파일에 기재된 이름과 실제 `Container` 이름이 일치하지 않아 `Container`를 정지, 삭제하지 못할 수가 있다. 
- 반대의 경우도 마찬가지다.
- 그러므로 `Container`이름은 가급적 손대지 않는 것이 좋다.


### `MySQL`, `WORDPRESS` 구축 실습

```null
version: "3"

services:
  mysql01:
    image: mysql:5.7
    networks:
      - net01
    volumes:
      - vol01:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 1234
      MYSQL_DATABASE: db01
      MYSQL_USER: 아이디
      MYSQL_PASSOWRD: 패스워드
    wordpress01:
    depends_on:
      - mysql01
    image: wordpress
    networks:
      - net01
    volumes:
      - vol02:/var/www/html
    ports:
      - 8085:50
    restart: always
    environment:
      WORDPRESS_DB_HOST: mysql01
      WORDPRESS_DB_NAME: db01
      WORDPRESS_DB_USER: 아이디
      WORDPRESS_DB_PASSWORD: 패스워드

networks:
  net01:
volumes:
  vol01:
  vol02:
```


### `Docker-compose` 명령어
#### ✅ 실행
- `Docker-compose` 파일을 실행하려면 `docker-compose` 명령어를 사용한다.

#### ✅ `Container` 주변 환경을 생성

```bash
docker-compose -f 컴포즈_파일_경로 up 옵션
docker-compose up -d  # 현재 작업 디렉토리를 컴포즈용 폴더로 사용
```

####  ✅ `Container`와 네트워크를 삭제

```bash
docker-compose -f 컴포즈_파일_경로 down 옵션
```

#### ✅ `Container`를 종료

```bash
docker-compose -f 컴포즈_파일_경로 stop 옵션
```


### `Docker-compose`는 `Container`의 이름을 바꾼다.
- `Docker-compose`로 실행한 `Container`의 이름은 임의로 결정된다.
- 예를 들어 `penguin`이라는 이름의 `Container`를 생성하면 `Docker-compose`가 실제로 생성한 `Container`의 이름은 `com_folder_penguin_1`과 같이 폴더 이름과 번호가 붙는다.
- 단 `-f` 옵션을 생략했다면 폴더 이름은 붙지 않는다.


### 스케일링
- `--scale` 옵션을 붙이면 같은 구성의 `Container`를 여러 세트 만든다.

```bash
docker-compose -f C:\Users\유저명\com_folder\docker-compose.yml up --scale penguin=3
```
- 단 포트 번호가 중복되면 `Container`가 실행되지 않으므로 주의해야 한다.


### 회고
- 책의 저자는 `Container`이름은 가급적 손대지 않는 것이 좋다고 하지만, 실제로 프로젝트를 진행하고 `Docker-compose` 파일을 통해 개발을 진행하다 보면, `Container` 이름을 변경하는 것이 훨씬 편했던 기억이 있다.
- `Docker-compose`는 `Container` 이름을 임의대로 바꾸어 버리는데, 단순히 `Docker-compose`의 규칙을 따르는 것보다는 개발자가 직접 `Container` 이름을 설정하여 직접 제어하는 것이 올바르다고 생각한다.