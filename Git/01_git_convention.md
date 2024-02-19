# Git Commit Message Convention

## Commit 예시
```
feat: 프로필이 이미지 미리보기 기능 추가(#15)

사용자의 편의를 위해 이미지 첨부시 미리보기 제공

 - img 태그 커스텀
 - 파일 이미지 확장자 체크 
 
Close: #1, #15 
```

## Type
| 명령어       |            예시             |
|:----------|:-------------------------:|
| feat      |         새로운 기능 추가         |
| fix       |           버그 수정           |
| design    |           문서 수정           |
| refactor  |          코드 리팩토링          |
| style     |     공백, 세미콜론 등 스타일 수정     |
| docs      | 문서 수정(추가, 수정, 삭제, README) |
| test      |            테스트            |
| chore     |   기타 변경사항(빌드 스크립트 등..)    |
| perf      |           성능 개선           |

## 제목(subject) 규칙
- 제목 첫 글자는 대문자 작성
- 제목 끝에 마침표(.) 금지
- 필요시 이슈 번호 추가

## 본문(body) 규칙
- 제목과 본문을 한 줄 띄워 분리하기
- 본문에 여러줄의 메시지를 작성할 땐 "-"로 구분하기
- 한 줄에 최대 72 글자까지만 입력하기
- 무엇을, 왜, 어떻게 했는지 작성

## 꼬리말(footer) 규칙
- 꼬리말은 선택사항이며, 이슈 트래킹을 위해서 사용된다  
- [#이슈 번호] 형식으로 작성
- 여러개일 경우 쉼표로 구분

### Reference
- [Udacity Git Commit Message Style Guide](https://udacity.github.io/git-styleguide/)
- [[유투브] 코햄의 프론트앤드유치원](https://www.youtube.com/watch?v=lQsDcg_jkJs&t=5s)
