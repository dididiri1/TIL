## Eureka Service Discover 프로젝트 생성

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
