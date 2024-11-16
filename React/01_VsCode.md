# 섹션2 JavaScript 기본

## 1.2) VsCode 설치하기

### VS Code용 한국어 팩 설치
![](https://github.com/dididiri1/TIL/blob/main/React/images/01_01.png?raw=true)

### 유용한 확장 플러그인 설치

### Prettier - 자동 정렬 
- Prettier 설치


![](https://github.com/dididiri1/TIL/blob/main/React/images/01_02.png?raw=true)

- command + , 설정 
- format on save 검색
- Editor: Format On Save 체크
![](https://github.com/dididiri1/TIL/blob/main/React/images/01_03.png?raw=true)

### Material Icon Theme 설치

![](https://github.com/dididiri1/TIL/blob/main/React/images/01_04.png?raw=true)

### Error Lens 설치

![](https://github.com/dididiri1/TIL/blob/main/React/images/01_05.png?raw=true)


### Live Server
![](https://github.com/dididiri1/TIL/blob/main/React/images/01_06.png?raw=true)

## 1.3) 자바스크립트 실습 환경 설정하기

![](https://github.com/dididiri1/TIL/blob/main/React/images/01_07.png?raw=true)

### command + 쉬픝트 + P
![](https://github.com/dididiri1/TIL/blob/main/React/images/01_08.png?raw=true)

## 1.4) 변수와 상수

``` js
// 1. 변수
let age = 27;
console.log(age);

age = 30;
console.log(age);

// 2. 상수
const birth = "2014.11.16";

// 3. 변수 명명규칙(네이밍 규칙)
// 3-1. $, _ 제외한 기호는 사용할 수 없다.

let $_name;

// 3-2. 숫자로 시작할 수 없다.
let name1;

// 3-3. 예약어를 사용할 수 없다.

// 4. 변수 명명 가이드
let salesCount = 1;
let refundCount = 1;
let totalSalesCount = salesCount - refundCount;
``` 

## 1.5) 자료형
``` js
// 1.  Number Tyoe
let num1 = 27;
let num2 = 1.5;
let num3 = -20;

// 사칙연산
console.log(num1 + num2);
console.log(num1 - num2);
console.log(num1 * num2);
console.log(num1 / num2);
console.log(num1 % num2);

let inf = Infinity;
let mInt = -Infinity;
let nan = NaN;

// 2. String Type
let myName = "홍길동";
let myLocation = "방학동";
let introduce = myName + myLocation;

// 백틱
let introduceText = `${myName}은 ${myLocation}에 거주합니다`;
console.log(introduceText);

// 3. Boolean Type
let isSwitchOn = true;
let isEmpty = false;

// 4. Null Type
let empty = null;

// 5. Undefind Type
let none;
console.log(none);
```

## 1.6) 형 변환
``` js
// 1. 묵시적 형 변환
// -> 자바스크립트 엔진이 알아서 형 변환 하는것

let num = 10;
let str = "20";

// 묵시적으로 num이 String으로 변환됨
const result = num + str;
console.log(result); // 1020

// 자바스크랍트 엔진이 오류를 발생시키지 않기 위해서 숫자를 문자열로 안목적으로 바꿈

// 2. 명시적 형 변환
// -> 프로그래머 내장함수 등을 이용해서 직접 형 변환을 명시
let str1 = "10";
let strToNum = Number(str1);
console.log(10 + strToNum); // 20

let str2 = "10게";
let strToNum2 = Number(str2);

console.log(strToNum2); // NaN

// 숫자값이 아닌 형태도 숫자값으로 변경 가능함
let strToNum3 = parseInt(str2);

console.log(strToNum3); // 10

// -> 숫자 -> 문자열
let num1 = 20;
let numToStr1 = String(num1);

console.log(numToStr1 + "입니다"); // 20입니다
```

## 1.7) 연산자 1
```
// 1. 대입 연산자
let var1 = 1;

// 2. 산술 연산자
let num1 = 3 + 2;
let num2 = 3 - 2;
let num3 = 3 * 2;
let num4 = 3 / 2;
let num5 = 3 % 2;

let num6 = 1 + 2 * 10;
console.log(num6);

// 3. 복합 대입 연산자
let num7 = 10;
num7 += 20;
num7 -= 20;
num7 *= 20;
num7 /= 20;
num7 %= 20;
console.log(num7);

// 4. 증감 연산자
let num8 = 10;
++num8;
num8++;

// 5. 논리 연산자
let or = true || false;

let and = true && false;

let not = !true;

// 6. 비교 연산자
let comp1 = 1 === 1; // === 값과 자료형 까지 비교함.(권장)
let comp2 = 1 == "1"; // == 두개는 값 자체만 비교 함.
let comp3 = 1 !== 2;
console.log(comp1, comp2, comp3); // true true false

let comp4 = 2 > 1;
let comp5 = 2 >= 1;

console.log(comp4, comp5); // true true
```

## 1.8) 연산자 2
```
// 1. null 병합 연산자
// -> 존재하는 값을 추려내는 기능
// -> null, undefined가 아닌 값을 찾아내는 연산자

let var1;
let var2 = 10;
let var3 = 20;

let var4 = var1 ?? var2;
console.log(var4); // 10

// 2. typeof 연산자
// -> 값의 타입을 문자열로 반환하는 기능을 하는 연산자

let var7 = 1;
var7 = "hello";

let t1 = typeof var7;
console.log(t1); // string

// 3. 삼항 연산자
// -> 항을 3개 사용하는 연산자
// -> 조건식을 이용해서 참, 거짓일 때의 값을 다르게 반환

let var8 = 10;

// 요구사항: 변수 res에 var8의 값이 짝수 -> "짝수", 홀수 -> "홀"
let res = var8 % 2 === 0 ? "짝수" : "홀수";
console.log(res); // 짝수
```
## 1.9) 조건문
```
// 1. if 조건문 (if문)
let num = 9;

if (num >= 10) {
  console.log("num은 10 이상입니다");
  console.log("조건이 참 입니다!");
} else {
  console.log("조건이 거짓입니다.");
}

// 2. Switch 문
// -> if문과 기능 자체는 동일
// -> 다수의 조건을 처리할 때 if보다 더 직관적이다.

let animal = "cat";

switch (animal) {
  case "cat": {
    console.log("고양이");
    break;
  }
  case "dog": {
    console.log("강아지");
    break;
  }
  case "bear": {
    console.log("곰");
    break;
  }
  default: {
    console.log("그런 동물은 없어요");
  }
}

```
## 1.10) 반복문
```
for (let i = 0; i < 5; i++) {
  console.log("반복!");

  if (i % 2 === 0) {
    continue;
  }
  console.log(i);

  if (i >= 5) {
    break;
  }
}

```

