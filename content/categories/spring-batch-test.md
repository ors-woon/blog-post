---
title: "Spring Batch Test"
date: 2024-01-11T21:54:42+09:00
tags: []
draft: true
author: "ors"
description: "spring-batch-test"
---

현재 진행 중인 프로젝트에서 springBatch 세팅을 진행했었다.
SpringBatch + Test 를 구성하면서 겪었던 이슈들을 정리해보고자 한다.

### `batch_job_instance' doesn't exist`

Spring Batch 는 Job 수행 중, 사용되는 meta data(Job / Step)를 DB에 저장하고 있다.

> 테이블 구조는 [문서](https://docs.spring.io/spring-batch/reference/schema-appendix.html#metaDataSchema) 참조

아무런 설정을 하지 않고, Table 을 구성하지 않는다면, `batch_job_instance' doesn't exist` error 를 겪게된다.

가장 좋은 해결 방법은 관련된 meta table 을 dev / real DB 에 추가하는 것이다.  
만약, 위 테이블을 Real DB에 추가하기가 싫다면, 아래처럼 in-memory(Map) repository 를 쓰도록 수정 할 수 있다.

```
class BatchConfig : DefaultBatchConfigurer() {
  @Override
  fun setDataSource(dataSource: DataSource)
  {
    // mmeta shem => in-memory repository를 사용합니다.
    // Map based JobRepository => MapJobRepositoryFactoryBean 에 저장
    // Job 이 완료되면 프로세스를 종료하기때문에, 결국은 저장하지 않는다는 의미
  }
}
```

현재 담당하고 있는 서비스의 배치 DB 에 데이터를 적재하면서까지 job 상태 추적 혹은 디버깅이 필요하지 않아는, 위처럼 in-memory repository 를 사용하도록 설정했다.  
임
`주의` springBatch 5.0 에 위 방식이 deprecated 되어 datasource 를 주입해줘야한다.

```
Reason: Failed to determine a suitable driver class


Action:

Consider the following:
If you want an embedded database (H2, HSQL or Derby), please put it on the classpath.
If you have database settings to be loaded from a particular profile you may need to activate it (no profiles are currently active).
```

batch 의 사용이 잦아지고, 중요한 서비스 코드가 배치로 수행될 경우 Table 을 추가하도록 논의


### test 시, bean 이 생성되면서 모든 job 들이 한번씩 수행됨

- Job A를 실행시키려했으나, 다른 Job(B~C)이 bean 으로 생성되면서 의도치 않은 Job 들이 동시에 실행됨
- Spring Bean 으로 등록하면서 Job 들이 멋대로 실행되고 있음
- 이전 프로젝트에서 이런 이슈가 있어서 test phase + Job 을 scan 하지 않음.
  - 문득 이번 프로젝트를 세팅하면서 생각해보니, Job 을 Bean 으로 만든거까진 이해하는데 왜 실행하지 ??
  - 찾아봅시다. (찾음) 


JobLauncherCommandLineRunner 를 통해 Spring Batch Job 들이 실행되는데, 이는 spring.batch.job.enabled=true일 때 자동으로 생성된다.
기본 Runner(JobLauncherCommandLineRunner)는 spring.batch.job.names 에 쉼표로 구분되어 지정된 Job들을 실행하며, spring.batch.job.names가 존재하지 않으면 모든 Job을 실행함.

```
Auto-configuration for Spring Batch. By default a Runner will be created and all jobs in the context will be executed on startup.
Disable this behavior with spring.batch.job.enabled=false
```

문제를 해결하기 위해 job.enabled 를 false 로 설정한다. (이전 프로젝트 코드도 수정하도록 하자..)

> Test 시에만 false. main 코드에 false 를 하면 job 실행이 안됨

다른 해결 방법으로 CommandLineRunner 를 직접 상속하여 재정의 할 수 있음.

batch Applcation 로직에 jobName 이 안들어 올 경우 exception 을 뱉도록 작성하고, 추후 위 방식이 문제가 되면 재정의하는 걸 검토해본다.

### test 시, springBatchTest annotation 을 사용하면, Job Bean 충돌이 남

`@SpringBatchTest` annotation 설정 시, JobLauncherTestUtils 을 bean 으로 만드는 과정에서 error 가 나옴.
JobLauncherTestUtils 가 아래처럼 job 을 bean 으로 받고 있고, Job 이 2개 이상일 경우 어떤 Job 을 넣을지 몰라서 에러가 남

> ???: N개 중에 어떤 Job 을 말하는거야 ?

```java
@Autowired
public void setJob(Job job) {
  this.job = job;
}
```
SpringBatchTest 을 선언하지 않고 JobLauncherTestUtils 을 직접 초기화 하도록 수정

```kotlin
/**
* @SpringBatchTest 를 사용하여  JobLauncherTestUtils 를 초기화하면,
* Bean 으로 등록된 Job 이 2개 일경우, Exception 발생함.
* SpringBatchTest 를 사용하지 않고 직접 초기화하는 방식으로 설정한다.
*/
fun initJobLauncher(job: Job) {
  jobLauncherTestUtils = JobLauncherTestUtils()
  jobLauncherTestUtils.jobLauncher = jobLauncher
  jobLauncherTestUtils.jobRepository = jobRepository
  jobLauncherTestUtils.job = job
}
```

> job 수행 시, 위 함수를 호출하여 util 에 job 을 넣도록 한다

다른 글들을 보니, `@SpringBootTest(Job::class, TestBatchConfig::class)` 같은 형태로 세팅을 하던데, 결국 TestBatchConfig 쪽에 beanScan 이나 MainApplication 에 입력했던 annotation 을 중복으로 입력해야해서 채택하진 않음.

이하 config 파일 예시

```kotlin
@Configuration
@EnableAutoConfiguration
@EnableBatchProcessing
@EntityScan("com.domain")
@EnableTransactionManagement
class TestBatchConfig


@SpringBatchTest
@SpringBootTest(classes = [TestBatchConfig::class, PrintJobConfig::class])
class UserCreateJobTest {
}
```

위 처럼 선언 시, Bean 이 하나만 만들어지기때문에 Bean Conflict 은 나지 않는다.

> 상황에 맞게 사용하면 될거 같다.


