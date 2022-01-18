---
layout: default
title: Slf4j 튜토리얼
parent: Slf4j
nav_order: 2
---

# Slf4j 튜토리얼
해당 라이브러리는 스프링 부트 사용을 기준으로 설명 한다. 

## Dependencies

Spring boot에서 기본 탑재 되어 있기에 별도로 **의존성을 추가할 필요는 없다.**  하지만 Slf4j 를 손쉽게 사용할 수 있는 옵션인 <code>lombok</code> 을 추가하는것을 권장한다.

<code>build.gradle</code>
```gradle
dependencies {
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}
```
