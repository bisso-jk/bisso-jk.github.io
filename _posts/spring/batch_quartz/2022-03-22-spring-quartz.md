---
title: Spring Batch + Quartz 2편
author: Bisso
date: 2022-03-22 09:46:00 +0900
categories: [Develop, Spring Batch+Quartz]
tags: [writing]
---

간단한 Spring Batch 설명을 마치고 Quartz 설명을 시작하도록 하겠습니다.

## 목차

---

>1. Quartz란?
>2. Quartz 구조
>3. Quartz 설정
>4. Quartz 적용
>5. Quartz 실행
>6. 마치며
>7. 참고자료


## 1. Quartz란?

- Quartz는 [Terracotta](http://www.quartz-scheduler.org)라는 회사에 의해 개발된 Job Scheduling 라이브러리
- DB기반으로(In-memory 환경에서 안됨) 스케줄러 Clustering 기능 제공
- In-memory Job Scheduler도 제공

## 2. Quartz 구조

- Quartz Architect Image

![quartz-architect](/assets/img/blog_image/quartz-architecture.png)

- Job : Scheduling 할 실제 작업을 구현한 객체
- JobFactory : Job을 인스턴스화 시키는 클래스
- JobDetail : Job을 실행시키기 위한 정보를 담고 있는 객체. Trigger가 JobDetail을 기반으로 스케줄링
- JobDataMap : Job이 `execute`함수를 실행할 때 사용할 수 있게 원하는 정보를 담을 수 있는 객체
- JobStore : Scheduler에 등록된 Job, Trigger, 실행이력 등이 저장되는 공간. type에 따라 memory 또는 jdbc에 저장
- Trigger : Job을 실행시킬 스케줄링 조건. 한 개 이상의 Trigger는 반드시 하나의 Job을 지정할 수 있다.
  Trigger의 종류로는 SimpleTrigger(ex) `.withIntervalInSeconds(10` - 매 10초마다 실행), CronTrigger(ex) `.setCronExpression("0 */1 * * * ?")` - 매 1분마다 실행)가 존재
- Scheduler : JobDetail과 Trigger의 등록을 유지하고 관리하고 등록된 Job의 실행을 담당
- SchedulerFactory : Scheduler 인스턴스를 생성하는 매커니즘

## 3. Quartz 설정

---

application.yml 설정과 JavaConfig로 설정하는 방법에 대해 설명하도록 하겠습니다.

