# 섹션 6.


## 5.1) 실습 준비하기

### 1. ESLint 설치
- 오류가 발생할 만한 코드가 있으면 경고를 띄어주는 프로그램
![](https://github.com/dididiri1/TIL/blob/main/React/images/06_01.png?raw=true)

###  1.1 설치 후 eslint.config.js 파일 rules에 맨 아래 두줄 추가

```
   ... 
    
   rules: {
      ...js.configs.recommended.rules,
      ...react.configs.recommended.rules,
      ...react.configs["jsx-runtime"].rules,
      ...reactHooks.configs.recommended.rules,
      "react/jsx-no-target-blank": "off",
      "react-refresh/only-export-components": [
        "warn",
        { allowConstantExport: true },
      ],
      "no-unused-vars": "off",
      "react/prop-types": "off",
    },
```

- "no-unused-vars" : " off" => 사용되지 않는 변수에 대해 에러를 출력하지 않도록 함

- "react/prop-types" : "off" => React 컴포넌트에서 prop-types 사용 여부를 검사하지 않도록 설정


## 5.2) React 컴포넌트
#### Root 컴포넌트(App.jsx)
```
import "./App.css";

const Header = () => {
  return (
    <header>
      <h1>header</h1>
    </header>
  );
};

function App() {
  return (
    <>
      <Header />
      <h1>안녕 리엑트!</h1>
    </>
  );
}

export default App;

```
> App은 부모컴포넌트라고 할 수 있으며, Header는 자식 컴포넌트라고 할 수 있다.
> 부모 컴포넌트 : 다른 컴포넌트를 포함하거나 호출하여 화면에 렌더링 하는 컴포넌트
> 자식 컴포넌트 : 부모 컴포넌트에서 호출되어 렌더링되는 컴포넌트. 필요하면 부모로부터 데이터를 받을 수 있다.

### 컴포넌트를 각각의 파일로 분리하기

#### App.jsx
```
import "./App.css";
import Header from "./components/Header";
import Main from "./components/Main";
import Footer from "./components/Footer";

function App() {
  return (
    <>
      <Header />
      <Main />
      <Footer />
    </>
  );
}

export default App;

```
#### Header.jsx
```
const Header = () => {
  return (
    <header>
      <h1>header</h1>
    </header>
  );
};

export default Header;

```

## 5.3) JSX로 UI 표현하기

### 1. JSX란?
- JavaScript Extensions: 확장된 자바 스크립트 문법을 말함
- JavaScript와 HTML을 혼용해서 사용할수 있다.
- React.js는 JSX 문법 사용

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_02.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_03.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_04.png?raw=true)

### 2. JSX 주의사항
- 중괄호 내부에는 자바스크립트 표현식만 넣을 수 있다.
  - 조건문, 반복문 X
  - 삼항연산자(연산식), 변수명, 숫자 O
```
// 조건문 반복문 불가 (에러)
{if() {} }
{for() {} }

// 삼항 연산자 및 연산식 가능
{number%2 === 0 ? "짝수" : "홀수"}
{number + 10}
```
- 숫자, 문자열, 배열 값만 렌더링 된다. 
  - obj 객체를 렌더링시 에러 obj.a은 가능
```
// 렌더링 가능
{10}
{number}
{[1,2,3]}
{obj.a}

// 렌더링 불가한 것 (에러는 안뜨지만 웹에 보이지 않음)
{true}
{undefined}
{null}

// 렌더링 불가 (에러로 백지됨)
{obj]
```
- 모든 태그는 닫혀있어야 한다.
```
<h1> </h1> // 가능
<img> // 불가
<img/> // 셀프 클로징 가능
<img></img> // 가능
```

- 최상위 태그는 반드시 하나여야만 한다.
  - 최상위 태그를 만들고 싶지 않다면, 그냥 빈 태그를 사용해도 됨 <> </>
```
const Main = () => {
  const number = 10;

  return (
    <>
      <main>
        <h1>main</h1>
        <h2>{number + 10}</h2>
        {number}
        {[1,2]}
      </main>
    </>
  );
};

export default Main;

```

### 3. JSX로 조건에 따른 로그인, 로그아웃 렌더링 해보기
- 삼항연산자
```
const Main = () => {
  const user = {
    name: "이정현",
    isLogin: true,
  };

  return <>{user.isLogin ? <div>로그아웃</div> : <div>로그인</div>}</>;
};

export default Main;

```

