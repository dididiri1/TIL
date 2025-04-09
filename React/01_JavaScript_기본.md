
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





