# 섹션 3 스프링 배치 도메인 이해

## Job
### 기본 개념
- 배치 계층 구조에서 가장 상위에 있는 개념으로서 **하나의 배치작업 자체**를 의미함
  - "API 서버의 접속 로그 데이터를 통계 서버로 옮기는 배치" 인 Job 자체를 의미한다.
- Job Configuration 을 통해 생성되는 **객체 단위**로서 배치작업을 **어떻게 구성**하고 실행할 것인지 전체적으로 설정하고 **명세**해 놓는 객체
- 배치 Job 을 구성하기 위한 최상위 인터페이스이며 스프링 배치가 기본 구현체를 제공한다
- 여러 Step 을 포함하고 있는 컨테이너로서 **반드시 한개 이상**의 Step으로 구성해야함

### 2. 기본 구현체
- SimpleJob
  - **순차적**으로 Step 을 실행시키는 Job
  - 모든 Job에서 유용하게 사용할 수 있는 표준 기능을 갖고 있음
- FlowJob
  - **특정한 조건**과 **흐름**에 따라 Step 을 구성하여 실행시키는 Job
  - Flow 객체를 실행시켜서 작업을 진행함

![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_01.png?raw=true)



## JobInstance

### 1. 기본 개념
- Job이 실행될 때 생성되는 Job 의 논리적 실행 단위 객체로서 고유하게 식별 가능한 작업 실행을 나타냄
- Job의 설정과 구성은 동일하지만 Job 이 실행되는 시점에 처리하는 내용은 다르게 때문에 Job의 실행을 구분해야함
  - 예를 들어 하루에 한뻔 씩 배치 **Job이 실행된다면 매일 실행되는 각각의 Job 을 JobInstance** 로 표현한다.

- JobInstance 생성 및 실행
  - 처음 시작하는 Job + JobParameter(두개의 인자) 일 경우 **새로운 JobInstance 생성**
  - 이전과 동일한 Job + JobParameter 으로 실행 할 경우 **이미 존재하는 JobInstance 리턴**
    - 내분적으로 JobNmae + jobKey (JobParamters 의 해시값) 를 가지고 JobInstance 객체를 얻음
- Job 과는 1:M 관계

### 2. BATCH_JOB_INSTANCE 테이블과 매핑
- JOB_NAME (Job)과 JOB_KEY (JobParameter 해시값) 동일한 데이터는 중복해서 저장할 수 없음 

![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_02.png?raw=true)

> 참고: Job & JobParamters 두개의 값을 가지고 db로 부터 확인하는 과정을 거친다.  
> 존재하는 값이면 기존 값 job 인스턴스를 return 하고, 만약에 존재하지 않으면 새롭게 job 인스턴스를 만든다.
> 그리고 만약에 기존 job 인스턴스의 값을 가지고 있으면 이후에는 job 인스턴스가 실행되지 않고 예외를 발생시킨다.
> 한 번 수행되어있기 때문에 동일한 내용으로는 수행이 될 수 없다는 의미로 배치 Job이 중단하게 된다.

![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_03.png?raw=true)

### 예제
#### JobInstanceConfiguration 
``` java
@Configuration
@RequiredArgsConstructor
public class JobInstanceConfiguration {

    private final JobBuilderFactory jobBuilderFactory;
    private final StepBuilderFactory stepBuilderFactory;

    @Bean
    public Job job() {
        return jobBuilderFactory.get("job")
                .start(step1())
                .next(step2())
                .build();
    }

    @Bean
    public Step step1() {
        return stepBuilderFactory.get("step1")
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
                        return RepeatStatus.FINISHED;
                    }
                })
                .build();
    }

    @Bean
    public Step step2() {
        return stepBuilderFactory.get("step1")
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
                        return RepeatStatus.FINISHED;
                    }
                })
                .build();
    }

}
``` 

#### JobRunner
``` java
@Component
public class JobRunner implements ApplicationRunner {

    @Autowired
    private JobLauncher jobLauncher;

    @Autowired
    private Job job;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        JobParameters jobParameters = new JobParametersBuilder()
                .addString("name", "user1")
                .toJobParameters();

        jobLauncher.run(job, jobParameters);
    }
}
``` 
#### BATCH_JOB_EXECUTION_PARAMS 테이블 데이터 생성 
- 같은 job, jobParameters 실행시 에러 발생
![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_04.png?raw=true)

