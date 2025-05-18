# 섹션 2. React-Router

## 4. Reat-Router 설정


###  react-router 설치
``` 
npm i react-router
``` 

### main.tsx
``` 
import { createRoot } from "react-dom/client";
import "./index.css";
import { RouterProvider } from "react-router";
import router from "./router/root";

createRoot(document.getElementById("root")!).render(
  <RouterProvider router={router}></RouterProvider>
);

``` 

### src/router/root.tsx
``` 
import { createBrowserRouter } from "react-router";

import { lazy, Suspense } from "react";

const Loading = () => <div>Loading...</div>;

const Main = lazy(() => import("../pages/mainPage"));
const About = lazy(() => import("../pages/aboutPage"));

const router = createBrowserRouter([
  {
    path: "/",
    element: (
      <Suspense fallback={<Loading />}>
        <Main />
      </Suspense>
    ),
  },
  {
    path: "about",
    element: (
      <Suspense fallback={<Loading />}>
        <About />
      </Suspense>
    ),
  },
]);

export default router;
``` 

``` 
import { NavLink } from "react-router";

function MainPage() {
  return (
    <div className="text-3xl">
      <div className="flex">
        <NavLink to="/">Main</NavLink>
      </div>
    </div>
  );
}

export default MainPage;
``` 

``` 
function AboutPage() {
  return (
    <div className="text-3xl">
      <div>About Page</div>
    </div>
  );
}

export default AboutPage;
``` 

### 3. <NavLink>를 통한 이동
#### <NavLink>는 리엑트 내부에서 컴포넌트 처리
- Syspense 와 lazy()로 필요 시점에 컴포넌트 로딩
- '/' 경로 접근시 MainPage만 로딩하여 보여줌.
- 'About'을 클릭하면 AboutPage 컴포넌트만 추가적으로 로딩되는 것을 확인

## 4. 레이아웃 공통 설정

### 4. 레이아웃 컴포넌트와 children

#### layouts 폴더를 생성, basicLayout.tsx 컴포넌트를 생성
- BasicLayut 컴포넌트 
  - 상단에 공통 메뉴와 링크, 아래로 페이지 컴포넌트 출력
- 'children' 속성으로 컴포넌트 내부에 다른 컴포넌트 적용 가능.
- 간단한 구조
  - nav 와 div로 구분하여 작성  