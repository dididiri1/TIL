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

## 캐싱 메커니즘 이해하기 2
![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_10.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_11.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_12.png?raw=true)

### 캐시 비활성(inactive)과 GC 개념 정리
TanStack Query에서 캐시된 데이터는 항상 사용 중인 상태로 유지되지 않는다.
어떤 쿼리를 참조하던 컴포넌트가 모두 언마운트되면, 해당 데이터는 inactive 상태로 전환된다.
이 시점부터는 더 이상 UI에서 사용되지 않는 데이터로 간주된다.

### gcTime이란?
gcTime은 inactive 상태로 변경된 캐시 데이터가 메모리에 유지되는 최대 시간을 의미한다.
이 시간이 지나면 해당 캐시는 자동으로 제거되며, 기본 설정 값은 5분이다.

이러한 구조 덕분에 React Query는 캐시를 무제한으로 쌓아두지 않고,
**불필요한 메모리 점유를 방지하면서 리소스를 효율적으로 관리**할 수 있다.

### staleTime과 gcTime의 차이
staleTime과 gcTime은 서로 영향을 주지 않는 독립적인 설정값이다.
- staleTime은 데이터가 신선한 상태(fresh) 로 유지되는 시간을 의미한다.
- gcTime은 데이터가 inactive 상태가 된 이후 메모리에서 제거되기까지의 시간을 의미한다.

따라서 데이터가 여전히 fresh 상태라 하더라도,
해당 쿼리를 사용하는 컴포넌트가 모두 사라지면 inactive로 전환되고,
gcTime이 경과하면 캐시는 삭제될 수 있다.

> staleTime이 gcTime보다 길게 설정되어 있어도 캐시가 유지된다는 보장은 없다.

### 전역 캐시 정책 설정
TanStack Query는 캐시 관련 옵션을 전역 단위로 설정할 수 있다.
main.tsx에서 QueryClient의 defaultOptions를 통해 공통 정책을 정의하면,
프로젝트 전반에 동일한 기준을 적용할 수 있다.

### 글러벌 옵션
#### main.tsx
```
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 0, // 개발단계에서는 보통 0초
      gcTime: 5 * 60 * 1000, // 보통 5분

      refetchOnMount: true,
      refetchOnWindowFocus: false,
      refetchOnReconnect: false,
      refetchInterval: false,
    },
  },
}); // 🔥 전역 서버 상태 저장소
```

### 쿼리별 옵션
- 개별로 옵션을 적용해 두면 이 옵션의 값으로 덮어 쒸어짐
```
export function useTodoDataById(id: number) {
  return useQuery({
    queryFn: () => fetchTodoById(id),
    queryKey: ["todos", id],

    staleTime: 5000,
    gcTime: 5000,

    // refetchInterval: 1000,
    // refetchOnMount: false,
    // refetchOnWindowFocus: false,
    // refetchOnReconnect: false,
    // refetchInterval: false,
  });
}
```

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/04_13.png?raw=true)


이 방식은 다음과 같은 목적에 유용하다:
- 데이터 신선도 관리
- 렌더링 성능 최적화
- 서버 요청 횟수 조절
- 메모리 사용량 관리


## 데이터 수정 요청 관리하기 (useMutation)
TanStack Query에서 useQuery는 데이터 조회(Read) 를 담당하는 훅이다.
서버로부터 데이터를 가져오고, 캐싱·stale 판단·refetch 같은 흐름을 자동으로 처리해준다.

반면 useMutation은 서버 데이터를 변경하는 요청(Create / Update / Delete) 을 다루기 위한 훅이다.

데이터 수정 요청은 조회와 달리,
- 요청 결과가 즉시 UI에 반영되어야 하고
- 성공/실패 여부에 따라 추가 처리가 필요하기 때문에 별도의 관리 흐름이 필요하다.
이 역할을 useMutation이 담당한다.

### 코드 예제
```
const { mutate, isPending } = useMutation({
    mutationFn: createTodo,
});
```
mutationFn에는 실제 데이터 변경을 수행하는 API 함수를 전달한다.

| 속성 | 설명                                   |
|------------|----------------------------------------|
| isPending  | 비동기 요청이 진행 중인지 여부          |
| onMutate   | 요청 시작 직후 실행되는 로직            |
| onSettled  | 성공·실패와 관계없이 요청 종료 시 실행 |
| onSuccess  | 요청 성공 시 실행되는 후처리            |
| onError    | 요청 실패 시 실행되는 에러 처리 로직    |
이 콜백들을 통해 요청 흐름 전반을 세밀하게 제어할 수 있다.