- IF문
```
const Main = () => {
  const user = {
    name: "이정현",
    isLogin: true,
  };

  if (user.isLogin) {
    return <div>로그아웃</div>;
  } else {
    return <div>로그인</div>;
  }
};

export default Main;
```

### 4. JSX 스타일 설정
- Main.css
```
.logout {
  background-color: red;
  border-bottom: 5px solid green;
}

```
```
import "./Main.css";

const Main = () => {
  const user = {
    name: "홍길동",
    isLogin: true,
  };

  if (user.isLogin) {
    return <div className="logout">로그아웃</div>;
  } else {
    return <div>로그인</div>;
  }
};

export default Main;
```

## 5.4) Props로 데이터 전달하기

### 1. Props란?
- Props: 컴포넌트에 전달된 값들 (Properties)
- Props를 쓰는 이유: 컴포넌트를 함수 호출하듯 전달하는 값에 따라 각각 다른 UI를 렌더링 하도록 만들 수 있음
- 컴퍼넌트에 전달된 값들을 특별히 props라고 부른다.

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_05.png?raw=true)


![](https://github.com/dididiri1/TIL/blob/main/React/images/06_06.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_07.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_08.png?raw=true)

### 2. Button Props 만들어보기

```
const Button = (props) => {
  console.log(props);
  return <button style={{ color: props.color }}>{props.text}</button>;
};

Button.defaultProps = {
  color: "black",
};

export default Button;

```
### 3 버튼을 text로 전달해서 렌더링 해보기
```
import "./App.css";
import Button from "./components/Button";

function App() {
  return (
    <>
      <Button text={"메일"} color={"red"} />
      <Button text={"카페"} />
      <Button text={"블로그"} />
    </>
  );
}

export default App;

```

### 4. Props를 편리하게
-  JS값 뿐만 아니라, 리액트 컴포넌트나 HTML요소도 전달 가능!
```
const Button = ({ text, color, children }) => {
  return <button style={{ color: color }}>{text}</button>;
};

Button.defaultProps = {
  color: "black",
};

export default Button;


```

## 5.5) 이벤트 처리하기

### 버튼 클릭 만들기
```
const Button = ({ text, color, children }) => {
  // 이벤트 객체
  const onClickButton = (e) => {
    console.log(e);
    console.log(text);
  };

  return (
    <button onClick={onClickButton} style={{ color: color }}>
      {text}
    </button>
  );
};

Button.defaultProps = {
  color: "black",
};

export default Button;

```


![](https://github.com/dididiri1/TIL/blob/main/React/images/06_09.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_10.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_11.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_12.png?raw=true)

## 5.6) State로 상태관리하기

```
import "./App.css";
import { useState } from "react";

function App() {
  const [count, countState] = useState(0);
  const [light, setLight] = useState("OFF");

  return (
    <>
      <div>
        <h1>{light}</h1>
        <button
          onClick={() => {
            setLight(light === "ON" ? "OFF" : "ON");
          }}
        >
          {light === "ON" ? "끄기" : "켜기"}
        </button>
      </div>
      <div>
        <h1>{count}</h1>
        <button
          onClick={() => {
            countState(count + 1);
          }}
        >
          +
        </button>
      </div>
    </>
  );
}

export default App;

```

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_13.png?raw=true)


## 5.7) State와 Props 

```
import "./App.css";
import { useState } from "react";

const Bulb = ({ light }) => {
  console.log(light);
  return (
    <div>
      {light === "ON" ? (
        <h1 style={{ backGroundColor: "orange" }}>ON</h1>
      ) : (
        <h1 style={{ backGroundColor: "gray" }}>OFF</h1>
      )}
    </div>
  );
};

function App() {
  const [count, countState] = useState(0);
  const [light, setLight] = useState("OFF");

  return (
    <>
      <div>
        <Bulb light={light} />
        <button
          onClick={() => {
            setLight(light === "ON" ? "OFF" : "ON");
          }}
        >
          {light === "ON" ? "끄기" : "켜기"}
        </button>
      </div>
      <div>
        <h1>{count}</h1>
        <button
          onClick={() => {
            countState(count + 1);
          }}
        >
          +
        </button>
      </div>
    </>
  );
}

export default App;

```
### 리엑트 컴포넌트 리렌더링이 발생하느 3가지 상황
- 첫 번째는 자신이 관리하는 state의 값이 변경될 때
- 두 번째는 제공받은 props의 값이 변경될 때
- 마지막으로 부모 컴포넌트가 리렌더링되면 자식 컴포넌트도 리랜더링 된다.  
  그래서 현재 이  Bulb 컴포넌트가 계속 리렌더링되고 있다.

### 컴포넌트로 분리
> 이러한 경우 관련 없는 두개의 state를 하나의 컴포넌트에 몰아 넣기보다는 서로 
> 다른 컴포넌트로 분리해주는것이 좋다. 

```
import "./App.css";
import { useState } from "react";

const Bulb = () => {
  const [light, setLight] = useState("OFF");

  console.log(light);
  return (
    <div>
      {light === "ON" ? (
        <h1 style={{ backGroundColor: "orange" }}>ON</h1>
      ) : (
        <h1 style={{ backGroundColor: "gray" }}>OFF</h1>
      )}

      <button
        onClick={() => {
          setLight(light === "ON" ? "OFF" : "ON");
        }}
      >
        {light === "ON" ? "끄기" : "켜기"}
      </button>
    </div>
  );
};

const Counter = () => {
  const [count, countState] = useState(0);

  return (
    <div>
      <h1>{count}</h1>
      <button
        onClick={() => {
          countState(count + 1);
        }}
      >
        +
      </button>
    </div>
  );
};

function App() {
  const [count, countState] = useState(0);

  return (
    <>
      <Bulb />
      <Counter />
    </>
  );
}

export default App;

```

### JSX로 컴포넌트 분리
#### Bulb.jsx
```
import {useState} from 'react';
const Bulb = () => {
    const [light, setLight] = useState("OFF");
  
    console.log(light);
    return (
      <div>
        {light === "ON" ? (
          <h1 style={{ backGroundColor: "orange" }}>ON</h1>
        ) : (
          <h1 style={{ backGroundColor: "gray" }}>OFF</h1>
        )}
  
        <button
          onClick={() => {
            setLight(light === "ON" ? "OFF" : "ON");
          }}
        >
          {light === "ON" ? "끄기" : "켜기"}
        </button>
      </div>
    );
  };

  export default Bulb;
```

#### Counter.jsx
```
import { useState } from "react";

const Counter = () => {
  const [count, countState] = useState(0);

  return (
    <div>
      <h1>{count}</h1>
      <button
        onClick={() => {
          countState(count + 1);
        }}
      >
        +
      </button>
    </div>
  );
};

export default Counter;

```
#### App.jsx
```
import "./App.css";
import { useState } from "react";
import Bulb from "./components/Bulb";
import Counter from "./components/Counter";

function App() {
  const [count, countState] = useState(0);

  return (
    <>
      <Bulb />
      <Counter />
    </>
  );
}

export default App;

```

## 5.8) State로 사용자 입력 관리하기 1
```
import { useState } from "react";

// 간단한 회원가입 폼
// 1. 이름
// 2. 생년월일
// 3. 국적
// 4. 자기소개

const Register = () => {
  const [name, setName] = useState("이름");
  const [brith, setBrith] = useState("");
  const [country, setCountry] = useState("");
  const [bio, setBio] = useState("");

  const onChangeName = (e) => {
    setName(e.target.value);
  };

  const onChangeBrith = (e) => {
    setBrith(e.target.value);
  };

  const onChangeCountry = (e) => {
    console.log(e.target.value);
    setCountry(e.target.value);
  };

  const onChangeBio = (e) => {
    console.log(e.target.value);
    setBio(e.target.value);
  };

  return (
    <div>
      <div>
        <input value={name} onChange={onChangeName} placeholder={"이름"} />
      </div>
      <div>
        <input
          type="date"
          value={brith}
          onChange={onChangeBrith}
          placeholder={"날짜"}
        />
        {brith}
      </div>
      <div>
        <select value={country} onChange={onChangeCountry}>
          <option value="kr">한국</option>
          <option value="us">미국</option>
          <option value="uk">영국</option>
        </select>
        {country}

        <div>
          <textarea value={bio} onChange={onChangeBio}></textarea>
        </div>
      </div>
    </div>
  );
};

export default Register;

```

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_14.png?raw=true)

