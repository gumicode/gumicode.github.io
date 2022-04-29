---
layout: default
title: 어댑터 패턴 Adapter Pattern
parent: 디자인 패턴 Design Pattern
nav_order: 200
last_modified_date: 2021-04-30 03:16:00
last_modified_at: 2021-04-30 03:16:00
---

# 어댑터 패턴 Adapter Pattern
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요
**어댑터 패턴** (Adapter Pattern) 이란, 기존 코드를 클라이언트가 사용하는 인터페이스의 구현체로 바꿔주는 패턴이다.

## 사용 목적
실 생활의 예시를 들자면 벽 콘센트에는 220V , 110V 등 국가마다 다른 규격을 사용하는데 사용하고자 하는 전자기기와 전원의 볼트가 서로 다를 경우 이를 사용하기 위해 변압기를 사용하게 된다. 어댑터 패턴 또한 중간에 변압기 역할을 하는 것을 추가하여 서로 인터페이스가 달라 통신이 불가능한 부분을 해결하기 위한 패턴이다.

클라이언트가 사용하는 인터페이스는 정해져 있고, 서버 입장에서 이미 사용중인 인터페이스도 서로 정해져 있는데 서로 인터페이스가 동일하지 않아 통신이 불가능하다면 각 인터페이스를 수정하지 않고 어댑터를 추가하여 서로 연결이 가능하도록 만들 수 있다. 이렇게 되면 인터페이스를 실제로 변경해야 하는 수고를 덜 수 있을 뿐더러, 인터페이스를 수정함으로 인해서 해당 요청 외에 다른 곳에서 발생되는 수 많은 예외사항들을 미리 예방할 수 있다.

간단하게 요약하자면, 기존의 인터페이스와 코드를 수정하지 않고 어댑터를 추가하여 통신의 문제로 서로 동작할수 없었던 문제를 해결하는 패턴이다. 외부 라이브러리를 사용하고 있어 코드를 직접 수정할수 없는 경우에도 해당 방법으로 해결 할 수 있다. 

**장점**
- 기존 코드를 변경하지 않고 원하는 인터페이스 구현체를 만들어 재사용할 수 있다.
- 기존 코드가 하던 일과 특정 인터페이스 구현체로 변환하는 작업을 각기 다른 클래스로 분리하려 관리할 수 있다.

**단점**
- 새 클래스가 생겨 복잡도가 증가할 수 있다. 경우에 따라서는 기존 코드가 해당 인터페이스를 구현하도록 수정하는것이 좋은 선택이 될 수도 있다.

## 생성 규칙

### 조건 예시

- UserDetails 인터페이스 A 가 있다.
- Account 클래스 B 가 있다.
- UserDetails 의 gerUsername() 호출시 Account 의 nickname 이 출력되어야 한다.
- 그러나 UserDetails 혹은 Account 의 코드는 직접 변경하면 안된다.

### 생성 예시

```java
public interface UserDetails {
    String getUsername();
}
```

```java
public class Account {
    private String nickname;
}
```
username 과 nickname 은 변수명은 서로 다르지만 동일한 데이터라고 가정한다. 

서로 연관관계가 없는 객체들이라 서로 통신이 불가능하다. 물론 코드를 변경하는 방법이 있겠지만, 만약 Account 라는 클래스가 외부에서 주입받은 라이브러리라서 코드를 수정할 수 없을 수 있고, Account 클래스가 여러가지 이유로함부로 코드를 수정해서는 안되는 상황일 수 있다. 이럴때 어댑터 패턴을 사용할 수 있다.

```java
public class AccountUserDetailsAdapter implements UserDetails{
    private Account account;

    public AccountUserDetailsAdapter(Account account) {
        this.account = account;
    }
    @Override
    public String getUsername() {
        return account.getNickname();
    }
}
```

위와 같이 새로운 어댑터 클래스를 추가 함으로써 중간에 중간 다리 역할을 하여 서로 연결을 시켜주는 패턴을 어댑터 패턴이라고 한다.

## 실전에서 사용된 예시

### JAVA
- java.util.Arrays#asList(T...)
- java.util.Coolections#list(Enumeration)
- java.io.InputSteamReader(InputStream)
- java.io.OutputSteamWriter(OutputStream)

### SPRING
- HandlerAdapter

## 결론
어댑터 패턴은 서로 연관관계가 없는 클래스나 인터페이스에 중간다리 역할로 묶어주는 역할을 한다. 기존 코드를 변경하지 않고 사용할수 있는 장점이 있지만 반대로 코드의 개수가 늘어나 복잡도가 증가할 수 있다. 그러므로 직접 코드를 변경하는 경우가 유리한지, 아니면 어댑터 클래스를 추가하는것이 유리한지 잘 판단하여 적용해야 한다.