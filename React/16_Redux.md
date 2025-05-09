# [React] 리액트 Redux
![](https://github.com/dididiri1/TIL/blob/main/React/images/16_01.png?raw=true)

## @reduxjs/toolkit이란?
Redux Toolkit(RTK)은 Redux 공식에서 권장하는 Redux 상태 관리를 간편하게 할 수 있게 도와주는 도구 모음이다.

복잡한 reducer, action, store 설정을 훨씬 간단하게 만들어준다.

### 주요 특징:
- createSlice, configureStore 등을 통해 보일러플레이트 코드 감소
- 내장된 Immer, Thunk 지원
- Redux 설정을 구조화하고 표준화

### 🛠 설치 방법
```
npm install @reduxjs/toolkit react-redux
```

### ⚛️ @reduxjs/toolkit 주요 함수
#### 1. configureStore()
- Redux store를 생성하는 함수이다.
- 기존의 createStore()보다 설정이 간단하며, 미들웨어, DevTools 연동 등을 기본 지원한다.
```
import { configureStore } from '@reduxjs/toolkit';

const store = configureStore({
  reducer: {
    counter: counterReducer
  },
});
```
#### 2. createSlice()
```
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => { state.value += 1; },
    decrement: (state) => { state.value -= 1; },
  },
});

export const { increment, decrement } = counterSlice.actions;
export default counterSlice.reducer;
```

#### 3. createAsyncThunk()
- 비동기 요청 처리용 액션 생성 도구이다.
- API 요청과 로딩/성공/실패 상태 관리를 자동으로 처리한다.
```
export const fetchData = createAsyncThunk('data/fetch', async () => {
  const response = await fetch('/api/data');
  return await response.json();
});
```


## react-redux란?
React에서 Redux를 사용할 수 있도록 연결해주는 공식 바인딩 라이브러리이다.
React 컴포넌트에서 Redux 상태를 쉽게 사용할 수 있게 해주는 Hooks(useSelector, useDispatch)
를 제공한다.

### 🛠 설치 방법
```
npm install react-redux
```

### ⚛️ react-redux 주요 훅
#### 1. useSelector()
- Redux store의 상태를 읽을 때 사용한다.
```
const count = useSelector((state) => state.counter.value);
```

#### 2. useDispatch()
- Redux 액션을 실행(디스패치)할 때 사용한다.
```
const dispatch = useDispatch<AppDispatch>();

dispatch(increment());
```

#### 3. Provider
Redux store를 React 앱에 주입하기 위한 컴포넌트이다.
```
import { Provider } from 'react-redux';
import store from './store';

<Provider store={store}>
  <App />
</Provider>
```
