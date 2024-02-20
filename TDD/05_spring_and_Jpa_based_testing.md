## 섹션 5 Spring & JPA 기반 테스트

### 통합 테스트
- 여러 모듈이 헙력하는 기능을 통합적으로 검증하는 테스트
- 일반적으로 작은 범위의 단위 테스트만으로는 기능 전체의 신뢰성을 보장할 수 없다.
- 풍부한 단위 테스트 & 큰 기능 단위를 검증하는 통합 테스트

### ORM (Object-Relational Mapping)

- 객체 지향 패러다임과 관계형 DB 패러다임의 불일치
- 이전에는 개발자가 객체의 데이터를 한딴한땀 매핑하여 DB에 저장 및 조회
- ORM을 사용함으로써 개발자는 단순 작업을 줄이고, 비즈니스 로직에 집중할 수 있다.

### JPA (Java Persitence API)
- Java 진영의 ORM 기술 표준
- 인터페이스이고, 여러 구현체가 있지만 보통 HIberante를 많이 사용한다.
- 반복정인 CRUD SQL을 생성 및 실행해주고, 여러 부가 기능들을 제공한다.
- 편리하지만 쿼리를 직접 작성하지 않기 때문에, 어떤 식으로 쿼리가 만들어지고 실행되는지 명확하게 이해하고 있어야 한다.

- Spring 진여에서는 JPA를 한번 더 추상화한 Spring Data JPA 제공
- QueryDSL과 조합하여 많이 사용한다.(타입체크, 동적쿼리)
- @Entity, @Id, @Column
- @ManyToOne, @OneToMany, @OneToOne, @ManyToMany

### 요구사항
- 키오스크 주문을 위한 상품 후보 리스트 조회하기
- 상품의 판매 상태: 판매중, 판매보류, 판매중지 ->  판매중, 판매보류인 상태의 상품을 화면에 보여준다.
- id, 상품 번호, 상품 타입, 판매 상태, 상품 이름, 가격


#### application.yml
``` yml
spring:
  profiles:
    default: local

  datasource:
    url: jdbc:h2:mem:~/cafeKioskApplication
    driver-class-name: org.h2.Driver
    username: sa
    password:

  jpa:
    hibernate:
      ddl-auto: none

---
spring:
  config:
    activate:
      on-profile: local

  jpa:
    hibernate:
      ddl-auto: create
    show-sql: true
    properties:
      hibernate:
        format_sql: true
    defer-datasource-initialization: true # (2.5~) Hibernate 초기화 이후 data.sql 실행

  h2:
    console:
      enabled: true

---
spring:
  config:
    activate:
      on-profile: test

  jpa:
    hibernate:
      ddl-auto: create
    show-sql: true
    properties:
      hibernate:
        format_sql: true

  sql:
    init:
      mode: never
``` 

### defer-datasource-initialization 옵션
- data.sql 쿼리를 Hibernate가 시작할때 자동으로 생성해줌.

#### data.sql
``` sql
insert into product(product_number, type, selling_status, name, price)
values ('001', 'HANDMADE', 'SELLING', '아메리카노', 4000),
       ('002', 'HANDMADE', 'HOLD', '카페라떼', 4500),
       ('003', 'BAKERY', 'STOP_SELLING', '크루아상', 3500);
``` 

