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

```

## 2.7) 배열 메서드1. 요소 조작

### 1. push()
- 배열의 맨 뒤에 새로운 요소를 추가
- 기존 배열을 변경하며, 새로운 배열 길이를 반환
```
let arr = [1, 2, 3];
arr.push(4, 5, 6);

console.log(arr); // [1, 2, 3, 4, 5, 6]
```

### 2. pop()
- 배열의 맨 뒤 요소를 제거하고 반환
- 기존 배열이 변경됨
```
let arr = [1, 2, 3];
const poppedItem = arr.pop();

console.log(poppedItem); // 3
console.log(arr); // [1, 2]
```

### 3. shift()
- 배열의 맨 앞 요소를 제거하고 반환
- 기존 배열이 변경됨
```
let arr = [1, 2, 3];
const shiftedItem = arr.shift();

console.log(shiftedItem); // 1
console.log(arr); // [2, 3]
```

### 4. unshift()
- 배열의 맨 앞에 새로운 요소를 추가
- 기존 배열을 변경하며, 새로운 배열 길이를 반환
```
let arr = [1, 2, 3];
const newLength = arr.unshift(0);

console.log(newLength); // 4
console.log(arr); // [0, 1, 2, 3]

```

### 5. slice()
- 배열의 특정 범위를 잘라서 새로운 배열로 반환
- 기존 배열은 변경되지 않음
- slice(start, end) 형식으로 사용하며, end는 포함되지 않음
```
let arr = [1, 2, 3, 4, 5];

console.log(arr.slice(2, 5)); // [3, 4, 5]
console.log(arr.slice(2)); // [3, 4, 5]
console.log(arr.slice(-3)); // [3, 4, 5]
console.log(arr); // [1, 2, 3, 4, 5] (원본 유지)
```

### 6. concat()
- 두 개 이상의 배열을 합쳐서 새로운 배열을 반환
- 기존 배열은 변경되지 않음
```
let arr1 = [1, 2];
let arr2 = [3, 4];

let mergedArr = arr1.concat(arr2);

console.log(mergedArr); // [1, 2, 3, 4]
console.log(arr1, arr2); // 원본 배열 유지
```

## 2.8) 배열 메서드2. 순회와 탐색

### 1. forEach
- 모든 요소를 순회하면서, 각각의 요소에 특정 동작을 수행시키는 메서드
```
let arr1 = [1, 2, 3];

arr1.forEach(function (item, idx, arr) {
  console.log(idx, item * 2);
});

let doubleArr = [];

arr1.forEach((item) => {
  doubleArr.push(item * 2);
});

console.log(doubleArr); // [2, 4, 6]
```

### 2. includes()
- 배열에 특정 요소가 포함되어 있는지 불리언 값(true/false) 반환
```
let arr2 = [1, 2, 3];
let isInclude = arr2.includes(3);

console.log(isInclude);
```

### 3. indexOf()
- 특정 요소의 인덱스(위치)를 반환
- 요소가 없으면 -1을 반환
```
let arr3 = [1, 2, 3];
let index = arr3.indexOf(2);

console.log(index); // 1
```

### 4. findIndex()
- 모든 요소를 순회하면서 콜백 함수를 만족하는 첫 번째 요소의 인덱스 반환
- 요소가 없으면 -1 반환
```
let arr4 = [1, 2, 3];

const findIndex = arr4.findIndex((item) => {
  if (item === 2) return true;
});

console.log(findIndex); // 1

const findIndex2 = arr4.findIndex((item) => item % 2 !== 0);
console.log(findIndex2); // 0 (첫 번째 홀수인 1의 인덱스)
```

### 5. indexOf vs findIndex
- indexOf()는 단순 값 비교(얕은 비교) 로 요소를 찾음
- findIndex()는 콜백 함수 조건을 통해 요소를 찾을 수 있음 (객체 탐색 가능)
```
let objectArr = [{ name: "춘식이" }, { name: "홍길동" }];

console.log(objectArr.indexOf({ name: "홍길동" })); // -1 (객체 비교 실패)

console.log(objectArr.findIndex((item) => item.name === "홍길동")); // 1 (홍길동의 인덱스)
```

> indexOf 메서드는 특정 값을 배열에서 찾을때 얕은 비교로만 진행하기 때문에 객체 값을 찾지 못하고
> 반변 findIndex 메서드는 콜백함수를 이용해서 우리가 작접 프로퍼티의 값을 기준으로 비교시킬 수가
> 있기 때문에 객체 값도 조건식만 잘 만들어 주면 쉽게 찾아낼 수 있다는 장점이 있다.

### 6. find()
- findIndex()와 유사하지만, 요소의 인덱스가 아닌 요소 자체를 반환
- 만족하는 요소가 없으면 undefined 반환
```
let arr5 = [{ name: "춘식이" }, { name: "홍길동" }];

const found = arr5.find((item) => item.name === "홍길동");

console.log(found); // { name: "홍길동" }
```

## 2.11) 동기와 비동기
```
console.log(1);

setTimeout(() => {
  console.log(2);
}, 3000);