## jobParameter
### 1. 기본 개념
- Job을 실행할 때 함께 포함되어 사용되는 파라미터를 가진 도메인 객체
- 하나의 Job에 존자할 수 있는 여러개의 **JobInstance를 구분하기 위한 용도**
- JobParameters와 JobInstance는 1:1 관계

### 2. 생성 및 바인딩
- 어플리케이션 실행 시 주입
  - Java -jar LogBatch.jar requestData=20210101
- 코드로 생성(2가지)
  - JobParamterBuilder, DefaultJobParamtersConverter
    - 주로 JobParamterBuilder를 많이 사용함!
- SpEL 이용(Spring에서 제공하는 표현식 언어)
  - @Value("#{jobParamter[requestDate]}"), @JobScope, @StepScope 선언 필수

### 3. BATCH_JOB_EXECUTION_PARAM 테이블과 매핑
  - JOB_EXECUTION 과 1:M의 관계


![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_05.png?raw=true)

### BATCH_JOB_EXECUTION_PARAMS
![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_06.png?raw=true)

### 실습(코드로 생성)
``` java
@Component
public class JobParameterTest implements ApplicationRunner {

    @Autowired
    private JobLauncher jobLauncher;

    @Autowired
    private Job job;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        JobParameters jobParameters = new JobParametersBuilder()
                .addString("name", "user1")
                .addLong("seq", 2L)
                .addDate("date", new Date())
                .addDouble("age", 16.5)
                .toJobParameters();

        jobLauncher.run(job, jobParameters);
    }
}
``` 



#### 예제(어플리케이션 실행 시 주입)
- build 후 jar 파일 생성후 명령어 실행
``` java
java -jar spring-batch-0.0.1-SNAPSHOT.jar name=user1 seq=2L data=2021-01-01 age=16.5
``` 

## JobExecution


### 1. 기본 개념
- JobInstance에 대한 한 번의 시도를 의미하는 객체로서 Job 실행 중에 발생한 정보들을 저장하고 있는 객체
  - 시작시간, 종료시간, 상태(시작됨,완료,실패) 종료상태의 속성을 가짐
- JobInstance 과의 관계
  - JobExecution은 'FAILED' 또는 'COMPLETED' 등의 Job의 실행 결과 상태를 가지고 있음
  - JobExecution 의 실행 상태 결과가 'COMPLETED' 면 JobInstance 실행이 완료된 것으로 간주해서 재 실행이 불가함
  - JobExecution 의 실행 상태 결과가 'FAILED' 면 JobInstance 실행이 완료되지 않은 것으로 간주해서 재실행이 가능함
    - JobParameter 가 동일한 값으로 Job 을 실행할지라도 JobInstance 를 계속 실행할 수 있음
  - JobExection 의 실행 상태 결과가 'COMPLETED'될 떄까지 하나의 JobInstance 내에서 여러 번의 시도가 생길 수 있음
### 2. BATCH_JOB_EXECUTION 테이블과 매핑
- JobInstance 와 JobExecution 는 1:M 의 관계로서 JobInstance에 대한 성공/실패의 내역을 가지고 있음

![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_07.png?raw=true)  


![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_08.png?raw=true)


![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_09.png?raw=true)  

## Step
- Step
- StepExecution
- StepContribution

### 1. 기본 개념
- Batch Job을 구성하는 독립적인 하나의 단계로서 실제 배치 처리를 정의하고 컨트롤하는 데 필요한 모든 정보를 가지고 있는 도메인 객체
- 단순한 단일 태스크 뿐 아니라 입력과 처리 그리고 출력과 관련된 복잡한 비즈니스 로직을 포함하는 모든 설정들을 담고 있다.
- 배치작업을 어떻게 구성하고 실행할 것인지 Job 의 세부 작업을 Task 기반으로 설정하고 명세해 놓는 객체
- 모든 Job은 하나 이상의 Step으로 구성됨

### 2. 기본 구현체
- TaskletStep
  - 가장 기본이 되는 클래스로서 Tasklet 타입의 구현체들을 제어한다.

- Partition Step
  - 멀티 스레드 방식으로 Step 을 여러 개로 분리해서 실행한다.

- JobStep
  - Step 내에서 Job 을 실행하도록 한다
- FlowStep
  - Step 내에서 Flow 를 실행하도록 한다


![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_10.png?raw=true)  

### 실습
#### CustomTasklet
``` java
public class CustomTasklet implements Tasklet {
    @Override
    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
        System.out.println("step1 has execution");

        return RepeatStatus.FINISHED;
    }
}
``` 

