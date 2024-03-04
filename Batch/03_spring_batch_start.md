# 스프링 배치 시작

## 프로젝트 구성 및 의존성 설정

### 스프링 배치 활성화
#### @EnableBatchProcessing
- 스프링 배치가 작동하기 위해 선언해야 하는 어노테이션

``` java
@SpringBootApplication
@EnableBatchProcessing
public class SpringBatchApplication {
    
    public static void main(String[] args) {
        SpringApplication.run(SpringBatchApplication.class, args);
    }
    
}
```
- 총 4개의 설정 클래스를 실행시키며 스프링 배치의 모든 초기화 및 실행 구성이 이루어진다.
- 스프링 부트 배치의 자동 설정 클래스가 실행됨으로 빈으로 등록된 모든 job 을 검색해서 초기화  
  와 동시에 job 을 수행하도록 구성됨

###  스프링 배치 초기화 설정 클래스
1. BatchAutoConfiguration
   - 스프링 배치가 초기화 될 때 자동으로 실행되는 설정 클래스
   - job 을 수행하는 JobLauncherApplicationRunner 빈을 생성

2. SimpleBatchConfiguration
   - JobBuilderFactory 와 StepBuilderFactory 생성
   - 스프링 배치의 주요 구성 요서 생성 - 프록시 객체로 생성됨
   
3. BatchConfiguration
   - BasicBatchConfigurer
     - SimpleBatchConfiguration 에서 생성한 프록시 객체의 실제 대상 객체를 생성하는 설정 클래스
     - 빈으로 의존성 주입 받아서 주요 객체들을 참조해서 사용할 수 있다
   - JpaBatchConfigurer
     - JPA 관련 객체를 생성하는 설정 클래스
   - 사용자 정의 BatchConfigurer 인터페이스를 구현하여 사용할 수 있음

## Hello Spring Batch 시작하기
1. @Configuration 선언
- 하나의 배치 Job을 정의하고 빈 설정

2. JobBuilderFactory
- job을 생성하는 빌더 팩토리

3. StepBuilderFactory
- step을 생성하는 빌더 팩토리

4. Job
- helloJob 이름으로 Job 생성

5. Step
- helloJob 이름으로 Step 생성
6. tasklet
- Step 안에서 단일 태스크로 수행되는 로직 구현

7. Job 구동 -> Step을 실행 -> Tasklet 을 실행

### Lombok 설정
- build.gradle 파일에 의존성 추가
``` 
// lombok
compileOnly 'org.projectlombok:lombok'
annotationProcessor 'org.projectlombok:lombok'
``` 

- Enable annotation processing 체크
![](https://github.com/dididiri1/TIL/blob/main/Batch/images/03_01.png?raw=true)

``` java
@RequiredArgsConstructor
@Configuration
public class HelloJobConfiguration {

    private final JobBuilderFactory jobBuilderFactory;
    private final StepBuilderFactory stepBuilderFactory;

    @Bean
    public Job helloJob() {
        return jobBuilderFactory.get("helloJob")
                .start(helloStep1())
                .next(helloStep2())
                .build();
    }

    @Bean
    public Step helloStep2() {
        return stepBuilderFactory.get("helloStep1")
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {

                        System.out.println("=========================");
                        System.out.println(" >> Hello Spring Batch!!");
                        System.out.println("=========================");

                        return RepeatStatus.FINISHED; // 한번만 실행
                    }
                })
                .build();
    }

    @Bean
    public Step helloStep1() {
        return stepBuilderFactory.get("helloStep2")
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {

                        System.out.println("=========================");
                        System.out.println(" >> step was executed");
                        System.out.println("=========================");

                        return RepeatStatus.FINISHED; // 한번만 실행
                    }
                })
                .build();
    }
}

``` 
