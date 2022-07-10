---
layout: default
title: Lombok @EqualsAndHashCode
parent: Lombok
nav_order: 100
last_modified_date: 2022-07-11 00:31:00
last_modified_at: 2022-07-11 00:31:00
---

# Lombok @EqualsAndHashCode
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

[공식문서 링크](https://projectlombok.org/features/EqualsAndHashCode)

클래스 어노테이션에 `@EqualsAndHashCode` 을 선언할 경우 해당된 필드를 기반으로 상속하는 equals 및 hashCode 메서드에 대한 구현을 생성할 수 있다.

`@EqualsAndHashCode(of= {"id"})` 를 통해 특정 필드만 메소드 구현을 설정하거나 `@EqualsAndHashCode(exclude= {"id"})` 를 통해 특정 필드 메소드 구현을 제외할 수 있다.

## 사용 예시

```java
@EqualsAndHashCode(of= {"id"})
public class Member {
    private Long id;
    private String username;
}
```

### 사용 시 주의사항

- 변경 가능한 필드에 대해 `@EqualsAndHashCode` 을 구현하면 안된다.
- 동일한 hashcode 를 가지고 있는 두개의 object 가 있을 경우 한곳에 수정하면 다른 object 까지 동시에 수정되기에 예상하지 못한 문제가 발생할 수 있다.
- 그러므로 같이 변경할 계획이 없는 유니크 필드만 지정하는것이 좋다. 
- 결론적으로 `@EqualsAndHashCode(of= {"id"})` 같이 of 로 직접 원하는 필드만 지정하는것이 안전하게 사용하는 방법이다. 
- 단순히 `@EqualsAndHashCode` 를 전체로 사용하는 행위는 좋은 방법이 아니다.

## Equals 와 HashCode 와의 관계

- 동일한 객체는 동일한 메모리 주소를 갖는것을 의미한다. 그러므로 동일한 객체는 동일한 해시코드를 가져야 한다. 
- 만약 이러한 규칙을 무시 할 경우 `HashSet.contains` 같은 컬렉션 동작이 올바르게 동작하지 않는다.
- 실제 코드환경에서 자주 사용되는 경우는 JPA 등에서 목록 조회시 Set 컬렉션으로 응답할 경우 hashcode 가 올바르게 설정되어 있지 않을경우 중복이 발생할 수 있다. 물론 Entity 의 경우에는 `@Id` 를 기준으로 동일한 hashcode 를 만들어 주기 때문에 문제가 없을 수 있으나, Entity -> Dto 으로 변환시 해당 유니크가 깨질 수 있다.
- JPA 같은 영속성 Entity 가 아니더라도 동일한 Object 에 대해서 Set 같은 유니크를 보장하기 위해서는 lombok의 `@EqualsAndHashCode` 을 활용할 수 있다.