#### CustomTasklet
``` java
    ``` 

    @Bean
    public Job job() {
        return jobBuilderFactory.get("job")
                .start(step1())
                .next(step2())
                .build();
    }

    @Bean
    public Step step1() {
        return stepBuilderFactory.get("step1")
                .tasklet(new CustomTasklet())
                .build();
    }
    
    ``` 
``` 

## StepExecution
### 1. 기본 개념 
- Step 에 대한 한번의 시도를 의미하는 개체로서 **Step 실행 중에 발상한 정보들을 저장하고 있는 객체**
  - 시작시간, 종료시간, 상태(시작됨, 완료, 실패), commit count, rollback count 등의 속성을 가짐
- Step이 매번 시도될 때마다 생성되며 각 Step 별로 생성 된다.
- Job 이 재시작 하더라도 이미 성공적으로 완료된 Step 은 재 실행되지 않고 실패한 Step 만 실행된다.
> 참고: job - StepExecution - step1, step2 ,step3 중 step3이 실패하면 job 재실행할수 있다   
> 성공한 step1~2는 skip 해버리고 실패한 Step3만 실해 된다.
> 그리고 성공했어도 job 재시작 시 모든 스텝들을 다시 실행할 수 있는 옵션이 있음
- 이전 단게 Step이 실패해서 현재 Step을 실행하지 않았다면 StepExecution을 생성하지 않는다. Step이 실제로 시작됐을 때만 StepExecution을 생성한다
 
- JobExecution 과의 관계
  - Step의 StepExecution 이 **모두 정상적으로 완료** 되어야 JobExecution이 정상적으로 완료된다.
  - Step의 StepExecution 중 **하나라도 실패**하면 JobExecution 은 실패한다

### 2. BATCH_STEP_EXECUTION 테이블과 매핑
- JobExecution 와 StepExecution 는 1:M 의 관계
- 하나의 Job 에 여러 개의 Step 으로 구성했을 경우 각 StepExecution 은 하나의 JobExecution 을 부모로 가진다.

> 참고: 동일한 잡 파라미터의 값을 전달하더라도 실패한 잡은 재시작이 가능하다.

![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_11.png?raw=true)  


![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_12.png?raw=true)


![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_13.png?raw=true)  

## StepContribution

### 1. 기본 개념
- 청크 프로세스의 변경 사항을 버퍼링 한 후 StepExecution 상태를 업데이트하는 도메인 객체
- 청크 커밋 직전에 StepExecution 의 apply 메소드를 호출하여 상태를 업데이트 함
- ExitStatus 의 기본 종료코드 외 사용자 정의 종료코드를 생성해서 적용 할 수 있음

### 2. 구조
![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_14.png?raw=true)


![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_15.png?raw=true) 

## ExecutionContext
### 1. 기본 개념
- 프레임워크에서 유지 및 관리하는 키/값으로 된 컬렉션으로 SepExecution 또는 JobExecuion 객체의 상태(state)를 저장하는 공유 객체
- DB 에 직렬화 한 값으로 저장됨 - {"key": "value"}
- 공유 범위
  - Step 범위 - 각 Step 의 StepExecution 에 저장되며 Step 간 서로 공유 안됨
  - Job 범위 - 각 Job위 JobExecution 에 저장되며 Job 간 서로 공유 안되며 해당 Job의 Step 간 서로 공유됨
- Job 재 시작시 이미 처리한 Row 데이터는 건너뛰고 이후로 수행하도록 할 때 상태 정보를 활용한다

### 2. 구조
![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_16.png?raw=true)



![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_17.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_18.png?raw=true)

### 실습
### ExecutionContextTasklet1
- 최초 실행되는 Tasklet으로 JobExecution 및 StepExecution 내에 존재하는 ExecutionContext에 key가 jobName, stepName인 데이터를 조회하고 없으면 데이터를 저장함
- 참고로 execute 메서드의 contribution, context 어떠한 Execution를 참조하든 상관이 없습니다.
``` 
@Component
public class ExecutionContextTasklet1 implements Tasklet {

    @Override
    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
        System.out.println("step1 was execution");

        ExecutionContext jobExecutionContext = contribution.getStepExecution().getJobExecution().getExecutionContext();
        ExecutionContext stepExecutionContext = contribution.getStepExecution().getExecutionContext();

        String jobName = chunkContext.getStepContext().getStepExecution().getJobExecution().getJobInstance().getJobName();
        String stepName = chunkContext.getStepContext().getStepExecution().getStepName();

        if (jobExecutionContext.get("jobName") == null) {
            jobExecutionContext.put("jobName", jobName);
        }

        if (stepExecutionContext.get("stepName") == null) {
            stepExecutionContext.put("stepName", stepName);
        }

        System.out.println("jobName = " + jobExecutionContext.get("jobName"));
        System.out.println("stepName = " + stepExecutionContext.get("stepName"));


        return RepeatStatus.FINISHED;
    }
}
``` 

