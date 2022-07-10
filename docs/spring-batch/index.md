---
layout: default
title: Spring Batch
has_children: true
nav_order: 103000
last_modified_date: 2022-06-28 01:00:00
last_modified_at: 2022-06-28 01:00:00
---

# Spring Batch
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

[공식문서](https://spring.io/projects/spring-batch)

엔터프라이즈 시스템의 일상적인 운영에 필수적인 강력한 배치 애플리케이션을 개발할 수 있도록 설계된 가볍고 포괄적인 배치 프레임워크

Spring Batch는 로깅/추적, 트랜잭션 관리, 작업 처리 통계, 작업 재시작, 건너뛰기, 리소스 관리 등 대용량 레코드 처리에 필수적인 재사용 가능한 기능을 제공하며, 최적화 및 파티셔닝 기술을 통해 대용량 및 고성능 일괄 작업을 가능하게 하는 고급 기술 서비스 및 기능을 제공한다. 단순하고 복잡한 대용량 일괄 작업은 확장성이 뛰어난 방식으로 프레임워크를 활용하여 상당한 양의 정보를 처리할 수 있다.

## 탄생 배경

배치 처리에서 요구하는 재사용 가능한 자바 기반 패치 아키텍처 표준의 필요성이 대두되어 Pivotal 과 Accenture 의 합작품으로 만들어지게 되었다.

## 특징

- Transaction management
- Chunk based processing
- Declarative I/O
- Start/Stop/Restart
- Retry/Skip
- Web based administration interface (Spring Cloud Data Flow)

## 초기화 설정 클래스

### `BatchAutoConfiguration`

- 스프링 배치가 초기화 될 때 자동으로 실행되는 설정 클래스
- `Job` 을 수행하는 `JobLauncherApplicationRunner` Bean 을 생성

### `SimpleBatchConfiguration`

- `JobBuilderFactory` 와 `StepBuilderFactory` 생성
- 스프링 배치의 주요 구성 요소 생성 - 프록시 객체로 생성됨

### `BatchConfigurerConfiguration`

- `BasicBatchConfigurer`
    - `SimpleBatchConfiguration` 에서 생성한 프록시 객체의 실제 대상 객체를 생성하는 설정 클래스
    - 빈으로 의존성 주입 받아서 주요 객체들을 참조해서 사용할 수 있따.
- `JpaBatchConfigurer`
    - JPA 관련 객체를 생성하는 설정 클래스