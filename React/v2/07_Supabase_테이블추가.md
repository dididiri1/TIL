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
