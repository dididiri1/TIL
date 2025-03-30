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

## 2.3) 단락 평가
- 단락평가를 이용하면 앞으로는 조건문을 이용하지 않고도 특정 상황에서 어떠한 함수를 호출하지 않도록 방지해 주거나 또는  
- 어떠한 값들을 굳이 계산하지 않도록 제한하는 등의 아주 다양한 기능들을 개발할 수 있다.

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_04.png?raw=true)
```
function returnFalse() {
  return false;
}

function returnTrue() {
  return true;
}

console.log(returnFalse() && returnFalse());

console.log(returnFalse() || returnFalse());

// 단락 평가 활용 사례
function printName(person) {
  const name = erson && person.name;
  console.log(name || "person의 값이 없음");
}

printName();
printName({ name: "홍길동" });
```

## 2.4) 구조 분해 할당

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

## 2.4) Spread 연산자와 Rest 매개변수

### 1. Spread 연산자
- Spread : 흙뿌리다, 펼치다 라는 뜻
- 객체나 배열에 저장된 여러개의 값을 개별로 뿌려주는 역할

```
let arr1 = [1, 2, 3];

let arr2 = [4, arr1[0], arr1[1], arr1[2], 5, 6];
console.log(arr2);

let arr3 = [4, ...arr1, 5, 6];
console.log(arr3); // [4, 1, 2, 3, 5, 6]

let obj1 = {
  a: 1,
  b: 2,
};

let obj2 = {
  ...obj1,
  c: 3,
  d: 4,
};

console.log(obj2); // {a: 1, b: 2, c: 3, d: 4}

function funA(p1, p2, p3) {
  console.log(p1, p2, p3);
}

funA(...arr1); // 1 2 3
```

### 2. Rest 매개변수
- Rest는 나머지, 나머지 매개변수
- Rest 변수의 이름은 아무거나 사용해도 상관없고 ...만 붙으면 됨.
```
function funcB(one, ...rest) {
  console.log(rest); // (2) [2, 3]
}

funcB(...arr1); 
```

## 2.5) 원시타입 vs 객체 타입

> 원시타입은 값 자체로서 변수에 저장이되고 복사가 되어 변수의 값을 수정하더라고 메모리에 저장된
> 원본 데이터는 수정되지 않기 때문에 이러한 값들을 ***불변값***이라고 부른다. 반면 객체 타입의 경우에는
> 값은 별도로 메모리 공간에 따로 보관해두고, 참조 값을 통해서 변수에 저장되고 또 복수가 되기 때문에
> 특정 프로퍼티의 값을 수정하게 되면 메모리에 저장된 원본 데이터 자체가 수정되어서 이러한 객체 타입의
> 값들을 ***가변값***이라고 부른다.

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_06.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_07.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_08.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_09.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_10.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_11.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_12.png?raw=true)

## 2.6) 반복문으로 배열과 객체 순회하기

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_13.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_14.png?raw=true)


### 1. 배열 순회
```
let arr = [1, 2, 3];
```
#### 1.1 배열 인덱스
```
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]);
}
```

#### 1.2 for of 반복문
```
for (let item of arr) {
  console.log(item);
}
```


### 2. 객체 순회
```
let person = {
  name: "홍길동",
  age: 27,
  hobby: "헬스",
};
```

#### 2.1 Object.key 사용
- 객체에서 key 값들만 뽑아서 새로운 배열로 반환
```
let keys = Object.keys(person);

for (let i = 0; i < keys.length; i++) {
  console.log(keys[i]);
}

for (let key of keys) {
  const value = person[key];
  console.log(key, value);
}
```

#### 2.2 Object.values
- 객체에서 vaule 값들만 뽑아서 새로운 배열로 반환
```
for (let value of values) {
  console.log(value);
}
```


#### 2.3 for in
```
for (let key in person) {
  const value = person[key];
  console.log(key, value);
}

```기


