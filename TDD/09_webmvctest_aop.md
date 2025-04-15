## WebMvcTest에서 AOP (@Aspect)가 안 걸리는 이유와 해결 방법

@WebMvcTest를 사용해서 컨트롤러 단위 테스트를 작성하는 중, AOP로 만든 유효성 검증 처리 로직이 전혀 동작하지 않는 문제를 겪었다.
Postman으로 테스트할 땐 잘 되는데, MockMvc 테스트에서는 유효성 검증 실패가 400이 아닌 201으로 응답 했다

그래서 이번 글에선 문제 원인과 해결 방법을 정리해보자!

### Controller 코드
``` 

@PostMapping("/api/v1/diary")
public ResponseEntity<?> createDiary(@RequestBody @Valid DiaryCreateRequest request, BindingResult bindingResult) {
    DiaryCreateResponse response = diaryService.createDiary(request);
    return new ResponseEntity<>(new ApiResponse<>(HttpStatus.CREATED.value(), "일기 등록 성공", response), HttpStatus.CREATED);
}
``` 

### AOP 로직
``` 
@Slf4j
@Component
@Aspect
public class BindingAspect {

    @Around("execution(* controller..api..*(..))")
    public Object validCheck(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        
        ``` 
        
        for (Object arg:args){
            if(arg instanceof BindingResult){
                BindingResult bindingResult = (BindingResult) arg;
                if(bindingResult.hasErrors()){
                    log.warn("❌ 검증 오류 발생: {}", bindingResult.getFieldErrors());

                    ``` 
                }
            }
        }
        ``` 
    }
}
``` 
### TDD 코드
``` 
@DisplayName("새 일기를 등록시 컨텐츠는 필수값이다.")
@Test
void createDiaryWithContent() throws Exception {
    //given
    DiaryCreateRequest request = DiaryCreateRequest.builder()
                .build();

    //when //then
    mockMvc.perform(post("/api/v1/diary")
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(request))
            )
            .andDo(print())
            .andExpect(status().isBadRequest())
            .andExpect(jsonPath("$.state").value("400"))
            .andExpect(jsonPath("$.message").value("잘못된 요청입니다."));
    }
``` 

## ✅ 해결 방법
- AOP까지 정상적으로 적용되게 하려면 아래처럼 테스트를 변경하면 된다.
``` 
@SpringBootTest
@AutoConfigureMockMvc
class DiaryApiControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Autowired
    ObjectMapper objectMapper;

    @MockBean
    DiaryService diaryService;

    // ... 테스트 메서드
}
``` 

## 원인 분석
> @WebMvcTest는 Controller와 MVC 관련 빈만 슬라이스 로딩하기 때문에,
> @Component, @Aspect로 등록한 AOP 빈은 스프링 AOP 프록시로 엮이지 않음.
> 그래서 AOP가 아예 동작하지 않게 된 것이었다.

#### 📝 정리
| 항목               |        	@WebMvcTest         | @SpringBootTest                       |
|:-----------------|:---------------------------:|:--------------------------------------|
| 로딩 범위            | Controller + MVC 관련 Bean만   | 전체 ApplicationContext                 |
| AOP 적용 여부        |      ❌ 기본적으로 안 됨 (제한적)      | ✅ 모든 AOP 적용됨                          |
| MockMvc 사용 가능 여부 |            ✅ 가능             | ✅ 가능 (@AutoConfigureMockMvc 필요)       |
| 목적               |      빠른 Web Layer 테스트       | 통합 테스트 (AOP, Security, Validation 포함) |


