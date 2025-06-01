---
title: 💎 ELK · API Gateway · Logback 중앙 관리자 서버 구축
date: 2025-04-17 21:52:00 +0900
categories:
  - Tech
tags:
  - Tech
  - ELK
  - API Gateway
  - Logback
---

### `ELK` · `API Gateway` 도입 근거
#### ✅ `ELK`
- 시스템과 서비스 로그를 중앙에서 통합 수집 및 저장할 수 있다. 
- `Kibana`를 통해 실시간 대시보드 및 시각화를 구성하여 운영 현황을 모니터링 할 수 있다.  
- 서비스 장애나 오류 발생 시, 로그 검색을 통해 빠르게 원인을 파악하고 대응할 수 있다.  

#### ✅ `API Gateway`
- `JWT` 인증 및 인가를 전담할 수 있다.  
- 너무 많은 요청을 보내는 클라이언트를 차단 및 제어 가능하다.
- `API` 버전 관리 가능하다.
- 서비스 별 상이한 `API` 계약을 표준화 하여 외부에 제공한다.

#### ✅ `Logback`
- 로그 파일 롤링 및 보존 설정이 유연하게 가능하다.


### 코드 작성
#### ✅ `ELK` `Docker-compose.yml` 및 설정 파일
```yml
# docker-compose.yml
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.18
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - ES_JAVA_OPTS=-Xms512m -Xmx512m
    volumes:
      - esdata:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
    networks:
      - efk
 
  kibana:
    image: docker.elastic.co/kibana/kibana:7.17.18
    container_name: kibana
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    depends_on:
      - elasticsearch
    networks:
      - efk

  logstash:
    image: docker.elastic.co/logstash/logstash:7.17.18
    container_name: logstash
    volumes:
      - ./logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml
      - ./logstash/pipeline:/usr/share/logstash/pipeline
      - ./sentinel-app/logs:/app/logs
    ports:
      - "5044:5044"
      - "9600:9600"
    depends_on:
      - elasticsearch
    networks:
      - efk

  gateway:
    build:
      context: ./sentinel-app
    container_name: sentinel-app
    ports:
      - "8080:8080"
    volumes:
      - ./sentinel-app/logs:/app/logs
    networks:
      - efk

volumes:
  esdata:

networks:
  efk:
  
# logstash.yml 
http.host: "0.0.0.0"
xpack.monitoring.enabled: false
```

```bash
# logstash.conf
input {
  file {
	path => "/app/logs/*.log"
	start_position => "beginning"
	sincedb_path => "/dev/null"
	codec => json
  }
}

filter {
  mutate {
	add_field => {
	  "log_message" => "%{message}"
	  "server_role" => "gateway"
	}
	remove_field => ["parsed", "host", "path", "message"]
  }
}

output {
  elasticsearch {
	hosts => ["http://elasticsearch:9200"]
	index => "sentinel-logs-%{+YYYY.MM.dd}"
  }

  stdout {
	codec => rubydebug
  }
}
```

#### `Logback` 설정 파일
```xml
# logback-spring.xml
<configuration>
	<include resource="net/logstash/logback/logback.xml"/>
	<property name="LOG_PATH" value="./logs"/>
	
	<appender name="JSON_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<fileNamePattern>${LOG_PATH}/app-%d{yyyy-MM-dd}.log</fileNamePattern>
			<maxHistory>30</maxHistory>
		</rollingPolicy>
		<encoder class="net.logstash.logback.encoder.LogstashEncoder">
			<fieldNames>
				<timestamp>@timestamp</timestamp>
				<level>level</level>
				<logger>logger</logger>
				<thread>thread</thread>
				<message>message</message>
			</fieldNames>
		</encoder>
	</appender>
	
	<root level="INFO">
		<appender-ref ref="JSON_FILE"/>
	</root>
</configuration>
```
- `Logback`을 사용하여 로그 파일을 생성한다.
- `<include resource="net/logstash/logback/logback.xml"/>` 라인을 통해 `Logstash`에 맞는 `JSON` 형식의 로그 파일을 출력한다.
- `<encoder class="net.logstash.logback.encoder.LogstashEncoder"> ... </encoder>` 라인을 통해 로그의 각 항목을 나타낸다.
- 이 항목들은 `Elasticsearch`에서 필터링 할 수 있는 `Metadata`가 되는 것이다.
- `Logstash`는 로그 파일을 읽어 `Elasticsearch`로 전달한다.
- `logstash.yml`의 `http.host` 키를 통해 타 서버에서도 로그를 전송할 수 있다.
- `logstash.conf` 설정을 통해 가공 및 필터링 하여 인덱스로 생성 및 데이터를 주입한다.

#### ✅ `API Gateway` 설정 파일일
```bash
implementation 'org.springframework.cloud:spring-cloud-starter-gateway:4.2.0'
```

```yml
# application.yml
spring:
  application:
    name: gateway
  cloud:
    gateway:
      routes:
        - id: 332-project-management-service
          uri: http://127.0.0.1:8081
          predicates:
            - Path=/project-management/**
          filters:
            - StripPrefix=1

        - id: 332-service-desk
          uri: http://127.0.0.1:8082
          predicates:
            - Path=/service-desk/**
          filters:
            - StripPrefix=1

server:
  port: 8080
```
- `Spring Cloud Gateway`를 사용했다.
 - `predicates` 키를 통해 경로 `Endpoint`를 설정했다.
 - `filters` 키의 `StripPrefix=1` 값을 통해 `predicates` 키에서 설정한 엔드포인트의 `/**`을 실제 API 요청 경로로 지정했다.
 - 가령 `http://localhost:8080/project-management/project`로 `POST` 따위의 요청이 들어오면 `API Gateway`가 요청을 `http://localhost:8081/project`로 `Redirect` 한다. 


### 로그 분석
#### ✅ 프로젝트 실행 및 로그 파일 확인
![](/assets/image/Pasted%20image%2020250601161156.png)

#### ✅ `Kibana UI` 확인
![](/assets/image/Pasted%20image%2020250601161225.png)
- 로그 파일과 동일한 일자에 데이터가 존재하는 것을 확인할 수 있다.

![](/assets/image/Pasted%20image%2020250601161244.png)
- `logback-spring.xml`에서 `level`을 필드에 추가했기 때문에 `Elasticsearch` 및 `Kibana`에서 로그 레벨 별 필터링을 할 수 있게 되었다.


### 회고
- 이제 기존 프로젝트들의 `JWT` 인증 로직을 `API Gateway` 서버에서 전담하도록 수정해야 한다.
- 또한 프로젝트들의 로그를 `API Gateway` 서버로 전송하여 통합 관리하도록 수정 할 것이다.