## 5.9) State로 사용자 입력 관리하기
### 모든 스테이트들을 하나의 객체로 묶기
```
import { useState } from "react";

// 간단한 회원가입 폼
// 1. 이름
// 2. 생년월일
// 3. 국적
// 4. 자기소개

const Register = () => {
  const [input, setInput] = useState({
    name: "",
    birth: "",
    country: "",
    bio: "",
  });

  const onChangeName = (e) => {
    setInput({
      ...input,
      name: e.target.value,
    });
  };

  const onChangeBrith = (e) => {
    setInput({
      ...input,
      birth: e.target.value,
    });
  };

  const onChangeCountry = (e) => {
    setInput({
      ...input,
      country: e.target.value,
    });
  };

  const onChangeBio = (e) => {
    setInput({
      ...input,
      bio: e.target.value,
    });
  };

  return (
    <div>
      <div>
        <input
          value={input.name}
          onChange={onChangeName}
          placeholder={"이름"}
        />
      </div>
      <div>
        <input
          type="date"
          value={input.brith}
          onChange={onChangeBrith}
          placeholder={"날짜"}
        />
        {input.brith}
      </div>
      <div>
        <select value={input.country} onChange={onChangeCountry}>
          <option value="kr">한국</option>
          <option value="us">미국</option>
          <option value="uk">영국</option>
        </select>
        {input.country}

        <div>
          <textarea value={input.bio} onChange={onChangeBio}></textarea>
        </div>
      </div>
    </div>
  );
};

export default Register;


```

