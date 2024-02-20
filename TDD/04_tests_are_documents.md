## 섹션 4 테스트는 문서다.

### 문서?

- 프로덕션 기능을 설명하는 테스트 코드 문서
- 다양한 테스트 케이스를 통해 프로덕션 코드를 이해하는 시각과 관점을 보완
- 어느 한 사람이 과거에 경험했던 고민의 결과물을 팀 차원으로 승격시켜서, 모두의 자산으로 공유할 수 있다.
-

### DisplayName 설정

``` java
class CafeKioskTest {

    @DisplayName("음료 1개 추가하면 주문 목록에 담긴다.")
    @Test
    void add() {
        CafeKiosk cafeKiosk = new CafeKiosk();
        cafeKiosk.add(new Americano());

        assertThat(cafeKiosk.getBeverages()).size().isEqualTo(1);
        assertThat(cafeKiosk.getBeverages()).hasSize(1);
        assertThat(cafeKiosk.getBeverages().get(0).getName()).isEqualTo("아메리카노");

    }
}
```

![](https://github.com/dididiri1/TIL/blob/main/TDD/images/04_01.png?raw=true)

### DisplayName을 섬세하게

- 명사의 나열보다 문장으로 - A이면 B이다. A이면 B가 아니고 C다
> → 음료 1개 추가 테스트  
> → **음료를 1개 추가할 수 있다.**

- 테스트 행위에 대한 결과까지 기술하기
> → 음료를 1개 추가할 수 있다.  
> → **음료를 1개 추가하면 주문 목록에 담긴다.**

- 도메인 용어를 사용하여 한층 추상화된 내용을 담기 (메서드 자체의 관점보다 도메인 정책 관점으로)
- 테스트의 현상을 중점으로 기술하지 말 것
> → 특정 시간 이전에 주문을 생성하면 실패한다.  
> → **영업 시작 시간 이전에는 주문을 생성할 수 없다.**

### BDD (Behavior Driven Development)

- TDD에서 파생된 개발 방법
- 함수 단위의 테스트에 집중하기보다, 시나리오에 기반한 테스트케이스(TC) 자체에 집중하여 테스트한다.
- 개발자가 아닌 사람이 봐도 이해할 수 있을 정도의 추상화 수준(레벨)을 권장

### Given / When / Then

- Given : 시나리오 진행에 필요한 모든 준비 과정(객체, 값, 상태 등)
    - 어떤 환경에서
- When : 시나리오 행동 진행
    - 어떤 행동을 진행했을 때
- Then : 시나리오 진행에 대한 결과 명시, 검증
    - 어떤 상태 변화가 일어난다.

### Reference
* [Practical Testing: 실용적인 테스트 가이드](https://inf.run/YLRXA)