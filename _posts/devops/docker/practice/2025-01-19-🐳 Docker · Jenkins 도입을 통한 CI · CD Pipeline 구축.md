---
title: 🐳 Docker · Jenkins 도입을 통한 CI · CD Pipeline 구축
date: 2025-01-19 01:22:00 +0900
categories:
  - Docker
tags:
  - Docker
  - Jenkins
  - 실습
---

### `CI` · `CD` `Pipeline` 구축은 왜 했나?
- 프로젝트 개발을 마치고 새로운 `Release`가 생길 때마다 배포를 하는 것은 효율적이지 못하다.
- 처음으로 진행한 프로젝트에서 그렇게 했었는데, 이번에는 프로젝트 배포에 있어 효율성 및 일관성을 가져가기 위해 자동화를 해보고자 한다.


### `Infrastructure` 구조
![](/assets/image/Pasted%20image%2020250531200820.png)
- 위 구조로 무중단 배포를 위해 `CI`/`CD` `Pipeline`을 구축했다.


### `CI` · `CD` `Pipeline` 구축 과정
1. `Docker` 설치
2. `Docker-compose` 설치
3. `Jenkins` 설치
4. `SSH Key` 등록
5. `GitHub Webhook` 설정
6. `Dockerfile`, `Docker-compose.yml`, `Jenkins` `Pipeline` `Script` 파일 작성
7. `application.yml` 파일 작성

#### ✅ `Docker` 설치
```bash
# Docker 설치
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

#### ✅ `Docker-compose` 설치
```bash
# 도커 컴포즈 설치
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

#### ✅ `Jenkins` 설치
```bash
# Jenkins 저장소 키 및 저장소 추가
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# 패키지 목록 갱신 및 Jenkins 설치
sudo apt update && sudo apt install jenkins -y

# Jenkins 서비스 시작 및 부팅 시 자동 시작 설정
sudo systemctl start jenkins
sudo systemctl enable jenkins

# UFW 방화벽에서 포트 8080 허용
sudo ufw allow 8080 && sudo ufw reload

# 초기 관리자 비밀번호 출력 (웹 사이트에 입력)
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
- `Jenkins`는 `Java` 기반이기 때문에 `JDK`가 설치되어 있어야 한다.
- `http://서버_IP:8080`에 초기 관리자 비밀번호를 입력하면 된다.

#### ✅ `SSH Key` 등록
```bash
# SSH Key 생성
ssh-keygen -t rsa -b 4096

# authorized_keys 파일에 공개키 등록
echo id_rsa.pub_내용 >> ~/.ssh/authorized_keys
```
- `SSH Key`를 생성하면 `~/.ssh/id_rsa` 비밀키 파일과 `~/.ssh/id_rsa.pub` 공개키 파일이 만들어 진다.
- `authorized_keys` 파일에 공개키 등록을 하고서, 비밀키는 `Jenkins Pipeline`이나 `job`에서 `SSH` 접속할 때의 인증을 위해 `Jenkins` 자격 증명에 추가해야 한다.

#### ✅ `GitHub` `Webhook` 설정
1. `GitHub` 프로젝트 `Repository` `Settings` > `Webhooks` > `Add Webhook`
2. `Payload URL`(`Ubuntu` 서버 `URL`), `Content type` 입력
3. `Enable SSL verification` 체크
4. `Just the push event` 체크

#### ✅ `Dockerfile` 작성
1. `spring-app` `Dockerfile`

```bash
FROM gradle:8.8-jdk17 as build
WORKDIR /app
COPY . .
RUN gradle clean build --no-daemon

FROM openjdk:17-jdk-slim
WORKDIR /usr/local/app
COPY --from=build /app/build/libs/프로젝트_jar_파일명 /usr/local/app/프로젝트_JAR_파일명.jar
ENTRYPOINT ["java", "-jar", "/usr/local/app/프로젝트_jar_파일명.jar"]

EXPOSE 18080
```
- `JAR` 파일을 빌드할 때는 `Daemon`을 설정하지 않는 게 좋다고 한다.
- 컨테이너는 생명 주기가 짧기 때문에 시간이 지남에 따라 망가지는 게 당연한 `Resource`이고, 그때마다 컨테이너 자원을 해제하고 재생성 해야 한다. 
- 그런데 `Daemon`이 설정되어 있다면 해제되지 않는 자원이 생길 수가 있다고 한다.
2. `react-app` `Dockerfile`

