# 3. Zustand를 이용한 전역 상태관리

### 섹션 개요
- 섹션 목표:
  - 전역 상태 관리의 필요성을 이해하고,
  - Zustand를 활용하여 실제 프로젝트 규모에 적합한 상태 관리 구조를 설계﹒구현할 수 있다.

- 핵심 학습 포인트:
  - 전역 상태 관리의 개념 및 필요성 이해
  - Zustand의 기본 사용법(create, set, get, selector)
  - 미들웨어 활용
  - 실전 Todo 기능 구현을 통해 CRUD 패턴 설계 및 유지보수 가능한 Store 구조 구성


## 전역 상태 관리와 Zustand

- 전역 상태 관리
![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/03_01.png?raw=true)
- 서비스 규모가 커질수록 사용자 정보, UI 상태, 테마 설정처럼 여러 화면에서 공유해야 하는 데이터가 증가한다.
- 이러한 데이터들을 한 곳에서 체계적으로 관리하고, 어디서든 접근할 수 있도록 만드는 것이 전역 상태 관리이다.
- 만약 전역 상태 도구를 사용하지 않는다면, props를 계속해서 깊은 레벨까지 전달해야 하는 Props Drilling 문제가 심화된다.

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/03_02.png?raw=true)
- 전역 상태 관리를 위한 대표 도구들
  - Context API
    - React에서 기본 제공
    - 전역 상태 관리르 위한 기능이라보다는 Props Dilling 이슈를 해결하기 위해 제공된 기능에 가깝다.
    - 불필요하게 다 리덴더링 될 수 있는 치명적인 한계점 존재
    - 범용적인 전역 상태 관리 보다는 국소적인 데이터를 공유를 위해 더 자주 사용된다.
  - Redux, Jotai, Zustand, Recoil 등
    - 대부분 안정적이고 생태계가 넓지만, 그중에서도 Zustand가 최근 가장 많이 선택되고 있는 추세

### Zustand를 선택하는 이유
#### 1. 많은 사람들이 사용하고 있음

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/03_03.png?raw=true)
#### 2. 용량이 매우 가벼움

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/03_04.png?raw=true)
#### 3. 매우 직관적이어서 배우기 쉬움


## Zustand 기본 사용법 1

### npm i zustand 명령어로 라이브러리를 설치한다.
```
npm i zustand 
```
- 보통 store 폴더를 생성한 후, 이 안에 전역 상태 관리 로직을 분리해 관리한다.
- Zustand의 create 함수는 하나의 전역 store를 생성하는 핵심 함수로,
- 전역 상태(State)와 해당 상태를 수정하는 함수(Action)를 포함한 객체를 반환한다.

### 코드 예제
- set() 함수 사용 예시 1 - 단순 값 업데이트
```
increase: () => {
  const count = get().count;
  set({ count: count + 1 });
},
```

- set() 함수 사용 예시 2 - 함수형 업데이트 방식
```
increase: () => {
  set((store) => ({
    count: store.count + 1
  }));
},
```


#### store/count.tsx
```
import { create } from "zustand";

type Store = {
  count: number;
  increase: () => void;
  decrease: () => void;
};

export const useCountStore = create<Store>((set, get) => ({
  count: 0,
  increase: () => {
    set((store) => {
      return {
        count: store.count + 1,
      };
    });
  },
  decrease: () => {
    set((store) => {
      return {
        count: store.count - 1,
      };
    });
  },
}));
```

#### counter-page.tsx
```
import { Button } from "@/components/ui/button";
import { useCountStore } from "@/store/count";

export default function CounterPage() {
  const { count, increase, decrease } = useCountStore();

  return (
    <div>
      <h1 className="text-2xl font-bold">Counter</h1>
      <div>{count}</div>
      <Button
        onClick={() => {
          decrease();
        }}
      >
        -
      </Button>
      <Button onClick={increase}>+</Button>
    </div>
  );
}
```
인자 필요 없으면: onClick={increase}
인자 전달해야 하면: () => increase(값)

## Zustand 기본 사용법 2

### React Developer Tools 설치
https://chromewebstore.google.com/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=ko&pli=1

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/03_05.png?raw=true)

#### store.tsx
```

```

### 컴포넌트로 분리

