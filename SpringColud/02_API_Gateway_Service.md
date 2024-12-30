# 섹션 2 API Gateway Service

## API Gateway Service
사용자가 설정한 라우팅 설정에 따라서 각 엔드포인트로 클라이언트 대신 요청하고,  
응답을 받으면 다시 클라이언트에게 전달하는 프록시 역할을 하는 서비스이다.  
시스템의 내부 구조는 숨기고 외부의 요청에 대해 적절한 형태로 가공해서 응답할 수 있다는 장점이 있다.

클라이언트 단에서 엔드포인트를 직접 호출하는 방식으로 서비스를 구현하면.. 상당한 애로사항이 있다.  
URI 수정을 할 때마다 클라이언트 개발자의 눈치를 보고 싶은 것이 아니라면. 게이트웨이 서비스는 필수적이다.  
단일 진입점을 가지고 있기 때문에 모든 정보를 일괄적으로 처리할 수 있는 API Gateway 서비스는 정보의 변경과 갱신에 있어서 유리함을 가질 수 밖에 없다.

![](https://github.com/dididiri1/TIL/blob/main/SpringColud/images/01_07.png?raw=true)

### API Gateway의 특징
- 인증 및 권한 부여
- 서비스 검색 통합
- 응답 캐싱
- 정책, 회로 차단기 및 Qos 다시 시도
- 속조 제한 
- 부하 분산
- 로깅, 추적, 상관 관계
  - 로그만 전문적으로 처리해줄 수 있는 시스템 ELK것도 있음
- 헤더, 쿼리 문자열 및 청구 변환
- IP 허용 목록에 추가

### Spring Cloud에서의 MSA간 통신
1) RestTemplate
``` java
RestTemplate restTemplate = new RestTemplate();
restTemplate.setForObject("http://localhost:8080/". User.class, 200);
```

2) Feign Client
- 마이크로 서비스 이름 가지고 호출하실 수 있게 된다.
``` java
@FeignClient("stores")
public interface StoreClient {
  @RequestMapping(method = RequestMethod.GET, value = "/stores")
  List<Stroe> getStores();
}
```

### Ribbon: Client side Load Balancer(넥플리스가 기분함 요즘 안씀 비공기 안되서)
- 마이크로 서비스의 이름만 가지고 호출할 수 있다는 장점이 있음
- 서비스 이름으로 호출
- Health Check
- 클라이언트 측면의 로드발란서이다. 스프링부트 2.4에서 Maintenance 상태이다.

### 구성
- First Service
- Second Service
- Netflix Zuul

## Netflix Zuul - 프로젝트 생성 (Deprecated)
### Step1) Fist Service, Second Service
- Dependencodes: Lombook, Spring Web, Eureka Discovery Client
```
@RestController
@RequestMapping("/")
public class FirstServiceController {

	@GetMapping("/welcome")
    public String welcome() {
    	return "Welcome to the First Service";
    }
}
```

```
server:
  port: 8001

spring:
  application:
    name: my-first-service

eureka:
  client:
    fetch-registry: false
    register-with-eureka: false
```

## Netflix Zuul - Filter 적용
```
@SpringBootApplication
@EnableZuulProxy
public class ZuulServiceApplication {
    
    public static void main(String[] args) {
        SpringApplicaption.run(ZuulServiceApplication.class, args);
    }
}

```

```
server:
  port: 8080

spring:
  application:
    name: my-zuul-service

zuul:
  routes:
    first-service:
      path: /first-service/**
      url: http://localhost:8001
    second-service:
      path: /second-service/**
      url: http://localhost:8002 
```

```
@Slf4j
@Component
public class ZuulLoggingFilter extends ZuulFilter {

    @Override
    public Object run() throws ZuulException {
        log.info("**************** printing logs: ");

        RequestContext ctx = RequestContext.getCurrentContext();
        HttpServletRequest request = ctx.getRequest();
        log.info("**************** " + request.getRequestURI());

        return null;
    }

    @Override
    public String filterType() {
        return "pre";
    }

    @Override
    public int filterOrder() {
        return 1;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }
}
```

## Spring Cloud Gateway 란?
Spring Cloud Gateway(SCG)란 MSA 환경에서 사용하는 API Gateway중 하나로 Spring5, Spring Boot2, Project Reactor로 
구축된Spring 환경의 API Gateway다. Spring Cloud Gateway는 API 라우팅 및 보안, 모니터링/메트릭 등의 기능을 간단하고 효과적인   
방법으로 제공한다.

