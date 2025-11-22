# 섹션9. 프로젝트2. 투두리스트

## 8.3) Create - 투두 추가하기

> 주의: todos 배열에 새로운 아이템을 추가하는거니깐 todos의 push 메소드의 인수로
> newTodo에 새로운 아이템을 넣으면 되지 않니? 이렇케 생각할수도 있지만 이렇케 하면 안된다.
> todos와 같은 상태값은 반드시 이 상태변화 함수를 호출해서 수정할수 있다. 그래야만 변경된
> state 값을 리액트가 감지하고 컴포넌트를 정상적으로 리렌더링할 수 있기 떄문이다.
> 따라서 push 메소드를 이용해 상태 값을 직접 변경하면 안된다. 
> **상태 변화 함수**인 setTodos를 호출하고 인수로 새로운 값을 넣어줘야 한다.

```
const onCreate = (content) => {
    const newTodo = {
      id: 0,
      isDone: false,
      content: content,
      date: new Date().getTime(),
    };

    todos.push(newTodo);
};
``` 

### Editor.jsx

``` 
import "./Editor.css";
import { useState, useRef } from "react";

const Editor = ({ onCreate }) => {
  // 상태 관리: 입력 필드의 내용을 저장하는 상태 변수
  const [content, setContent] = useState("");

  // 참조 변수: 입력 필드의 DOM 요소를 참조하기 위해 사용
  const contentRef = useRef();

  // 입력 필드 내용이 변경될 때 호출되는 함수
  // 사용자가 입력한 값을 상태 변수 content에 업데이트
  const onChangeCotent = (e) => {
    setContent(e.target.value);
  };

  // 키보드 이벤트 처리 함수
  const onKeyDown = (e) => {
    if (e.ketCode === 13) {
      onSubmit();
    }
  };

  const onSubmit = () => {
    if (content === "") {
      contentRef.current.focus(); // 입력 필드로 포커스 이동
      return;
    }

    onCreate(content); // 부모 컴포넌트에서 전달받은 onCreate 함수 실행
    setContent(""); // 입력 필드 초기화
  };

  return (
    <div className="Editor">
      <input
        ref={contentRef} // DOM 요소 참조
        value={content} // 상태 변수 content로 값 제어
        onKeyDown={onKeyDown} // 키보드 이벤트 처리
        onChange={onChangeCotent} // 입력 값 변경 처리
        placeholder="새로운 Todo..." 
      />
      <button onClick={onSubmit}>추가</button>
    </div>
  );
};

export default Editor;

``` 

### App.jsx
``` 
import "./App.css";
import { useState, useRef } from "react";
import Header from "./components/Header";
import Editor from "./components/Editor";
import List from "./components/List";

const mockData = [
  {
    id: 0,
    isDone: false,
    content: "Raact 공부하기",
    data: new Date().getTime(),
  },
  {
    id: 1,
    isDone: false,
    content: "책 읽기",
    data: new Date().getTime(),
  },
  {
    id: 2,
    isDone: false,
    content: "헬스장 가기",
    data: new Date().getTime(),
  },
];

function App() {
  const [todos, setTodos] = useState(mockData);
  const idRef = useRef(3);

  const onCreate = (content) => {
    const newTodo = {
      id: idRef.current++,
      isDone: false,
      content: content,
      date: new Date().getTime(),
    };

    setTodos([newTodo, ...todos]);
  };

  return (
    <div className="App">
      <Header />
      <Editor onCreate={onCreate} />
      <List />
    </div>
  );
}

export default App;

``` 

## 8.5) Read - 투두리스트 렌더링하기

![](https://github.com/dididiri1/TIL/blob/main/React/images/09_01.png?raw=true)

``` 
const List = ({ todos }) => {
  return (
    <div className="List">
      <h4>Todo List</h4>
      <input type="text" placeholder="검색어를 입력하세요." />
      <div className="todos_wrapper">
        {todos.map((todo) => {
          return <TodoItem {...todo} />; // Missing "key" prop for element in iterator
        })}
      </div>
    </div>
  );
};
```
> 💡 리엑트에서는 내부적으로 리스트 형태로 렌더링된 컴퍼런들이나 또는 어던 요소들을 서로 구분할 떄   
> 각각의 요소를 **Key**라는 prop을 통해서 구분하게 되서 고유한 값을 전달 해줘야 된다.

``` 
import "./List.css";
import TodoItem from "./TodoItem";

const List = ({ todos }) => {
  return (
    <div className="List">
      <h4>Todo List</h4>
      <input type="text" placeholder="검색어를 입력하세요." />
      <div className="todos_wrapper">
        {todos.map((todo) => {
          return <TodoItem key={todo.id} {...todo} />;
        })}
      </div>
    </div>
  );
};


export default List;

``` 

## 8.6) Update - 투두 수정하기

## 8.7) Delete - 투두 삭제하기

### 1) onDelete 함수 만들기
- 함수를 만들고 매개 변수로 targetId 삭제할 아이템의 아이디를 받아온다.
- 인수로는 todos 배열에서 targetId와 일치하는 Id를 갖는 요소만 삭제한 새로운 배열 한다.
#### App.jsx
``` 
  const onDelete = (targetId) => {
    setTodos(todos.filter((todo) => todo.id !== targetId));
  };
``` 
- App 컴퍼넌트에 onDelete 함수를 List 컴퍼넌트에 props로 전달 해준다.
``` 
  return (
    <div className="App">
      <Header />
      <Editor onCreate={onCreate} />
      <List todos={todos} onUpdate={onUpdate} onDelete={onDelete} />
    </div>
  );
```
#### List.jsx 

``` 
const List = ({ todos, onUpdate, onDelete }) => {
    ``` 

      <div className="todos_wrapper">
        {filteredTodos.map((todo) => {
          return (
            <TodoItem
              key={todo.id}
              {...todo}
              onUpdate={onUpdate}
              onDelete={onDelete}
            />
          );
    
    ```   
          
};

export default List;
``` 
#### TodoItem.jsx
``` 
import "./TodoItem.css";

const TodoItem = ({ id, isDone, content, date, onUpdate, onDelete }) => {
  const onChageCheckBox = () => {
    onUpdate(id);
  };

  const onClickDeleteButton = () => {
    onDelete(id);
  };

  return (
    <div className="TodoItem">
      <input onChange={onChageCheckBox} checked={isDone} type="checkbox" />
      <div className="content">{content}</div>
      <div className="date">{new Date(date).toLocaleDateString()}</div>
      <button onClick={onClickDeleteButton}>삭제</button>
    </div>
  );
};

export default TodoItem;
``` 

### Reference
* [한 입 크기로 잘라 먹는 리액트(React.js) : 기초부터 실전까지](https://inf.run/FiFhg)

