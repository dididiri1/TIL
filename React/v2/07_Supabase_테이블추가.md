# Supabasse 테이블 추가 방법

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/07_01.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/07_02.png?raw=true)

### 수퍼베이스 로그인
```
npx supabase login
```
### 데이터 구조 동기화 (Syncing)
type-gen을 실행하면 수파베이스 서버에 접속해서
현재 DB 구조(스키마)를 읽어와 database.types.ts 같은 파일로 만들어줍니다.
```
npm run type-gen
```

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/07_03.png?raw=true)

#### types.ts
```
import { type Database } from "./database.types";

export type PostEnitiy = Database["public"]["Tables"]["post"]["Row"];
export type ProfileEntity = Database["public"]["Tables"]["profile"]["Row"];

export type useMutationCallback = {
  onSuccess?: () => void;
  onError?: (error: Error) => void;
  onMutate?: () => void;
  onSettled?: () => void;
};
```

## 회원가입시 프로필 정보 자동 생성하기
```
import supabase from "@/lib/supabase";

export async function fetchProfile(userId: string) {
  const { data, error } = await supabase
    .from("profile")
    .select("*")
    .eq("id", userId)
    .single();

  if (error) throw error;
  return data;
}
```

```
export const QUERY_KEYS = {
  profile: {
    all: ["profile"],
    list: ["profile", "list"],
    byId: (userId: string) => ["profile", "byId", userId],
  },
};
```

```
import GlobalLoader from "@/components/global-loader";
import { useProfileData } from "@/hooks/queries/use-profile-data";
import supabase from "@/lib/supabase";
import { useIsSessionLoaded, useSession, useSetSession } from "@/store/session";
import { useEffect, type ReactNode } from "react";

export default function SessionProvider({ children }: { children: ReactNode }) {
  const session = useSession();

  const setSession = useSetSession();
  const isSessionLoaded = useIsSessionLoaded();

  const { data: profile, isLoading: isProfileLoading } = useProfileData(
    session?.user.id,
  );

  useEffect(() => {
    supabase.auth.onAuthStateChange((event, session) => {
      setSession(session);
    });
  }, []);

  if (!isSessionLoaded) return <GlobalLoader />;
  if (isProfileLoading) return <GlobalLoader />;

  return children;
}
```

### 리트라이 옵션 끄기
- 텐스택쿼리의 기본 리트라이 옵션이 3회로 설정 되어있기 떄문이다.
- 프로필 데이터가 업는 경우에는 4회까지 됨
![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/07_03.png?raw=true)

#### main.tsx
```
// 리트라이 옵션 끄기
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      retry: false,
      refetchOnWindowFocus: false,
    },
  },
});
```
🔒 RLS란?
정의: 테이블의 각 행(Row)에 대해 누가 읽고 쓸 수 있는지 제어하는 보안 규칙입니다.
기본값: 수파베이스는 테이블 생성 시 RLS가 기본으로 켜져 있으며, 별도의 **Policy(정책)** 가 없으면 외부 접근을 모두 차단합니다.
주의: 실제 서비스 배포 시에는 반드시 다시 활성화하고 Select, Insert 정책을 세부적으로 설정해야 합니다.
![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/07_05.png?raw=true)


### 2. React Query의 enabled 옵션
- useQuery가 실행되는 시점을 제어하는 아주 중요한 "스위치" 옵션입니다.

#### 🧐 왜 사용하는가? (핵심 이유)
- 에러 방지: userId가 없는 상태(undefined/null)에서 서버에 요청을 보내면 400 에러가 발생합니다. enabled는 이를 사전에 차단합니다.
- 불필요한 요청 차단: 데이터가 준비되지 않았을 때는 네트워크 자원을 낭비하지 않고 대기합니다.
- 자동 실행(Trigger): enabled 값이 false에서 true로 바뀌는 순간(예: 로그인 성공 후 ID 획득), 리액트 쿼리가 
  이를 감지하고 자동으로 데이터를 불러옵니다.
```
useQuery({
  queryKey: ['profile', userId],
  queryFn: () => fetchProfile(userId!),
  // ✅ 이 부분!
  enabled: !!userId, 
});
```