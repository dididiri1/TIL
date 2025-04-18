
## 1.12) 함수 표현식과 화살표 함수

### 1. 함수 표현식
```
function funcA() {
  console.log("funcA");
}

let varA = funcA;
console.log(varA);

let varB = function funcB() {
  console.log("funcB");
};

varB();
// funcB ❌
```

### 2. 화살표 함수
```
let varC = () => {
  return 1;
};

let varD = (value) => value + 1;

let varF = (value) => {
  return value + 1;
};
```

## 1.13) 콜백함수

![](https://github.com/dididiri1/TIL/blob/main/React/images/01_09.png?raw=true)


### 1. 콜백 함수
```
function main(value) {
  value();
}

main(() => {
  console.log("i am sub");
});
```

### 2. 콜백함수의 활용
```
function repeat(count, callback) {
  for (let idx = 1; idx <= count; idx++) {
    callback(idx);
  }
}

repeat(5, (idx) => {
  console.log(idx);
});

repeat(5, (idx) => {
  console.log(idx * 2);
});
```

## 1.14) 스코프

![](https://github.com/dididiri1/TIL/blob/main/React/images/01_10.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/01_11.png?raw=true)

### 스코프
- 전역(전체 영역) 스코프 / 지역(특정 영역) 스코프
- 전역 스코프 : 전체 영역에서 접금 가능
- 지역 스코프 : 특정 영역에서만 접근 가능
```
let a = 1; // 전역 스코프
function funcA() {
  let b = 2;
  console.log(a);
}

funcA();
console.log(b); ❌
```

```
let a = 1; // 전역 스코프
function funcA() {
  let b = 2;
  console.log(a);
}

funcA();

if (true) {
  let c = 1;
}

for (let i = 0; i < 10; i++) {
  let d = 1;
}
```
## 1.15) 객체 1


## 1.17) 배열
### 1. 객체 생성
```
let obj1 = new Object();
let obj2 = {};
```

### 2. 객체 프로퍼티 (객체 속성)
```
let person = {
  name: "홍길동",
  age: 27,
  habby: "헬스",
};
```

### 3. 객체 프로피티를 다루는 방법
#### 3.1 특정 프로퍼티 접근 (점 표기법, 괄호 표기법)
```
let name = person.name;
console.log(name);

let age = person["age"];

console.log(age);

let property = "hobby";
let hobby = person[property];

console.log(hobby);
```

### 3.1 새로운 프로퍼티를 추가하는 방법
```
person.job = "developer";
person["food"] = "감자탕";

console.log(person);
```

### 3.3 프로퍼티를 수정하는 방법
```
person.job = "educator";
person["food"] = "삼겹살";

console.log(person);
```

### 3.4 프로퍼티를 삭제하는 방법
```
delete person.job;
delete person["food"];

console.log(person);
```

### 3.5 프로퍼티의 존재 유무를 확인하는 방법 (in 연산자)
```
let result1 = "name" in person;
let result2 = "cat" in person;

console.log(result1); // true
console.log(result2); // false
```


### 1. 배열 생성
```
let arrA = new Array();
let arrB = [];

let arrC = [1, 2, 3, true, "hello", () => {}, {}, []];
```

### 2. 배열 요소 접근
```
let item1 = arrC[0];
let item2 = arrC[1];

accC[0] = "hello";
console.log(item1, item2);
```


