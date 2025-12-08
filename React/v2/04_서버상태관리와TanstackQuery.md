# 서버상태관리와 Tanstack Qeury

## 서버 상태 관리란?

### 📌 개념
React App의 서버 상태(Server State) 를 체계적으로 관리하기 위한 라이브러리. API 호출과 관련된 비동기 상태(loading, error, success, data) 를 직접 관리하지 않아도 되도록 다양한 기능을 제공한다.

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_01.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_02.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_03.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_04.png?raw=true)

### ✨ 주요 기능
- 캐싱(Cache): 동일한 요청을 빠르게 처리하고 불필요한 요청을 방지
- 요청 중복 방지(Deduplication)
- 백그라운드 Refetch: 데이터가 오래되었을 때 자동으로 최신화
- 무한 스크롤 / 페이지네이션 지원
- Optimistic Update: 반응형 UI 업데이트 지원
- Stale Time / GC / Query Key 기반 상태 관리

### 🚀 왜 필요한가?
전역 상태 관리 라이브러리(Redux / Zustand / Recoil 등)는 UI 상태(Client State) 관리에 최적화되어 있다. 하지만 서버 상태(Server State) 는 다음과 같은 특성이 있어 일반 전역 상태로 관리하려면 매우 복잡해진다.
- 서버 데이터는 외부 요인에 의해 변경된다
- 최신 여부(staleness)를 판단해야 한다
- 캐싱, refetch 조건, 동시 요청 처리 등이 어렵다
➡️ TanStack Query는 이러한 서버 데이터의 생명주기 전체를 자동으로 관리한다.

## 실습용 서버 설정하기
### json-server
- JSON 파일만으로 간단한 Mock API 서버를 구축할 수 있는 도구이다.
- 백엔드 없이도 RESTful API 형태의 CRUD 엔드포인트를 제공해 프론트엔드 개발 및 테스트 환경을 빠르게 구성할 수 있다.

### 설치
```
npm i json-server -D
```

### server/db.json
```
{
  "todos": [
    {
      "id": 1,
      "content": "Todo 1",
      "isDone": true
    },
    {
      "id": 2,
      "content": "Todo 2",
      "isDone": true
    },
    {
      "id": 3,
      "content": "Todo 3",
      "isDone": false
    }
  ]
}
```

### vite.config.ts
- 서버 폴더 이하의 파일에 변화가 생겨도 리액트 앱이 리랜더링 되지 않는다.
```
export default defineConfig({
  
  ...
  
  server: {
    watch: {
      ignored: ['**/server/**'],
    },
  },
});
```

### 서버 실행
```
npx json-server server/db.json
```
- 해당 명령어 실행 시 server/db.json을 기반으로 로컬 API 서버가 자동으로 생성된다.
- 기본 포트는 3000이며, endpoint UI까지 제겅되어 브라우저에서 직접 확인 가능

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_05.png?raw=true)

### 📌 핵심 정리
json-server는 프론트엔드 개발 단계에서 가벼운 Mock API 환경을 손쉽게 구축하기 위한 도구이다.
별도의 서버 구현 없이 JSON 파일만 준비하면 CRUD 요청을 테스트할 수 있도록 REST API 형태의 엔드포인트를 자동 생성해준다.

이를 활용하면 실제 백엔드가 준비되지 않은 상태에서도 데이터 흐름 검증, 상태 관리 라이브러리 실습, UI 연동 테스트 등을 빠르게 진행할 수 있다.
또한 Vite 환경에서 일부 설정을 조정하는 이유는 HMR(Hot Module Reload)로 인한 불필요한 새로고침을 방지하고 API 연동 테스트 시 
안정적인 개발 환경을 유지하기 위함이다.

## 데이터 조회 요청 관리하기

### TanStack Query 설치
```
npm i @tanstack/react-query
```
### QueryClient
- TanStack Query에서 관리되는 서버 상태의 중앙 저장소 역할
- 캐싱, 요청 중복 제거, 결과 저장, Refetch 제어 등 서버 데이터 생명주기 전체를 관리한다.
- React App 최상단에서 Provider로 감싸야 모든 컴포넌트에서 Query 기능을 사용할 수 있다.

### main.tsx 설정
```
import { createRoot } from "react-dom/client";
import "./index.css";
import App from "./App.tsx";
import { BrowserRouter } from "react-router-dom";
import { QueryClientProvider, QueryClient } from "@tanstack/react-query";

const queryClient = new QueryClient(); // 🔥 전역 서버 상태 저장소

createRoot(document.getElementById("root")!).render(
  <BrowserRouter>
    <QueryClientProvider client={queryClient}>
      <App />
    </QueryClientProvider>
  </BrowserRouter>,
);
```

### QueryClient
#### useQuery
- 컴포넌트 마운트 시 queryFn을 자동 실행하여 데이터를 요청한다.
- 결과는 queryKey 기준으로 캐싱 및 상태 관리된다.
- 반환 값:
  - data
  - isLoading, isError, error
  - 상태 관련 여러 옵션(refetch, retry, staleTime, gcTime ...)
- 예외 처리 및 재시도 로직 기본 제공(기본적으로 3번 정도 재시도)
  - 재시도 비활성화 예시: retry: 0

