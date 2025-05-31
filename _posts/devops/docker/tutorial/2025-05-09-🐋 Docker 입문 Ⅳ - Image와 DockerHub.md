---
title: 🐋 Docker 입문 Ⅳ - Image와 DockerHub
date: 2025-05-09 21:57:00 +0900
categories:
  - Docker
tags:
  - Docker
  - 입문
---

![](/assets/image/Pasted%20image%2020250529002434.png)
> 📘 `『그림과 실습으로 배우는 `Docker` & 쿠버네티스』`를 읽고 정리한 글입니다.

### `Container`로 `Image` 만들기
```bash
# commit 커맨드
docker commit 컨테이너_이름 새로운_이미지_이름
# Dockerfile 스크립트
docker build -t 생성할_이미지지_이름 재료_폴더_경로
```


###  `Container` 개조
- `Container`에서 리눅스 명령어를 실행하여 소프트웨어를 설치하거나 설정을 변경`Container`에서 명령어를 실행하려면 우리의 명령어를 전달해줄 `shell`이 필요하다.
- 대부분의 `Container`에는 가장 일반적으로 사용되는 `bash` 셸이 설치되어있다.
- `docker run -it` 또는 `docker exec -it` 커맨드와 함께 사용한다.
- 실행 중인 `Container`에는 `docker exec -it`를 사용한다.
- `docker run -it`를 통해 셸을 구동하면 정작 소프트웨어(아파치 등)은 실행되지 않는다.
- `bash`를 통해 `Container` 내부를 조작하는 동안에는 `Docker` 명령을 사용할 수 없다.
- `Container` 안에서 할 일을 마쳤다면 `exit`를 통해 다시 `Container`에서 나와야 한다.
- 정리하면 `Docker` 엔진을 통해 `Container`를 조작하고, `Container` 내부에서는 `bash`를 통해 `Container` 속 소프트웨어를 추가하거나, 소프트웨어의 실행, 종료, 설정 변경, 파일 복사 및 이동, 삭제 등의 작업을 한다.
- `Docker`와 `Container`는 별개의 언어를 사용한다.
- 가령 호스트의 운영체제와 `Container` A, `Container` B의 운영체제가 모두 다를 경우 명령어가 모두 달라질 수 있다.
- `Docker`에서 공식적으로 특별한 이유가 없다면 데비안 계열을 기반으로 하는 것이 좋다고 명확히 방침을 밝혔다.


### `DockerHub`
- `Image`를 배포하는 곳을 `Docker` `Registry`라고 한다.
- `Docker` 허브는 `Docker` 제작사에서 운영하는 공식 `Docker` `Registry`다.
- 외부에 공개되지 않은 `Docker` `Registry`도 있다.
- `Registry`는 `Image`를 배포하는 장소이다.
- `Repository`는 `Registry`를 구성하는 단위다.


### Tag
- `Image`를 업로드하려면 `Image`에 태그를 붙여야 한다.
- 태그는 `Registry` 주소 또는 `Docker` 허브 ID / `Repository`명 / 버전의 형식을 띤다.
- 버전은 생략할 수 있지만 관리 차원에서 붙이는 것이 좋다.


### `Image`에 태그를 부여해 복제
```bash
docker tag 기존_이미지_이름 레지스트리_주소/레포지토리_이름:버전
```


### `Image` 업로드
```bash
docker push 레지스트리_주소/레포지토리_이름:버전
```
- 태그에 포함되어 있는 `Registry`의 `Repository`로 업로드 한다.
- `Repository`는 처음 업로드할 때는 존재하지 않으나 `push` 커맨드를 실행하며 만들어진다.
- `Docker` 허브에 미리 `Repository`를 만들어 놓고 `push`하는 것이 올바르다.
- `push`를 통해 만들어진 `Repository`는 자동적으로 `public` 상태가 되기 때문이다.


### 비공개 `Registry` 만드는 법
```bash
docker run -d -p 5000:5000 registry
```
- 비공개 `Registry`를 만들기 위해서는 `Registry`용 `Container`를 사용한다.
- `Registry`는 기본적으로 포트 `5000`을 사용한다.