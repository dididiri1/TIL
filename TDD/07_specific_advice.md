# 섹션 7 더 나은 테스트를 작성하기 위한 구체적 조언

## 한 문단에 한 주제
- 한 가지 테스트에서는 한 가지 목적의 검증만 수행을 한다.

## 완벽하게 제어하기

## 테스트 환경의 독립성을 보장하자
- 다른 API들을 끌어다가 사용할 경우 테스트 간 결합도가 생기는 케이스가 있을수 있으며, 그런 부분에서 독립성을 보장해야 된다.
- 독립적으로 테스트 환경을 구성하고 최대한 독립성을 보장해서 구성하는게 제일 좋음
- 밑에 코드를보며 deductQuantity todo 문제

``` java
    @DisplayName("재고가 부족한 상품으로 주문을 생성하려는 경우 예외가 발생한다.")
    @Test
    void createOrderWithNoStock() {
        // given
        LocalDateTime registeredDateTime = LocalDateTime.now();

        Product product1 = createProduct(BOTTLE, "001", 1000);
        Product product2 = createProduct(BAKERY, "002", 3000);
        Product product3 = createProduct(HANDMADE, "003", 5000);
        productRepository.saveAll(List.of(product1, product2, product3));

        Stock stock1 = Stock.create("001", 2);
        Stock stock2 = Stock.create("002", 2);
        stock1.deductQuantity(1); // todo
        stockRepository.saveAll(List.of(stock1, stock2));

        OrderCreateServiceRequest request = OrderCreateServiceRequest.builder()
                .productNumbers(List.of("001", "001", "002", "003"))
                .build();

        // when // then
        assertThatThrownBy(() -> orderService.createOrder(request, registeredDateTime))
                .isInstanceOf(IllegalArgumentException.class)
                .hasMessage("재고가 부족한 상품이 있습니다.");
    }

    private Product createProduct(ProductType type, String productNumber, int price) {
        return Product.builder()
                .type(type)
                .productNumber(productNumber)
                .price(price)
                .sellingStatus(SELLING)
                .name("메뉴 이름")
                .build();
    }
```


### 생성자와 정적 팩토리 메소드
- 정적 펙토르 메소드
    - 장점1 : 생성자에 넘기는 메게변수 이름만으로는 반환될 객체의 특성을 설명하기 어렵지만, 정적 펙토리 메소드는  
      이름 지을수 있어 객체 특성을 쉽게 표현할 수 있다.
    - 장점2 : 인스턴스를 새로 생성하지 않아도 된다.
```java
package sample.cafekiosk.spring.domain.stock;

import lombok.AccessLevel;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import sample.cafekiosk.spring.domain.BaseEntity;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Entity
public class Stock extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String productNumber;

    private int quantity;

    
    // 생성자
    public Stock(String productNumber, int quantity) {
        this.productNumber = productNumber;
        this.quantity = quantity;
    }

    // 정적 펙토리 메소드
    public static Stock create(String productNumber, int quantity) {
        return Stock.builder()
                .productNumber(productNumber)
                .quantity(quantity)
                .build();
    }
}

``` 

## 테스트 간 독립성을 보장하자

### 두 가지 이상의 테스트가 하나의 자원을 공유할때 잘못된 얘
- 테스트간에 순서는 무관해야 됨. A테스트가 수행된 이후 B테스트가 수행되어야 성공을 한다는 개념 자체가 없어야됨.
- 각각 독립적으로 언제 수행이 되도 항상 같은 결과를 내야됨.
- 공유 자원을 사용하는 경우에는 테스트 간 순서가 생길 수 있기 때문이다.
```java
class StockTest {

    private static final Stock stock = Stock.create("100", 1);

    @DisplayName("재고의 수량이 제공된 수량보다 작은지 확인하다.")
    @Test
    void isQuantityLessThan() throws Exception {
        //given
        int quantity = 2;

        //when
        boolean result = stock.isQuantityLessThan(quantity);

        //then
        assertThat(result).isTrue();

    }

    @DisplayName("재고를 주어진 개수만큼 차감할 수 있다.")
    @Test
    void deductQuantity() throws Exception {
        //given
        int quantity = 1;

        //when
        stock.deductQuantity(quantity);

        //then
        assertThat(stock.getQuantity()).isEqualTo(0);
    }
}
``` 

```java
class StockTest {

    @DisplayName("재고의 수량이 제공된 수량보다 작은지 확인하다.")
    @Test
    void isQuantityLessThan() throws Exception {
        //given
        Stock stock = Stock.create("001", 1);
        int quantity = 2;

        //when
        boolean result = stock.isQuantityLessThan(quantity);

        //then
        assertThat(result).isTrue();

    }

    @DisplayName("재고를 주어진 개수만큼 차감할 수 있다.")
    @Test
    void deductQuantity() throws Exception {
        //given
        Stock stock = Stock.create("100", 1);
        int quantity = 1;

        //when
        stock.deductQuantity(quantity);

        //then
        assertThat(stock.getQuantity()).isEqualTo(0);
    }
}
```