### 데이터 수정 이후 자주 수행하는 작업
- 서버 데이터 재조회(refetch)
- 캐시 무효화(cache invalidation)
- 낙관적 업데이트(optimistic update) 적용
데이터 변경 후 화면 상태를 최신으로 유지하기 위해 이러한 작업들이 함께 사용된다.

### 코드 예제
```
import { useMutation } from '@tanstack/react-query';
import { createTodo } from '../../api/create-todo';

export function useCreateTodoMutation() {
  return useMutation({
    mutationFn: createTodo,
    onMutate: () => {
      // 요청 시작 시 처리
    },
    onSettled: () => {
      // 요청 종료 후 공통 처리
    },
    onSuccess: () => {
      // 성공 시 후처리
    },
    onError: (error) => {
      // 실패 시 에러 처리
    },
  });
}
```

### 정리
- useMutation은 데이터 조회와 무관한 변경 요청의 상태를 관리하기 위한 훅이다.
- 단순히 API를 호출하는 수준을 넘어, 요청 전·중·후 흐름 전체를 제어할 수 있다.
- useQuery와 달리 자동 캐싱이나 자동 refetch는 제공되지 않으며, 필요에 따라 직접 캐시 무효화나 재요청을 처리해야 한다.

## 캐시 데이터 다루기 1 - 데이터 무효화 하기
TanStack Query에서 특정 queryKey를 invalidate 하면,
해당 캐시는 **stale 상태로 전환**되고 필요 시 **자동으로 refetch**가 트리거된다.

이를 통해 데이터 변경 이후에도 화면을 새로고침하거나 강제 리렌더링 없이
필요한 데이터만 최신 상태로 유지할 수 있다.

### 캐시 무효화 방법
캐시 제어는 useQueryClient 훅을 통해 수행한다.
```
const queryClient = useQueryClient();

queryClient.invalidateQueries({
  queryKey: [ ... ],
});
```
invalidateQueries를 사용하면 **지정한 queryKey에 해당하는 캐시만 선택적으로 무효화**할 수 있다.

### 왜 queryKey를 구조적으로 관리해야 할까?
queryKey를 일관된 구조로 정의하면 다음과 같은 장점이 있다.
- 여러 위치에서 재사용 가능
- 불필요한 전체 refetch 방지
- 프로젝트 규모가 커져도 캐싱 전략을 예측 가능하게 유지
- 유지보수성과 가독성 향상

### 예제: 데이터 추가 후 캐시 무효화
```
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { createTodo } from '../../api/create-todo';

export function useCreateTodoMutation() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: createTodo,
    onSuccess: () => {
      queryClient.invalidateQueries({
        queryKey: ['todos'], // 특정 캐시를 stale 처리 → refetch 유도
      });
    },
    onError: (error) => {
      console.log(error);
    },
  });
}
```
데이터를 추가한 뒤 해당 목록 쿼리를 무효화하면,
자동으로 최신 데이터로 다시 조회된다.

### Query Key 구조화 예시 (Query Key Factory Pattern)
```
export const QUERY_KEYS = {
  todo: {
    all: ['todo'],
    list: ['todo', 'list'],
    detail: (id: string) => ['todo', 'detail', id],
  },
};
```
```
import { fetchTodos } from "@/api/fetch-todos";
import { QUERY_KEYS } from "@/lib/constants";
import { useQuery } from "@tanstack/react-query";

export function useTodosData() {
  return useQuery({
    queryFn: fetchTodos,
    queryKey: QUERY_KEYS.todo.list,
  });
}
```

이처럼 queryKey를 하나의 객체로 관리하면,
쿼리 구조를 일관되게 유지할 수 있고 실수를 줄일 수 있다.

### 정리
- invalidateQueries는 데이터 변경 이후 UI 동기화를 위한 핵심 메커니즘이다.
- 전체 화면을 새로고침할 필요 없이, 정확히 필요한 캐시만 무효화하여 갱신할 수 있다.
- queryKey를 구조적으로 관리하면, 규모가 커져도 안정적이고 예측 가능한 캐시 관리가 가능하다.


## 캐시 데이터 관리 ② – 응답 결과 활용하기
invalidateQueries를 사용하면 데이터 변경 이후 해당 캐시가 stale 처리되고,
자동으로 refetch가 발생하여 항상 **최신 서버 데이터**를 다시 가져온다.

이 방식은 구현이 단순하고 안정적이라는 장점이 있지만,
반복적으로 사용하면 **불필요한 네트워크 요청과 렌더링 비용**이 발생할 수 있다.

