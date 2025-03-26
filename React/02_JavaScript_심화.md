# JavaScript 심화

## 2.1) Truthy & Falsy
![](https://github.com/dididiri1/TIL/blob/main/React/images/02_01.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_02.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_03.png?raw=true)

### 1. Falsy
```
let f1 = undefined;
let f2 = null;
let f3 = 0;
let f4 = -0;
let f5 = NaN;
let f6 = "";
let f7 = 0n;

if (!f1) {
  console.log("falsy");
}
```

### 2. Truuthy
```
let t1 = "hello";
let t2 = 123;
let t3 = [];
let t4 = {};
let t5 = () => {};

if (t1) {
  console.log("Truthy");
}
```

### 3. 활용 사례
```
function printName(person) {
  if (person === undefined || person === null) {
    console.log("pserson의 값이 없음");
    return;
  }

  console.log(person.name);
}

let person = null;
printName(person);
```

```
function printName(person) {
  if (!person) {
    console.log("pserson의 값이 없음");
    return;
  }

  console.log(person.name);
}

let person = { name : "홍길동" };
printName(person);
```
> 객체 값들은 모두 다 truthy 이기 떄문에 조건식이 거짓이 되어 프로퍼티에 접근할 수 있고
> undefined, null 등은 Falsy 이기 떄문에 조건이 참이 되어 pserson의 값이 없음을 출력 한다.

## 2.2) 구조 분해 할당

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_05.png?raw=true)

### 1. 배열의 구조 분해 할당
```
let arr = [1, 2, 3];

let [one, two, three, four = 4] = arr;
console.log(one, two, three, four) // 1 2 3 4
```

### 2. 객체의 구조 분해 할당
```
let person = {
  name: "홍길동",
  age: 7,
  hobby: "헬스",
};

let { name, age, hobby, extra } = person;
console.log(name, age, hobby, extra); // 홍길동 7 헬스 undefined
```

### 3. 객체 구조 분해 할당을 이용해서 함수의 매개변수를 받는 방법
```
const func = ({ name, age, hobby }) => {
  console.log(name, age, hobby);
};

func(person);
```