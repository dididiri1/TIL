# 섹션 10 useReducer

## 9.1) useReducer를 소개 합니다.

![](https://github.com/dididiri1/TIL/blob/main/React/images/10_01.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/10_02.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/10_03.png?raw=true)

#### Exam.jsx
``` 
import { useReducer } from "react";

// reducer : 변환기
// -> 상태를 실제로 변화시키는 변환기 역할

function reducer(state, action) {
  if (action.type === "INCREASE") {
    return state + action.data;
  } else if (action.type === "DECREASE") {
    return state - action.data;
  }

  // switch문이 일반적으로 많이 씀.
  switch (action.type) {
    case "INCREASE":
      return state + action.data;
    case "DECREASE":
      return state + action.data;
    default:
      state;
  }
}

const Exam = () => {
  // dispatch : 발송하다, 급송하다
  // -> 상태 변화가 있어야 한다는 사실을 알리는, 발송하는 함수
  const [state, dispath] = useReducer(reducer, 0);

  const onClickPlus = () => {
    // 인수: 상태가 어떻케 변화되길 원하는지
    // -> 액션 객체
    dispath({
      type: "INCREASE",
      data: 1,
    });
  };

  const onClickMinus = () => {
    dispath({
      type: "DECREASE",
      data: 1,
    });
  };

  return (
    <div>
      <h1>{state}</h1>
      <button onClick={onClickPlus}>+</button>
      <button onClick={onClickMinus}>-</button>
    </div>
  );
};

export default Exam;

``` 

## 9.2) 투투리스트 업그레이드
``` 
import "./App.css";
import { useState, useRef, useReducer } from "react";
import Header from "./components/Header";
import Editor from "./components/Editor";
import List from "./components/List";
import Exam from "./components/Exam";

const mockData = [
  {
    id: 0,
    isDone: false,
    content: "React 공부하기",
    date: new Date().getTime(),
  },
  {
    id: 1,
    isDone: false,
    content: "책 읽기",
    date: new Date().getTime(),
  },
  {
    id: 2,
    isDone: false,
    content: "헬스장 가기",
    date: new Date().getTime(),
  },
];

function reducer(state, action) {
  switch (action.type) {
    case "CREATE":
      return [action.data, ...state];
    case "UPDATE":
      return state.map((item) =>
        item.id === action.targetId ? { ...item, isDone: !item.isDone } : item
      );
    case "DELETE":
      return state.filter((item) => item.id !== action.targetId);
    default:
      return state;
  }
}

function App() {
  //const [todos, setTodos] = useState(mockData);
  const [todos, dispatch] = useReducer(reducer, mockData);

  const idRef = useRef(3);

  const onCreate = (content) => {
    dispatch({
      type: "CREAtE",
      data: {
        id: idRef.current++,
        isDone: false,
        content: content,
        date: new Date().getTime(),
      },
    });
  };

  const onUpdate = (targetId) => {
    dispatch({
      type: "UPDATE",
      targetId: targetId,
    });
  };

  const onDelete = (targetId) => {
    dispatch({
      type: "DELETE",
      targetId: targetId,
    });
  };

  return (
    <div className="App">
      <Header />
      <Editor onCreate={onCreate} />
      <List todos={todos} onUpdate={onUpdate} onDelete={onDelete} />
    </div>
  );
}

export default App;

``` 