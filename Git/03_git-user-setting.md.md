
## Git Commit 에러 해결: User 정보 설정
Git을 처음 설치하거나 새 컴퓨터에서 커밋을 시도할 때 발생

## 발생 원인
신원 미확인: Git은 코드의 수정 이력을 남길 때 "누가(Author)" 작성했는지 기록함.
설정 누락: 새 시스템에 Git만 설치하고, 작성자의 이름(Name)과 이메일(Email)을 설정하지 않아 Git이 기록을 거부한다.

## 해결방법
### ✅ 사용자 정보 등록
```
# 1. 이름 등록 (GitHub 닉네임 또는 실명)
git config --global user.name "사용자이름"

# 2. 이메일 등록 (GitHub 가입 이메일 권장)
git config --global user.email "your-email@example.com"
```

### ✅ 설정 확인
- 정보가 정상적으로 입력되었는지 확인하려면 다음 명령어
```
git config --list
```