## 한 눈에 들어오는 Test Fixture 구성하기
### Test Fixture

- Fixture : 고정물, 고정되어 있는 물체
- 테스트를 위해 원하는 상태로 고정시킨 일련의 객체

## Test Fixture 클렌징 (3가지)

### @Transactional

### deleteAllInBatch
- deleteAllInBatch는 테이블 전체를 벌크성으로 날릴 수 있는 메소드인데 지우는 순서를 잘 고려야해야 한다.
- 외래키 조건이 걸려 있거나 조건들이 순서에 따라 영향을 받는다. 해서 어떤걸 먼저 순서를 고민해야되는게 단점이지만 deleteAll() 보다 성능이 좋다.

``` java
@AfterEach
void tearDown() {
    orderProductRepository.deleteAllInBatch();
    productRepository.deleteAllInBatch();
    orderRepository.deleteAllInBatch();
}
``` 

### deleteAll
- deleteAll의 장점은 Order를 지우고 Product를 지웠을때 Order를 지우면서 OrderProduct까지 같이 지워준다.
- foreign key 맺고 있는 애들은 전부 찾아와서 select하고 하나씩 껀껀히 반복문을 돌면서 지워서 성능이 저하되는 단점이 있음.

``` java
@AfterEach
void tearDown() {
    orderRepository.deleteAll();
    productRepository.deleteAll();
}
``` 

#### 정리
> @Transactional 편하니깐 써야지 보다는 상황의 맞쳐 잘 알고 써야된다.

## @ParameterizedTest(파리미터라이즈 테스트)
- 하나의 테스트 케이스데 값을 여러 개로 바꿔보면서 테스트를 하고 싶을때 사용

### @ValueSource 방식
- 파라미터 하나일 때 간단한 형태
```java
class ProductTypeTest {

    @ParameterizedTest
    @ValueSource(ints = {1, 2, 3})
    void testWithValueSource(int argument) throws Exception {
        //then
        assertThat(argument > 0 && argument < 4);
    }
    
}
```


### @CsvSource 방식
```java
class ProductTypeTest {
     
    @DisplayName("상품 타입이 재고 관련 타입인지를 체크한다.")
    @CsvSource({"HANDMADE,false", "BOTTLE,true", "BAKERY,true"})
    @ParameterizedTest
    void containsStockType4(ProductType productType, boolean expected) throws Exception {
        //when
        boolean result = ProductType.containsStockType(productType);

        //then
        assertThat(result).isEqualTo(expected);
    }
}
``` 