#### 구조화 Best Practice
- API 요청 로직
- useQuery 훅 정의
- UI 컴포넌트
  - 모두 분리하는 것이 유지보수성과 테스트 편의성 측면에서 유리

### 코드 예제
#### src/api/fetch.todos.ts
```
import { API_URL } from "@/lib/constants";
import type { Todo } from "@/types";

export async function fetchTodos() {
  const response = await fetch(`${API_URL}/todos`);
  if (!response.ok) throw new Error("Fetch Failed");

  const data: Todo[] = await response.json();
  return data;
}
```
#### src/hooks/quries/use-todos.data.ts
```
import { fetchTodos } from "@/api/fetch-todos";
import { useQuery } from "@tanstack/react-query";

export function useTodosData() {
  return useQuery({
    queryFn: fetchTodos,
    queryKey: ["todos"],
    //retry: 0,
  });
}
```
### todo-list-page.tsx
```
import TodoEditor from "@/components/todo-list/todo-editor";
import TodoItem from "@/components/todo-list/todo-item";
import { useTodosData } from "@/hooks/quries/use-todos.data";

export default function TodoListPage() {
  const { data: todos, isLoading, error } = useTodosData();

  if (error) return <div>오류가 발생했습니다.</div>;
  if (isLoading) return <div>로딩 중 입니다...</div>;

  return (
    <div className="flex flex-col gap-5 p-5">
      <h1 className="text-2xl font-bold">TodoList</h1>
      <TodoEditor />
      {todos?.map((todo) => (
        <TodoItem key={todo.id} {...todo} />
      ))}
    </div>
  );
}
```

## 캐싱 메커니즘 이해하기 1
- useQuery로 가져온 데이터는 useQuery 내 queryKey를 기준으로 자동으로 캐싱된다.
- 같은 queryKey를 사용하는 컴포넌트는 네트워크 요청 없이 캐시된 데이터를 즉시 활용한다

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_06.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_07.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_09.png?raw=true)

- TanStack Query는 상황에 따라 최신 데이터 유지 전략(Stale-While-Refetch)을 제공한다.
  - 화면은 캐시 데이터로 빠르게 렌더링
  - 이후 백그라운드에서 최신 데이터 요청
  - 완료 후 UI 업데이트
  

### TanStack Query 캐시의 5가지 상태

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_08.png?raw=true)

| 상태        | 설명                                 |
|------------|--------------------------------------|
| fetching   | 서버에서 데이터를 가져오는 중        |
| fresh      | 최신 상태의 데이터 (캐시 직후 상태) |
| stale      | 시간이 지나 만료된 데이터           |
| staleTime  | fresh → stale 로 전환되는 시간       |
| refetching | stale 상태에서 데이터를 다시 요청 중 |

- 리페칭(데이터 다시 불러옴)
  - 1. Mount: 캐시 데이터를 사용하는 컴포넌트가 Mount 되었을 때
  - 2. WindowFocus: 사용자가 해당 탭에 다시 돌아왔을 때
  - 3. Reconnet: 인터넷 연결이 끊어졌다가 다시 연결되었을 때
  - 4. Interval: 일정 주기로 자동 요청

### DevTools
- 캐시의 상태 변화를 시각적으로 확인할 수 있는 도구
```
npm i @tanstack/react-query-devtools
```
- main.tsx에 ReactQueryDevTools 컴포넌트를 추가하면 실시간 캐시 상태 확인 가능

### Refetch 옵션
| 옵션                   | 설명                           |
|------------------------|--------------------------------|
| refetchOnMount         | 컴포넌트 마운트 시 refetch 여부 |
| refetchOnWindowFocus   | 브라우저 포커스 복귀 시 refetch |
| refetchOnReconnect     | 네트워크 재연결 시 refetch      |
| refetchInterval        | 일정 주기마다 자동 refetch      |

- 기본 staleTime = 0이므로 응답 직후 곧바로 stale 처리됨
- stale !== 무효, stale 데이터도 렌더링에 즉시 사용 가능
- stale 데이터 노출 후 백그라운드 refetch → 화면 갱신 방식으로 UX 향상

### 핵심정리
- TanStack Query의 핵심은 단순 데이터 요청 도구가 아니라, 데이터의 생명주기 관리를 담당한다는 점이다.
- 캐싱 + stale + refetch 조합으로 API 요청 최소화 & 더 부드러운 UI 경험
- stale 상태의 데이터도 화면을 즉시 구성하여 UX 최적화 가능

#### use-todo-data-by-id.ts
```
import { fetchTodoById } from "@/api/fetch-todo-by-id";
import { useQuery } from "@tanstack/react-query";

export function useTodoDataById(id: number) {
  return useQuery({
    queryFn: () => fetchTodoById(id),
    queryKey: ["todos", id],
    // refetchInterval: 1000,  // 10초 간격으로 자동 refetch
    staleTime: 5000,   // 5초 동안 fresh 상태 유지
    refetchOnMount: false, 
    refetchOnWindowFocus: false,
    refetchOnReconnect: false,
    refetchInterval: false,
  });
}
```