### 통합 이벤트 핸들러 묶기
```
import { useState } from "react";

// 간단한 회원가입 폼
// 1. 이름
// 2. 생년월일
// 3. 국적
// 4. 자기소개

const Register = () => {
  const [input, setInput] = useState({
    name: "",
    birth: "",
    country: "",
    bio: "",
  });

  const onChange = (e) => {
    setInput({
      ...input,
      [e.target.name]: e.target.value,
    });
  };

  return (
    <div>
      <div>
        <input
          name="name"
          value={input.name}
          onChange={onChange}
          placeholder={"이름"}
        />
      </div>
      <div>
        <input
          name="brith"
          type="date"
          value={input.brith}
          onChange={onChange}
          placeholder={"날짜"}
        />
        {input.brith}
      </div>
      <div>
        <select name="country" value={input.country} onChange={onChange}>
          <option value="kr">한국</option>
          <option value="us">미국</option>
          <option value="uk">영국</option>
        </select>
        {input.country}

        <div>
          <textarea name="bio" value={input.bio} onChange={onChange}></textarea>
        </div>
      </div>
    </div>
  );
};

export default Register;

```

## 5.10) useRef로 컴포넌트의 변수 생성하기

### useRef란?
useReference의 줄임말로 컴퍼넌트 내부에 새로운 레퍼런스 객체를 생성해주는 기능이다.  
이 레퍼런스 객체는 컴퍼넌트 내부의 변수로서 일반적인 값들을 저장할 수 있다.   
useRef를 이용하면 컴퍼넌트가 렌더링하는 특정 DOM 요소에 접근할 수 있고, 해당 요소를 조작하는 것도 가능하다.
```
const refObject = useRef();
```

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_15.png?raw=true)


