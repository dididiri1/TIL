# 섹션 4. 리엑트와 API서버 통신

## 1. Ajax 통신 처리
- Axios 라이브러리를 추가
```
npm install axios
```

## 2. 타입 처리를 위한 준비
- 페이지 처리를 위한 공통 타입 types폴더 추가
```
interface PageParam {
  page?: string | number;
  size?: string | number;
}

```

```
interface Todo {
  tno: number;
  title: string;
  writer: string;
  dueDate: string | null;
  complete: boolean;
}

```

## Ajax 통신 처리
- 리액트 프로젝트 내에 api 폴더를 추가하고 todoApi.tsx 파일을 추가
```
import axios from "axios";

export const API_SERVER_HOST = "http://localhost:8080";

const prefix = `${API_SERVER_HOST}/api/todo`;
export const getOne = async (tno: number) => {
  const res = await axios.get(`${prefix}/${tno}`);
  return res.data;
};

```

## useEffect
- useEffect()를 활용하여 컴포넌트 내에서 특정 조건을 충족할 때 동작하는 방법 제공
- 컴포넌트 실행 시 단 한 번만 실행되는 비동기 처리
- 컴포넌트의 여러 상태 중 특정 상태 변경 시 비동기 처리

### ReadPage 데이터 조회
```
import { useParams } from "react-router";
import ReadComponent from "../../components/todo/readComponent";

const ReadPage = () => {
  const { tno } = useParams();

  return (
    <div className="bg-white w-full">
      <div className="text-4xl">Todo Read Page {tno}</div>
      <ReadComponent tno={Number(tno)}></ReadComponent>
    </div>
  );
};

export default ReadPage;
```

```
import { useEffect, useState } from "react";
import { getOne } from "../../api/todoApi";

const initState: Todo = {
  tno: 0,
  title: "",
  writer: "",
  dueDate: null,
  complete: false,
};

const ReadComponent = ({ tno }: { tno: number }) => {
  const [todo, setTodo] = useState<Todo>(initState);

  useEffect(() => {
    getOne(tno).then((data) => {
      console.log(data);
      setTodo(data);
    });
  }, [tno]);

  return (
    <div className="border-2 border-sky-200 mt-10 m-2 p-4 text-2xl">
      {makeDiv("Tno", todo.tno)}
      {makeDiv("Writer", todo.writer)}
      {makeDiv("Title", todo.title)}
      {makeDiv("Status", todo.complete ? "Completed" : "Not Yet")}
    </div>
  );
};

const makeDiv = (title: string, value: string | number) => (
  <div className="flex justify-center">
    <div className="relative mb-4 flex w-full flex-wrap items-stretch">
      <div className="w-1/5 p-6 text-right font-bold">{title}</div>
      <div className="w-4/5 p-6 rounded-r border border-solid shadow-md">
        {value}
      </div>
    </div>
  </div>
);

export default ReadComponent;

```

