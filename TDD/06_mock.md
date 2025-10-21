## 섹션 6 Mock을 마주하는 자세

![](https://github.com/dididiri1/TIL/blob/main/TDD/images/06_01.png?raw=true)

### JPQL
```java
@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {

    @Query("select o from Order o where o.registeredDateTime >= :startDateTime" +
            " and o.registeredDateTime < :endDateTime" +
            " and o.orderStatus = :orderStatus")
    List<Order> findOrdersBy(LocalDateTime startDateTime, LocalDateTime endDateTime, OrderStatus orderStatus);

}
``` 

### Native Query
```java
@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {

    @Query(value = "select o from Order o where o.registeredDateTime >= :startDateTime" +
            " and o.registeredDateTime < :endDateTime" +
            " and o.orderStatus = :orderStatus", nativeQuery = true)
    List<Order> findOrdersBy(LocalDateTime startDateTime, LocalDateTime endDateTime, OrderStatus orderStatus);
    
}
``` 

### Stubbing(스터빙)
- Mock객체의 when 메소드를 이용한 stubbing 사용법
``` java
Mockito.when(mailSendClient.sendEmail(any(String.class), any(String.class), any(String.class), any(String.class)))
                .thenReturn(true);
```
|        메소드         |                    기능                     |
|:------------------:|:-----------------------------------------:|
|     thenReturn     |      스터빙한 메소드 호출 후 어떤 객체를 리턴할 건지 정의       |
|     thenThrow      | 스터빙한 메소드 호출 후 어떤 Exception을 Throw할 건지 정의  |
| thenCallRealMethod |                 실제 메소드 호출                 |

> 참고 : OrderStatistService.java 메일 전송 로직에는 @Transactional 안하는게 좋음   
>       이메일 전송 같이 긴 네트워크 작업이 있는 로직은 불필요함.

## Test Double
### Dummy
- 아무 것도 하지 않는 깡통 객체

### Fake
- 단순한 형태로 동일한 기능은 수행하나, 프로덕션에서 쓰기에는 부족한 객체

### Spy
- Stub이면서 호출된 내용을 기록하여 보여줄 수 있는 객체
- 일부는 실제 객체처럼 동작시키고 일부만 Stubbing할 수 있다.

### Stub 행위 검증 (Behavior Verification)
- 테스트에서 요청한 것에 대해 미리 준비한 결과를 제공하는 객체 그 외에는 응답하지 않는다.

### Mock 상태 검증 (State Verification)
- 행위에 대한 기대를 명세하고, 그에 따라 동작하도록 만들어진 객체

## 순수 Mockito로 검증해보기

### Mockito 검증 방법
|       메소드       |        기능         |
|:---------------:|:-----------------:|
|  times(int n)   |   몇번 호출 했는지 검증    |
|     never()     | 한 번도 호출 되지 않는지 검증 |
|  atLeastOne()   | 최소 한번은 호츨 됬는지 검증  |
| atLeast(int n)  |  최소 n번 호출 됬는지 검증  |
|  atMostOnce()   | 최대 한번은 호출 됬는지 검증  |
|  atMost(int n)  |  최대 n번 호출 됬는지 검증  |
|  calls(int n)   |   n번이 호출 됬는지 검증   |
|     only()      | 해당 메소드만 실행 됬는지 검증 |
| timeout(long n) |      타임아웃 검증      |
|  after(long n)  |     걸리는 시간 검증     |
| description() |     실패 문구 검증      |
| InOrder |       순서 검증       |

### @Mock
```java
package sample.cafekiosk.spring.api.service.mail;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.Spy;
import org.mockito.junit.jupiter.MockitoExtension;
import sample.cafekiosk.spring.cliemt.mail.MailSendClient;
import sample.cafekiosk.spring.domain.history.mail.MailSendHistory;
import sample.cafekiosk.spring.domain.history.mail.MailSendHistoryRepository;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class MailServiceTest {

    @Mock
    private MailSendClient mailSendClient;

    @Mock
    private MailSendHistoryRepository mailSendHistoryRepository;

    @InjectMocks
    private MailService mailService;

    @DisplayName("메일 전송 테스트")
    @Test
    void sendMail() throws Exception {
        //given
        when(mailService.sendMail(any(String.class), any(String.class), any(String.class), any(String.class))).thenReturn(true);

        //when
        boolean result = mailService.sendMail("", "", "", "");

        //then
        assertThat(result).isTrue();
        Mockito.verify(mailSendHistoryRepository, times(1)).save(any(MailSendHistory.class));
    }
}
``` 

### @Spy
```java
package sample.cafekiosk.spring.api.service.mail;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.Spy;
import org.mockito.junit.jupiter.MockitoExtension;
import sample.cafekiosk.spring.cliemt.mail.MailSendClient;
import sample.cafekiosk.spring.domain.history.mail.MailSendHistory;
import sample.cafekiosk.spring.domain.history.mail.MailSendHistoryRepository;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class MailServiceTest {

    @Spy
    private MailSendClient mailSendClient;

    @Mock
    private MailSendHistoryRepository mailSendHistoryRepository;

    @InjectMocks
    private MailService mailService;

    @DisplayName("메일 전송 테스트")
    @Test
    void sendMail() throws Exception {
        //given
        doReturn(true)
                .when(mailSendClient)
                .sendEmail(any(String.class), any(String.class), any(String.class), any(String.class));


        //when
        boolean result = mailService.sendMail("", "", "", "");

        //then
        assertThat(result).isTrue();
        Mockito.verify(mailSendHistoryRepository, times(1)).save(any(MailSendHistory.class));
    }
}
``` 

### BDDMockito
- Mockito랑 기능은 같은 하지만 given 있어서 직관적임
```java
package sample.cafekiosk.spring.api.service.mail;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.BDDMockito;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.junit.jupiter.MockitoExtension;
import sample.cafekiosk.spring.cliemt.mail.MailSendClient;
import sample.cafekiosk.spring.domain.history.mail.MailSendHistory;
import sample.cafekiosk.spring.domain.history.mail.MailSendHistoryRepository;

import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.times;

@ExtendWith(MockitoExtension.class)
class MailServiceTest {

    @Mock
    private MailSendClient mailSendClient;

    @Mock
    private MailSendHistoryRepository mailSendHistoryRepository;

    @InjectMocks
    private MailService mailService;

    @DisplayName("메일 전송 테스트")
    @Test
    void sendMail() throws Exception {
        //given
        BDDMockito.given(mailSendClient.sendEmail(any(String.class), any(String.class), any(String.class), any(String.class)))
                .willReturn(true);

        //when
        boolean result = mailService.sendMail("", "", "", "");

        //then
        assertThat(result).isTrue();
        Mockito.verify(mailSendHistoryRepository, times(1)).save(any(MailSendHistory.class));
    }
}
``` 

### Classicist VS. Mockist

#### Classicist
- Classicist는 전통적인 테스트 방법이다. 코드가 동작하는지 확인하기 위해 실제 코드와 상호 작용하고 테스트하는 것에 중점을 줌
- 꼭 필요할때만 Mocking를 사용하자!

#### Mockist
- Mockist 테스트는 종종 테스트 더블(test doubles)라고 알려진 가짜 객체를 사용하여 외부 의존성을 대하며, 의존성이 예상대로 상호 작용하는지 검증

#### 언제 Mocking을 써야 되나~?
- 외부 시스템일 경우 Mocking 처리 하는게 좋타!
  ![](https://github.com/dididiri1/TIL/blob/main/TDD/images/06_02.png?raw=true)

### 키워드 정리
- Test Double, Stubbing
    - dummmy, fake, stub, spy, mock
- @Mock, @MockBean, @Spy, @SpyBean, @InjectMocks
- BDDMockito
- Classicist VS. Mockist

### Reference
* [Practical Testing: 실용적인 테스트 가이드](https://inf.run/YLRXA)