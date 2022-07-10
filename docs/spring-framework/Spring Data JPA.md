---
layout: default
title: Spring Data JPA
parent: 스프링 프레임워크 Spring Framework
nav_order: 110
last_modified_date: 2022-07-10 00:00:00
last_modified_at: 2022-07-10 00:00:00
---

# Spring Data JPA
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

## 대표적인 기능

### AuditorAware

관리자 페이지에서 작업을 하게 되면 누가 만들었고 누가 수정했는지 이러한 내용을 DB에 저장하는 작업이 반복적으로 발생한다. 이를 자동화 하기 위해 AuditorAware 사용한다.

아래는 Spring Security 와 연동하여 로그인 정보를 가져와서 저장하는 방법이다.

```java
@Configuration
@EnableJpaAuditing
public class AuditorAwareImpl implements AuditorAware<Long> {
	@Override
	public Optional<Long> getCurrentAuditor() {
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
        if(authentication == null || !authentication.isAuthenticated()) {
            return null;
        }
        return Optional.of((Member)authentication.getPrincipal()).getMemberId();
	}
}
```

```java
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class BaseEntity {
    @CreatedBy
    private Long createdBy;
    @LastModifiedBy
    private Long modifyBy;
}
```

위처럼 공통 BaseEntity 를 선언하여 상속받아 사용할 수 있다. 

## 대표적인 어노테이션 

### @EnableJpaAuditing

모든 Entity 에 공통으로 들어나는 컬럼을 정의할 때 사용한다. 일반적으로 `createDatetime` `updateDatetime` 은 모든 Entity 에 사용하는 경우가 많기에 공통으로 정의하고 상속받아 사용한다.

이때 아래처럼 `AuditingEntityListener` 를 사용하기 위한 옵션이다. 

```java
@Configuration
@EnableJpaAuditing
public class AuditConfig {
}
```

```java
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class BaseEntity {
    @CreatedDate
    @Column(nullable = false, updatable = false)
    private LocalDateTime createDatetime;

    @LastModifiedDate
    @Column(nullable = false)
    private LocalDateTime updateDatetime;
}
```

```java
@Entity
public class Member extends BaseEntity {
}
```