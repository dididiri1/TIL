# Date 라이브러리 - dafe-fns

![](https://github.com/dididiri1/TIL/blob/main/React/images/24_01.png?raw=true)

date-fns는 자바스크립트에서 날짜와 시간을 처리할 때 유용한 기능들을 제공하는 라이브러리입니다. 최신 기능인 국제화도 지원하며, 
다양한 환경에서 다재다능하게 활용할 수 있습니다. 또한 TypeScript를 완벽히 지원하고, 빠른 속도와 가벼운 크기로 성능 면에서도 
뛰어난 라이브러리입니다.

### 😩 기존 방식: 직접 날짜 계산 (노가다 코드)
```
export const getMonthlyData = (pivotDate: Date, data: Diary[]) => {
  const beginDate = new Date(
    pivotDate.getFullYear(),
    pivotDate.getMonth(),
    1,
    0,
    0,
    0
  ).getTime();

  const endDate = new Date(
    pivotDate.getFullYear(),
    pivotDate.getMonth() + 1,
    0,
    12,
    59,
    59
  ).getTime();

  return data.filter(
    (item) => beginDate <= item.createdDate && item.createdDate <= endDate
  );
};
```

### 1. 설치
```
npm install date-fns
```
### 2. 리팩토링된 getMonthlyData
```
import { startOfMonth, endOfMonth } from "date-fns";

export const getMonthlyData = (pivotDate: Date, data: Diary[]) => {
  const beginDate = startOfMonth(pivotDate).getTime();
  const endDate = endOfMonth(pivotDate).getTime();

  return data.filter(
    (item) => beginDate <= item.createdDate && item.createdDate <= endDate
  );
};
```

## Tree Shaking 활용

### 기존 예시
```
  const [pivotDate, setPivotDate] = useState(new Date());

  const onDecreaseMonth = () => {
    setPivotDate(new Date(pivotDate.getFullYear(), pivotDate.getMonth() - 1));
  };

  const onIncreaseMonth = () => {
    setPivotDate(new Date(pivotDate.getFullYear(), pivotDate.getMonth() + 1));
  };
```

### Tree Shaking으로 개선한 코드
```
  const [pivotDate, setPivotDate] = useState(new Date());
  // 현재 월 기준으로 이전/다음 월 계산
  const onDecreaseMonth = () => {
    setPivotDate((prev) => subMonths(prev, 1));
  };
    
  const onIncreaseMonth = () => {
    setPivotDate((prev) => addMonths(prev, 1));
  };
```


