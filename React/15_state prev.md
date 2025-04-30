# React 상태 업데이트에서 prev를 사용하는 이유

React에서 상태(state)를 업데이트할 때, 가끔 prev라는 이전 상태값을 사용하는 경우를 볼 수 있다. 
이 글에서는 prev를 사용하는 이유와, 사용하지 않을 때 생기는 문제점에 대해 알아보겠다.

## 달력 월을 바꾸는 코드 예시
```
const [pivotDate, setPivotDate] = useState(new Date());

const onDecreaseMonth = () => {
  setPivotDate((prev) => addMonths(prev, -1)); // ✅ 안전
};

const onIncreaseMonth = () => {
  setPivotDate(addMonths(pivotDate, 1));       // ✅ 간단하지만 주의 필요
};
```
여기서 prev는 이전 상태값을 의미하며, 함수형 업데이트 방식으로 React가 보장하는 최신 상태를 기반으로 
다음 상태를 계산할 수 있다.

## prev를 쓰지 않으면 문제가 생길 수 있는 경우
```
// ❌ 문제 발생 가능
const onDoubleDecreaseMonth = () => {
  setPivotDate(addMonths(pivotDate, -1));
  setPivotDate(addMonths(pivotDate, -1));
};
```
### 왜 문제가 생기나?
- pivotDate는 상태값인데, setPivotDate는 비동기적으로 동작
- 두 줄 모두 같은 pivotDate 값을 참조하게 됨
- 그래서 결과적으로 한 번만 감소된 상태가 적용됨
- 
## prev를 사용한 안전한 방식
```
const onDoubleDecreaseMonth = () => {
  setPivotDate((prev) => addMonths(prev, -1));
  setPivotDate((prev) => addMonths(prev, -1));
};
```
### 이렇게 하면?
- 첫 번째 줄: 이전 상태값 기준으로 한 달 감소
- 두 번째 줄: 방금 업데이트된 값 기준으로 또 한 달 감소
- 결과적으로 두 달이 정확히 감소됨

## 요약 정리

| 방식                         |       설명        | 안전성                |
|:---------------------------|:---------------:|:-------------------|
| setState(newValue)         |   상태값 직접 참조     | 비동기 특성상 연속 호출 시 위험 |
| setState(prev => newValue) | 이전 상태 기준으로 업데이트 | 안전하고 권장되는 방식       |


> 상태 업데이트가 이전 상태값을 기반으로 계산되어야 할 때는 무조건 prev를 사용하자.
> 특히 상태를 연속으로 바꾸거나, 동시에 여러 상태를 변경할 때는 prev가 안정성을 보장해준다.
> prev를 쓰는 방식은 리액트의 비동기 렌더링을 고려한 안전한 패턴이다.

