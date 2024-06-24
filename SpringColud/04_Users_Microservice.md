# 섹션 4 Users Microservice

## Users Microservice 개요

![](https://github.com/dididiri1/TIL/blob/main/SpringColud/images/04_01.png?raw=true)

### APIs
| 기능               |       URI(API Gateway 사용 시)       | URI(API Gateway 미사용 시) |
|:-----------------|:---------------------------------:|:----------------------:|
| 사용자 정보 등록        |        /user-service/users        |         /users         |
| 전체 사용자 조회        |        /user-service/users        |         /users         |
| 사용자 정보, 주문 내역 조회 |   /user-service/users/{user_id}   |    /user/{user_id}     |
| 작동 상태 확인         | /user-service/users /health_check |   /user/health_check   |
| 환영 메세지           |    /user-service/users/welcome    |     /user/welcome      |

## Users Microservice - 프로젝트 생성

### Depedencies
- Spring Boot DevTools
- Lombok
- Spring Web
- Eureka Discovery Client
- ModelMapper
- Spting Boot Starter Data JPA
- H2 Database

### application.yml
- 포트 번호를 0으로 지정하여 랜덤 포트로 지정하였고, instance-id 값을 지정해줌
```
server:
  port: 0

spring:
  application:
    name: user-service

eureka:
  instance:
    instance-id: ${spring.application.name}:${spring.application.instance_id:${random.value}}
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://127.0.0.1:8761/eureka

greeting:
  message: Welcome to the Simple E-commerce.
```

### Environment 객체 의존 주입
```
@RestController
@RequestMapping("/")
public class UserController {

    private Environment env;

    public UserController(Environment env) {
        this.env = env;
    }

    ```

    @GetMapping("/welcome")
    public String welcome() {
        return env.getProperty("greeting.message");
    }
}

```

### @Value 어노테이션 사용
```
@RestController
@RequestMapping("/")
public class UserController {

    private Environment env;

    @Autowired
    private Greeting greeting;

    public UserController(Environment env) {
        this.env = env;
    }

    ```

    @GetMapping("/welcome")
    public String welcome() {
        return greeting.getMessage();
    }
}

```