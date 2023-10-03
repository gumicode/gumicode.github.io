---
layout: default
title: <p style='display:none;'>Spring Boot Actuator -</p> Quick Start
parent: Spring Boot Actuator
nav_order: 100
last_modified_date: 2023-10-03 17:00:00
last_modified_at: 2023-10-03 17:00:00
---

# Quick Start
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

본 페이지는 Spring Boot Actuator 를 빠르게 익히기 위한 페이지 입니다. 사용된 샘플 코드는 [Github](https://github.com/gumicode/spring-boot-actuator-quick-start) 에서 확인하실수 있습니다.

참고한 공식 문서는 아래와 같습니다. 

- [Spring Actuator](https://docs.spring.io/spring-boot/docs/current/actuator-api/htmlsingle/)
- [Spring Boot Actuator Web API Documentation](https://docs.spring.io/spring-boot/docs/current/actuator-api/htmlsingle/)


## What Actuator

스프링 부트 엑추에이터는 서비스 환경에서 필요한 다양한 기능 및 모니터링 지표를 코드 몇줄로 간단히 추가할수 있도록 제공한다.

필요한 의존성은 다음과 같다. web 의존성이 없으면 서버로써의 역할을 하지 못하기 때문에 함께 필요하다.

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

[http://localhost:8080/actuator](http://localhost:8080/actuator) 로 접속하면 actuator 에서 제공하는 endpoint 가 노출된다. 

```json
{
    "_links": {
        "self": {
            "href": "http://localhost:8080/actuator",
            "templated": false
        },
        "health": {
            "href": "http://localhost:8080/actuator/health",
            "templated": false
        },
        "health-path": {
            "href": "http://localhost:8080/actuator/health/{*path}",
            "templated": true
        }
    }
}
```

엑추에이터에서는 보안상의 이유로 endpoint 를 모두 포함하지 않기 때문에 <code>application.properties</code> 에 다음과 같이 추가를 하면 모든 endpoint 를 추가할 수 있다.

```properties
management.endpoints.web.exposure.include=*
```
```json
{
    "_links": {
        "self": {
            "href": "http://localhost:8080/actuator",
            "templated": false
        },
        "beans": {
            "href": "http://localhost:8080/actuator/beans",
            "templated": false
        },
        "caches-cache": {
            "href": "http://localhost:8080/actuator/caches/{cache}",
            "templated": true
        },
        "caches": {
            "href": "http://localhost:8080/actuator/caches",
            "templated": false
        },
        "health-path": {
            "href": "http://localhost:8080/actuator/health/{*path}",
            "templated": true
        },
        "health": {
            "href": "http://localhost:8080/actuator/health",
            "templated": false
        },
        "info": {
            "href": "http://localhost:8080/actuator/info",
            "templated": false
        },
        "conditions": {
            "href": "http://localhost:8080/actuator/conditions",
            "templated": false
        },
        "configprops-prefix": {
            "href": "http://localhost:8080/actuator/configprops/{prefix}",
            "templated": true
        },
        "configprops": {
            "href": "http://localhost:8080/actuator/configprops",
            "templated": false
        },
        "env": {
            "href": "http://localhost:8080/actuator/env",
            "templated": false
        },
        "env-toMatch": {
            "href": "http://localhost:8080/actuator/env/{toMatch}",
            "templated": true
        },
        "loggers": {
            "href": "http://localhost:8080/actuator/loggers",
            "templated": false
        },
        "loggers-name": {
            "href": "http://localhost:8080/actuator/loggers/{name}",
            "templated": true
        },
        "heapdump": {
            "href": "http://localhost:8080/actuator/heapdump",
            "templated": false
        },
        "threaddump": {
            "href": "http://localhost:8080/actuator/threaddump",
            "templated": false
        },
        "metrics-requiredMetricName": {
            "href": "http://localhost:8080/actuator/metrics/{requiredMetricName}",
            "templated": true
        },
        "metrics": {
            "href": "http://localhost:8080/actuator/metrics",
            "templated": false
        },
        "scheduledtasks": {
            "href": "http://localhost:8080/actuator/scheduledtasks",
            "templated": false
        },
        "mappings": {
            "href": "http://localhost:8080/actuator/mappings",
            "templated": false
        }
    }
}
```

반대로 내가 원하는 엔드포인트만 추가하고 싶다거나 제외하고 싶을땐 아래와 같이 사용할 수 있다.

```properties
management.endpoints.web.exposure.include=metrics,loggers
management.endpoints.web.exposure.exclude=health
```

## 엔드포인트 대표 목록

본 페이지에서는 주로 사용하는 대표적인 엔드포인트에 대해서만 소개 한다. [공식문서](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.endpoints) 에 들어가면 모든 엔드포인트 목록을 확인할 수 있다.

| Endpoint ID | Description |
|:--|:--|
| health <b>(*)</b> | 어플리케이션의 상태 정보를 표시한다. 쿠버네티스에서 실행하기 위한 Probe 인 liveness readiness 도 지원한다. |


### health

Health는 서비스 운영시 어플리케이션이 살아있는지 상태를 체크해주는 중요한 엔드포인트이다. 

아직 서비스 운영 경험이 없으신 분들은 Health Check 기능이 왜 필요한지 모를 수 있다. 어플리케이션을 배포할 때는 앞 단에 로드밸런서 혹은 트래픽을 라우팅 을 관리하는 서비스가 있는데, 이때 헬스 체크를 하지 않고 트래픽을 어플리케이션으로 라우팅하게 되면 심각한 장애로 발생될 수 있다. 

[http://localhost:8080/actuator/health](http://localhost:8080/actuator/health) 로 접속하면 health check 상태를 확인할 수 있다.
```json
{
    "status": "UP"
}
```

 Controller 에서 <code>@GetMapping</code> 으로 단순히 응답을 구현한 것과 차이점은 헬스체크는 단순히 어플리케이션의 PING 여부만 체크하는것이 아니다. 경우에 따라서는 DB Connection 등 외부 서비스와의 통신까지 헬스체크 여부에 포함하여 사용 할 수 있고 엑추에이터는 이 기능을 코드 몇줄로 간단히 기능을 제공해 주는 역할을 한다. 

 엑추에이터는 보안상의 이유로 해당 엔드포인트에서 실행중인 컴포넌트를 숨김처리하여 보여주는데, 다음과 같이 show-components 를 추가 해 주면 해당 엔드포인트에서 동작중인 모든 컴포넌트를 확인할 수 있다. 

```properties
#모든 컴포넌트 상태정보를 숨김해제 한다.
management.endpoint.health.show-components=always

#컴포넌트 안에 수치까지 모두 보고싶다면 해당 옵션을 사용한다.
management.endpoint.health.show-details=always 
```

```json
{
    "status": "UP",
    "components": {
        "diskSpace": {
            "status": "UP"
        },
        "ping": {
            "status": "UP"
        }
    }
}
```

초기에는 활성화 된 컴포넌트가 <code>diskSpace</code>, <code>ping</code> 밖에 없지만 아래에서 나열하는 컴포넌트 의존성을 추가할 경우 자동으로 등록 된다. 

#### Components

본 페이지에서는 주로 사용하는 대표적인 컴포넌트에 대해서만 소개 한다. [공식문서](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.endpoints.health.auto-configured-health-indicators) 에 들어가면 모든 컴포넌트 목록을 확인할 수 있다.

| Component ID | Class Indicator | Description |
|:--|:--|
| db <b>(*)</b> | [DataSourceHealthIndicator](https://github.com/spring-projects/spring-boot/tree/v3.1.4/spring-boot-project/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/jdbc/DataSourceHealthIndicator.java) | DataSource 가 작동 중인지 확인 합니다. |
| diskspace | [DiskSpaceHealthIndicator](https://github.com/spring-projects/spring-boot/tree/v3.1.4/spring-boot-project/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/system/DiskSpaceHealthIndicator.java) | 디스크 공간이 부족한지 확인합니다. |
| jms <b>(*)</b> | [JmsHealthIndicator](https://github.com/spring-projects/spring-boot/tree/v3.1.4/spring-boot-project/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/jms/JmsHealthIndicator.java) | JMS 브로커가 작동 중인지 확인합니다. |
| mongo | [MongoHealthIndicator](https://github.com/spring-projects/spring-boot/tree/v3.1.4/spring-boot-project/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/data/mongo/MongoHealthIndicator.java) | Mongo 데이터베이스가 작동 중인지 확인합니다. |
| rabbit | [RabbitHealthIndicator](https://github.com/spring-projects/spring-boot/tree/v3.1.4/spring-boot-project/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/amqp/RabbitHealthIndicator.java) | Rabbit 서버가 작동 중인지 확인합니다. |
| redis <b>(*)</b> | [RedisHealthIndicator](https://github.com/spring-projects/spring-boot/tree/v3.1.4/spring-boot-project/spring-boot-actuator/src/main/java/org/springframework/boot/actuate/data/redis/RedisHealthIndicator.java) | Redis 서버가 작동 중인지 확인합니다. |

해당 컴포넌트들은 기본적으로 항상 활성화 되기 때문에 특정 컴포넌트가 문제가 발생할 경우 헬스체크가 실패하여 전면 장애로 발생할 수 있다. 그러므로 안전하게 기본 실행을 비활성화 시키고 원하는 컴포넌트만 추가 하는것을 권장한다.

```properties
management.health.defaults.enabled=false
management.health.db.enabled=true
management.health.redis.enabled=true
management.health.mongo.enabled=true
```

#### DataSourceHealthIndicator

DataSourceHealthIndicator 를 활성화 하기 위해서 간단하게 JPA 의존성을 추가한다.