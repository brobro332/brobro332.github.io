---
title: 🚀 Raspberry Pi 서버에 프로젝트 배포
date: 2025-04-06 14:32:00 +0900
categories:
  - infra
tags:
  - infra
  - Raspberry Pi
---

### 서버 원격 접속
![](/assets/image/Pasted%20image%2020250601153900.png)
- `IP`를 모를 경우 공유기 사이트에 접속하여 확인하면 된다.


### `Raspberry Pi` 도입 근거
- `On-premise` 방식으로 서버를 구축해보고 싶기도 했고, 실제로 서버가 필요했다.


### 프로젝트 배포 과정
1. `JDK` 설치
2. `SSH Key` 생성
3. `Docker`, `Docker-compose` 설치
4. 방화벽 설정
5. `Docker` 및 프로젝트 관련 파일 원격 전송
6. `Docker-compose`파일 및 `JAR` 파일 실행

#### ✅ `JDK` 설치
```bash
# 패키지 갱신
sudo apt update
sudo apt install -y wget gnupg software-properties-common

# 최신 OpenJDK 버전 갱신
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt update

# JDK 설치
sudo apt install -y openjdk-21-jdk

# 버전 확인
java -version
```

#### ✅ `SSH Key` 생성
```bash
# SSH Key 생성
ssh-keygen -t rsa -b 4096 -f ~/.ssh/my_rpi_key

# SSH 원격 접속
ssh 사용자명@라즈베리파이_IP

# 공개키 등록
mkdir -p ~/.ssh
sudo vi ~/.ssh/authorized_keys
```
- `Raspberry Pi` `OS` 설치 시 접속을 위한 공개키는 주지만 개인키는 주지 않았다.
- 그렇기 때문에 호스트 `PC`에서 위 명령어를 입력하여 `SSH Key`를 생성하고, 공개키를 `Raspberry Pi` 서버에 등록하자.

#### ✅ `Docker`, `Docker-compose` 설치
```bash
# HTTPS용 인증서, 다운로드 도구, GPG 키 처리 도구 설치
sudo apt update
sudo apt install -y ca-certificates curl gnupg

# 도커 GPG 공개키 등록
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# 저장소 등록
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu jammy stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 도커 설치
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 설치 확인
docker --version
docker compose version
```
- `Docker`의 `GPG Key`를 설정하여 `APT`가 해당 패키지가 신뢰할 수 있는 출처에서 왔는지 검증할 수 있도록 해야 한다.

```bash
sudo snap install docker          # version 27.5.1
sudo apt  install docker-compose  # version 1.29.2-6
```
- 필자는 `docker-compose up -d` 명령어가 실행되지 않아서 위 명령어로 버전을 업그레이드 하였다.

#### ✅ 방화벽 설정
```bash
sudo apt update
sudo apt install ufw -y

sudo ufw allow 8080
sudo ufw allow 8081
sudo ufw allow 8082
sudo ufw allow 22

sudo ufw enable
```

#### ✅ `Docker` 및 프로젝트 관련 파일 원격 전송
- `FileZila`를 통해 파일을 전송했다.

#### ✅ `Docker-compose`파일 및 `JAR` 파일 실행
```bash
# 도커 컴포즈 파일 백그라운드 실행
docker-compose up -d

# Jar 파일 백그라운드 실행 및 로그 파일 생성
nohub java -jar samsami-hub-0.0.1-SNAPSHOT.jar > app.log 2>&1 &

# 실행 중인 프로젝트 PID 확인
ps aux | grep samsami-hub-0.0.1-SNAPSHOT.jar

# 프로젝트 로그 확인
tail -f app.log

# 도커 컴포즈 실행 프로세스 및 볼륨 다운
docker-compose down -v

# 백그라운드에서 실행 중인 프로젝트 다운
kill -9 PID
```


### 회고
- 이번 실습에서는 `On-premise` 방식도 `Cloud` 방식과 크게 다른 건 없었다.
- 추후 `Load Balancing`이나 보안 설정 등 네트워크 측면에서 조금 더 신경 써봐야겠다고 느꼈다.