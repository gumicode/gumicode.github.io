---
layout: default
title: 스프링 배치 Spring batch
parent: 스프링 프레임워크 Spring Framework
nav_order: 500
last_modified_date: 2022-06-28 01:00:00
last_modified_at: 2022-06-28 01:00:00
---

# 스프링 배치 Spring batch
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


## 도메인 이해

### Job

기본 개념

- 배치 계층 구조에서 가장 상위에 있는 개념으로서 하나의 배치작업 자체를 의미함
    - "API 서버의 접속 로그 데이터를 통계 서버로 옮기는 배치" 인 Job 자체를 의미한다.
- Job Configuration 을 통해 생성되는 객체 단위로서 배치작업을 어떻게 구성하고 실행할 것인지 전체적으로 설정하고 명세해 놓은 객체
- 배치 Job 을 구성하기 위한 최상위 인터페이스이며 스프링 배치가 기본 구현체를 제공한다.
- 여러 Step 을 포함하고 있는 컨테이너로서 반드시 한개 이상의 Step 으로 구성해야 함

기본 구현체
- SimpleJob
    - 순차적으로 Step 을 실행 시키는 Job
    - 모든 Job에서 유용하게 사용할 수 있는 표준 기능을 갖고 있음 
- FlowJob
    - 특정한 조건과 흐름에 따라 Step 을 구성하여 실행시키는 Job
    - Flow 객체를 실행시켜서 작업을 진행함 

### JobInstance

기본 개념

- Job 이 실행될 때 생성되는 Job 의 논리적 실행 단위 객체로서 고유하게 식별 가능한 작업 실행을 나타냄
- Job 의 설정과 구성은 동일하지만 Job 이 실행되는 시점에 처리하는 내용은 다르기 때문에 Job 의 실행을 구분해야 함
    - 예를 들어 하루에 한 번 씩 배치 Job이 실행된다면 매일 실행되는 각각의 Job 을 JobInstance 로 표현 한다.
- JobInstance 생성 및 실행
    - 처음 시작하는 Job + JobParameter 일 경우 새로운 JobInstance 생성
    - 이전과 동일한 Job + JobParameter 으로 실행 할 경우 이미 존재하는 JobInstance 리턴
        - 내부적으로 JobName + jobKey (jobParameters 의 해시값) 을 가지고 JobInstance 객체를 얻음
- Job 과는 1:N 관계

BATCH_JOB_INSTANCE 테이블과 매핑

- JOB_NAME (Job) 과 JOB_KEY (JobParameter 해시값) 가 동일한 데이터는 중복해서 저장할 수 없음