console.log(3);
```

## 2.12) 비동기 작업 처리하기 1. 콜백함수
```
function orderFood() {
  setTimeout(() => {
    const food = "치킨";
    callback(food);
  }, 3000);
}

function cooldwonFood(food, callback) {
  setTimeout(() => {
    const cooldownedFood = `식은 ${food}`;
    callback(cooldownedFood);
  }, 3000);
}

function freezedFood(food) {
  setTimeout(() => {
    const freezedFood = `냉동된 ${food}`;
    callback(freezedFood);
  }, 1500);
}

orderFood((food) => {
  console.log(food);
  cooldwonFood(food, (cooldownedFood) => {
    console.log(cooldownedFood);

    freezedFood(cooldownedFood, (freezedFood) => {
      console.log(freezedFood);
    });
  });
});
```

## 2.13) 비동기 작업 처리하기 2. Promise
비동기 작업을 효율적으로 처리할 수 있도록 도와주는 자바스크립의 내장 객체

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_15.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_16.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_17.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_18.png?raw=true)

```
const promise = new Promise((resolve, reject) => {
  // 비동기 작업 실행하는 함수
  // executor

  setTimeout(() => {
    console.log("안녕");
    resolve("안녕");
  }, 2000);
});

setTimeout(() => {
  console.log(promise);
}, 3000);
```

![](https://github.com/dididiri1/TIL/blob/main/React/images/02_19.png?raw=true)

```
const promise = new Promise((resolve, reject) => {
  // 비동기 작업 실행하는 함수
  // executor

  setTimeout(() => {
    console.log("안녕");
    reject("실해했는지 이유...");
  }, 2000);
});

setTimeout(() => {
  console.log(promise);
}, 3000);
```
![](https://github.com/dididiri1/TIL/blob/main/React/images/02_20.png?raw=true)
> Executor 함수에서 Reject를 호출하게 되면 Promise의 비동기 작업이 실패하게 되는 거고 반대로 Resolve를
> 호출하게 되면 이 Promise의 비동기 작업이 성공하게 된다. 그리고 각각 Resolve와 Reject 함수 모두 인수로 
> Promise의 결과 값을 전달 할 수 있다.


### then 메소드 
- then 메소드는 프로미스의 비동기 작업이 성공했을 때만 호출되는 메소드이다.
```
const promise = new Promise((resolve, reject) => {
  // 비동기 작업 실행하는 함수
  // executor

  setTimeout(() => {
    const num = 10;

    if (typeof num === "number") {
      resolve(num + 10);
    } else {
      reject("num이 숫자가 아닙니다.");
    }
  }, 2000);
});

// then 메소드
// -> 그 후에
promise.then((value) => {
  console.log(value);
});


```

### catch 메소드
- catch 메소드는 반대로 비동기 작업이 실패 햇을 때만 호출되는 메소드이다.
```
promise.catch((error) => {
  console.log(error);
});
```

### Promise 체이닝
```
promise.then((value) => {
  console.log(value);
}).catch((error) => {
  console.log(error);
});
```

```
function add10(num) {
  const promise = new Promise((resolve, reject) => {
    setTimeout(() => {
      if (typeof num === "number") {
        resolve(num + 10);
      } else {
        reject("num이 숫자가 아닙니다.");
      }
    }, 2000);
  });

  return promise;
}

add10(0)
  .then((result) => {
    console.log(result);
    return add10(result);
  })
  .then((result) => {
    console.log(undefined);
  })
  .then((result) => {
    console.log(result);
  })
  .catch((error) => {
    console.log(error);
  });
```
```
10
20
num이 숫자가 아닙니다.
```

## 2.14) 비동기 작업 처리하기 3. Async Await
### Async
- 어떤 함수를 비동기 함수로 만들어주는 키워드
- 함수가 프로미스를 반환하도록 변환해주는 그런 키워드
```
async function getData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({
        name: "홍길동",
        id: "winterload",
      });
    }, 1500);
  });
}

console.log(getData());
```
```
Promise {<pending>}
    [[Prototype]]: Promise
    [[PromiseState]]: "fulfilled"
    [[PromiseResult]]: Object
```

### Await
- async 함수 내부에서만 사용이 가능 한 키워드

```
function printData() {
  getData().then((result) => {
    console.log(result);
  });
}

printData();

```
> 호출 결과가 Promise를 반환하니깐 then 메소드를 붙여서 result 매개변수로 받아오면서 이런식으로 작성 해줬어야 한다.

```
async function printData() {
  const data = await getData();
  console.log(data); // 1.5후에 {name: '홍길동', id: 'winterload'}
}

printData();
```
> async와 await를 이용하면 then 메소드 복잡하게 쓸 필요가 없다. then 메소드를 쓰지 않아도 알아서 getDdate 함수가 반환
> 하는 Promise가 종료 되기를 기다린다.

```
function printData() {
  const data = await getData();   // ❌
  console.log(data); // 1.5후에 {name: '홍길동', id: 'winterload'}
}

printData();
```
> 참고로 await 함수는 async가 붙지 않은 함수에서 사용하면 오류가 난다.

### Reference
* [한 입 크기로 잘라 먹는 리액트(React.js) : 기초부터 실전까지](https://inf.run/FiFhg)

