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

본 페이지는 Spring Boot Actuator 를 빠르게 익히기 위한 페이지 입니다. 사용된 샘플 코드는 [Github](https://github.com/gumicode/spring-batch-skeleton) 에서 확인하실수 있습니다.

참고한 공식 문서는 아래와 같습니다. 

- [Spring Actuator](https://docs.spring.io/spring-boot/docs/current/actuator-api/htmlsingle/)
- [Spring Boot Actuator Web API Documentation](https://docs.spring.io/spring-boot/docs/current/actuator-api/htmlsingle/)


## What Actuator

스프링 부트 엑추에이터는 서비스 환경에서 필요한 다양한 기능 및 모니터링 지표를 코드 몇줄로 간단히 추가할수 있도록 기능을 제공한다.

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

{: .note-title }
> My note title
>
> A paragraph with a custom title callout

Default label
{: .label }


아직 서비스 운영 경험이 없으신 분들은 Health Check 기능이 왜 필요한지 모를 수 있다. 실제 운영환경에서는 어플리케이션 앞 단에 로드밸런서 혹은 트래픽을 라우팅 해주는 앞단이 있는데, 이때 헬스 체크를 하지 않고 트래픽을 라우팅하게 되면 심각한 장애로 발생될 수 있다. 