#### ExecutionContextTasklet2
- ExecutionContext가 Job, Step 간 데이터 공유가 가능한지 파악하기 위해 Tasklet1에서 저장한 데이터를 조회하여 확인한다.

## JobRepository
### 1. 기본 개념
- 배치 작업 중의 정보를 저장하는 저장소 역할
- Job이 언제 수행되었고, 언제 끝났으며, 몇 번이 실행되었고 실행에 대한 결과 등의 배치 작업의 수행과 관련된 모든 meta data 를 저장함
  - JobLauncher, job, step 구현체 내부에서 CRUD 기능을 처리함

![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_19.png?raw=true)

- @EnableBatchProcessing 어노테이션만 선언하면 JobRepository 가 자동으로 빈으로 생성됨
- BatchConfigurer 인터페이스를 구현하거나 BasicBatchConfigurer를 상속해서 JobRepository 설정을 커스터마이징 할 수 있다
  - JDBC 방식으로 설정- JobRepositoryFactoryBean
    - 내부적으로 AOP 기술을 통해 트랜잭션 처리를 해주고 있음
    - 트랜잭션 isolation의 기본값은 SERIALIZEBLE로 최고 수준, 다른 레벨(READ_committed, REPEATABLE_READ)로 지정 가능
    - 메타테이블의 Table Prefix를 변경할 수 있음, 기본 값은 “BATCH_” 임

- In Memory 방식으로 설정 – MapJobRepositoryFactoryBean
  - 성능 등의 이유로 도메인 오브젝트를 굳이 데이터베이스에 저장하고 싶지 않을 경우
  - 보통 Test 나 프로토타입의 빠른 개발이 필요할 때 사용

```
import org.springframework.batch.core.*;
import org.springframework.batch.core.repository.JobRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class JobRepositoryListener implements JobExecutionListener {

    @Autowired
    private JobRepository jobRepository;

    @Override
    public void beforeJob(JobExecution jobExecution) {

    }

    @Override
    public void afterJob(JobExecution jobExecution) {
        String jobName = jobExecution.getJobInstance().getJobName();

        JobParameters jobParameters = new JobParametersBuilder()
                .addString("requestDate", "20210102").toJobParameters();

        JobExecution lastJobExecution = jobRepository.getLastJobExecution(jobName, jobParameters);
        if (lastJobExecution != null) {
            for (StepExecution execution : lastJobExecution.getStepExecutions()) {
                BatchStatus status = execution.getStatus();
                System.out.println("status = " + status);

                ExitStatus exitStatus = execution.getExitStatus();
                System.out.println("exitStatus = " + exitStatus);

                String stepName = execution.getStepName();
                System.out.println("stepName = " + stepName);
            }
        }
    }
}

``` 
## JobLauncher

### 1. 기본 개념
- 배치 Job을 실행시키는 역할을 한다. 
- Job과 Job Parameters를 인자로 받으며 요청된 배치 작업을 수행한 후 최종 client 에게 JobExecution을 반환함
- 스프링 부트 배치가 구동이 되면 JobLauncher 빈이 자동 생성 된다.
- Job 실행
  - JobLanicher.run(Job, JobParameters)
  - 스프링 부트 배치에서는 JobLauncherApliactionRunner 가 자동적으로 JobLanucher을 실행시킨다.
  - 동기적 실행
    - taskExecutor를 SyncTaskExecutor로 설정할 경우(기본값은 SyncTaskExecutor)
    - JobExecution을 획득하고 배치 처리를 최종 완료한 이후 Client에게 JobExecution 을 반환
    - 스케줄러에 의한 배치처리에 적합 함 - 배치처리시간이 길어도 상관없는 경우
  - 비 동기적 실행
    - taskExecutor 가 SimpleAsyncTaskExecutor 로 설정할 경우
    - JobExecution 을 흭득한 후 Client에게 바로 JobExecution 을 반환하고 배치처리를 완료한다.
    - HTTP 요청에 의한 배치처리에 적합함 - 배치처리 시간이 길 경우 응답이 늦어지지 않도록 함