![](https://github.com/dididiri1/TIL/blob/main/React_SpringBoot/images/04_01.png?raw=true)

## 네비게이션 관련 커스텀 훅
### 조회 화면의 버튼 처리
- 페이지 컴포넌트
  - React-Router를 이용해 내용 컴포넌트에 필요한 기능과 속성 설정. 
  - ReadPage에 라우팅 함수 정의 및 ReadComponent에 함수 속성 전달.
- 내용 컴포넌트
  - 실제 화면 구성, 버튼 등 내용 처리.
  - ReadComponent에 전체 구성 요소 처리.

```
import { createSearchParams, useNavigate, useSearchParams } from "react-router";

const useCustomMove = () => {
  const nav = useNavigate();

  const [queryParams] = useSearchParams();

  const pageStr: string | null = queryParams.get("page");
  const sizeStr: string | null = queryParams.get("size");

  const page: number = pageStr ? Number(pageStr) : 1;
  const size: number = sizeStr ? Number(sizeStr) : 10;

  const queryDefault = createSearchParams({
    page: String(page),
    size: String(size),
  }).toString();

  const moveToModify = (tno: number) => {
    nav({ pathname: `../modify/${tno}`, search: queryDefault });
  };

  const moveToRead = (tno: number) => {
    nav({ pathname: `../read/${tno}`, search: queryDefault });
  };

  const moveToList = (pageParam?: PageParam) => {
    let queryStr = "";
    if (pageParam) {
      const pageNum = Number(pageParam.page) || 1;
      const sizeNum = Number(pageParam.size) || 10;

      queryStr = createSearchParams({
        page: String(pageNum),
        size: String(sizeNum),
      }).toString();
    } else {
      queryStr = queryDefault;
    }
    nav({ pathname: "../list", search: queryStr });
  };

  return { page, size, moveToList, moveToModify, moveToRead };
};

export default useCustomMove;
```
## 목록 페이지로 이동
### ReadComponent에 useCustomMove( )에 moveToList( )를 이용하고 버튼을 추가

```
import { useEffect, useState } from "react";
import { getOne } from "../../api/todoApi";
import useCustomMove from "../../hooks/useCustomMove";

const initState: Todo = {
  tno: 0,
  title: "",
  writer: "",
  dueDate: null,
  complete: false,
};

const ReadComponent = ({ tno }: { tno: number }) => {
  const [todo, setTodo] = useState<Todo>(initState);

  const { moveToList }: UseCustomMoveReturn = useCustomMove();

  useEffect(() => {
    getOne(tno).then((data) => {
      console.log(data);
      setTodo(data);
    });
  }, [tno]);

  return (
    <div className="border-2 border-sky-200 mt-10 m-2 p-4 text-2xl">
      {makeDiv("Tno", todo.tno)}
      {makeDiv("Writer", todo.writer)}
      {makeDiv("Title", todo.title)}
      {makeDiv("Status", todo.complete ? "Completed" : "Not Yet")}
      <div className="flex justify-end p-4">
        <button
          type="button"
          className="rounded p-4 m-2 text-xl w-32 text-white bg-blue-500"
          onClick={() => moveToList()}
        >
          List
        </button>
      </div>
    </div>
  );
};

const makeDiv = (title: string, value: string | number) => (
  <div className="flex justify-center">
    <div className="relative mb-4 flex w-full flex-wrap items-stretch">
      <div className="w-1/5 p-6 text-right font-bold">{title}</div>
      <div className="w-4/5 p-6 rounded-r border border-solid shadow-md">
        {value}
      </div>
    </div>
  </div>
);

export default ReadComponent;

```

## 조회에서 수정/삭제로 이동 기능
- useCustomMove에 수정/삭제로 이동할 수 있는 기능(moveToModify), 조회로 이동할 수 있는 기능(moveToRead)를 추가

#### global.d.ts
```
interface UseCustomMoveReturn {
  moveToList: (PageParam?: PageParam) => void;
  moveToModify: (tno: number) => void;
  moveToRead: (tno: number) => void;
  page: number;
  size: number;
}
```

#### useCustomMove.ts
```
import { createSearchParams, useNavigate, useSearchParams } from "react-router";

const useCustomMove = () => {
  const nav = useNavigate();

  const [queryParams] = useSearchParams();

  const pageStr: string | null = queryParams.get("page");
  const sizeStr: string | null = queryParams.get("size");

  const page: number = pageStr ? Number(pageStr) : 1;
  const size: number = sizeStr ? Number(sizeStr) : 10;

  const queryDefault = createSearchParams({
    page: String(page),
    size: String(size),
  }).toString();

  const moveToModify = (tno: number) => {
    nav({ pathname: `../modify/${tno}`, search: queryDefault });
  };

  const moveToRead = (tno: number) => {
    nav({ pathname: `../read/${tno}`, search: queryDefault });
  };

  const moveToList = (pageParam?: PageParam) => {
    let queryStr = "";
    if (pageParam) {
      const pageNum = Number(pageParam.page) || 1;
      const sizeNum = Number(pageParam.size) || 10;

      queryStr = createSearchParams({
        page: String(pageNum),
        size: String(sizeNum),
      }).toString();
    } else {
      queryStr = queryDefault;
    }
    nav({ pathname: "../list", search: queryStr });
  };

  return { page, size, moveToList, moveToModify, moveToRead };
};

export default useCustomMove;
```


## 목록 데이터 처리
### 목록 데이터 처리를 위한 타입 정의
- API 서버에서 전달하는 페이징 처리 결과를 위한 타입 생성
#### types/global.dts
```
interface PageReqeustDto {
  page: number;
  size: number;
}

interface PageResponseDTO<T> {
  dtoList: T[]; // 데이터 목록 (제네릭 타입)
  pageNumList: number[]; // 페이지 번호 목록
  pageRequestDTO: PageRequestDTO | null; // 요청에 사용된 페이지 정보
  prev: boolean; // 이전 페이지 존재 여부
  next: boolean; // 다음 페이지 존재 여부
  totalCount: number; // 전체 항목 수
  prevPage: number; // 이전 페이지 번호
  nextPage: number; // 다음 페이지 번호
  totalPage: number; // 전체 페이지 수
  current: number; // 현재 페이지 번호
}
```

### axios getList API 
```
import axios from "axios";

export const API_SERVER_HOST = "http://localhost:8080";

const prefix = `${API_SERVER_HOST}/api/todo`;

 ...

export const getList = async (pageParam: PageParam) => {
  const res = await axios.get(`${prefix}/list`, { params: pageParam });
  return res.data;
};
```

### 5 목록 데이터 처리
### components/todo 폴더에 ListComponent 생성
```
import { useEffect, useState } from "react";
import useCustomMove from "../../hooks/useCustomMove";
import { getList } from "../../api/todoApi";

const initState: PageResponseDTO<Todo> = {
  dtoList: [],
  pageNumList: [],
  pageRequestDTO: null,
  prev: false,
  next: false,
  totalCount: 0,
  prevPage: 0,
  nextPage: 0,
  totalPage: 0,
  current: 0,
};

const ListComponent = () => {
  const { page, size, moveToRead, moveToList }: UseCustomMoveReturn =
    useCustomMove();

  // serverData 나중에 사용  
  const [serverData, setServerData] = useState(initState);

  useEffect(() => {
    getList({ page, size }).then((data) => {
      console.log(data);
      setServerData(data);
    });
  }, [page, size]);

  return <div>Todo List Component</div>;
};

export default ListComponent;
```

### ListPage에서 ListComponent를 import
```
import ListComponent from "../../components/todo/listComponent";

const ListPage = () => {
  return (
    <div className="bg-white w-full">
      <div className="text-4xl">Todo List Page</div>

      <ListComponent></ListComponent>
    </div>
  );
};

export default ListPage;
```

### 서버에서 가져온 데이터들을 ListComponent에서 출력

```
return (
    <div className="border-2 border-blue-100 mt-10 mr-2 ml-2">
      <div className="flex flex-wrap mx-auto justify-center p-6">
        {serverData.dtoList.map((todo) => (
          <div
            key={todo.tno}
            className="w-full min-w-[400px] p-2 m-2 rounded shadow-md"
            onClick={() => moveToRead(todo.tno)}
          >
            <div className="flex">
              <div className="font-extrabold text-2xl p-2 w-1/12">
                {todo.tno}
              </div>
              <div className="text-1xl m-1 p-2 w-8/12 font-extrabold">
                {todo.title}
              </div>
              <div className="text-1xl m-1 p-2 w-2/10 font-medium">
                {todo.dueDate}
              </div>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
```

### 페이징 처리 서버에서 전달 받은 데이터

![](https://github.com/dididiri1/TIL/blob/main/React_SpringBoot/images/04_02.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React_SpringBoot/images/04_03.png?raw=true)

### 동일 페이지 클릭 시 문제
- 컴포넌트의 상태를 변경하는 방식으로 작성
- useCustomMove에 refresh 상태 추가
#### global.d.ts
```
interface UseCustomMoveReturn {
  moveToList: (PageParam?: PageParam) => void;
  moveToModify: (tno: number) => void;
  moveToRead: (tno: number) => void;
  page: number;
  size: number;
  refresh: boolean; // 추가
}
```

```
import { useState } from "react";
import { createSearchParams, useNavigate, useSearchParams } from "react-router";

const useCustomMove = () => {
  const nav = useNavigate();

  const [queryParams] = useSearchParams();

  const [refresh, setRefresh] = useState<boolean>(false); // ✅ 추가

  ...

  const moveToList = (pageParam?: PageParam) => {
    let queryStr = "";
    if (pageParam) {
      const pageNum = Number(pageParam.page) || 1;
      const sizeNum = Number(pageParam.size) || 10;

      queryStr = createSearchParams({
        page: String(pageNum),
        size: String(sizeNum),
      }).toString();

      if (queryStr === queryDefault) {  // ✅ 추가
        setRefresh(!refresh);
      }
    } else {
      queryStr = queryDefault;
    }
    nav({ pathname: "../list", search: queryStr });
  };

  return { page, size, moveToList, refresh, moveToModify, moveToRead };
};

export default useCustomMove;

```

## 06 등록 컴포넌트와 모달 처리
### 등록 컴포넌트와 모달 처리
![](https://github.com/dididiri1/TIL/blob/main/React_SpringBoot/images/04_04.png?raw=true)


- components > todo > addComponent.tsx
- pages > todo > addPage.tsx 에 import


```
import { lazy, Suspense } from "react";
import { Navigate } from "react-router";

const Loading = () => <div>Loading...</div>;
const TodoIndex = lazy(() => import("../pages/todo/indexPage"));

...

const TodoAdd = lazy(() => import("../pages/todo/addPage"));

const todoRouter = () => {
  return {
    path: "todo",
    Component: TodoIndex,
    children: [
      ... 
      
      {
        path: "add",
        element: (
          <Suspense fallback={<Loading />}>
            <TodoAdd />
          </Suspense>
        ),
      },
    ],
  };
};

export default todoRouter;

```

```
import { useState, type ChangeEvent } from "react";
import { postAdd } from "../../api/todoApi";

const initState: TodoAdd = {
  title: "",
  writer: "",
  dueDate: "",
};

const AddComponent = () => {
  const [todo, setTodo] = useState<TodoAdd>({ ...initState });

  const handleChangeTodo = (e: ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setTodo((prevState) => ({
      ...prevState,
      [name]: value,
    }));
  };

  const handleClickAdd = () => {
    postAdd(todo)
      .then((result) => {
        console.log(result);
        setTodo({ ...initState }); // 초기화
      })
      .catch((e) => {
        console.error(e);
      });
  };

  return (
    <div className="border-2 border-sky-200 mt-10 m-2 p-4">
      {/* TITLE 입력 */}
      <div className="flex justify-center">
        <div className="relative mb-4 flex w-full flex-wrap items-stretch">
          <div className="w-1/5 p-6 text-right font-bold">TITLE</div>
          <input
            className="w-4/5 p-6 rounded-r border border-solid border-neutral-500 shadow-md"
            name="title"
            type="text"
            value={todo.title}
            onChange={handleChangeTodo}
          />
        </div>
      </div>

      {/* WRITER 입력 */}
      <div className="flex justify-center">
        <div className="relative mb-4 flex w-full flex-wrap items-stretch">
          <div className="w-1/5 p-6 text-right font-bold">WRITER</div>
          <input
            className="w-4/5 p-6 rounded-r border border-solid border-neutral-500 shadow-md"
            name="writer"
            type="text"
            value={todo.writer}
            onChange={handleChangeTodo}
          />
        </div>
      </div>

      {/* DUEDATE 입력 */}
      <div className="flex justify-center">
        <div className="relative mb-4 flex w-full flex-wrap items-stretch">
          <div className="w-1/5 p-6 text-right font-bold">DUEDATE</div>
          <input
            className="w-4/5 p-6 rounded-r border border-solid border-neutral-500 shadow-md"
            name="dueDate"
            type="date"
            value={todo.dueDate}
            onChange={handleChangeTodo}
          />
        </div>
      </div>

      {/* ADD 버튼 */}
      <div className="flex justify-end">
        <div className="relative mb-4 flex p-4 flex-wrap items-stretch">
          <button
            type="button"
            className="rounded p-4 w-36 bg-blue-500 text-xl text-white"
            onClick={handleClickAdd}
          >
            ADD
          </button>
        </div>
      </div>
    </div>
  );
};

export default AddComponent;
```
```
import AddComponent from "../../components/todo/addComponent";

const AddPage = () => {
  return (
    <div className="bg-white w-full">
      <div className="text-4xl">Todo Add Page</div>
      <AddComponent />
    </div>
  );
};

export default AddPage;
```

```
import axios from "axios";

export const API_SERVER_HOST = "http://localhost:8080";

const prefix = `${API_SERVER_HOST}/api/todo`;

 ...

export const postAdd = async (todoObj: TodoAdd) => {
  const res = await axios.post(`${prefix}/`, todoObj);
  return res.data;
};

```

### 등록 처리 Mutation 과 모달 처리
- 등록이 처리된 후에 모달 화면을 보여주고 완료되면 이동 처리

#### global.d.ts
```
interface ResultModal {
  title: string;
  content: string;
  callbackFn?: () => void;
}
```

#### components/common/resultModal.tsx
```
const ResultModal = ({ title, content, callbackFn }: ResultModal) => {
  return (
    <div
      className="fixed top-0 left-0 z-[1055] flex h-full w-full justify-center"
      style={{ backgroundColor: "rgba(169, 169, 169, 0.7)" }}
      onClick={() => {
        if (callbackFn) {
          callbackFn();
        }
      }}
    >
      <div
        className="absolute bg-white shadow dark:bg-gray-700 w-1/4 rounded mt-10 mb-10 px-6 min-w-[600px]"
        onClick={(e) => e.stopPropagation()} // 클릭 전파 방지
      >
        <div className="justify-center bg-warning-400 mt-6 mb-6 text-2xl border-b-4 border-gray-500 text-center">
          {title}
        </div>
        <div className="text-4xl border-orange-400 border-b-4 pt-4 pb-4 text-center">
          {content}
        </div>
        <div className="justify-end flex">
          <button
            className="rounded bg-blue-500 mt-4 mb-4 px-6 pt-4 pb-4 text-lg text-white"
            onClick={() => {
              if (callbackFn) {
                callbackFn();
              }
            }}
          >
            Close Modal
          </button>
        </div>
      </div>
    </div>
  );
};

export default ResultModal;

```


```
import { useState, type ChangeEvent } from "react";
import { postAdd } from "../../api/todoApi";
import useCustomMove from "../../hooks/useCustomMove";
import ResultModal from "../common/resultModal";

const initState: TodoAdd = {
  title: "",
  writer: "",
  dueDate: "",
};

const AddComponent = () => {
  const [todo, setTodo] = useState<TodoAdd>({ ...initState });

  const [result, setResult] = useState<number | null>(null);  // ✅ 추가

  const { moveToList }: UseCustomMoveReturn = useCustomMove(); // ✅ 추가

  const handleChangeTodo = (e: ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setTodo((prevState) => ({
      ...prevState,
      [name]: value,
    }));
  };

  const handleClickAdd = () => {
    postAdd(todo)
      .then((result) => {
        console.log(result);

        setResult(result.TNO); // ✅ 추가

        setTodo({ ...initState }); // 초기화
      })
      .catch((e) => {
        console.error(e);
      });
  };

  const closeModal = () => { // ✅ 추가
    setResult(null);
    moveToList();
  };

  return (
    <div className="border-2 border-sky-200 mt-10 m-2 p-4">
      {/* 모달 처리 */} // ✅ 추가
      {result && (
        <ResultModal
          title="등록 처리 완료"
          content={`New ${result} Added`}
          callbackFn={closeModal}
        ></ResultModal>
      )}

      {/* TITLE 입력 */}
      <div className="flex justify-center">
        <div className="relative mb-4 flex w-full flex-wrap items-stretch">
          <div className="w-1/5 p-6 text-right font-bold">TITLE</div>
          <input
            className="w-4/5 p-6 rounded-r border border-solid border-neutral-500 shadow-md"
            name="title"
            type="text"
            value={todo.title}
            onChange={handleChangeTodo}
          />
        </div>
      </div>

      {/* WRITER 입력 */}
      <div className="flex justify-center">
        <div className="relative mb-4 flex w-full flex-wrap items-stretch">
          <div className="w-1/5 p-6 text-right font-bold">WRITER</div>
          <input
            className="w-4/5 p-6 rounded-r border border-solid border-neutral-500 shadow-md"
            name="writer"
            type="text"
            value={todo.writer}
            onChange={handleChangeTodo}
          />
        </div>
      </div>

      {/* DUEDATE 입력 */}
      <div className="flex justify-center">
        <div className="relative mb-4 flex w-full flex-wrap items-stretch">
          <div className="w-1/5 p-6 text-right font-bold">DUEDATE</div>
          <input
            className="w-4/5 p-6 rounded-r border border-solid border-neutral-500 shadow-md"
            name="dueDate"
            type="date"
            value={todo.dueDate}
            onChange={handleChangeTodo}
          />
        </div>
      </div>

      {/* ADD 버튼 */}
      <div className="flex justify-end">
        <div className="relative mb-4 flex p-4 flex-wrap items-stretch">
          <button
            type="button"
            className="rounded p-4 w-36 bg-blue-500 text-xl text-white"
            onClick={handleClickAdd}
          >
            ADD
          </button>
        </div>
      </div>
    </div>
  );
};

export default AddComponent;

```

## 07 수정/삭제 처리

- 삭제(Delete 버튼) : 삭제 결과를 모달창으로 보여주고 '/todo/list'로 이동
- 수정(Modify 버튼) : 수정 결과를 모달창으로 보여주고 '/todo/read/번호'로 이동
- 결과의 출력은 공통적으로 모달 창을 이용 

### 수정을 위한 타입 추가 및 API 호출 함수
- todo.d.ts에 타입 추가 
```
interface TodoModify {
  tno: number;
  title: string;
  dueDate: string | null;
  complete: boolean;
}
```
- todoApi.tsx에 수정/삭제 함수를 추가
```
export const deleteOne = async (tno: number) => {
  const res = await axios.delete(`${prefix}/${tno}`);
  return res.data;
};

export const putOne = async (todo: TodoModify) => {
  const res = await axios.put(`${prefix}/${todo.tno}`, todo);
  return res.data;
};
```

### 수정/삭제를 위한 컴포넌트 
#### ModifyComponent.tsx 추가
```
import { useEffect, useState } from "react";

const initState = {
  tno: 0,
  title: "",
  writer: "",
  dueDate: null,
  complete: false,
};

const ModifyComponent = ({ tno }: { tno: number }) => {
  const [todo, setTodo] = useState({ ...initState });

  useEffect(() => {}, [tno]);

  return (
    <div className="border-2 border-sky-200 mt-10 m-2 p-4">
      <div className="flex justify-end p-4">
        <button
          type="button"
          className="inline-block rounded p-4 m-2 text-xl w-32 text-white bg-red-500"
        >
          Delete
        </button>
        <button
          type="button"
          className="rounded p-4 m-2 text-xl w-32 text-white bg-blue-500"
        >
          Modify
        </button>
      </div>
    </div>
  );
};

export default ModifyComponent;
```

### ModifyPage에 ModifyComponent를 추가
```
import { useParams } from "react-router";
import ModifyComponent from "../../components/todo/modifyComponent";

const ModifyPage = () => {
  const { tno } = useParams();

  return (
    <div className="p-4 w-full bg-white">
      <div className="text-3xl font-extrabold">Todo Modify Page</div>

      <ModifyComponent tno={Number(tno)} />
    </div>
  );
};

export default ModifyPage;

```


### ModifyComponent에 handleClickModify( ), handleClickDelete( ) 함수 정의, 버튼에 이벤트 처리
```
  const handleClickModify = () => {
    const todoModify: TodoModify = {
      tno: todo.tno,
      title: todo.title,
      dueDate: todo.dueDate,
      complete: todo.complete,
    };

    putOne(todoModify)
      .then((data) => {
        console.log("modify result: " + data);
      })
      .catch((err) => {
        console.log("수정 실패 했습니다:", err);
      });
  };

  const handleClickDelete = () => {
    deleteOne(tno).then((data) => {
      console.log("delete result: " + data);
    });
  };
```
```
  <div className="flex justify-end p-4">
        <button
          type="button"
          className="inline-block rounded p-4 m-2 text-xl w-32 text-white bg-red-500"
          onClick={handleClickDelete}
        >
          Delete
        </button>
        <button
          type="button"
          className="rounded p-4 m-2 text-xl w-32 text-white bg-blue-500"
          onClick={handleClickModify}
        >
          Modify
        </button>
  </div>
```

### 수정/삭제와 모달창
- ModifyComponent에 화면 이동에 필요한 기능을 가져오고 모달창이 close될 때 호출하도록 변경
```
import { useEffect, useState, type ChangeEvent } from "react";
import { deleteOne, getOne, putOne } from "../../api/todoApi";
import useCustomMove from "../../hooks/useCustomMove";
import ResultModal from "../common/resultModal";

const initState = {
  tno: 0,
  title: "",
  writer: "",
  dueDate: null,
  complete: false,
};

const ModifyComponent = ({ tno }: { tno: number }) => {
  const [todo, setTodo] = useState({ ...initState });

  const [result, setResult] = useState<string | null>(null);

  const { moveToList, moveToRead } = useCustomMove();

  useEffect(() => {
    getOne(tno).then((data) => {
      console.log(data);
      setTodo(data);
    });
  }, [tno]);

  const handleChangeTodo = (e: ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setTodo((prevState) => ({
      ...prevState,
      [name]: value,
    }));
  };

  const handleChangeTodoComplete = (e: ChangeEvent<HTMLSelectElement>) => {
    const value = e.target.value;
    todo.complete = value === "Y";
    setTodo({ ...todo });
  };

  const handleClickModify = () => {
    const todoModify: TodoModify = {
      tno: todo.tno,
      title: todo.title,
      dueDate: todo.dueDate,
      complete: todo.complete,
    };

    putOne(todoModify)
      .then((data) => {
        console.log("modify result: " + JSON.stringify(data));
        setResult("Modify");
      })
      .catch((err) => {
        console.log("수정 실패 했습니다:", err);
      });
  };

  const handleClickDelete = () => {
    deleteOne(tno).then((data) => {
      console.log("delete result: " + data);
      setResult("Delelte");
    });
  };

  const closeModal = () => {
    if (result === "Delete") {
      moveToList();
    } else {
      moveToRead(tno);
    }

    setResult(null);
  };

  return (
    <div className="border-2 border-sky-200 mt-10 m-2 p-4">
      {result && (
        <ResultModal
          title={"처리결과"}
          content={result}
          callbackFn={closeModal}
        ></ResultModal>
      )}
      <div className="flex justify-center mt-10">
        <div className="relative mb-4 flex w-full flex-wrap items-stretch">
          <div className="w-1/5 p-6 text-right font-bold">TNO</div>
          <div className="w-4/5 p-6 rounded-r border border-solid shadow-md bg-gray-100">
            {todo.tno}
          </div>
        </div>
      </div>

      <div className="flex justify-center">
        <div className="relative mb-4 flex w-full flex-wrap items-stretch">
          <div className="w-1/5 p-6 text-right font-bold">WRITER</div>
          <div className="w-4/5 p-6 rounded-r border border-solid shadow-md bg-gray-100">
            {todo.writer}
          </div>
        </div>
      </div>

      <div className="flex justify-center">
        <div className="relative mb-4 flex w-full flex-wrap items-stretch">
          <div className="w-1/5 p-6 text-right font-bold">TITLE</div>
          <input
            className="w-4/5 p-6 rounded-r border border-solid border-neutral-300 shadow-md"
            name="title"
            type="text"
            value={todo.title}
            onChange={handleChangeTodo}
          />
        </div>
      </div>

      <div className="flex justify-center">
        <div className="relative mb-4 flex w-full flex-wrap items-stretch">
          <div className="w-1/5 p-6 text-right font-bold">DUEDATE</div>
          <input
            className="w-4/5 p-6 rounded-r border border-solid border-neutral-300 shadow-md"
            name="dueDate"
            type="date"
            value={todo.dueDate || ""}
            onChange={handleChangeTodo}
          />
        </div>
      </div>

      <div className="flex justify-center">
        <div className="relative mb-4 flex w-full flex-wrap items-stretch">
          <div className="w-1/5 p-6 text-right font-bold">COMPLETE</div>
          <select
            name="complete"
            className="border-solid border-2 rounded m-1 p-2"
            onChange={handleChangeTodoComplete}
            value={todo.complete ? "Y" : "N"}
          >
            <option value="Y">Completed</option>
            <option value="N">Not Yet</option>
          </select>
        </div>
      </div>

      <div className="flex justify-end p-4">
        <button
          type="button"
          className="inline-block rounded p-4 m-2 text-xl w-32 text-white bg-red-500"
          onClick={handleClickDelete}
        >
          Delete
        </button>
        <button
          type="button"
          className="rounded p-4 m-2 text-xl w-32 text-white bg-blue-500"
          onClick={handleClickModify}
        >
          Modify
        </button>
      </div>
    </div>
  );
};

export default ModifyComponent;
```
  
