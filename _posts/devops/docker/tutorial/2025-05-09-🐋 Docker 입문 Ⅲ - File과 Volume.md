---
title: 🐋 Docker 입문 Ⅲ - File과 Volume
date: 2025-05-09 00:01:00 +0900
categories:
  - Docker
tags:
  - Docker
  - 입문
---

![](/assets/image/Pasted%20image%2020250529002434.png)
> 📘 `『그림과 실습으로 배우는 Docker & 쿠버네티스』`를 읽고 정리한 글입니다.

### `Container`와 호스트 간 파일 복사
```bash
# 컨테이너 → 호스트
docker cp 컨테이너_이름:컨테이너_경로 호스트_경로 
# 호스트 → 컨테이너
docker cp 호스트_경로 컨테이너_이름:컨테이너_경로
```
- 파일 복사는 `Container`에서 호스트로, 호스트에서 `Container`로 양방향 모두 가능하다.
- `mv`로 파일명을 변경하거나 `rm`로 파일을 삭제할 수도 있다.


### `Volume`과 `Mount`
- `Volume`은 스토리지의 한 영역을 분할한 것이다.
- `Mount`는 대상을 연결해 운영체제의 관리 하에 두는 것이다.
- 실제로 `Container`를 사용하려면 스토리지 영역을 `Mount`해야 한다.
- 왜냐하면 데이터가 이 스토리지에 있기 때문에 `Mount`를 하지 않으면 `Container`를 삭제할 때 같이 삭제되어 버린다.
- `Container`는 생성 및 폐기가 매우 빈번하기 때문에 매번 데이터를 옮기는 대신 `Container` 외부에 둔 데이터에 접근해 사용하는 것이다.
- 이를 데이터 퍼시스턴시라고 한다.
- `Mount`는 `run` 커맨드의 옵션 형태로 지정한다.

#### ✅ `Volume` `Mount`
```bash
docker run -v 볼륨_이름:컨테이너_마운트_경로
```
- `Docker` 엔진이 관리하는 영역 내에 만들어진 `Volume`을 `Container`에 디스크 형태로 `Mount` 한다.
- 자주 쓰지는 않지만 지우면 안 되는 파일을 `Mount` 한다.
- `Docker` `Container`를 경유하지 않고 직접 `Volume`에 접근할 방법이 없다.

#### ✅ `Bind` `Mount`
```bash
docker run -v 스토리지_실제_경로:컨테이너_마운트_경로
```
- `Docker`가 설치된 컴퓨터의 문서 폴더 또는 바탕화면 폴더 등 `Docker` 엔진에서 관리하지 않는 영역의 기존 디렉터리를 `Container`에 `Mount` 한다.
- 폴더 속에 파일을 직접 두거나 열어볼 수 있기 때문에 자주 사용하는 파일을 두는 데 사용한다.


### `Volume` 커맨드
```bash
# `Volume` 생성
docker volume create 볼륨_이름
# `Volume` 삭제
docker volume rm 볼륨_이름
# `Volume` 상세정보 확인
docker volume inspect 볼륨_이름
```


###  심화 - `Volume` 백업
```bash
docker run --rm -v 볼륨_이름:/source -v 백업_저장_폴더명:/target 컨테이너_이름
tar cvzf /경로/파일명.tar.gz -C /source .
```
- `tar -C  A B`는  A로 이동하여 B의 파일을 압축하는 커맨드다.


#### 심화 - 백업의 복원
```bash
docker run --rm -v 볼륨_이름:/source -v 백업_저장_폴더명:/target 컨테이너_이름 
tar xvzf /경로/파일명.tar.gz -C /source
```