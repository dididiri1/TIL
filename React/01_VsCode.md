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