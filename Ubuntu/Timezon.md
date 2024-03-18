## 1. 우분투 타임존 설정

### 현재 시간을 확인
``` commend
date
```

### 기존의 localtime 파일을 제거하고 Asia/Seoul과 같은 원하는 타임존을 가리키는 심볼릭 링크를 생성
``` commend
sudo rm /etc/localtime
sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```
