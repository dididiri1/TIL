## 섹션 3 TDD: Test Driven Development

### TDD: Test Driven Development
- 프로덕션 코드보다 테스트 코드를 먼저 작성하여 테스트가 구현 과정을 주도하도록 하는 방법론

- RED: 실패하는 테스트 작성
- GREEN: 테스트 통과 최소한의 코딩
- REFACTOR: 구현 코드 개선 테스트 통과 유지

![](https://github.com/dididiri1/TIL/blob/main/TDD/images/02_01.gif?raw=true)

### 선 기능 구현, 후 테스트 작성

- 테스트 자체의 누락 가능성
- 특정 테스트(해피 케이스) 케이스만 검증할 가능성
- 잘못된 구현을 다소 늦게 발견할 가능성
- 과감한 리펙토링이 가능해진다.

### 선 테스트 작성, 후 기능 구현

- 복잡도가 낮은, 테스트 가능한 코드로 구현할 수 있게 한다.
- 쉽게 발견하기 어려운 엣지(Edge) 케이스를 놓치지 않게 해준다.
- 구현에 대한 빠른 피드백을 받을 수 있다.
- 과감한 리팩토링이 가능해진다.


### TDD: 관점의 변화

![](https://github.com/dididiri1/TIL/blob/main/TDD/images/03_01.png?raw=true)

**클라이언트** 관점에서의 **피드백**을 주는 Test Driven

### Reference
* [Practical Testing: 실용적인 테스트 가이드](https://inf.run/YLRXA)