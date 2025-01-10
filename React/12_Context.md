# 섹션 12. Context

## 11.) Context란

![](https://github.com/dididiri1/TIL/blob/main/React/images/12_01.png?raw=true)

- 컴포넌트의 이 계층 구조상에서 이렇케 바로 한 단계 아래로 전달하는 것은 아무런 문제가 되지 않았다.
- 왜냐면 앱 컴포넌트와 차일드 컴포넌트가 현재 부모와 자식 관계를 가지고 있기 때문이다.
![](https://github.com/dididiri1/TIL/blob/main/React/images/12_02.png?raw=true)

> 이런 경우 데이터를 바로 전달할 수 있었지만 만약 컴포넌트의 계층 구조가 이렇게 두 단계 이상으로 깊어지게 된다면
> 이때에는 앱 컴포넌트에서 자식 B 컴포넌트에게 데이터를 전달할때 props를 이용하게 되면 이렇게 
> 다이렉트로 전달할 수가 없다. 왜냐하면 prop란 부모에서 자식으로만 데이터를 전달할 수 있기 때문이다.
> 이럴때에는 어쩔 수 없이 이 **ChildA**가 컴포넌트가 중간 다리 역할을 해줘야 된다. 
> 그렇기 때문에 먼저 ChildA에게 데이터를 전달하고 그리고 나서 ChildA가 ChildB에게 데이터를
> 연달아 전달하는 방식으로 props를 이용했어야 됫다.
![](https://github.com/dididiri1/TIL/blob/main/React/images/12_06.png?raw=true)


![](https://github.com/dididiri1/TIL/blob/main/React/images/12_03.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/12_04.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/images/12_05.png?raw=true)

> 많은 데이터들을 전달해야 되기 떄문에 props의 이름이 중간에 바뀌기라도 하면 모든 컴포넌트를 다 찾아가서 일일이
> 이름을 다 바꿔줘야 된다. 이런 상황을 props Drilling이라고 한다.
> 이거를 해결하기 위해 React Context이다.

![](https://github.com/dididiri1/TIL/blob/main/React/images/12_07.png?raw=true)

> 컨텍스트는 데이터를 보관하는 일종의 데이터 보관소 역할을 하는 객체이다. Context를 새롭게 생성한 다음에 
> 앱 컴퍼넌트에서 원래 자식 컴퍼넌트에게 전달되던 이 ToDos, onCreate, onUpdate, onDelete와 가은
> 이런 함수들을 Context에 보관해 놓으면 props를 이용하지 않고 다이렉트로 Context를 통해서 필요한
> 데이터를 공급해 줄 수가 있다.

![](https://github.com/dididiri1/TIL/blob/main/React/images/12_08.png?raw=true)

> 컨텍스트는 여러개 만드는 것도 가능하며 왼쪽에 있는 자식 컴포넌트들을 A Context에 데이터만 공급받도록 
> 설정해 줄 수 있고, 오른쪽에 있는 자식 컴포넌트들은 이렇게 B Context라는 새로운 컨텍스트를 통해서
> 데이터를 공급받을 수 있도록 설정핼 줄 수도 있다.

## 11.2) Context 사용하기

- import context 추가
``` 
import {
  useState,
  useRef,
  useReducer,
  useCallback,
  createContext,
} from "react";
``` 

``` 
const TodoContext = createContext();

function App() {
    ``` 
    
    return (
    <div className="App">
      <Header />
      <TodoContext.Provider
        value={{
          todos,
          onCreate,
          onUpdate,
          onDelete,
        }}
      >
        <Editor onCreate={onCreate} />
        <List todos={todos} onUpdate={onUpdate} onDelete={onDelete} />
      </TodoContext.Provider>
    </div>
  );
}
``` 
> 이렇캐 해주면 Provider 컴포넌트 아래에 있는 모든 컴포넌트들은 전부 다 TodoContext의  
> 데이터를 공급받을 수 있게 된다. 이때 공급할 데이터는 어떻케 설정하냐면 이 Provider 컴포넌트에게   
> value로 props로 전달해주면 된다. todo, onCreate, onUpdate 등.. 모두 다 컨텍스트로 하려고 하니까
> 객체로 묶어서 전달해준다. 

- Editor 컴포넌트 Context 사용
### Editor.jsx
``` 
import { TodoContext } from "../App";

onst Editor = () => {
  const { onCreate } = useContext(TodoContext);

  const [content, setContent] = useState("");
  const contentRef = useRef();
  
  ...
   
``` 