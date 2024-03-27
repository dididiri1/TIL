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
## DB 스키마 생성 및 이해(1)
### 1. 스프링 배치 메타 데이터
- 스프링 배치의 실행 및 관리를 위한 목적으로 여러 도메인들(Job, Step, JobParameters..)의 정보들을 저장, 업데이트, 조회할 수 있는 스키마 제공
- 과거, 현재의 실행에 대한 세세한 정보, 실행에 대한 성공과 실패 여부 등을 일목요현하게 관리함으로서 배치운용에 있어 리스크 발생시 빠른 대처 가능
- DB 와 연동할 경우 필수적으로 메타 테이블이 생성 되어야 함.

### 2. DB 스키마 제공
- 파일 위치 : 
- DB 유형별로 제공

### 3. 스키마 생성 설정
- 수동 생성 - 쿼리 복사 후 직접 실행
- 자동 생성 - spring.batch.jdbc.initialize-schema 설정
- 속성값 3가지 
  - ALWAYS 
    - 스크립트 항상 실행
    - **RDBMS 설정이 되어 있을 경우 내장 DB 보다 우선적으로 실행**
  - EMBEDDED : 내장 DB 일 때만 실행되며 스키마가 자동 생성됨, 기본값
  - NEVER
    - 스크립트 항상 실행 안함
    - 내장 DB 일경우 스크립트가 생성이 안되기 떄문에 오류 발생
    - 운영에서 수동으로 스크립트 생성 후 설정하는 것을 권장
 
> 참고: 운영에서는 ALWAYS 위험하고 그래서 NEVER로 하고 수동으로 메타 테이블 스키마를 생성하는 것을 권장함.

- Job 관련 테이블
  - BATCH_JOB_INSTANCE
    - Job 이 실행될 떄 JobInstance 정보가 저장되며 job_name과 job_key를 키로 하여 하나의 데이터가 저장
    - 동일한 job_name 과 job_key 로 중복 저장될 수 없다.
    
  - BATCH_JOB_EXECUTION
    - job 의 실행정보가 저장되며 Job 생성, 시작, 종료 시간, 실행상태, 메세지 등을 관리
  - BATCH_JOB_EXECUTION_PARAMS
    - Job과 함께 실행되는 JobParameter 정보를 저장

### Docker & Mysql 설치

#### 1. Mysql 이미지 다운
* `$ docker pull mysql`

#### 2. 이미지 확인
* `$ docker images`
``` 
REPOSITORY                   TAG       IMAGE ID       CREATED        SIZE
mysql                        latest    019814493c7a   2 months ago   632MB
``` 

#### 3. MySQL 컨테이너 생성 & 실행
* `$ docker run --name mysql -e MYSQL_ROOT_PASSWORD=1234 -d -p 3307:3306 mysql`

- -name <container_name> : <container_name> 이름의 컨테이너를 실행한다.
- -e : 컨테이너 내에서 사용할 환경변수를 설정
- -e MYSQL_ROOT_PASSWORD=<password> : MySQL의 root 권한의 비밀번호를 <password>로 설정한다.
- -d : detach 모드로 컨테이너가 실행된다. 컨테이너가 백그라운드로 실행된다고 보면 된다.
- -p <호스트 포트> <컨테이너 포트> : 호스트와 컨테이너의 포트를 연결한다
- mysql:latest : 컨테이너에 사용할 이미지

