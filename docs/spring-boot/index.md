---
layout: default
title: Spring Boot
has_children: true
nav_order: 100010
last_modified_date: 2022-07-10 00:00:00
last_modified_at: 2022-07-10 00:00:00
---

# Spring Boot
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

## 대표적인 어노테이션 

### @Configuration

환경 설정을 담당하는 스프링 빈 객체를 정의할 때 사용한다.  Bean 객체중 가장 최상위 우선순위를 가진다. 

### @EnableAutoConfiguration

스프링과 스프링부트의 가장 큰 차이점은 별도의 설정 없이 자동으로 환경을 설정해 주는 부분이다. exclude 를 통해 자동으로 주입 해주는 설정을 제거할 수 있다.

- `DataSourceAutoConfiguration`
    - jdbc datasource 를 자동으로 구성할 수 있다. 
- `DataSourceTransactionManagerAutoConfiguration`
    - Transaction Manager 를 자동으로 구성할 수 있다.
- `HibernateJpaAutoConfiguration`
    - Hibernate ORM JPA 관련 내용을 자동으로 구성할 수 있다.

### @ComponentScan

특정 패키지 경로를 기준으로 자동으로 추적하여 bean 을 등록 해 준다. 기본 설정은 스프링 부트 패키지 하위 경로부터 탐색한다. 
예를들어 `com.example` 이 프로젝트 패키지 경로라면 상위 패키지에는 탐색하지 않는다. 상위 패키지 경로도 탐색하고 싶다면 별도로 패키지 경로를 아래와 같이 추가해야 한다.

```java
@ComponentScan({"com","kr.co.example"})
```

 여기서 주의사항은 `@ComponentScan` 의 경우 `@Component` 나 `@Configuration` `@Service` 의 Bean 만 의존성을 자동으로 주입해준다. 

`@Repository` 의 경우엔 `@EnableJpaRepositories` 을 사용해야 한다.

```java
@EnableJpaRepositories({"com","kr.co.example"})
```

 `@Entity` 의 경우  `@EntityScan` 을 사용하여야 한다.

 ```java
@EntityScan({"com","kr.co.example"})
```