### @MethodSource 방식
- 이경우 프로덕션 코드에서는 private method를 보통 아래쪽에 명시하는 편인데. given의 역활이기 떄문에 테스트 바로 위에다가 명시할수도 있다.
```java
class ProductTypeTest {
     
     private static Stream<Arguments> provideProductTypesForCheckingStockType() {
        return Stream.of(
                Arguments.of(ProductType.HANDMADE, false),
                Arguments.of(ProductType.BOTTLE, true),
                Arguments.of(ProductType.BAKERY, true)
        );
    }

    @DisplayName("상품 타입이 재고 관련 타입인지를 체크한다.")
    @MethodSource("provideProductTypesForCheckingStockType")
    @ParameterizedTest
    void containsStockType5(ProductType productType, boolean expected) throws Exception {
        //when
        boolean result = ProductType.containsStockType(productType);

        //then
        assertThat(result).isEqualTo(expected);
    }
    
}
``` 
### 참고
- [Junit5 Parameterized Tests](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests)
- [Spock Data Tables](https://spockframework.org/spock/docs/2.3/data_driven_testing.html#data-tables)

## @DynamicTest
- Runtime 중에 생성되는 동적 테스트이며, given 안에서 when이 연속적으로 이루어지는 형태를 가진다.
- 일련의 시나리오를 테스트 하고 싶을때 단계별로 어떤 행위와 검증을 수행하고 싶을때 사용
- @TestFactory 사용

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.DynamicTest;
import org.junit.jupiter.api.TestFactory;

import java.util.Collection;
import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;

class StockTest {
    
    @DisplayName("재고 차감 시나리오")
    @TestFactory
    Collection<DynamicTest> sockDeductionDynamicTest() throws Exception {
        //given
        Stock stock = Stock.create("001", 1);

        return List.of(
                DynamicTest.dynamicTest("재고를 주어진 개수만큼 차감할 수 있다.", () -> {
                    //given
                    int quantity = 1;

                    //when
                    stock.deductQuantity(quantity);

                    //then
                    assertThat(stock.getQuantity()).isZero();
                }),
                DynamicTest.dynamicTest("재고보다 많은 수의 수량으로 차감 시도하는 경우 예외가 발생한다.", () -> {
                    //given
                    int quantity = 1;

                    //when then
                    assertThatThrownBy(() -> stock.deductQuantity(quantity))
                            .isInstanceOf(IllegalArgumentException.class)
                            .hasMessage("차감할 재고 수량이 없습니다."); // 메세지 검증
                })
        );

    }

}
``` 

## 테스트 수행도 비용이다. 환경 통합하기
- 테스트를 작성하는 이유 중 하나는 사람이 수동하는 검증하는 비용보다 기계의 도움을 받아서 수시로 우리가 피드백을  
  받고 우리의 프로덕션 코드를 발전시켜 나갈 수 있는 도구
- 해서 테스트 자체를 자주 수행하려면 테스트가 수행되는 시간이 다 비용기 떄문에 관리가 필요하다.

```java
package sample.cafekiosk.spring;

import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.context.ActiveProfiles;
import sample.cafekiosk.spring.cliemt.mail.MailSendClient;

@ActiveProfiles("test")
@SpringBootTest
public abstract class IntegrationTestSupport {

    @MockBean
    protected MailSendClient mailSendClient;
}
``` 

```java
package sample.cafekiosk.spring;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;
import sample.cafekiosk.spring.api.controller.order.OrderController;
import sample.cafekiosk.spring.api.controller.product.ProductController;
import sample.cafekiosk.spring.api.service.order.OrderService;
import sample.cafekiosk.spring.api.service.product.ProductService;

@WebMvcTest(controllers = {
        OrderController.class,
        ProductController.class
})
public abstract class ControllerTestSupport {

    @Autowired
    protected MockMvc mockMvc;

    @Autowired
    protected ObjectMapper objectMapper;

    @MockBean
    protected OrderService orderService;

    @MockBean
    protected ProductService productService;
    
}
``` 

## Q. private 메서드의 테스트는 어떻게 하나요?
- 객체가 공개한 API들을 테스트하다 보면 자연스럽게 검증이 되기 떄문이다.  
  만약에 욕망이 강하게 든다 객체 분리의 신호롸 봐야함.

```java
package sample.cafekiosk.spring.api.service.product;

import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Component;
import sample.cafekiosk.spring.domain.product.ProductRepository;

@RequiredArgsConstructor
@Component
public class ProductNumberFactory {

    private final ProductRepository productRepository;

    public String createNextProductNumber() {
        String latestProductNumber = productRepository.findLatestProductNumber();
        if (latestProductNumber == null) {
            return "001";
        }
        int latestProductNumberInt = Integer.valueOf(latestProductNumber);
        int nextProductNumberInt = latestProductNumberInt + 1;

        return String.format("%03d", nextProductNumberInt);
    }
}
``` 

## Q. 테스트에서만 필요한 메서드가 생겼는데 프로덕션 코드에서는 필요 없다면?
- Test에서만 사용하는 ① ProductCreateRequest 생성자
- 있어도 상관없지만 **보수적**으로 접근하기!
```java
package sample.cafekiosk.spring.api.controller.product.request;

import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import sample.cafekiosk.spring.api.service.product.request.ProductCreateServiceRequest;
import sample.cafekiosk.spring.domain.product.Product;
import sample.cafekiosk.spring.domain.product.ProductSellingStatus;
import sample.cafekiosk.spring.domain.product.ProductType;

import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Positive;

@Getter
@NoArgsConstructor
public class ProductCreateRequest {

    @NotNull(message = "상품 타입은 필수입니다.")
    private ProductType type;

    @NotNull(message = "상품 판매상태는 필수입니다.")
    private ProductSellingStatus sellingStatus;

    @NotBlank(message = "상품 이름은 필수입니다.")
    private String name;

    @Positive(message = "상품 가격은 양수여야 합니다.")
    private int price;

    @Builder // ①
    private ProductCreateRequest(ProductType type, ProductSellingStatus sellingStatus, String name, int price) {
        this.type = type;
        this.sellingStatus = sellingStatus;
        this.name = name;
        this.price = price;
    }

    public ProductCreateServiceRequest toServiceRequest() {
        return ProductCreateServiceRequest.builder()
                .type(type)
                .sellingStatus(sellingStatus)
                .name(name)
                .price(price)
                .build();
    }
}
``` 

## 키워드 정리
- 테스트 하나 당 목적은 하나!
- 완벽한 제어
- 테스트 환경의 독립성, 테스트 간 독립성
- Test Fixture
- deleteAll(), deleteAllInBatch()
- @ParameterizedTest, @DynamicTest
- 수행 환경 통합하기
- private method test
- 테스트에서만 필요한 코드

### Reference
* [Practical Testing: 실용적인 테스트 가이드](https://inf.run/YLRXA)