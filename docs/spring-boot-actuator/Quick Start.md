---
layout: default
title: <p style='display:none;'>Spring Boot Actuator -</p> Quick Start
parent: Spring Boot Actuator
nav_order: 100
last_modified_date: 2023-10-03 17:00:00
last_modified_at: 2023-12-11 13:00:00
---

# Quick Start
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

본 페이지는 Spring Boot Actuator 를 빠르게 시작하기 위한 페이지 입니다. 사용된 샘플 코드는 [Github](https://github.com/gumicode/spring-boot-actuator-quick-start) 에서 확인하실수 있습니다.

참고한 공식 문서는 아래와 같습니다. 

- [Spring Actuator](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/actuator.html)
- [Spring Boot Actuator Web API Documentation](https://docs.spring.io/spring-boot/docs/current/actuator-api/htmlsingle/)


## What Actuator?

Spring Boot Actuator 는 서비스에 필요한 모니터링 지표를 코드 몇줄로 간단히 추가할수 있으며 이 지표를 외부에서 가져갈수 있도록 Endpoint 를 제공한다. Endpoint 을 Web 으로 노출 할 것기에 필요한 의존성은 다음과 같다.

<b>build.gradle</b>

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

[http://localhost:8080/actuator](http://localhost:8080/actuator) 로 접속하면 actuator 에서 제공하는 endpoint 가 노출된다. 

<b>application.yml</b>

```yaml
management:
  endpoints:
    web:
      exposure:
        include: '*'
```

Actuator 에서는 보안상의 이유로 endpoint 를 모두 포함하지 않기 때문에 위같이 추가를 하면 모든 endpoint 를 추가할 수 있다.

<b>application.yml</b>

```yml
management:
  endpoints:
    web:
      exposure:
        include: metrics,loggers
        exclude: health
```

반대로 내가 원하는 엔드포인트만 추가하고 싶다거나 제외하고 싶을땐 위같이 사용할 수 있다.

## Endpoints

[![endpoints](/meta/docs/spring-boot-actuator/endpoints.png){: width="2014" }](/meta/docs/spring-boot-actuator/endpoints.png){: target="_blank" }

Endpoint 를 사용하면 애플리케이션을 모니터링하고 상호 작용할 수 있으며, 여러 Endpoint 가 기본 내장되어 있다.

위 이미지에서 나온 요소들은 actuacotr autoconfigure 에서 제공해주는 요소들이며, 이 외에도 내가 커스텀하게 사용하고 싶다면 직접 indicator 를 구현하여 Component 에 적용할 수 있다.

Actuator 에서는 지표를 확인 하고자 하는 요소를 Component 라고 부른다. 그리고 Component 를 외부로 노출시키기 위한 경로를 Endpoint 라고 한다. 

앞으로 자주 사용될 Actuator 에서 사용주로 사용하는 용어를 정리 하자면 다음과 같다. 

- `Endpoint` : 모니터링 지표를 외부에 제공해주기 위한 진입점 
- `Component` : Endpoint 에 지표를 노출시키고자 하는 요소. 하나의 Endpoint 안에 여러개의 컴포넌트가 존재할 수 있으며 Endpoint 와 Component 는 1:N 관계이다.
- `Indicator` :  Component 에서 출력하고자 하는 지표값을 구현 클래스이다. Component 와 Indicator 는 1:1 관계이다.

Endpoint 하위에서도 N개의 Component 를 추가할수 있으며, 대표적으로 Health Endpoint 같은 경우에는 다양한 components 를 제공하고 있다.

### Health

어플리케이션이 트래픽을 받을 준비가 되어 있다고 알려주는 역할을 Health Endpoint 에서 담당한다. Health는 서비스 운영시 어플리케이션이 살아있는지 상태를 체크해주는 Spring boot actuator 의 대표적인 endpoint 이다. 

Health Check 가 필요한 이유는 어플리케이션을 배포할 때는 앞 단에 로드밸런서 혹은 트래픽을 라우팅 을 관리하는 서비스가 있는데 헬스 체크를 하지 않고 트래픽을 라우팅하게 되면 심각한 장애로 발생될 수 있다. 경우에 따라선 초기화 작업이 시간이 걸릴 수 있기 때문이다. 

[http://localhost:8080/actuator/health](http://localhost:8080/actuator/health) 로 접속하면 health check 상태를 확인할 수 있다.
```json
{
    "status": "UP"
}
```

아래는 Health Endpoint 에서 autoconfigure 로 지원하는 components 목록이다.

[![health-components](/meta/docs/spring-boot-actuator/health-components.png){: width="157" }](/meta/docs/spring-boot-actuator/health-components.png){: target="_blank" }

 헬스체크는 단순히 어플리케이션의 PING 여부만 체크하는것이 아니다. DB Connection 등 외부 서비스와의 통신까지 헬스체크 여부에 포함하여 사용 할 수 있고 Actuator 를 사용하여 코드 몇줄로 간단히 추가할 수 있다. 물론 기본으로 제공해주는 클래스에는 한계가 있기 때문에 커스텀으로도 추가하여 사용할 수 있다.

 참고로 Actuator 는 보안상의 이유로 해당 엔드포인트에서 실행중인 컴포넌트를 숨김처리하여 보여주는데, 다음과 같이 show-components 를 추가 해 주면 해당 엔드포인트에서 동작중인 모든 컴포넌트를 확인할 수 있다. 

<b>application.yml</b>

```yml
management:
  endpoint:
    health:
      show-components: always # 사용중인 health components 를 모두 노출시킨다. 운영 환경에서는 주의해서 사용해야 한다.
      show-details: always # 사용중인 health components 의 상세수치를 모두 노출시킨다. 운영 환경에서는 주의해서 사용해야 한다.
```

해당 컴포넌트들은 기본적으로 모두 자동으로 활성화 되기 때문에 자동으로 추가되는것을 비활성화 하기 위해선 다음과 같이 옵션을 주어야 한다.

<b>application.yml</b>

```yml
management:
  health:
    defaults:
      enabled: false # 내가 원하는 것만 활성화 시키기 위해 기본으로 활성화 되는 components 를 비활성화 시킨다.
```

개인적으로는 자동으로 모든 컴포넌트를 활성화 하는것보다는 직접 선언하는것을 선호한다.

#### Components

본 페이지에서는 주로 사용하는 대표적인 컴포넌트에 대해서만 소개 한다. [공식문서](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.endpoints.health.auto-configured-health-indicators) 에 들어가면 모든 컴포넌트 목록을 확인할 수 있다.

| Component ID | Class Indicator | Description |
|:--|:--|
| db <b>(*)</b> | [DataSourceHealthIndicator](https://github.com/spring-projects/spring-boot/tree/v3.1.4/spring-boot-project/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/jdbc/DataSourceHealthIndicator.java) | DataSource 가 작동 중인지 확인 합니다. |
| diskspace | [DiskSpaceHealthIndicator](https://github.com/spring-projects/spring-boot/tree/v3.1.4/spring-boot-project/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/system/DiskSpaceHealthIndicator.java) | 디스크 공간이 부족한지 확인합니다. |
| jms | [JmsHealthIndicator](https://github.com/spring-projects/spring-boot/tree/v3.1.4/spring-boot-project/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/jms/JmsHealthIndicator.java) | JMS 브로커가 작동 중인지 확인합니다. |
| mongo <b>(*)</b> | [MongoHealthIndicator](https://github.com/spring-projects/spring-boot/tree/v3.1.4/spring-boot-project/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/data/mongo/MongoHealthIndicator.java) | Mongo 데이터베이스가 작동 중인지 확인합니다. |
| rabbit | [RabbitHealthIndicator](https://github.com/spring-projects/spring-boot/tree/v3.1.4/spring-boot-project/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/amqp/RabbitHealthIndicator.java) | Rabbit 서버가 작동 중인지 확인합니다. |
| redis <b>(*)</b> | [RedisHealthIndicator](https://github.com/spring-projects/spring-boot/tree/v3.1.4/spring-boot-project/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/data/redis/RedisHealthIndicator.java) | Redis 서버가 작동 중인지 확인합니다. |

##### db

DB Connection 상태를 체크하는 Component 이다. DB Component 를 활성화 하기 위해서 테스트를 위한 MySQL 과 JPA 의존성을 추가한다. 

<b>application.yml</b>

```yml
management:
  health:
    db:
      enabled: true # db component autoconfigure 를 활성화 시킨다.
```

<b>build.gradle</b>
```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    runtimeOnly 'com.mysql:mysql-connector-j'
}
```

그리고 github 샘플코드에 추가되어있는 `./scripts/docker-mysql-install.sh` 스크립트를 실행하거나 아래처럼 터미널에서 Docker 로 MySQL 를 설치한다.

<b>Terminal</b>
```bash
docker run --name spring-boot-actuator-quick-start-mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=1234 -d mysql --default-authentication-plugin=mysql_native_password
```

<b>application.yml</b>

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306
    username: root
    password: 1234
```

위 세팅을 끝내고 서버를 실행후 [http://localhost:8080/actuator/health/db](http://localhost:8080/actuator/health/db) 로 접속하면 DB Components 가 추가된것을 확인할 수 있다.

모든 Endpoints 는 Hierachy 구조를 가지고 있기 때문에 Endpoint 에 추가된 모든 Compoent 의 상태가 모두 OK 로 전환 되어야한 상위 Endpoint 의 상태가 OK 로 응답 받을 수 있다. 하위 컴포넌트만 상태를 체크하고 싶다면 위 URL 처럼 `{endpoint}/{component}` 로 URL 를 호출하면 해당 컴포넌트의 상태만을 확인할수도 있다. 

###### Connection Failed Test

DB 를 중지시켜 Connection 을 끊게 되면 어떻게 되는지 테스트를 해보도록 하자. 

<b>Terminal</b>

```bash
docker stop spring-boot-actuator-quick-start-mysql
```

실행중인 mysql docker 컨테이너를 중지시킨다. 이후 [http://localhost:8080/actuator/health/db](http://localhost:8080/actuator/health/db) 를 호출 해보면 아래와 같이 로그가 발생한다.
 
 <b>Log</b>

```bash
2023-11-11T18:21:55.804+09:00  WARN 19068 --- [nio-8080-exec-6] com.zaxxer.hikari.pool.PoolBase          : HikariPool-1 - Failed to validate connection com.mysql.cj.jdbc.ConnectionImpl@1633e883 (No operations allowed after connection closed.). Possibly consider using a shorter maxLifetime value.
2023-11-11T18:22:25.804+09:00  WARN 19068 --- [nio-8080-exec-6] o.s.b.a.jdbc.DataSourceHealthIndicator   : DataSource health check failed
2023-11-11T18:22:25.812+09:00  WARN 19068 --- [nio-8080-exec-6] o.s.b.a.health.HealthEndpointSupport     : Health contributor org.springframework.boot.actuate.jdbc.DataSourceHealthIndicator (db) took 30011ms to respond
```

호출후 30초 동안 기다리면 위와 같은 로그가 발생하며 아래와 같이 status 가 DOWN 으로 표시 되며 503 상태로 응답하는걸 확인할 수 있다.

```json
{
    "status": "DOWN",
    "details": {
        "error": "org.springframework.jdbc.CannotGetJdbcConnectionException: Failed to obtain JDBC Connection"
    }
}
```

30초 뒤에 응답이 오는 이유는 DB Connection Timeout 이 기본값으로 30초이기 때문이며 테스트시에는 불편하므로 1초로 변경해주자. 

<b>application.yml</b>

```yaml
spring:
  datasource:
    hikari:
      connection-timeout: 1000 # DB Connection Timeout (ms)
```

단 주의할 점은 실제 대규모 서비스를 운영해보면 알겠지만 네트워크 문제로 DB Connection 이 1초 동안 끊기는 경우는 자주 발생한다.
DB 커넥션이 실패할때마다 즉시 헬스체크를 실패로 응답해버리면 쿠버네티스 같은 인프라를 운영하고 있을 경우 서버 상태가 안좋음을 감지하여 서버를 죽였다가 재 시작하기 때문이다. 

DB connection-timeout 이 너무 짧게 설정 되어 있다면 서버를 불필요하게 죽였다가 내리는 경우가 잦아질수 있다. 반대로 너무 길어도 문제가 될수 있으므로 적절한 시간을 조정하는것이 필요하다.

##### diskSpace

서버의 디스크 공간의 상태를 체크하는 Component 이다. diskSpace Component 를 활성화 하기 위해서 yml 를 추가한다. 

<b>application.yml</b>

```yml
management:
  health:
    diskSpace:
      enabled: true # diskspace 를 활성화 시킨다. 디스크 용량을 확인할 수 있다.
```

위 세팅을 끝내고 서버를 실행후 [http://localhost:8080/actuator/health/diskSpace](http://localhost:8080/actuator/health/diskSpace) 로 접속하면 diskSpace Components 가 추가된것을 확인할 수 있다.

```json
{
    "status": "UP",
    "details": {
        "total": 494384795648,
        "free": 104469712896,
        "threshold": 10485760,
        "path": "/Users/****/project/java/spring-boot-actuator-quick-start/.",
        "exists": true
    }
}
```

| Component ID | Class Indicator |
|:--|:--|
| total | 전체 디스크 용량 |
| free | 사용 가능한 디스크 용량 |
| threshold | 사용가능한 최소 디스크 공간  |
| path | 어플리케이션을 실행하고 있는 프로젝트 File Path |
| exists |어플리케이션을 실행하고 있는 프로젝트 File Path 가 존재하는지 여부 |

싱글 서버로 파일 저장까지 사용하고 있다면 디스크 사용량을 체크할때 유용하게 사용할 수 있다. 최근에는 MS 환경으로 사용하면서 서버에 직접 파일을 저장하는 경우는 없어서 사용할 일이 잘 없다.

##### jms

스프링은 AMQP 프로토콜의 비동기 메세지 서비스의 표준 템플릿을 제공하는데 이를 JMS(Java Message Service) 라고 한다. 

AMQP 프로토콜을 사용하는 대표적인 서비스는 RabbitMq, ActiveMQ 가 있다. Kafka 는 비동기 메세지 서비스이긴 하지만 AMQP 프로토콜을 사용하지 않는다.

현재 스프링 JMS 는 공식적으로 ActiveMQ 만 지원하고 있다. Actuaor 는 JMS 상태를 체크하는 Component 를 제공하고 있다.