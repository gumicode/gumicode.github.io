---
layout: default
title: '@EnableJpaAuditing'
parent: Spring Data JPA
nav_order: 1000
last_modified_date: 2022-07-10 00:00:00
last_modified_at: 2022-07-10 00:00:00
---

# @EnableJpaAuditing
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

Entity 에 값을 입력할 때 반복적으로 발생될 수 있는 부분을 자동화 하는데 사용한다.

일반적으로 `createDatetime` `updateDatetime` 같이 공통 컬럼을 자동 주입하기 위해 사용하거나 등록자 및 수정자를 직접 컬럼에 값을 삽입하는 형태가 아닌 자동화 하기 위해 AuditorAware 사용한다.

## 사용 예시

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

    @CreatedBy
    private Long createdBy;

    @LastModifiedBy
    private Long modifyBy;
}
```

```java
@Entity
public class Member extends BaseEntity {
}
```


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