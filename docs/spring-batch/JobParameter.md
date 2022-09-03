---
layout: default
title: JobParameter
parent: Spring Batch
nav_order: 102
last_modified_date: 2022-09-04 00:00:00
last_modified_at: 2022-09-04 00:00:00
---

# Job
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

Job 을 실행할 때 함께 포함되어 사용되는 파라미터 객체

- 하나의 Job 에 존재할 수 있는 여러개의 JobInstance 를 구분할 때 사용
- JobParameter 와 JobInstance 는 1:1 관계

## 지원하는 데이터 타입

JobParameter 는 아래와 같이 총 4개의 타입을 지원한다.

|타입|내용|
|------|---|
|String|일반적인 문자열을 쓰고 싶을 때 사용한다.|
|Date|날짜 타입을 사용할때 사용한다. LocalDate 는 사용할 수 없다.|
|Long|숫자값을 사용할 때 사용한다.|
|Double|소수점이 포함된 숫자를 사용할 때 사용한다.|

## JobParameter 를 주입하는 방법

- 어플리케이션 실행시 외부에서 주입
- 객체를 직접 선언하여 주입

### 어플리케이션 실행시 외부에서 주입

어플리케이션을 실행 할 때 외부에서 값을 주입하여 JOB 을 실행하는 방법이다.

배치의 경우 일반적으로 스프링 WEB 의존성을 사용하지 않기에 배치 동작에 필요한 파라미터를 어플리케이션 실행시 주입하는 방법을 주로 사용한다.

```bash
java -jar batch.jar --spring.batch.job.names=JOB_NAME requestDate=20210101
```

### 객체를 직접 선언하여 주입

실시간으로 어플리케이션이 동작중일때 주입 하는 방법이다.

스프링 WEB 의존성이 떠 있는 상태에서 스케줄링 기능을 통하여 배치를 실행할 경우 주로 사용한다.
이때는 위와 같은 방법으로는 파라미터를 주입할 수 없으므로 아래처럼 직접 객체를 선언하여 JOB 을 실행한다. 

```java
Job job = new SimpleJob("JOB_NAME");
JobParameters jobParameters = jobParametersBuilder
        .addString("key1", "value")
        .addDate("key2", new Date())
        .addLong("key3", 10L)
        .addDouble("key4", 10.0)
        .toJobParameters();
jobLauncher.run(job, jobParameters);
```

## JobParameter 를 가져오는 방법

- SpEL
- StepContribution
- ChunkContext

### SpEL

```java
@Getter
@Setter
@JobScope
@Component
public class SampleJobParameter {
    @Value("#{jobParameters[platform]}") 
    private final String platform; 
}
```

```java
@Bean
@JobScope
public Step testlet(@Value("#{jobParameters[platform]}") final String platform) {
    return stepBuilderFactory
            .get("testlet")
            .tasklet(
                    (stepContribution, chunkContext) -> {
                        return RepeatStatus.FINISHED;
                    })
            .build();
}
```

- 메소드와 필드 에서 사용할 수 있다. 
- 단 주의할점이 `@JobScope` , `@StepScope` 가 선언된 곳에서만 호출할 수 있다.

### StepContribution

정확하게 말하면, JobParameter 는 JobExecution 에 존재한다. 
그렇지만 StepContribution 은 StepExcution 을 가져올 수 있고 StepExcution 는 JobExcution 을 가져올 수 있으므로 해당 방법을 통해 가져올 수 있다. 


```java
@Bean
public Step testlet() {
    return stepBuilderFactory
            .get("testlet")
            .tasklet(
                    (stepContribution, chunkContext) -> {

                        JobParameters jobParameters1 = stepContribution.getStepExecution().getJobExecution().getJobParameters();
                        String key1 = jobParameters1.getString("key1");
                        Date key2 = jobParameters1.getDate("key2");
                        Long key3 = jobParameters1.getLong("key3");
                        Double key4 = jobParameters1.getDouble("key4");

                        return RepeatStatus.FINISHED;
                    })
            .build();
}
```


### ChunkContext

정확하게 말하면, StepContext 에서 가져온다. 
그렇지만 chunkContext 은 StepContext 을 가져올 수 있고 StepContext 는 JobParameter 을 가져올 수 있으므로 해당 방법을 통해 가져올 수 있다. 대신 이때는 Map 형태로 가져 올 수 있다. 

```java
@Bean
public Step testlet() {
    return stepBuilderFactory
            .get("testlet")
            .tasklet(
                    (stepContribution, chunkContext) -> {

                        Map<String, Object> jobParameters2 = chunkContext.getStepContext().getJobParameters();
                        Object key11 = jobParameters2.get("key1");
                        Object key21 = jobParameters2.get("key2");
                        Object key31 = jobParameters2.get("key3");
                        Object key41 = jobParameters2.get("key4");

                        return RepeatStatus.FINISHED;
                    })
            .build();
}
```