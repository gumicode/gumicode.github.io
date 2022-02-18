---
layout: default
title: Slf4j 개요
parent: Slf4j
nav_order: 1
last_modified_date: 2021-02-18 00:00:00
last_modified_at: 2021-02-18 00:00:00
---

# Slf4j 개요

스프링 프레임워크에서 기본 탑재된 로그 추상화 라이브러리 [공식사이트](http://www.slf4j.org/) 

SLF4J(Simple Logging Facade for Java)는 다양한 logging 프레임워크(예: java.util.logging, logback, log4j)에 대한 단순 외관 또는 추상화 역할을 하여 최종 사용자가 배포 시 원하는 logging 프레임워크를 연결할 수 있도록 한다.

log4j, logback 같은 logging 라이브러리를 직접 사용하지 않고 Slf4j으로 한 번 더 감싸서 **사용하는 이유**는 추상화 라이브러리를 통해 파편화되어 있는 수많은 로그 라이브러리를 **일관된 사용법**으로 사용할 수 있게 해 주며 동시에 로그 라이브러리의 **학습 곡선**을 줄여준다. 그리고 코드 변경 없이 구현체 의존성만 변경하면 **버전 및 다른 라이브러리로 쉽게 교체**할 수 있다.

직접 라이브러리를 사용하는 것보다 유지 보수 및 안전성 면에서 많은 이점을 가져갈 수 있다. **파사드 패턴**의 대표적인 라이브러리라고 할 수 있다.

최근에 발생한 [Log4j 보안 취약점 사태](https://namu.wiki/w/Log4j%20%EB%B3%B4%EC%95%88%20%EC%B7%A8%EC%95%BD%EC%A0%90%20%EC%82%AC%ED%83%9C?from=Log4j#Log4j) 와 같이 특정 라이브러리 특정 버전의 문제가 발생했을 때 최신버전으로 업데이트 하거나 다른 라이브러리로 비교적 손쉽게 교체할 수 있다.