``` java
package sample.cafekiosk.spring.domain.product;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.tuple;
import static org.junit.jupiter.api.Assertions.*;
import static sample.cafekiosk.spring.domain.product.ProductSellingStatus.*;
import static sample.cafekiosk.spring.domain.product.ProductType.HANDMADE;

//@SpringBootTest
@ActiveProfiles("test")
@DataJpaTest // JPA 전용
class ProductRepositoryTest {

    @Autowired
    private ProductRepository productRepository;

    @DisplayName("원하는 판매상태를 가진 상품들을 조회한다.")
    @Test
    void findAllBySellingStatusIn() throws Exception {
        //given
        Product product1 = Product.builder()
                .productNumber("001")
                .type(HANDMADE)
                .sellingStatus(SELLING)
                .name("아메리카노")
                .price(4000)
                .build();

        Product product2 = Product.builder()
                .productNumber("002")
                .type(HANDMADE)
                .sellingStatus(HOLD)
                .name("카페라떼")
                .price(4500)
                .build();

        Product product3 = Product.builder()
                .productNumber("003")
                .type(HANDMADE)
                .sellingStatus(STOP_SELLING)
                .name("팥빙수")
                .price(7000)
                .build();

        productRepository.saveAll(List.of(product1, product2, product3));

        //when
        List<Product> products = productRepository.findAllBySellingStatusIn(List.of(SELLING, HOLD));


        //then
        assertThat(products).hasSize(2)
                .extracting("productNumber", "name", "sellingStatus")
                .containsExactlyInAnyOrder(
                        tuple("001", "아메리카노", SELLING),
                        tuple("002", "카페라떼", HOLD)
                );

    }
}
``` 

### 리스트 테스트 방법
- 리스트 사이즈 검증 - hasSize()
- 필드 이름 검증 - extracting()
- 필드 값 검증 - containsExactlyInAnyOrder(tuple(), tuple(), ..)