- Dependecies
  - DevTools, EureKa Discovery Clinet, Gateway

![](https://github.com/dididiri1/TIL/blob/main/SpringColud/images/02_01.png?raw=true)


- aplication.yml
```
server:
  port: 8000

eureka:
  client:
    register-with-eureka: false // 일단 false
    fetch-registry: false // 일단 false
    service-url:
      defaultZone: http://127.0.0.1:8761/eureka


spring:
  application:
    name: apigateway-service
  cloud:
    gateway:
      routes:
        - id: first-service
          uri: http://localhost:8081/
          predicates:
            - Path=/first-service/**
        - id: second-service
          uri: http://localhost:8082/
            - Path=/second-service/**
```

> 지금까지의 실습에서는 톰캣으로 서버를 띄웠으나, 지금은 Netty라는 내장 서버를 사용해 띄운 것이다.  
> Tomcat: 동기 방식  
> Spring Cloud Gateway: 비동기 방식

## Spring Cloud Gateway - Filter 적용

![](https://github.com/dididiri1/TIL/blob/main/SpringColud/images/02_02.png?raw=true)

- 필터 빈으로 등록해서 사용하기
```
import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FilterConfig {

    @Bean
    public RouteLocator gatewayRoutes(RouteLocatorBuilder builder) {
        return builder.routes()
                .route(r -> r.path("/first-service/**")
                        .filters(f -> f.addRequestHeader("first-request", "first-request-header")
                                .addResponseHeader("first-response", "first-response"))
                        .uri("http://localhost:8081"))
                .route(r -> r.path("/second-service/**")
                        .filters(f -> f.addRequestHeader("second-request", "second-request-header")
                                .addResponseHeader("second-response", "second-response"))
                        .uri("http://localhost:8082"))
                .build();
    }
}

```

- yml으로 필터 사용하기
```
server:
  port: 8000

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
    service-url:
      defaultZone: http://127.0.0.1:8761/eureka


spring:
  application:
    name: apigateway-service
  cloud:
    gateway:
      routes:
        - id: first-service
          uri: http://localhost:8081/
          predicates:
            - Path=/first-service/**
          filters:
            - AddRequestHeader=first-request, first-requests-header2
            - AddResponseHeader=first-reponse, first-reponse-header2
        - id: second-service
          uri: http://localhost:8082/
          predicates:
            - Path=/second-service/**
          filters:
            - AddRequestHeader=second-request, second-requests-header2
            - AddResponseHeader=second-repon
```

## Spring Cloud Gateway - Custom Filter
```
server:
  port: 8000

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
    service-url:
      defaultZone: http://127.0.0.1:8761/eureka


spring:
  application:
    name: apigateway-service
  cloud:
    gateway:
      routes:
        - id: first-service
          uri: http://localhost:8081/
          predicates:
            - Path=/first-service/**
          filters:
            - CustomFilter
        - id: second-service
          uri: http://localhost:8082/
          predicates:
            - Path=/second-service/**
          filters:
            - CustomFilter
```

```
@Component
@Slf4j
public class CustomFilter extends AbstractGatewayFilterFactory<CustomFilter.Config> {

    public CustomFilter() {
        super(Config.class);
    }

    @Override
    public GatewayFilter apply(CustomFilter.Config config) {
        return (exchange, chain) -> {
            ServerHttpRequest request = exchange.getRequest();
            ServerHttpResponse response = exchange.getResponse();

            log.info("Custom PRE filter: reqeust id -> {}", request.getId());

            return chain.filter(exchange).then(Mono.fromRunnable(() -> {
                log.info("Custom PRE filter: response code -> {}", response.getStatusCode());
            }));
        };
    }

    public static class Config {

    }
}

```

## Spring Cloud Gateway - Global Filter
- CustomFilter와 생성하는 방법은 동일하지만, route 별로 적용하는 것이 아닌 공통적으로 실행되는 필터
- 모든 필터의 가장 첫번째로 실행이 되고 가장 마지막에 종료가 된다.

> 참고: 차이점 글러벌 필터는 어떤한 라우터 정보라도 공통적으로 다 시행될 수 있는 공통 필터 개념으로 
> 이전 커스텀 필터랑 차이가 있다. 즉 커스텀 필터는 필터가 필요할 경우 개별적으로 등록 해야됨

```
server:
  port: 8000

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
    service-url:
      defaultZone: http://127.0.0.1:8761/eureka


spring:
  application:
    name: apigateway-service
  cloud:
    gateway:
      default-filters:
        - name: GlobalFilter
          args:
            baseMessage: Spring Cloud Gateway Global Filter
            preLogger: true
            postLogger: true
      routes:
        - id: first-service
          uri: http://localhost:8081/
          predicates:
            - Path=/first-service/**
          filters:
            - CustomFilter
        - id: second-service
          uri: http://localhost:8082/
          predicates:
            - Path=/second-service/**
          filters:
            - CustomFilter
```

```
@Component
@Slf4j
public class GlobalFilter extends AbstractGatewayFilterFactory<GlobalFilter.Config> {

    public GlobalFilter(){
        super(Config.class);
    }

    @Override
    public GatewayFilter apply(Config config) {
        // Global Pre Filter
        return (exchange, chain) -> {
            ServerHttpRequest request = exchange.getRequest();
            ServerHttpResponse response = exchange.getResponse();

            log.info("Global Filter baseMessage : {}", config.getBaseMessage());

            if(config.isPreLogger()){
                log.info("Global Filter Start : request id -> {}", request.getId());
            }

            // Global Post Filter
            return chain.filter(exchange).then(Mono.fromRunnable(() -> {
                if(config.isPostLogger()) {
                    log.info("Global Filter End : response code -> {}", response.getStatusCode());
                }
            }));
        };
    }

    @Data
    public static class Config {
        private String baseMessage;
        private boolean preLogger;
        private boolean postLogger;
    }
}
```

## Spring Cloud Gateway - Logging Filter

```
server:
  port: 8000

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
    service-url:
      defaultZone: http://127.0.0.1:8761/eureka


spring:
  application:
    name: apigateway-service
  cloud:
    gateway:
      default-filters:
        - name: GlobalFilter
          args:
            baseMessage: Spring Cloud Gateway Global Filter
            preLogger: true
            postLogger: true
      routes:
        - id: first-service
          uri: http://localhost:8081/
          predicates:
            - Path=/first-service/**
          filters:
            - CustomFilter
        - id: second-service
          uri: http://localhost:8082/
          predicates:
            - Path=/second-service/**
          filters:
            - name: CustomFilter
            - name: LoggingFilter
              args:
                baseMessage: Hi, there
                preLogger: true
                postLogger: true
```

```
@Component
@Slf4j
public class LoggingFilter extends AbstractGatewayFilterFactory<LoggingFilter.Config> {

    public LoggingFilter(){
        super(Config.class);
    }

    @Override
    public GatewayFilter apply(Config config) {

        GatewayFilter filter = new OrderedGatewayFilter((exchange, chain) ->{
            ServerHttpRequest request = exchange.getRequest();
            ServerHttpResponse response = exchange.getResponse();

            log.info("Logging Filter baseMessage : {}", config.getBaseMessage());

            if(config.isPreLogger()){
                log.info("Logging PRE Filter Start : request id -> {}", request.getId());
            }

            return chain.filter(exchange).then(Mono.fromRunnable(() -> {
                if(config.isPostLogger()) {
                    log.info("Logging POST Filter End : response code -> {}", response.getStatusCode());
                }
            }));
        }, Ordered.HIGHEST_PRECEDENCE);

        return filter;
    }

    @Data
    public static class Config {
        private String baseMessage;
        private boolean preLogger;
        private boolean postLogger;
    }
}
```

## Spring Cloud Gateway - Load Balancer

![](https://github.com/dididiri1/TIL/blob/main/SpringColud/images/02_03.png?raw=true)

- API Gateway Service 프로젝트 : eureka client, 8000포트
- first Service 프로젝트 : eureka client, 8081포트
- second Service 프로젝트 : eureka client, 8082포트
- discovery Service 프로젝트 : eureka servcer, 8761포트 

```
@RestController
@RequestMapping("/first-service")
@Slf4j
public class FirstServiceController {

    Environment env;

    public FirstServiceController(Environment env) {
        this.env = env;
    }
    
    @GetMapping("/check")
    public String check(HttpServletRequest request) {
        log.info("Linux port={}", request.getServerPort());
        return String.format("Hi, there. This is a message from Second Service. on PORT $s"
                , env.getProperty("Local.server.port"));
    }
}
```