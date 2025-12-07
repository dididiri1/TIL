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


## 🔹전역 상태 관리와 Zustand

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


## 🔹Zustand 기본 사용법 1

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

## 🔹Zustand 기본 사용법 2

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


## 🔹Zustand 미들웨어 1

#### 미들웨어란?
![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/03_07.png?raw=true)

#### Zustand 미들웨어 종류 및 활용
Zustand는 가벼운 상태관리 라이브러리지만, 다양한 미들웨어(middleware) 를 활용하여 확장성과 유지보수성을 높일 수 있다.
그 중 combine 미들웨어는 state와 actions를 분리하고 타입을 자동 추론하도록 도와주어 구조적인 설계가 가능하게 한다.

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/03_08.png?raw=true)

#### 🧠 핵심 정리
combine 미들웨어를 사용해 state와 action 함수를 분리하여 스토어를 생성하는 이유는,
첫 번째 인수로 전달한 state를 기반으로 타입을 자동 추론해주기 때문이다.
따라서 이전처럼 `create` 함수나 `combine` 함수에 별도로 타입을 정의하지 않아도 되며,
combine은 전달된 초기 state 객체를 기준으로 스토어의 타입을 자동으로 추론한다.

### ✨ combine 미들웨어 장점 요약
| 장점 | 설명                                          |
|------|---------------------------------------------|
| 역할 분리 | state와 actions가 명확히 분리되어 가독성과 유지보수성 향상      |
| 타입 자동 추론 | 첫 번째 인수의 state 기반으로 타입 자동 추론, 별도의 타입 선언 불필요 |
| 확장성 향상 | 프로젝트 규모가 커져도 구조적 확장이 쉬움                     |
| 가독성 개선 | 기능별 구조가 명확하여 코드 이해가 쉬워짐                     |
| 테스트 편의성 | actions가 분리되어 있어 유닛 테스트 작성이 용이              |

#### 🚫 사용 전 (state & actions 혼합 구조)
- 모든 상태와 액션이 하나의 객체 안에 있어 확장 시 복잡해짐
```
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
#### ✅ 사용 후 (combine 미들웨어 활용)
- 구조가 명확히 분리되고, 타입 추론이 자동으로 처리됨
- 
```
export const useCountStore = create(
  combine({ count: 0 }, (set, get) => ({
    actions: {
      increase: () => {
        set((state) => {
          return {
            count: state.count + 1,
          };
        });
      },
      decrease: () => {
        set((state) => {
          return {
            count: state.count - 1,
          };
        });
      },
    },
  })),
);
```

### 🧊 immer 미들웨어 요약
`immer`는 Zustand에서 상태 업데이트 시 **불변성(immutability)** 을 자동으로 관리해주는 미들웨어이다.  
기존에는 `set({ value: value + 1 })` 와 같이 얕은 복사를 직접 수행해야 했지만,  
`immer`를 사용하면 **직접 값을 변경하는 방식처럼 작성해도 불변성이 자동으로 유지**된다.

### immer 설치
```
npm i immer
```

### ✨ 장점
| 장점 | 설명 |
|------|------|
| 가독성 향상 | 불변성 처리를 숨기므로 코드가 간결하고 읽기 쉬움 |
| 복잡한 구조 업데이트에 강력 | 중첩 객체/배열 업데이트 시 코드가 단순해짐 |
| 실수 방지 | 불변성 관리 실수로 인한 버그 감소 |

### ❌ immer 사용 전
```
set((state) => ({
  count: state.count + 1,
}));
```
- 항상 새로운 객체 반환 필요
- 깊은 객체/배열일 경우 매우 복잡해짐
```
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

### ✅ immer 사용 후
```
set((state) => {
  state.count += 1;
})
```
- 직관적이고 깔끔한 코드
- 깊은 객체 구조 업데이트도 쉬움
- 내부적으로 새로운 객체 생성 → 불변성 유지
```
export const useCountStore = create(
  immer(
    combine({ count: 0 }, (set, get) => ({
      actions: {
        increaseOne: () => {
          set((state) => {
            state.count += 1;
          });
        },
        decreaseOne: () => {
          set((state) => {
            state.count -= 1;
          });
        },
      },
    })),
  ),
);
```