![](https://github.com/dididiri1/TIL/blob/main/TDD/images/05_01.png?raw=true)

### Persistence Layer(퍼시스턴스)
- Data Access의 역활
- 비즈니스 가공 로직이 포함되어서는 안 된다.
- Data에 대한 CRUD에만 집중한 레이어

### Business Layer(비즈니스)
- 비즈니스 로직을 구현하는 역할
- Persistence Layer와의 상호작용(Data를 읽고 쓰는 행위)을 통해 비즈니스 로직을 전개시킨다.
- **트랜잭션**을 보장해야 한다.

### 요구사항 1
- 상품 번호 리스트를 받아 주문 생성하기
- 주문은 주문 상태, 주문 등록 시간을 가진다.
- 주문의 총 금액을 계산할 수 있어야 한다.

### 요구사항 2
- 주문 생성 시 재고 확인 및 개수 차감 후 생성하기
- 재고는 상품번호를 가진다.
- 재고와 관련 있는 상품 타입은 병 음료, 베이커리이다.

### Presentation Layer(프레젠테이션)

- 외부 세계의 요청을 가장 먼저 받는 계층
- 파라미터에 대한 최소한의 검증을 수행한다.

### MockMvc
- Mock(가짜, 대여) 객체를 사용해
    - 스프링 MVC 동작을 재현할 수 있는 테스트 프레임워크
    - 실제 객체를 만들어서 테스트하기 어려운 경우 가짜 객체를 만들어서 테스트하는 기술

### 요구사항 3

![](https://github.com/dididiri1/TIL/blob/main/TDD/images/05_02.png?raw=true)

- 관리자 페이지에서 신규 상품을 등록할 수 있다.
- 상품명, 상품 타입, 판매 상태, 가격 등을 입력받는다.


### @Transactional(readOnly = true)

- readOnly = ture : 읽기전용
- CRUD 에서 CUD 동작 x / only Read
- JPA : CUD 스냅샷 저장, 변경감지 X (성능 향상)
- CQRS - Command / Query 용으로 서비스로 분리를 하자
- Master DB(쓰기용), Slave DB(읽기용) readOnly 값으로 구분해서 나눌수 있음
- createProduct(command, CUD) @Transactional(readOnly = false)
- getSellingProducts(Query, R) @Transactional(readOnly = true)
- Service에 전체적으로 @Transactional(readOnly = true) 걸고 CUD 메소드에 따로 설정


``` java
@Transactional(readOnly = true)
@RequiredArgsConstructor
@Service
public class ProductService {

    private final ProductRepository productRepository;

    @Transactional
    public ProductResponse createProduct(ProductCreateRequest request) {
        String nextProductNumber = createNextProductNumber();

        Product product = request.toEntity(nextProductNumber);
        Product saveProduct = productRepository.save(product);

        return ProductResponse.of(saveProduct);
    }

    public List<ProductResponse> getSellingProducts() {
        List<Product> products = productRepository.findAllBySellingStatusIn(ProductSellingStatus.forDisplay());

        return products.stream()
                .map(product -> ProductResponse.of(product))
                .collect(Collectors.toList());
    }
}
``` 

### @WebMvcTest
- Controller 빈들만 주입 받아서 사용하는 가벼운 테스트
``` java
package sample.cafekiosk.spring.api.controller.product;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.web.servlet.MockMvc;
import sample.cafekiosk.spring.api.service.product.ProductService;
import sample.cafekiosk.spring.domain.product.ProductRepository;

@WebMvcTest
class ProductControllerTest {

    @Autowired
    private MockMvc mockMvc; // 주입 O
    
    @Autowired
    private ProductController productController; // 주입 O
    
    @Autowired
    private ProductService productService; // 주입 X
    
    @Autowired
    private ProductRepository productRepository; // 주입 X
    
}
``` 

### @MockBean
- @MockBean 사용하면 service, repository 주입 가능
``` java
package sample.cafekiosk.spring.api.controller.product;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;
import sample.cafekiosk.spring.api.service.product.ProductService;
import sample.cafekiosk.spring.domain.product.ProductRepository;

@WebMvcTest
class ProductControllerTest {

    @Autowired
    private MockMvc mockMvc; // 주입 O
    
    @Autowired
    private ProductController productController; // 주입 O
    
    @MockBean
    private ProductService productService; // 주입 O
    
    @MockBean
    private ProductRepository productRepository; // 주입 O
    
}
``` 

### JpaAuditing
- 생성일, 수정일 자동 시간을 매핑해서 넣어줌.

- 1. @EntityListeners(AuditingEntityListener.class) 어노테이션 추가
``` java
package sample.cafekiosk.spring.domain;

import lombok.Getter;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;
import java.time.LocalDateTime;

@Getter
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class BaseEntity {

    @CreatedDate
    private LocalDateTime createDateTime;

    @LastModifiedDate
    private LocalDateTime modifiedDateTime;

}

``` 

- 2. @EnableJpaAuditing 어노테이션 추가(자주 까먹을수도 있음)
``` java
package sample.cafekiosk.spring;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

@EnableJpaAuditing
@SpringBootApplication
public class CafekioskApplication {

    public static void main(String[] args) {
        SpringApplication.run(CafekioskApplication.class, args);
    }

}

``` 

### 참고
> MockMvc 테스트시 @WebMvcTest JPA 빈들을 주입받지 않기 때문에 문제가 발생한다.  
> 따라서 config로 분리해서 문제를 해결해야된다.
``` java
package sample.cafekiosk.spring.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

@EnableJpaAuditing
@Configuration
public class JapAuditingConfig {

}
``` 

### @NotNull, @NotEmpty, @NotBlank
- @NotNull는 null만 허용하지 않고 ""이나 " "은 허용됨
- @NotEmpty는 null, "", 허용하지 않지만 " "은 허용됨
- @NotBlank는 null, "", " " 모두 허용하지 않습니다.

> 참고: String 타입은 @NotBlank, enum 타입은 @NotNull, List 타입은 @NotEmpty 사용하자.

### 키워드 정리
- Layered Architecture
- Hexagonal Architecture
- 단위 테스트 VS. 통합 테스트
- IoC, DI, AOP
- ORM, 패러다임의 불일치, Hibernate
- Spring Data JPA
- QueryDSL - 타입체크, 동적쿼리
    - 장점 : 빌더 패턴, 컴파일 단계에서 에러가 나면 잡을수 있고, 동적쿼리(where 조건)

### 키워드 정리
- @SpringBootTest VS. @DataJpaTest
- @SpringBootTest VS. @WebMvcTest
- @Transactional (readOnly = true)
- Optimistic Lock(낙관적 락), Pessimistic Lock(비관적 락)
- CQRS

### 키워드 정리
- @WebMvcTest
- ObjectMapper
- @RestControllerAdvice, @ExceptionHandler
- Spring bean validation
- @NotNull, @NotEmpty, @NotBlank, …
- Mock, Mockito, @MockBean

### Reference
* [Practical Testing: 실용적인 테스트 가이드](https://inf.run/YLRXA)