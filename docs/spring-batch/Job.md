---
layout: default
title: Job
parent: Spring Batch
nav_order: 100
last_modified_date: 2022-07-10 00:00:00
last_modified_at: 2022-07-12 00:00:00
---

# Job
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

배치 계층 구조에서 가장 상위에 있는 개념으로서 하나의 배치작업 자체를 의미

`Job Configuration` 을 통해 생성되는 객체 단위로서 배치작업을 어떻게 구성하고 실행할 것인지 전체적으로 설정하고 명세해 놓은 객체이다.
배치 `Job` 을 구성하기 위한 최상위 인터페이스이며 스프링 배치가 기본 구현체를 제공한다.
여러 `Step` 을 포함하고 있는 컨테이너로서 반드시 **한개 이상**의 `Step` 으로 구성해야 한다.

`excute()` 를 통하여 `Job` 을 실행한다.

![job.png](/meta/docs/spring-batch/job.png)

## 주요 컴포넌트

### SimpleJob

Job 인터페이스를 구현받은 대표적인 구현체다. 순차적으로 `Step` 을 실행 시키는 `Job`. 모든 `Job에서` 유용하게 사용할 수 있는 표준 기능을 갖고 있다.

### FlowJob

Job 인터페이스를 구현받은 대표적인 구현체다. 특정한 조건과 흐름에 따라 `Step` 을 구성하여 실행시키는 `Job`. `Flow` 객체를 실행시켜서 작업을 진행한다.

### JobLauncher

Job 을 실행 시키는 런처이다. 런처를 실행시키기 위해서 `Job`, JobParameters 가 필요하다.

### JobParameters

Job 을 실행하기 위하여 외부에서 주입받는 파라미터

### JobInstance

`Job` 이 실행될 때 생성되는 Job 의 논리적 실행 단위 객체로서 고유하게 식별 가능한 작업 실행을 나타낸다.

`Job` 의 설정과 구성은 동일하지만 `Job` 이 실행되는 시점에 처리하는 내용은 다르기 때문에 `Job` 의 실행을 구분해야 함. 
예를 들어 하루에 한 번 씩 배치 `Job` 이 실행된다면 매일 실행되는 각각의 `Job` 을 `JobInstance` 로 표현 한다.

#### JobInstance 생성 및 실행

- 처음 시작하는 `Job` + `JobParameter` 일 경우 새로운 `JobInstance` 생성
- 이전과 동일한 `Job` + `JobParameter` 으로 실행 할 경우 이미 존재하는 `JobInstance` 리턴
- `Job` 과는 1:M 관계

![jobInstance.png](/meta/docs/spring-batch/jobInstance.png)