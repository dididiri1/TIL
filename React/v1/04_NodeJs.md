# 섹션 3

## Node.js
#### Node.js의 탄생과정
자바스크립트 코드는 브라우저 내장 자바스크립트 엔진을 이용하여 실행되므로 웹브라우저에서만  
실행가능했으나 C++로 개발되어 브라우저 외의 환경에서도 작동가능한 크롬의 V8 엔진을 사용하여  
브라우저 뿐만 아닌 다른 환경에서도 자바스크립트를 사용할 수 있게 되었는데 이것이 Node.js 이다.

> node.js
> -크롬 v8 엔진으로 만들어짐
> -자바스크립트의 실행환경 (자바스크립트 런타임)
 

## 3.2) Node.js 설치하기

node.js를 별도의 프로그램으로 사용하기 위해서는 아래 공식 홈페이지에 접속하여 다운로드 후 설치하면 된다.

https://nodejs.org/ko

```
node -v
```

### NPM(Node Package Manager) 설치 확인하기

아래 링크에 들어가 필요한 라이브러리를 탐색하고 설치할 수 있다.

https://www.npmjs.com/

```
npm -v
```

## 3.3) Node.js 사용하기

### 프로젝트
- 특정 목적을 갖는 프로그램의 단위
### 패키지
- Node.js에서 사용하는 프로그램의 단위 
### Node.js 패키지 만들기
- VsCode로 루트 폴더 열고 터미널 열기 (command + J)

### 패키지 초기화
```
npm init
```

### 패키지 스크립트
- 일종의 매크로. 아래와 같은 간단한 실행이 가능함
- 파일과 경로가 복잡해질 경우 script라는 패키지의 기능을 이용해서 한 방에 복잡한 경로에 있는 파일도 노드를 통해 쉽게 실행이 가능함


## 3.4) Node.js 모듈 시스템 이해하기

![](https://github.com/dididiri1/TIL/blob/main/React/images/03_02.png?raw=true)

### 모듈 시스템
- 보통은 여러 가지의 기능을 구현해야 된다라고 하면 기능별로 파일을 나눠서 개발
- 이때 이렇게 각각의 js파일들을 모듈이라는 이름으로 부름
- 모듈을 생성하고, 불러오고 사용하는 등의 모듈을 다루는 다양한 기능을 제공하는 시스템을 모듈 시스템이라고 함
- JS에는 다양한 모듈 시스템이 존재하나, 강의에서는 가장 대표적인 두가지를 다룸

![](https://github.com/dididiri1/TIL/blob/main/React/images/03_01.png?raw=true)

### CommonJS
- 모듈이라는 내장 객체에 export라는 프로퍼티의 값으로 객체를 저장
- 내보내진 값은 다른 모듈에서 내장 함수인 require를 이용해서 모듈의 경로를 인수로 전달하면서 불러와 사용이 가능함
- npm run start

#### math.js
```
// math 모듈

function add(a, b) {
  return a + b;
}

function sub(a, b) {
  return a - b;
}

module.exports = {
  add,
  sub,
};

```
#### index.js
```
const moduleData = require("./math");

console.log(moduleData.add(1, 2));
console.log(moduleData.sub(1, 2));

// 객체의 구조분해 할당 사용
const { add, sub } = require("./math");

console.log(add(1, 2));
console.log(sub(1, 2));
```

### ES 모듈 시스템

```
{
  "name": "section03",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node src/index.js"
  },
  "author": "",
  "license": "ISC",
  "description": "",
  "type": "module"
}

```
- package.json에 "type": "module"이 추가
- ES 모듈 시스템과 CommonJS와 함께 사용할 수 없음
- 어떤 값을 내보낼 때 export 키워드 뒤에 객체를 리터럴로 생성해서 내보내고 싶은 값을 담아 주면 됨

#### math.js
```
// math 모듈

function add(a, b) {
  return a + b;
}

export function sub(a, b) {
  return a - b;
}

export { add };

export default function multply(a, b) {
  return a * b;
}
```

#### index.js
```
import { add, sub } from "./math.js";

import multply from "./math.js";

console.log(add(1, 2));
console.log(sub(1, 2));
console.log(multply(1, 2));

```

## 3.5) Node.js 라이브러리 사용하기
### 라이브러리란?
- 프로그램을 개발할 때 필요한 다양한 기능들을 미리 만들어 모듈화 해 놓은 것

### 라이브러리 사용하기
- https://www.npmjs.com
- randomcolor 검색

#### Install
```
npm i randomcolor
```

- package.json에 다음 항목이 추가됨
```
"dependencies": {
   "randomcolor": "^0.6.2"
}
```

- node_modules 폴더와 package-lock.json 파일이 추가됨
  - node_modules : 실제 설치된 라이브러리의 저장소
  - package.json : 대략적인 버전이 명시됨, ^ : 버전 레인지
  - package-lock.json : 실제로 설치된 버전의 정보가 명시가 된다.

```
import randomColor from "randomcolor";

const color = randomColor();
console.log(color);
```

- 만약에 node_modules나 package-lock.json 파일이 없어졌다면 
- npm i로 새롭게 다시 다운로드 받을 수 있음 package.jsondp 기록된 라이브러리들을 다시 설치 해준다.


### Reference
* [한 입 크기로 잘라 먹는 리액트(React.js) : 기초부터 실전까지](https://inf.run/FiFhg)

