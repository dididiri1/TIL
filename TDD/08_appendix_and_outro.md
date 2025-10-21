## 섹션 8 Appendix

- [Guava](https://github.com/google/guava)


## 학습 테스트
- 잘 모르는 기능, 라이브러리, 프레임워크를 학습하기 위해 작성하는 테스트
- 여러 테스트 케이스를 스스로 정의하고 검증하는 과정을 통해 보다 구체적인 동작과 기능을 학습할 수 있다.
- 관련 문서만 읽는 것보다 훨씬 재미있게 학습할 수 있다.

## Spring REST Docs
- 테스트 코드를 통한 API 문서 자동화 도구
- API 명세를 문서로 만들고 외부에 제공함으로써
- 협업을 원활하게 한다.
- 기본적으로 AsciiDoc을 사용하여 문서를 작성한다.

## REST Docs VS. Swagger
### REST Docs
#### 장점
- 테스트를 통과해야 문서가 만들어진다. (신뢰도가 높다.)
- 프로덕션 코드에 비침투적이다.
#### 단점
- 코드 양이 많다.
- 설정이 어렵다.

### Swagger
#### 장점
- 적용이 쉽다.
- 문서에서 바로 API 호출을 수행해볼 수 있다.
#### 단점
- 프로덕션 코드에 침투적이다.
- 테스트와 무관하기 때문에 신뢰도가 떨어질 수 있다.

### REST Docs 설치 방법
- build.gradle 추가
- Settings -> Plugins -> asciidoc 설치

#### 설정 1
```java
package sample.cafekiosk.spring.docs;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.restdocs.RestDocumentationContextProvider;
import org.springframework.restdocs.RestDocumentationExtension;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

import static org.springframework.restdocs.mockmvc.MockMvcRestDocumentation.documentationConfiguration;

@ExtendWith(RestDocumentationExtension.class)
@SpringBootTest
public abstract class RestDocsSupport {

    protected MockMvc mockMvc;

    @BeforeEach
    void setUp(WebApplicationContext webApplicationContext, RestDocumentationContextProvider provider) {
        this.mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext)
                .apply(documentationConfiguration(provider))
                .build();
    }
}
``` 

#### 설정 2
```java
package sample.cafekiosk.spring.docs;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.restdocs.RestDocumentationContextProvider;
import org.springframework.restdocs.RestDocumentationExtension;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;

import static org.springframework.restdocs.mockmvc.MockMvcRestDocumentation.documentationConfiguration;

@ExtendWith(RestDocumentationExtension.class)
public abstract class RestDocsSupport {

    protected MockMvc mockMvc;
    protected ObjectMapper objectMapper = new ObjectMapper();

    @BeforeEach
    void setUp(RestDocumentationContextProvider provider) {
        this.mockMvc = MockMvcBuilders.standaloneSetup(initController())
                .apply(documentationConfiguration(provider))
                .build();
    }

    protected abstract Object initController();
}
``` 

### 참고
- [Spring REST Docs](https://docs.spring.io/spring-restdocs/docs/current/reference/htmlsingle/)
- [Asciidoctor](https://asciidoctor.org/)

## 섹션 9 Outro

### 타엽하지 않는 마음
#### 가까이 보면 느리지만, 멀리보면 가장 빠르다.


### Reference
* [Practical Testing: 실용적인 테스트 가이드](https://inf.run/YLRXA)