#### Viewer.tsx
```
import { useCountStore } from "@/store/count";

export default function Viewer() {
  const { count } = useCountStore();

  return <div>{count}</div>;
}
```
#### Controller.tsx
```
import { useCountStore } from "@/store/count";
import { Button } from "../ui/button";

export default function Controller() {
  const { increase, decrease } = useCountStore();

  return (
    <div>
      <Button
        onClick={() => {
          decrease();
        }}
      >
        -
      </Button>
      <Button onClick={increase}>+</Button>
    </div>
  );
}
```

#### counter-page.tsx
```
import Controller from "@/components/counter/controller";
import Viewer from "@/components/counter/viewer";
import { useCountStore } from "@/store/count";

export default function CounterPage() {
  return (
    <div>
      <h1 className="text-2xl font-bold">Counter</h1>
      <Viewer />
      <Controller />
    </div>
  );
}
```

### 핵심 내용
- Zustand로 생성한 store를 컴포넌트에서 사용할 때,
- store 내부 값 중 어떤 업데이틀가 되면 해당 컴포넌트는 리렌더링 된다.
![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/03_06.png?raw=true)
- 따라서, 불필요한 렌더링을 줄이기 위해 store 전체가 아닌 필요한 특정 값만 선택적으로 구독하는 것이 중요하다.
- 이를 위해 useCountStore(store => store.xxx) 형태로 값을 선택하는 selector 함수를 사용할 수 있다.
- 실무에서는 selector를 컴포넌트에서 직접 작성하지 않고, store 파일에서 커스텀 훅 형태로 래핑하여 관리하는 방식을 많이 사용한다.
  - 목적: 코드 가독성 향상, 재사용성 증가, 렌더링 최적화, 유지보수 단순화

### 코드 예제
#### 1. Store 전체를 불러오는 경우
```
import { useCountStore } from "@/store/count";
import { Button } from "../ui/button";

export default function Controller() {
  const { increase, decrease } = useCountStore();
  return (
    <div>
      <Button onClick={increase}>+</Button>
      <Button onClick={decrease}>-</Button>
    </div>
  );
}
```

#### 2. 특정 프로퍼티만 선택적으로 불러오기(selector 함수)
```
import { useCountStore } from "@/store/count";
import { Button } from "../ui/button";

export default function Controller() {
  const increase = useCountStore((store) => store.increase);
  const decrease = useCountStore((store) => store.decrease);

  return (
    <div>
      <Button onClick={increase}>+</Button>
      <Button onClick={decrease}>-</Button>
    </div>
  );
}
```

### 3. 액션을 별도 객체로 묶어 반환하는 패턴
- 컴포넌트
```
const { increase, decrease } = useCountStore((store) => store.action);
```
#### Store.tsx
```
import { create } from "zustand";

type Store = {
  count: number;
  actions: {
    increase: () => void;
    decrease: () => void;
  };
};

export const useCountStore = create<Store>((set, get) => ({
  count: 0,
  actions: {
    increase: () => {
      set((store) => {
        return {
          count: store.count + 1,
        };
      });
    },
    decrease: () => {
      set((store) => {
        return {
          count: store.count - 1,
        };
      });
    },
  },
}));
```

### 커스텀 훅 패턴
- store 내부 구조가 바뀌어도 컴포넌트 수정 필요 없음
```
import { create } from "zustand";

type Store = {
  count: number;
  actions: {
    increase: () => void;
    decrease: () => void;
  };
};

export const useCountStore = create<Store>((set, get) => ({
  count: 0,
  actions: {
    increase: () => {
      set((store) => {
        return {
          count: store.count + 1,
        };
      });
    },
    decrease: () => {
      set((store) => {
        return {
          count: store.count - 1,
        };
      });
    },
  },
}));

export const useCount = () => {
  const count = useCountStore((store) => store.count);
  return count;
};

export const useIncreaseCount = () => {
  const increase = useCountStore((store) => store.actions.increase);
  return increase;
};

export const useDecreaseCount = () => {
  const decrease = useCountStore((store) => store.actions.decrease);
  return decrease;
};
```

```
import { useDecreaseCount, useIncreaseCount } from "@/store/count";
import { Button } from "../ui/button";

export default function Controller() {
  const increase = useIncreaseCount();
  const decrease = useDecreaseCount();

  return (
    <div>
      <Button
        onClick={() => {
          decrease();
        }}
      >
        -
      </Button>
      <Button onClick={increase}>+</Button>
    </div>
  );
}
```