![](https://github.com/dididiri1/TIL/blob/main/React/images/06_16.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_17.png?raw=true)


![](https://github.com/dididiri1/TIL/blob/main/React/images/06_18.png?raw=true)


![](https://github.com/dididiri1/TIL/blob/main/React/images/06_19.png?raw=true)

### useRef 는 초기 값도 설정가능하고, 레퍼런스 객체의 값을 사용하고 싶다면 점 표기법을 사용해서 출력도 가능하다.

```
// 초기 값 설정
const refObj = useRef(0); // {current:0}

// 레퍼런스 객체 값
console.log(refObj.current); // 0
```

### useRef의 활용 사례
- 컴포넌트 내부에서 렌더링에 영향을 미치지 않아야 되는 변수를 생성할 때 활용할 수 있다. (예: count 기능)  
- 컴포넌트가 렌더링하고 있는 DOM 요소들을 직접 조작할 때에도 활용할 수 있다. 아래에 onSubmit 함수에서는       
  사용자가 이름을 입력받는 input 태그에 정확히 입력했는지 확인하고 빈 문자열이면 해당 태그에 focus 가 되도록 조작할 것이다.
```
import { useState, useRef } from "react";

// 간단한 회원가입 폼
// 1. 이름
// 2. 생년월일
// 3. 국적
// 4. 자기소개

const Register = () => {
  const [input, setInput] = useState({
    name: "",
    birth: "",
    country: "",
    bio: "",
  });

  const countRef = useRef(0);
  const inputRef = useRef();

  const onChange = (e) => {
    countRef.current++;
    console.log(countRef.current);

    setInput({
      ...input,
      [e.target.name]: e.target.value,
    });
  };

  const onsubmit = () => {
    if (input.name === "") {
      // 이름을 입력하는 DOM 요소 포커스
      inputRef.current.focus();
    }
  };

  return (
    <div>
      <div>
        <input
          ref={inputRef}
          name="name"
          value={input.name}
          onChange={onChange}
          placeholder={"이름"}
        />
      </div>
      <div>
        <input
          name="brith"
          type="date"
          value={input.brith}
          onChange={onChange}
          placeholder={"날짜"}
        />
        {input.brith}
      </div>
      <div>
        <select name="country" value={input.country} onChange={onChange}>
          <option value="kr">한국</option>
          <option value="us">미국</option>
          <option value="uk">영국</option>
        </select>
        {input.country}

        <div>
          <textarea name="bio" value={input.bio} onChange={onChange}></textarea>
        </div>
      </div>

      <button onClick={onsubmit}>제출</button>
    </div>
  );
};

export default Register;

```

## 5.11) React Hooks
### React Hooks 특징
> 💡 함수 컴포넌트에서도 클래스 컴포넌트의 기능을 낚아채듯이 가져와 사용할수 있게해주는     
> React Hooks 기능을 개발하게 된다. 지난 시간까지 살펴봤던 useState나 useRef 같은 이러한  
> 리엑트의 내장 함수들을 모두 React Hooks이다. 그래서 useState는 state라는 기능을 낚아채오는  
> Hook이었고 uefRef는 Reference라는 기능을 낚아채오는 Hook이었다.

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_20.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_21.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_22.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_23.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/06_24.png?raw=true)

### 1. 함수 컴포넌트, 커스텀 훅 내부에서만 호출 가능
```
import { useState } from "react";

//const state = useState(); // error

const HookExam = () => {
  const state = useState();

  return <div>hookexam</div>;
};

export default HookExam;

```

### 2. 조건부나 반복문에서는 호출이 불가능하다.
- 내부에서 Hook을 호출하게 되면 서로 다른 hook 들의 호출 순서가 엉망이 되어 버린 현상 발생 오류
```
import { useState } from "react";

const HookExam = () => {
  
    if (true) {
        const state = useState();
    }

    for(;;) {
        // 
    }

  return <div>hookexam</div>;
};

export default HookExam;
```
  
### 3. 나만의 훅(Custom Hook)을 직접 만들 수 있다.
- 보통 이런 방식으로 state를 통해서 사용자의 입력을 처리함.
- 아래 코드에서 input 이 많이 생긴다면 각각의 state와 이벤트 핸들러를 생성해야 할 것이다. 
- 이럴 때 중복으로 작성하지 않고 별도의 함수로 만들면 훨씬 편리하다.
```
import { useState } from "react";

const HookExam = () => {
  const [input, setInput] = useState("");

  const onChange = (e) => {
    setInput(e.target.value);
  }
    

  return <div><input value={input} onChange={onChange}/></div>;
};

export default HookExam;

```
### 커스텀 훅 만들어서 분리하기!
- use라는 접두사를 사용하는 함수를 커스텀 훅이라고 판단하게 된다.
```
import { useState } from "react";

function useInput() {
  const [input, setInput] = useState("");

  const onChange = (e) => {
    setInput(e.target.value);
  };

  return [input, setInput];
}

const HookExam = () => {
  const [input, onChange] = useInput();

  return (
    <div>
      <input value={input} onChange={onChange} />
    </div>
  );
};

export default HookExam;

```

### Hooks 별도 폴더를 만들어서 관리
```
import useInput from "./../hooks/useInput";

const HookExam = () => {
  const [input, onChange] = useInput();

  return (
    <div>
      <input value={input} onChange={onChange} />
    </div>
  );
};

export default HookExam;
```
#### useInput.jsx
```
import { useState } from "react";

function useInput() {
  const [input, setInput] = useState("");

  const onChange = (e) => {
    setInput(e.target.value);
  };

  return [input, setInput];
}

export default useInput;

```

### Reference
* [한 입 크기로 잘라 먹는 리액트(React.js) : 기초부터 실전까지](https://inf.run/FiFhg)



