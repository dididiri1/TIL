# 섹션 12. TanStack Query (React Query) 설정

- **[TanStack Query (React Query)]**
    - 서버 데이터의 캐싱 기능을 제공하여 **불필요한 서버 호출을 줄임**
    - **간결한 코드**로 비동기 처리 구현 가능
    - 데이터 동기화, 로딩 상태, 에러 핸들링 등을 **자동으로 관리**

- **[Zustand]**
    - 기존 Redux의 복잡함을 해소한 **간편한 상태 관리 라이브러리**
    - 리덕스 툴킷 이전의 **불편함 없이 가벼운 전역 상태 관리** 가능
    - 보일러플레이트 코드 최소화

---

## 🎯 개발 목표

1. **TanStack Query(React Query)의 설정 및 상품 관리 기능 적용**
    - 상품 목록 불러오기
    - 서버 데이터 캐싱
    - 로딩/에러 처리 UI 연동

2. **Zustand 설정 및 로그인 상태 처리 적용**
    - 로그인 상태 전역 관리
    - 로그인/로그아웃 처리
    - 사용자 인증 상태에 따른 UI 분기 처리

## TanStack Query 라이브러리 설치
```
npm i @tanstack/react-query
```
```
npm i @tanstack/react-query-devtools 
```

## TanStack Query의 설정
QueryClient를 지정 : main.tsx
- 나중에 스토어 뺌 -> Zustand 변경
```

```

# TanStack Query 핵심 개념 정리

## 핵심 개념
TanStack Query는 데이터를 보관하며, 상태에 따라 서버에서 데이터를 가져오거나 캐시된 데이터를 활용한다.

## 데이터 상태 변화
개발자 도구에서 확인할 수 있는 대표적인 상태는 다음과 같다.
- fetching: 데이터를 가져오는 중
- fresh: 데이터가 최신 상태로 처리 완료됨

## staleTime의 활용
staleTime은 데이터의 신선도를 판단하는 기준 시간이다. 이 시간이 지나면 해당 데이터는 stale 상태가 되어 다시 서버로부터 데이터를 가져오게 된다.

## 상태 변화 예시
개발자 도구를 통해 다음과 같은 상태 흐름을 확인할 수 있다:
fetching → fresh → (10초 후) stale

## 서버 호출 타이밍
현재 화면이 활성화될 때 TanStack Query는 필요한 경우 서버에 다시 요청을 보낸다. 사용자가 다른 프로그램을 선택했다가 돌아오는 경우 일정 시간 동안은 서버를 호출하지 않고 캐시된 데이터를 사용한다.

# useQuery의 자동 서버 호출 동작

## 기본 동작
'useQuery'는 기본적으로 브라우저가 다시 활성화될 때 서버를 자동으로 다시 호출하는 동작을 가진다.  
이는 캐시된 데이터가 'stale' 상태일 경우 적용된다.

## stale 상태에 따른 서버 호출
- stale 상태의 데이터는 오래된 것으로 간주되어 서버에 재요청을 보낸다.

## staleTime 설정에 따른 동작 변화
- staleTime이 짧을수록 데이터가 빠르게 stale 상태로 전환되어 서버를 자주 호출하게 된다.
- staleTime이 길면 데이터가 오랜 시간 fresh 상태를 유지하므로 서버 호출이 줄어든다.

## 참고 링크
자세한 옵션은 공식 문서에서 확인 가능하다:  
https://tanstack.com/query/latest/docs/framework/react/overview
