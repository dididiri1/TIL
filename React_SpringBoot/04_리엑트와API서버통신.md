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