### 수동 생성
#### 1. External libraries -> srping-batch-core-4.3.7.jar -> /org/springframework/batch/core/schema-*.sql
![](https://github.com/dididiri1/TIL/blob/main/Batch/images/03_02.png?raw=true)

#### 2. schema-mysql.sql
``` log
-- Autogenerated: do not edit this file

CREATE TABLE BATCH_JOB_INSTANCE  (
	JOB_INSTANCE_ID BIGINT  NOT NULL PRIMARY KEY ,
	VERSION BIGINT ,
	JOB_NAME VARCHAR(100) NOT NULL,
	JOB_KEY VARCHAR(32) NOT NULL,
	constraint JOB_INST_UN unique (JOB_NAME, JOB_KEY)
) ENGINE=InnoDB;

CREATE TABLE BATCH_JOB_EXECUTION  (
	JOB_EXECUTION_ID BIGINT  NOT NULL PRIMARY KEY ,
	VERSION BIGINT  ,
	JOB_INSTANCE_ID BIGINT NOT NULL,
	CREATE_TIME DATETIME(6) NOT NULL,
	START_TIME DATETIME(6) DEFAULT NULL ,
	END_TIME DATETIME(6) DEFAULT NULL ,
	STATUS VARCHAR(10) ,
	EXIT_CODE VARCHAR(2500) ,
	EXIT_MESSAGE VARCHAR(2500) ,
	LAST_UPDATED DATETIME(6),
	JOB_CONFIGURATION_LOCATION VARCHAR(2500) NULL,
	constraint JOB_INST_EXEC_FK foreign key (JOB_INSTANCE_ID)
	references BATCH_JOB_INSTANCE(JOB_INSTANCE_ID)
) ENGINE=InnoDB;

CREATE TABLE BATCH_JOB_EXECUTION_PARAMS  (
	JOB_EXECUTION_ID BIGINT NOT NULL ,
	TYPE_CD VARCHAR(6) NOT NULL ,
	KEY_NAME VARCHAR(100) NOT NULL ,
	STRING_VAL VARCHAR(250) ,
	DATE_VAL DATETIME(6) DEFAULT NULL ,
	LONG_VAL BIGINT ,
	DOUBLE_VAL DOUBLE PRECISION ,
	IDENTIFYING CHAR(1) NOT NULL ,
	constraint JOB_EXEC_PARAMS_FK foreign key (JOB_EXECUTION_ID)
	references BATCH_JOB_EXECUTION(JOB_EXECUTION_ID)
) ENGINE=InnoDB;

CREATE TABLE BATCH_STEP_EXECUTION  (
	STEP_EXECUTION_ID BIGINT  NOT NULL PRIMARY KEY ,
	VERSION BIGINT NOT NULL,
	STEP_NAME VARCHAR(100) NOT NULL,
	JOB_EXECUTION_ID BIGINT NOT NULL,
	START_TIME DATETIME(6) NOT NULL ,
	END_TIME DATETIME(6) DEFAULT NULL ,
	STATUS VARCHAR(10) ,
	COMMIT_COUNT BIGINT ,
	READ_COUNT BIGINT ,
	FILTER_COUNT BIGINT ,
	WRITE_COUNT BIGINT ,
	READ_SKIP_COUNT BIGINT ,
	WRITE_SKIP_COUNT BIGINT ,
	PROCESS_SKIP_COUNT BIGINT ,
	ROLLBACK_COUNT BIGINT ,
	EXIT_CODE VARCHAR(2500) ,
	EXIT_MESSAGE VARCHAR(2500) ,
	LAST_UPDATED DATETIME(6),
	constraint JOB_EXEC_STEP_FK foreign key (JOB_EXECUTION_ID)
	references BATCH_JOB_EXECUTION(JOB_EXECUTION_ID)
) ENGINE=InnoDB;

CREATE TABLE BATCH_STEP_EXECUTION_CONTEXT  (
	STEP_EXECUTION_ID BIGINT NOT NULL PRIMARY KEY,
	SHORT_CONTEXT VARCHAR(2500) NOT NULL,
	SERIALIZED_CONTEXT TEXT ,
	constraint STEP_EXEC_CTX_FK foreign key (STEP_EXECUTION_ID)
	references BATCH_STEP_EXECUTION(STEP_EXECUTION_ID)
) ENGINE=InnoDB;

CREATE TABLE BATCH_JOB_EXECUTION_CONTEXT  (
	JOB_EXECUTION_ID BIGINT NOT NULL PRIMARY KEY,
	SHORT_CONTEXT VARCHAR(2500) NOT NULL,
	SERIALIZED_CONTEXT TEXT ,
	constraint JOB_EXEC_CTX_FK foreign key (JOB_EXECUTION_ID)
	references BATCH_JOB_EXECUTION(JOB_EXECUTION_ID)
) ENGINE=InnoDB;

CREATE TABLE BATCH_STEP_EXECUTION_SEQ (
	ID BIGINT NOT NULL,
	UNIQUE_KEY CHAR(1) NOT NULL,
	constraint UNIQUE_KEY_UN unique (UNIQUE_KEY)
) ENGINE=InnoDB;

INSERT INTO BATCH_STEP_EXECUTION_SEQ (ID, UNIQUE_KEY) select * from (select 0 as ID, '0' as UNIQUE_KEY) as tmp where not exists(select * from BATCH_STEP_EXECUTION_SEQ);

CREATE TABLE BATCH_JOB_EXECUTION_SEQ (
	ID BIGINT NOT NULL,
	UNIQUE_KEY CHAR(1) NOT NULL,
	constraint UNIQUE_KEY_UN unique (UNIQUE_KEY)
) ENGINE=InnoDB;

INSERT INTO BATCH_JOB_EXECUTION_SEQ (ID, UNIQUE_KEY) select * from (select 0 as ID, '0' as UNIQUE_KEY) as tmp where not exists(select * from BATCH_JOB_EXECUTION_SEQ);

CREATE TABLE BATCH_JOB_SEQ (
	ID BIGINT NOT NULL,
	UNIQUE_KEY CHAR(1) NOT NULL,
	constraint UNIQUE_KEY_UN unique (UNIQUE_KEY)
) ENGINE=InnoDB;

INSERT INTO BATCH_JOB_SEQ (ID, UNIQUE_KEY) select * from (select 0 as ID, '0' as UNIQUE_KEY) as tmp where not exists(select * from BATCH_JOB_SEQ);
```

#### 3. Mysql Workbench에 쿼리 실행하면 테이블 생성됨.

![](https://github.com/dididiri1/TIL/blob/main/Batch/images/03_03.png?raw=true)

### 자동 생성

#### 1. Gradle 추가
```
// Mysql
implementation 'mysql:mysql-connector-java:8.0.33'
```

#### 2. application.yml t설정
```

spring:
  profiles:
    active: local

---
spring:
  config:
    activate:
      on-profile: local
  datasource:
    hikari:
      jdbc-url: jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
      username: sa
      password:
      driver-class-name: org.h2.Driver
  batch:
    jdbc:
      initialize-schema: embedded


---
spring:
  config:
    activate:
      on-profile: mysql
  datasource:
    hikari:
      jdbc-url: jdbc:mysql://localhost:3307/springbatch?useUnicode=true&characterEncoding=utf8
      username: root
      password: 1234
      driver-class-name: com.mysql.cj.jdbc.Driver
  batch:
    jdbc:
      initialize-schema: always
```

#### 3. intellij active profiles 변경(우선순위 높음 yml보다)
![](https://github.com/dididiri1/TIL/blob/main/Batch/images/03_05.png?raw=true)