```bash
FROM node:18-alpine AS build

WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

#### ✅ `Docker-compose.yml` 작성
```yml
services:
  oracle-db:
    container_name: oracle-db
    image: gvenzl/oracle-xe:11-slim
    ports:
      - "1521:1521"
    environment:
        ORACLE_PASSWORD: 패스워드
    volumes:
      - oracle-data:/opt/oracle/oradata
    networks:
      - app-network

  spring-app:
    container_name: spring-app
    image: spring-app
    build: ./spring-app
    ports:
      - "18080:18080"
    volumes:
      - ./logs:/usr/local/app/logs
    depends_on:
      - oracle-db
    restart: on-failure
    networks:
      - app-network

  react-app:
    container_name: react-app
    build: ./react-app
    image: react-app
    ports:
      - "3000:80"
    volumes:
      - ./react-app:/app
    networks:
      - app-network

  networks:
    app-network:
      name: app-network
      driver: bridge
  
volumes:
  oracle-data:
    driver: local
```

#### ✅ `Jenkins` `Pipeline` `Script`
```bash
pipeline {
	agent any
	stages {
		stage('Checkout') {
			steps {
				git branch: '브랜치명', url: '깃허브_프로젝트_주소'
			}
		}
		
		stage('Prepare') {
			steps {
				sshagent(['젠킨스_자격증명_키']) {
					sh 'mkdir -p ${WORKSPACE}/프로젝트/src/main/resources'
 
					sh 'scp -o StrictHostKeyChecking=no 사용자명@서버도메인:/home/ubuntu/application.yml ${WORKSPACE}/프로젝트/src/main/resources/application.yml'
				}
			}
		}
		
		stage('Build and Deploy with Docker Compose') {
			steps {
				sh 'docker-compose -f ${WORKSPACE}/docker-compose.yml up --build -d'
			}
		}
	}
	
	post {
		failure {
			echo 'Deployment failed.'
		}
	}
}
```
- `GitHub` `Webhook`을 통해 `Push Event`를 감지해서 소스 코드를 `${WORKSPACE}`로 가져온다. 
- 서버에서 직접 소스 코드의 `resources` 경로로 주입해 준다. 
- `Docker-compose.yml` 파일을 빌드하면 네트워크가 생성되고, 그 안에서 `Docker` `Container`들이 생성되고 실행된다.

#### ✅ `application.yml` 파일 작성
```yml
spring:
  datasource:
    url: jdbc:oracle:thin:@oracle-db:1521:xe?oracle.jdbc.timezoneAsRegion=false
    username: 사용자명
    password: 비밀번호
    driver-class-name: oracle.jdbc.OracleDriver
  jpa:
    database-platform: org.hibernate.dialect.OracleDialect
    hibernate:
      ddl-auto: update

logging:
  level:
    org.hibernate.SQL: debug
    org.hibernate.orm.jdbc.bind: trace
    최상위_도메인.2차_도메인.프로젝트명: debug
  file:
    name: /usr/local/app/logs/app.log

jwt:
  secret-key: 문자열

server:
  port: 18080
```


### 프로젝트 빌드
![](/assets/image/Pasted%20image%2020250531215121.png)
- `GitHub`에 변경 사항을 `Push`하거나, `Jenkins Console`에서 "지금 빌드" 버튼을 누르면 된다.
- `Pipeline` 실행이 성공하면 위 이미지처럼 `Docker` `Container`가 모두 올라와 있어야 한다. 


### 회고
![](/assets/image/Pasted%20image%2020250531215311.png)
- `Jenkins` `Pipeline` `Script`를 `101`번이나 삽질하면서 몇 가지 느낀 점이 있다.
	1. 초기 `Pipeline` 구축 시 테스트 코드는 너무 오랜 시간이 소요되므로 제외하고 빌드 하는 것이 좋다.
	2. `application.yml` 파일은 프로젝트 내에 포함하되, 안의 내용은 `.env`를 통해 외부에서 관리하고 주입하는 것이 좋다.
- 혹자는 완벽하게 이해하고 적은 횟수를 시도해서 성공하는 것이 좋다고 생각 할 수 있고, 그게 맞을 수도 있다.
- 그런데 필자는 많은 시도에서 겪는 `Trouble-shooting` 과정에서도 얻을 게 많다고 생각하기 때문에 `101`번의 시도가 의미가 있었다고 생각한다.
