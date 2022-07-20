---
title: Spring Batch + Quartz 1편
author: Bisso
date: 2022-03-21 09:46:00 +0900
categories: [Develop, Spring Batch+Quartz]
tags: [writing]
---

이번에 Spring Batch와 Quartz를 주제로 공유드리고자 합니다.

Spring Batch만 사용하였다가 Quartz의 Clustering 기능이 필요하게 되어 두개의 기술을

한 페이지에 구성하려니 스크롤 내리는게 귀찮으실 것 같아 Spring Batch를 1편에 Quartz를 2편으로 구성하였습니다.

두개의 블로그 중에 필요한 블로그를 참고해주시면 좋을 것 같습니다.

자 그럼 시작하겠습니다!!

## 목차

---

>1. Spring Batch란?
>2. Spring Batch 설명
>3. Spring Batch 적용
>4. 마치며
>5. 참고자료


## 1. Spring Batch란?

---

- 일반적으로 배치란 일괄처리(한꺼번에 처리)를 의미
- 로깅/추적, 트랜잭션 관리, job 프로세싱 통계, job 재시작, 스킵, 리소스 관리 같은 대용량 데이터 처리에 필수적인 기능을 재사용할 수 있는 형태로 제공

## 2. Spring Batch 설명

---

- Batch Architect Image

![batch-architect](/assets/img/blog_image/batch-architecture.png)

- Job : 전체 배치 프로세스를 캡슐화한 엔티티
- JobInstance : Job의 실행 단위. Job 실행 시, 하나의 JobInstance가 생성
- JobParameters : JobParameters의 객체로 JobInstance를 구별. 그리고 JobInstance에 전달되는 매개변수 역할. Type은 String, Double, Long, Date 만 지원
- JobExecution : JobInstance에 대한 실행 시도에 대한 객체. JobInstance에 대한 상태, 시작시간, 종료시간, 생성시간 등의 정보
- Step : Job의 배치처리를 정의, 순차적 단계를 캡슐화. Job은 최소 1개 이상의 Step을 가져야 함
- StepExecution : Step 실행 시도에 대한 객체
- JobLauncher : Job과 JobParameters를 사용하여 Job을 실행하는 객체

## 3. Spring Batch 적용

---

Job과 Step에 중점을 두고 적용하는 법에 대해 간단한 예제 코드로 설명드리겠습니다.

메타테이블 설정은 [https://docs.spring.io/spring-batch/docs/current/reference/html/schema-appendix.html](https://docs.spring.io/spring-batch/docs/current/reference/html/schema-appendix.html) 참고하시면 됩니다.

### a. Step 구성

Step 구성은 Tasklet으로 구성하여 등록하였습니다.

**Tasklet**이란 Step안에서 수행될 비즈니스 로직 전략의 인터페이스라고 합니다.

Tasklet으로 간단하게 execute에 로직만 구현하면 되므로 쉬운 구성이 가능합니다.

만약 대용량 데이터를 많이 다룬다하면 Reader - Processor - Writer 를 이용한 Chunk Oriented Processing을 권장합니다.

- Tasklet 구성

```java
public class TestTasklet implements Tasklet {
	
	@Override
    public RepeatStatus execute(StepContribution stepContribution, ChunkContext chunkContext) throws Exception {

        // 비지니스 로직이 들어갈 부분

        return RepeatStatus.FINISHED;
    }
}
```

- Step 구성

```java
@Bean
public Step stepOne() {
    return stepBuilderFactory.get("stepOne")
            .tasklet(new TaskTasklet())
            .build();
}
```

### b. Job 구성

구현한 Step을 실행할 Job을 등록합니다.

```java
@Bean
public Job setTestJob() throws Exception {
	return jobBuilderFactory.get("testJob")
					.incrementer(new RunIdIncrementer())
					.start(set) // 하위 실행 할 tasklet을 더 구성했다면 .next(new 새로운Tasklet())로 추가해주시면 됩니다.
					.build();
}
```

### c. 완성된 Config

현 예제는 Quartz를 추가 구성 할 때 그대로 쓸 예정입니다.

```java
@Configuration
@RequiredArgsConstructor
public class BatchConfig {

    private final JobBuilderFactory jobBuilderFactory;
    private final StepBuilderFactory stepBuilderFactory;

		// Step Configuration
    @Bean
		@JobScope
    public Step stepOne() {
        return stepBuilderFactory.get("stepOne")
								.allowStartIfComplete(true)
                .tasklet(new TaskTasklet())
                .build();
    }

		// Job Configuration 
		@Bean
		public Job setTestJob() throws Exception {
			return jobBuilderFactory.get("testJob")
							.incrementer(new RunIdIncrementer())
							.start(stepOne()) // 하위 실행 할 tasklet을 더 구성했다면 .next(new 새로운Tasklet())로 추가해주시면 됩니다.
							.build();
		}
}
```

## 4. 마치며

---

스프링 배치의 간단한 설명과 구성에 대해서 알아보았습니다.

처음 블로그를 쓰는 터라 부족함이 많지만 읽어주셔서 감사합니다.

Spring Batch를 막연하게 코딩하여 쓰기만 했지만 블로깅을 작성하면서 자세히 알아볼 수 있는 시간이 되었습니다.

더 자세히 알아보고 싶으시다면 아래 참고자료의 jojoldu님 가이드나 Spring Batch Documentation을 봐주시면 될 것 같습니다.

**다음 편에서 Quartz에 대해서 설명드리겠습니다.**

## 5. 참고자료

---

[**Spring Batch - Reference Documentation**](https://docs.spring.io/spring-batch/docs/current/reference/html/index.html)

[**마크다운 markdown 작성법**](https://gist.github.com/ihoneymon/652be052a0727ad59601)

[**jojoldu님의 Spring batch 가이드**](https://jojoldu.tistory.com/324)