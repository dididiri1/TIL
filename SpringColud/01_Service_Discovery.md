# Spring Cloud로 개발하는 마이크로서비스 애플리케이션(MSA)

![](https://github.com/dididiri1/TIL/blob/main/SpringColud/images/01_00.png?raw=true)

# 섹션 1 Service Discovery

## Eureka Service Discover - 프로젝트 생성

![](https://github.com/dididiri1/TIL/blob/main/SpringColud/images/01_01.png?raw=true)


![](https://github.com/dididiri1/TIL/blob/main/SpringColud/images/01_02.png?raw=true)

```
server:
  port: 8761

spring:
  application:
    name: user-service

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
```

## User Service - 프로젝트 생성
![](https://github.com/dididiri1/TIL/blob/main/SpringColud/images/01_03.png?raw=true)


![](https://github.com/dididiri1/TIL/blob/main/SpringColud/images/01_04.png?raw=true)

```
server:
  port: 9001

spring:
  application:
    name: user-service

eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://127.0.0.1:8761/eureka

```

![](https://github.com/dididiri1/TIL/blob/main/SpringColud/images/01_05.png?raw=true)


> $ mvn spring-boot:run -Dspring-boot.run.jvmArguments='-Dserver.port=[포트번호]'
> $ java -jar -Dserver.port=9004 ./target/user-service-0.0.1-SNAPSHOT.jar

## User Service - Load Balancer
### 랜덤 포트 방법
- 일반적인 환경에서는 지정된 PORT를 사용하여 어플리케이션을 운영한다. 그러나 MSA와 같이 LB 환경을 위해 여러 서비스를 등록하거나 자동으로 서비스가 증가되어야 하는 상황에서는 random port를 이용한다.
- 0번 포트를 사용하면 랜덤 포트를 사용하겠다는 의미
- 인스터스 아이디 설정
``` yml
server:
  port: 0

spring:
  application:
    name: user-service


eureka:
  instance:
    instance-id: ${spring.cloud.client.hostname}:${spring.application.instance_id:${random.value}}
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://127.0.0.1:8761/eureka
```

#### Maven을 사용하는 Spring Boot 프로젝트를 실행하려면 다음 명령어
```
mvn spring-boot:run
```

#### Gradle 빌드 실행
```
./gradlew bootRun
```

![](https://github.com/dididiri1/TIL/blob/main/SpringColud/images/01_06.png?raw=true)