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

## 2.9) 배열 메서드 3. 배열 변형
### 5가지 배열 변형 메소드

### 1. filter
- 기존 배열에서 조건을 만족하는 요소들만 필터링하여 새로운 배열로 반환
```
let arr1 = [
  { name: "홍길동", hobby: "테니스" },
  { name: "김구라", hobby: "테니스" },
  { name: "춘식이", hobby: "독서" },
];

const tennisPeople = arr1.filter((item) => item.hobby === "테니스");

console.log(tennisPeople);

```

### 2. map
- 배열의 모든 요소를 순회하면서, 각각 콜백함수를 실행하고 그 결과값들을 모아서 새로운 배열로 반환
```
let arr2 = [1, 2, 3];
const mapResult1 = arr2.map((item, idx, arr) => {
  console.log(idx, item);
  return item * 2;
});

console.log(mapResult1); // [2, 4, 6]

let names = arr1.map((item) => item.name);
console.log(names);
```

### 3. sort
- 배열을 사전순으로 정렬하는 메서드
- 숫자로 크기순으로 정렬식 조건을 만들어줘야됨.
```
let arr3 = [10, 3, 5];
arr3.sort((a, b) => {
  if (a > b) {
    // b가 a 앞에 와라 양수 반환
    return 1;
  } else if (a < b) {
    return -1;
  } else {
    return 0;
  }
});

console.log(arr3); // [3, 5, 10]
```

### 4. toSorted
- 원본 배열은 납두고 정렬된 새로운 배열을 반환하는 메소드
```
let arr5 = ["c", "a", "b"];
const sorted = arr5.toSorted();

console.log(arr5);
console.log(sorted);
```

### 5. join
- 배열의 모든 요소를 하나의 문자열로 합쳐서 반환하는 메소드
```
let arr6 = ["hi", "im", "winterload"];
const loined = arr6.join(",");
console.log(arr6); // hi,im,winterload
```

## 2.10) Date 객체와 날짜
### 1. Date 객체를 생성하는 방법
```
let date1 = new Date(); // 생성자
console.log(date1);

let date2 = new Date("1997-01-07 10:10:10");
console.log(date2);
```

### 2. 타임 스탬프
- 특정 시간이 "1970.01.01 00시 00분 00초"로 부터 몇 ms가 지났는지를 의미하는 숫자값
```
let ts1 = date1.getTime();
let date4 = new Date(ts1);

console.log(date4);
```

### 3. 시간 요소들을 추출하는 방법
```
let year = date1.getFullYear();
let month = date1.getMonth() + 1;
let date = date1.getDate();

let hour = date1.getHours();
let minute = date1.getMinutes();
let seconds = date1.getSeconds();
```

###  4. 시간을 수정하기
```
date1.setFullYear(2023);
date1.setMonth(2); // 자바스크립트 달은 0부터 시작함. 3월
date1.setDate(30);
date1.setHours(23);
```

### 5. 시간을 여러 포맷으로 출력하기
```
console.log(date1.toDateString());
console.log(date1.toLocaleDateString());
```