이러한 단점을 보완하기 위해,
**서버 응답 결과를 직접 캐시에 반영하는 방식**을 사용할 수 있다.

### setQueryData란?

setQueryData는 TanStack Query가 관리하는 **쿼리 캐시를 직접 수정할 수 있는 API**이다.
- refetch 없이 캐시 데이터를 즉시 변경
- UI가 바로 반영되어 사용자 경험 향상
- 리스트 추가 / 삭제 / 수정과 같은 단순 변경에 특히 효율적

즉, 서버 요청 결과를 그대로 다시 요청하는 대신
**이미 받은 응답 데이터를 캐시에 직접 반영하는 방식**이다.

### invalidateQueries vs setQueryData
| 구분 | invalidateQueries | setQueryData |
|------|-------------------|--------------|
| refetch | 발생함 | 발생하지 않음 |
| 데이터 최신성 | 서버 기준 최신 데이터 보장 | 응답 데이터 기반 |
| 성능 | 느릴 수 있음 (네트워크 요청) | 빠름 (즉시 렌더링) |
| 적합한 상황 | 복잡한 연산, 여러 데이터 변경 | 단일 항목 추가/수정/삭제 |

### 예제: 응답 데이터를 이용한 캐시 직접 업데이트
```
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { createTodo } from '../../api/create-todo';
import { QUERY_KEYS } from '../../lib/constants';
import type { Todo } from '../../types/todo';

export function useCreateTodoMutation() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: createTodo,
    onSuccess: (newTodo) => {
      queryClient.setQueryData<Todo[]>(
        QUERY_KEYS.todo.list,
        (prevTodos) => {
          if (!prevTodos) return [newTodo];
          return [...prevTodos, newTodo];
        }
      );
    },
    onError: (error) => {
      console.log(error);
    },
  });
}
```
이 방식은 새로 생성된 데이터를 리스트 캐시에 바로 추가하여
**추가적인 refetch 없이 UI를 즉시 업데이트**한다.

### 정리
- invalidateQueries만 사용하면 매번 refetch가 발생해 네트워크 비용과 렌더링 부담이 커질 수 있다.
- setQueryData는 응답값을 직접 캐시에 반영하여 즉각적인 UI 업데이트가 가능하다.
- CRUD 작업에서 리스트 ↔ 상세 화면을 동기화할 때 매우 강력한 패턴이다.
- 데이터 변경 결과가 명확한 경우, invalidateQueries보다 우선적으로 고려할 수 있는 전략이다.


## 캐시 데이터 다루기 3 – 낙관적 업데이트 (Optimistic Update)
### 낙관적 업데이트란
- 서버 요청이 성공할 것이라고 가정하고, 요청을 보내는 즉시 UI와 캐시를 먼저 변경하는 전략이다.
- 네트워크 응답을 기다리지 않기 때문에 사용자에게 즉각적인 피드백 제공
- 성공 후 refetch를 하지 않아도 되고, 캐시 업데이트로 UI 반영이 가능하다.
단, 요청 실패 시 상태를 되돌리는 rollback 로직이 반드시 필요

### 코드 예제
- 컴포넌트
```
const { mutate } = useUpdateTodoMutation();
  const handleCheckboxClick = () => {
    mutate({
      id,
      isDone: !isDone,
    });
  };
```
- useMutation 구현
```
export function useUpdateTodoMutation() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: updateTodo,
    onMutate: (updatedTodo) => {
      // 낙관적 업데이트 로직
      queryClient.setQueryData<Todo[]>(QUERY_KEYS.todo.list, (prevTodoList) => {
        if (!prevTodoList) return [];
        return prevTodoList.map((prevTodo) =>
          prevTodo.id === updatedTodo.id
            ? { ...prevTodo, ...updatedTodo }
            : prevTodo
        );
      });
    },
    onError: () => {
    // rollback 로직
    },
  });
}
```

### 정리
- onMutate는 데이터를 수정하기 전에 **캐시에 반영할 수 있는 가장 빠른 지점**
- 응답 대기 없이 변경된 UI를 먼저 보여줄 수 있어 UX 향상
- 서버 응답이 실패해도 UI는 고쳐놨기 때문에 **rollback 처리 로직이 필수**

### 실무/프로젝트 적용 아이디어:
- Todo 체크박스 즉시 토글
  - Ajax 요청 지연/실패가 잦은 환경에서 특히 유용하다.
- 좋아요/즐겨찾기/북마크 기능
  - 클릭 즉시 반응하는 UI 필요
