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
![](https://github.com/dididiri1/TIL/blob/main/Batch/images/04_04.png?raw=true)

### 예제(코드로 생성)
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



### 예제(어플리케이션 실행 시 주입)
- build 후 jar 파일 생성후 명령어 실행
``` java
java -jar spring-batch-0.0.1-SNAPSHOT.jar name=user1 seq=2L data=2021-01-01 age=16.5
``` 