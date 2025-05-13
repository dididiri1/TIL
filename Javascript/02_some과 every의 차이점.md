# [JS] some() vs every() 차이점 정리
자바스크립트의 some()과 every()는 배열을 순회하면서 조건 검사를 수행할 때 유용하게 사용하는 메서드이다.
두 메서드는 동작 방식이 비슷해 보이지만, 결과에 대한 조건이 다르다.

## ✅ 1. some()
some()은 배열 요소 중 **하나라도 조건을 만족하면** true를 반환한다.  
조건을 만족하는 요소를 찾으면 즉시 **순회를 중단**합니다.
```  
const numbers = [1, 2, 3, 4, 5];

const hasEven = numbers.some((num) => num % 2 === 0);
console.log(hasEven); // true (2가 짝수니까)
```  

### 📌 요약 
- 모든 요소가 조건을 만족해야 true
- 하나라도 만족하지 않으면 false
- 조건 불일치 시 즉시 순회 종료

## ✅ 2. every()
every()는 배열의 **모든 요소가 조건을 만족해야만** true를 반환한다.  
**조건을 하나라도 만족하지 않으면** 즉시 false를 반환하고 순회를 중단합니다.

```  
const scores = [15, 20, 18, 25];

const allAboveTen = scores.every((score) => score > 20);
console.log(allAboveTen); // true
```  
### 📌 요약
- 모든 요소가 조건을 만족해야 true
- 하나라도 만족하지 않으면 false
- 조건 불일치 시 즉시 순회 종료

### 🧠 정리
> 
> - some()은 하나라도 만족하면 true → "하나라도 있으면 돼?"  
> - every()는 전부 만족해야 true → "전부 다 맞는지 확인해!"
> 
> 둘 다 조건에 따라 조기 종료되어 성능적으로 효율적이며,  
> 배열을 순회하면서 조건 검사할 때 매우 유용합니다.