- 메타테이블 설명 : [https://developyo.tistory.com/252](https://developyo.tistory.com/252) 참고
- 메타테이블 SQL : [https://github.com/quartz-scheduler/quartz/tree/master/quartz-core/src/main/resources/org/quartz/impl/jdbcjobstore](https://github.com/quartz-scheduler/quartz/tree/master/quartz-core/src/main/resources/org/quartz/impl/jdbcjobstore) 참고
- application.yml : 기존의 jdbc 설정이 있다면 datasource 내용은 skip 하셔도 됩니다.

```yaml
#==============================================================================#
# Configure Quartz                                                             #
#==============================================================================#
spring:
  datasource:
    url: jdbc:mariadb://localhost:3306/quartz
    username: quartz_admin
    password: 1q2w3e4r
    hikari:
      driver-class-name: org.mariadb.jdbc.Driver
      maximum-pool-size: 50
      connection-test-query: SELECT 1
      connection-timeout: 30000
      idle-timeout: 30000
      minimum-idle: 5
  batch:
    job:
      enabled: false
  quartz:
    scheduler-name: testScheduler
    job-store-type: jdbc
    overwrite-existing-jobs: true
    jdbc:
      initialize-schema:never

org:
  quartz:
    scheduler:
      instanceId: AUTO
      instanceName: testScheduler-1.0
      makeSchedulerThreadDaemon: true
      interruptJobsOnShutdown: true
    jobStore:
      class: org.quartz.impl.jdbcjobstore.JobStoreTX
      driverDelegateClass: org.quartz.impl.jdbcjobstore.StdJDBCDelegate
      useProperties: false
      tablePrefix: QRTZ_
      isClustered: true
      clusterCheckinInterval: 20000
    threadPool:
      class: org.quartz.simpl.SimpleThreadPool
      threadCount: 10
      threadPriority: 5
      threadsInheritContextClassLoaderOfInitializingThread: true
```

- QuartzConfig.java

```java
@Slf4j
@Configuration
public class QuartzConfig {
    
  // JobRegistry 자동 등록
  @Bean
  public JobRegistryBeanPostProcessor jobRegistryBeanPostProcessor(JobRegistry jobRegistry) {
    JobRegistryBeanPostProcessor jobRegistryBeanPostProcessor = new JobRegistryBeanPostProcessor();
    jobRegistryBeanPostProcessor.setJobRegistry(jobRegistry);
    return jobRegistryBeanPostProcessor;
  }
	
  // Quartz Scheduler 설정
  @Bean
  public SchedulerFactoryBean schedulerFactoryBean(DataSource dataSource, QuartzProperties quartzProperties) throws Exception {
    SchedulerFactoryBean schedulerFactoryBean = new SchedulerFactoryBean();
    schedulerFactoryBean.setDataSource(dataSource); // jdbc 설정
    schedulerFactoryBean.setQuartzProperties(quartzProperties()); // quartz 설정 적용
    schedulerFactoryBean.setWaitForJobsToCompleteOnShutdown(true); // job 완료 시 shutdown 설정
    schedulerFactoryBean.setOverwriteExistingJobs(true);

    return schedulerFactoryBean;
  }
  
  // Properties를 application.yml에서 읽어옴
  @Bean
  public Properties quartzProperties() throws IOException {
    PropertiesFactoryBean propertiesFactoryBean = new PropertiesFactoryBean();
    propertiesFactoryBean.setLocation(new ClassPathResource("/application.yml"));
    propertiesFactoryBean.afterPropertiesSet();
    return propertiesFactoryBean.getObject();
  }

}
```

## 4. Quartz 적용

---

기존 등록했던 Spring Batc에 Quartz의 JobDetail, Trigger만 Configuration 해주면 된다

- BatchConfig.java

```java
@Configuration
@RequiredArgsConstructor
@EnableBatchProcessing // 배치 기능 활성화 -> @SpringBootApplication이 선언되어 있는 곳에 해줘도 됩니다. 테스트 코드라 작성하였습니다.
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

  @Bean
  @JobScope
  public Step stepTwo() {
    return stepBuilderFactory.get("stepTwo")
            .tasklet(new TaskTwo())
            .allowStartIfComplete(true)
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

  // JobDetail Configuration
  @Bean(name = "quartzJobTest")
  public JobDetailFactoryBean setQuartzJobTest() {
    JobDetailFactoryBean jobDetailFactoryBean = new JobDetailFactoryBean();
    Map<String, Object> map = new HashMap<>();
    map.put("job", demoJobOne().getName());

    jobDetailFactoryBean.setDurability(true); // 지속성
    jobDetailFactoryBean.setJobClass(CustomQuartzJob.class);
    jobDetailFactoryBean.setRequestsRecovery(true); // 리커버리
    jobDetailFactoryBean.setJobDataAsMap(map);

    return jobDetailFactoryBean;
  }

  // Trigger Configuration
  @Bean
  public CronTriggerFactoryBean setQuartzTriggerTest() {
    CronTriggerFactoryBean cronTriggerFactoryBean = new CronTriggerFactoryBean();

    cronTriggerFactoryBean.setName("quartzTriggerTest");
    cronTriggerFactoryBean.setJobDetail(setQuartzJobTest().getObject()); 
    cronTriggerFactoryBean.setCronExpression("0 */1 * * * ?"); // cron expression settings

    return cronTriggerFactoryBean;
  }
}
```

- CustomQuartzJob.java

```java
@Slf4j
@PersistJobDataAfterExecution // Job 로직에서 JobDataMap 데이터를 수정하면 실행이후에 DB에 저장이 안됩니다. 해당 어노테이션 사용 시, JobDataMap을 수정한 데이터를 다음 실행 때에도 반영된 데이터를 읽을 수 있습니다.
@DisallowConcurrentExecution // Job 동시실행 방지 어노테이션
public class CustomQuartzJob implements Job {

  @Autowired
  private JobLauncher jobLauncher;

  @Autowired
  private JobLocator jobLocator;

  @Override
  public void execute(JobExecutionContext context) throws JobExecutionException {
    try {
      JobDataMap jobDataMap = context.getMergedJobDataMap();

      String jobName = jobDataMap.get("job").toString();

      log.info("jobDataMap : {}", jobDataMap);

      log.info("start");

      jobLauncher.run(jobLocator.getJob(jobName), new JobParameters());

      log.info("end");
    } catch (Exception e) {
      log.error("batch error");
    }
  }
}
```

## 5. Quartz 실행

- 어플리케이션 실행 화면 : 쿼츠가 동작한 후 Job이 정상적으로 실행되었습니다.

![quartz_execute](/assets/img/blog_image/quartz_execute.png)

- 어플리케이션 실행 후, QRTZ_SCHEDULER_STATE(Scheduler), QRTZ_JOB_DETAILS(JobDetail), QRTZ_TRIGGERS(Trigger), QRTZ_CRON_TRIGGER(Trigger Cron) 메타테이블에 등록되었고, 해당 Job 실행 후 Spring Batch 메타 테이블에도 실행이력이 저장됩니다.

![quartz_scheduler_state](/assets/img/blog_image/qrtz_scheduler_state.png)

- 클러스터링 테스트 : 동일 프로젝트 복제 후, 다른 포트로 실행 → QRTZ_SCHEDULER_STATE 테이블의 SCHED_NAME과 INSTANCE_NAME으로 동일 스케줄러의 다른 인스턴스를 구별
    - 실행화면

![clustering_test](/assets/img/blog_image/clustering_test.png)

- QRTZ_SCHEULER_STATE Table : 스케줄러가 다른 InstanceName으로 구별되어 있음.
  만약, 스케줄러 하나가 종료되면 LAST_CHECKIN_TIME과 CHECKIN_INTERVAL로 비교해서 스케줄러가 종료 됐는지 확인 후 업데이트
    - 두개 같은 스케줄러 동작

![clustering_test_table](/assets/img/blog_image/clustering_test_table.png)

- InstanceName (...)1647055337368 스케줄러 종료 시, interval 지난 후 자동으로 업데이트되고 다른 인스턴스에서 정상적으로 Job이 실행되는 것을 볼 수 있다.(failover 기능)

![scheduler_failover](/assets/img/blog_image/scheduler_failover.png)

![clustering_state_alone](/assets/img/blog_image/clustering_state_alone.png)

- 클러스터링 시, CustomQuartzJob.java의 @DisallowConcurrentExecution 어노테이션에 의해서 서로의 Job이 동시에 실행되지 않고 랜덤하게 분산되어 실행된다.

![last_job](/assets/img/blog_image/last_job.png)


## 6. 마치며

---

Spring Batch + Quartz 를 적용하면서, 많은 블로그와 공식 문서를 보면서 작성하였는데 제 생각에 ‘이렇게 하면 될 것 같다’ 작성한 것이기 때문에 잘못된 정보가 있을 수 있으므로 피드백 부탁드립니다 ^^

회사 소스 코드를 인용할 수 없어 테스트 코드를 이용하여 너무 간단한 설명이 된 것 같네요

아무튼 한번 구성해보시면 좋을 것 같습니다.

블로그 봐주셔서 감사합니다.

## 7. 참고자료

---

[Quartz - Documentation](http://www.quartz-scheduler.org)

[advenoh blog](https://blog.advenoh.pe.kr/spring/Quartz-Job-Scheduler%EB%9E%80/)

[https://kouzie.github.io/spring/Spring-Boot-스프링-부트-Quartz/#](https://kouzie.github.io/spring/Spring-Boot-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-Quartz/#)

[https://examples.javacodegeeks.com/enterprise-java/spring/batch/quartz-spring-batch-example/](https://examples.javacodegeeks.com/enterprise-java/spring/batch/quartz-spring-batch-example/)