## 🔹Zustand 미들웨어 2

### 🧊 subscribeWithSelector
- selector 함수로 특정 상태만 구독하며, 해당 값이 변할 때 특정 동작을 수행할수 있도록 돕는 미들웨어
- 스토어 전체를 구독하는 것이 아니라 필요한 값만 최소한으로 감지할 수 있어 효율적

#### import
```
import { combine, subscribeWithSelector } from "zustand/middleware";
```

#### 사용법
```
useCountStore.subscribe(
  (store) => store.count,
  (count, prevCount) => {
    console.log(count, prevCount);
  },
);
```

#### 결과
```
1 0        count.ts:37 
2 1        count.ts:37
3 2        count.ts:37
...
```
> 참고: subscribeWithSelector는 보통 사용자가 로그아웃을 해서 세션을 보관하는 어떤 스토어의 값을
> 바뀌었을 때 로그인 페이지로 다시 보낸다든지 하는 사이트 이펙트를 관린할때 종종 사용됨.

### 🧊 persist
store 값을 localStorage, sessionStorage 등 클라이언트 스토리지에 영구 저장하도록 도와주는 미들웨어
새로고침해서 리액트 앱이 초기화 되도 store의 count 값이 불러와서 적용 시켜준다, 그런데 action 객체 비어있기 떄문에
함수들이 사라져서 버튼이 동적하지 않는다.
이를 해결하기 위해, persis 옵션 partialize 안에 직접 명시해주는것이 안전하다.
count 값을 제외한 action 함수는 스토리지에 보관하지 않기 때문에 아까 같은 문제는 발생하지 않음.

#### localStorage
![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/03_09.png?raw=true)
```
import { persist } from "zustand/middleware";
```
```
export const useCountStore = create(
  persist(
    subscribeWithSelector(
      immer(
        combine({ count: 0 }, (set, get) => ({
          actions: {
            increaseOne: () => {
              set((state) => {
                state.count += 1;
              });
            },
            decreaseOne: () => {
              set((state) => {
                state.count -= 1;
              });
            },
          },
        })),
      ),
    ),
    {
      name: "countStore",
      partialize: (store) => ({
        count: store.count,
      }),
    },
  ),
);
```

#### sessionStorage
```
import { createJSONStorage } from "zustand/middleware";
```
```
export const useCountStore = create(
  persist(
    
    ...
    
    {
      name: "countStore",
      partialize: (store) => ({
        count: store.count,
      }),
      storage: createJSONStorage(() => sessionStorage),
    },
  ),
);
```

### devtools
- Redux DevTools와 동일한 UI로 store의 상태와 액션 호출 히스토리를 디버깅할 수 있도록 지원
- Chrome 확장 프로그램 Redux DevTools 설치 필요
#### Redux DevTools 설치 링크
https://chromewebstore.google.com/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=ko

#### 코드 예제
```
export const useCountStore = create(
  devtools(
    persist(
      subscribeWithSelector(
        immer(
          combine({ count: 0 }, (set, get) => ({
            actions: {
              increaseOne: () => {
                set((state) => {
                  state.count += 1;
                });
              },
              decreaseOne: () => {
                set((state) => {
                  state.count -= 1;
                });
              },
            },
          })),
        ),
      ),
      {
        name: "countStore",
        partialize: (store) => ({
          count: store.count,
        }),
        storage: createJSONStorage(() => sessionStorage),
      },
    ),
    {
      name: "countStore",
    },
  ),
);
```

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/03_10.png?raw=true)

### 미들웨어는 적용 순서가 중요하다.
- 적용 순서에 따라 동작 방식이 달라질 수 있다.
- devtools → persist → subscribeWithSelector → immer → combine


## 투두리스트 UI 구현하기