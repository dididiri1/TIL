# 섹션 7. 프로젝트1. 카운터 앱

## 6.1) 프로젝트 소개 및 준비

```
"no-unused-vars": "off",
"react/prop-types": "off",
```

## 6.2) UI 구현하기
#### App.jsx
```
import "./App.css";
import Viewer from "./components/Viewer";
import Controller from "./components/Controller";

function App() {
  return (
    <div className="App">
      <h1>Simple Controller</h1>
      <section>
        <Viewer />
      </section>
      <section>
        <Controller />
      </section>
    </div>
  );
}

export default App;

```

### App.css
```
body {
  padding: 20px;
}

.App {
  margin: 0 auto;
  width: 400px;
}

.App > section {
  background-color: rgb(245, 245, 245);
  border: 1px solid rgb(240, 240, 240);
  border-radius: 5px;
  padding: 20px;
  margin-bottom: 10px;
}

```

### Viewer.jsx
```
const Viewer = () => {
  return (
    <div>
      <div>현재 카운트 :</div>
      <h1>0</h1>
    </div>
  );
};

export default Viewer;

```

### Controller.jsx
```
const Controller = () => {
  return (
    <div>
      <button>-1</button>
      <button>-10</button>
      <button>-100</button>
      <button>+100</button>
      <button>+10</button>
      <button>+1</button>
    </div>
  );
};

export default Controller;

```

## 6.3) 기능 구현하기
> 💡 props라는 건 기본적으로 부모에서 자식으로만 전달할 수 있기 떄문에 두 컴퍼넌트 전달을 불가능 하지만  
> 앱 컴포넌트는 두 컴포넌트의 부모 컴포넌트아기 떄문에 두 컴포넌트 모두에게 props로 값을 전달 할 수 있다.  
> 따라서 countState는 viewer에게, 그리고 setCount함수는 별도의 이벤트 핸드러로 마들어서 컨트롤러 컴퍼넌트에게   
> 전달해 주면 된다.


![](https://github.com/dididiri1/TIL/blob/main/React/images/07_01.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/07_02.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/07_03.png?raw=true)

#### App.jsx
```
import "./App.css";
import Viewer from "./components/Viewer";
import Controller from "./components/Controller";
import { useState } from "react";

function App() {
  const [count, setCount] = useState(0);

  const onClickButton = (value) => {
    setCount(count + value);
  };

  return (
    <div className="App">
      <h1>Simple Controller</h1>
      <section>
        <Viewer count={count} />
      </section>
      <section>
        <Controller onClickButton={onClickButton} />
      </section>
    </div>
  );
}

export default App;

```

#### Viewer.jsx
```
const Viewer = ({ count }) => {
  return (
    <div>
      <div>현재 카운트 :</div>
      <h1>{count}</h1>
    </div>
  );
};

export default Viewer;

```

#### Controller.jsx
```
const Controller = ({ onClickButton }) => {
  return (
    <div>
      <button
        onClick={() => {
          onClickButton(-1);
        }}
      >
        -1
      </button>
      <button
        onClick={() => {
          onClickButton(-10);
        }}
      >
        {" "}
        -10
      </button>
      <button
        onClick={() => {
          onClickButton(-100);
        }}
      >
        {" "}
        -100
      </button>
      <button
        onClick={() => {
          onClickButton(+100);
        }}
      >
        +100
      </button>
      <button
        onClick={() => {
          onClickButton(+10);
        }}
      >
        +10
      </button>
      <button
        onClick={() => {
          onClickButton(+1);
        }}
      >
        {" "}
        +1
      </button>
    </div>
  );
};

export default Controller;

```



### State Lifting (State 끌어올리기)
- 결론적으로 리엑트에서는 props라니느 기능을 이용해서 부모에서 자식 방향으로만 데이터를 전달할수 있음!

![](https://github.com/dididiri1/TIL/blob/main/React/images/07_04.png?raw=true)

### Reference
* [한 입 크기로 잘라 먹는 리액트(React.js) : 기초부터 실전까지](https://inf.run/FiFhg)

