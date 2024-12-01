# 섹션 5.


## 4.1) React.js를 소개합니다.

### React의 기술적인 특징

1. 컴포넌트를 기반으로 UI를 표현한다.
2. 화면 업데이트 구현이 쉽다.
3. 화면 업데이트가 빠르게 처리된다.

## 4.2) 첫 React App 생성하기
![](https://github.com/dididiri1/TIL/blob/main/React/images/05_01.png?raw=true)


* Vite로 리액트 앱 생성

(Vite? 프론트엔드 개발을 위한 최신 빌드 도구, 개발 툴. 기본 설정이 적용된 React App 생성 가능)

1. 폴더 확인 후 터미널에 아래 코드 실행 (command + j 터미널 열기)
```
npm create vite@latest
```

2. create-vite 생성 확인 : y 입력

3. 프로젝트 이름 설정

4. 프레임워크 선택 : React → JavaScript

확인해보면 node_modules와 package-lock.json이 설치가 안된 것을 볼 수 있다.

앞 시간에 배웠던 것처럼 터미널에 아래와 같이 입력해준다.
```
npm i
```


- React App 실행(개발 모드로 실행)
```
npm run dev

VITE v6.0.1  ready in 328 ms

➜  Local:   http://localhost:5173/
➜  Network: use --host to expose
➜  press h + enter to show help
```

- 헬프 명령어
```
h
```
- 그 외 Vite 명령어
```
r + enter : 현재 실행중인 Vite 서버를 재시작 함
u + enter : 현재 실행중인 Vite 서버의 URL을 콘솔에 표시함
o + enter : 현재 실행중인 Vite 웹 애플리케이션을 브라우저에서 열어줌
c + enter : 콘솔 창 클리어
q + enter : 현재 실행중인 Vite 서버 종료
```

![](https://github.com/dididiri1/TIL/blob/main/React/images/05_02.png?raw=true)

