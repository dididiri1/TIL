

# Git 명령어

## Commit & Push

#### 1. 수정된 파일들을 스테이징 영역에 추가
```
git add .
```

#### 2. 메시지와 함께 커밋
```
git commit -m "first commit"
```

#### 3. 원격 서버 반영
```
git push origin master
```

---

## Fetch & Merge
### 1. 원격 저장소(origin)의 최신 소식만 가져오기
```
git fetch origin
```

### 2. 내 코드와 원격 코드 사이에 뭐가 다른지 확인하기 (간단히 보기)
```
git log HEAD..origin/master --oneline

e62a352 (origin/master, origin/HEAD) B유저가 수정했어요!
```

### 3. 원격지의 최신 코드를 내 로컬 코드에 합치기
```
git merge origin/master
```

---

## Stash

### Stash 기본 명령어
#### 1. 메시지 없이 빠르게 보관할 때 (추천하지 않음)
```
git stash
```

#### 2. 메시지를 넣어서 확실하게 보관할 때 (실무 권장 ⭐)
```
git stash push -m "보관할 메시지 작성"
```

#### 3. 새로 만든 파일(Untracked)까지 포함해서 보관할 때
```
git stash push -m "새 파일 포함 보관" -u
```

#### 4. 보관된 스테시 목록 확인하기
```
git stash list
```


### 보관한 코드 꺼내기

#### 1. 가장 최근에 보관한 코드 꺼내면서 금고에서 지우기 (충돌 나면 안 지워짐)
```
git stash pop
```

#### 2. 보관한 코드 꺼내기만 하고 금고에는 남겨두기
```
git stash apply
```

#### 3. 특정 번호의 스테시 꺼내기
```
git stash apply "stash@{0}"
```

### 스테시 삭제 및 정리

#### 1. 가장 최근 스테시(0번) 삭제하기
```
git stash drop
```

#### 2. 특정 번호의 스테시 삭제하기
```
git stash drop "stash@{0}"
```

#### 3. 모든 스테시 한 번에 다 지우기
```
git stash clear
```
