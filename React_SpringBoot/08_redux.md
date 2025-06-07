# 8강 리덕스

## 리덕스 툴킷 설정
```
npm install @reduxjs/toolkit react-redux
```

## 스토어 설정
- stc 폴더내에 stroe.tsx 파일을 추가
```
import { configureStore } from "@reduxjs/toolkit";

const store = configureStore({
  reducer: {},
});

export type AppDispatch = typeof store.dispatch;

export type RootState = ReturnType<typeof store.getState>;

export default store;
```
- main.tsx에 Provider 추가
```
import { createRoot } from "react-dom/client";
import "./index.css";
import { RouterProvider } from "react-router";
import router from "./router/root";
import { Provider } from "react-redux";
import store from "./store";

createRoot(document.getElementById("root")!).render(
  <Provider store={store}>
    <RouterProvider router={router}></RouterProvider>
  </Provider>
);
```

## 슬라이스와 리듀서
- 스토어와 어플리케이션 상태
  - 스토어는 어플리케이션 내에서 공유되는 상태 데이터를 나타냄.
- 리듀서 역할
  - 리듀서는 현재 스토어에 있는 어플리케이션 상태를 가공하는 역할 수행.
- 액션과 호출
  - 컴포넌트들은 리듀서를 액션을 이용해 호출. 액션은 스토어의 상태를 변화를 나태내는 정보를 가짐
- 리듀서의 처리
  - 리듀서는 액션의 페이로드 값을 처리하고, 새로운 어플리케이션 상태 데이터를 반환.
- RTK의 슬라이스
  - RTK에서는 리듀서와 액션을 별도로 작성하지 않고, 슬라이스(slice)라는 이름으로 한 번에 작성할 수 있음.

## loginSlice.tsx 
```
import { createSlice } from "@reduxjs/toolkit";

export interface LoginInfo {
  email: string;
}

const initState: LoginInfo = {
  email: "",
};

const loginSlice = createSlice({
  name: "loginSlice",
  initialState: initState,
  reducers: {
    login: (state, action) => {
      console.log("login ....");
      return state;
    },
    logout: (state, action) => {
      console.log("logout ....");
      return state;
    },
  },
});

export default loginSlice.reducer;

export const { login, logout } = loginSlice.actions;
```

## store.tsx에 slices 설정
```
import { configureStore } from "@reduxjs/toolkit";
import loginSlice from "./features/loginSlice";

const store = configureStore({
  reducer: {
    loginSlice: loginSlice,
  },
});
export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;

export default store;
```

## useSelector / useDispatch

### useSelector( ) 활용
컴포넌트 내에서 어플리케이션 상태를 받아와 원하는 데이터를 선택하기 위해 사용됨.  
예) 로그인 상태 변경을 감지해야 하는 컴포넌트는 useSelector( )를 활용.

### useDispatch( ) 활용
새로운 어플리케이션 상태를 리듀서를 통해 반영하기 위해 사용됨.  
예) 로그인 페이지에서 로그인 처리 후, 새로운 어플리케이션 상태를 전송(dispatch)할 때 useDispatch( )를 활용.


## 비동기 호출과 createAsyncThunk( )

- 리덕스 툴킷의 `createAsyncThunk()`를 활용하여 비동기 작업을 보다 간편하게 처리
- API 호출 및 비동기 상태에 따른 처리를 효율적으로 구현
- `useActionState()`와 동일하게 상태에 따른 처리가 가능하지만 `createAsyncThunk()`의 경우 애플리케이션 전체에서 상태를 사용할 수 있음
- 이전 `LoginComponent`의 경우 `action`의 변경 상태를 감지 → `useEffect()`를 이용 → `useDispatch()`를 이용하는 복잡한 과정이 필요
- 개선된 방식에서는 `useDispatch()`로 `createAsyncThunk()`로 만들어진 함수를 호출하고 상태를 직접 처리

### loginPost.tsx
```
import axios from "axios";

export const loginPost = async (email: string, pw: string) => {
  const headers = {
    headers: {
      "Content-Type": "application/x-www-form-urlencoded",
    },
  };

  const form = new FormData();
  form.append("username", email);
  form.append("password", pw);

  const res = await axios.post(
    "http://localhost:8080/api/member/login",
    form,
    headers
  );

  // 2초간 delay
  await new Promise((resolve) => setTimeout(resolve, 2000));

  return res.data;
};

```
- loginSlice의 상태 변경과 createAsyncThunk 처리 
```

```

## 04 쿠키를 이용한 어플리케이션 상태 저장
- SPA의 '새로 고침' 문제 해결을 위해 브라우저 내 쿠키나 LocalStorage 활용.
- 'react-cookie' 라이브러리를 통해 리액트에서 브라우저에서 접근가능한 쿠키 관리 가능.

### 'react-cooke' 설치
```
npm install react-cookie
```

### util/cookieUtil.ts
```
import { Cookies } from "react-cookie";

const cookies = new Cookies();

/**
 * 쿠키 설정
 * @param name 쿠키 이름
 * @param value 쿠키 값
 * @param days 유지 기간 (일 수)
 */
export const setCookie = (name: string, value: string, days: number) => {
  const expires = new Date();
  expires.setUTCDate(expires.getUTCDate() + days); // 보관 기한
  return cookies.set(name, value, { path: "/", expires });
};

/**
 * 쿠키 가져오기
 * @param name 쿠키 이름
 */
export const getCookie = (name: string) => {
  return cookies.get(name);
};

/**
 * 쿠키 삭제
 * @param name 쿠키 이름
 * @param path 경로 (기본값 "/")
 */
export const removeCookie = (name: string, path = "/") => {
  cookies.remove(name, { path });
};
```

### 로그인 결과의 쿠키 보관
- 로그인에 성공하면 결과를 쿠키로 보관하는 처리 : loginSlice에서 cookieUtil을 이용하도록 수정
```
extraReducers: (builder) => {
    builder
      .addCase(loginPostAsync.fulfilled, (state, action) => {
        console.log("fulfilled");
        const newState: LoginInfo = action.payload;

        newState.status = "fulfilled";

        setCookie("member", JSON.stringify(newState), 1); // 추가

        return newState;
      })
      
      ...
  },
});
```

### 쿠키를 이용한 어플리케이션 상태 저장
- 새로고침으로 App이 초기화되었을때 쿠키에 저장된 사용자 정보를 로딩하도록 useCustomLogin 수정
- useEffect( )를 이용해서 사용자 정보가 없는 경우 쿠키 로딩 시도
- 로딩된 정보는 useDispatch( )를 이용해서 상태로 전환 