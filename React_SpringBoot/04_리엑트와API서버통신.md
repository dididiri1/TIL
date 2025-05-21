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

