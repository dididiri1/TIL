# [Linux] AWS EC2 Linux - 계정 생성 및 sudo 권한 부여

## 1. 사용자 계정 생성 및 sudo 권한 부여 방법
### 2. 새 사용자 계정 생성
- adduser 명령어를 사용해 새 계정을 생성한다.
```
sudo adduser {계정명}
```

### 3. 비밀번호 생성
```
sudo passwd {계정명}
```

### 4. sudo 권한 부여 (필요 시)
```
sudo usermod -aG wheel {계정명}
```


### 5. SSH 설정 파일 수정 (비밀번호 로그인 허용)
- 기본적으로 비밀번호 로그인은 비활성화되어 있으므로 아래 설정을 바꿔야 한다.
```
sudo vi /etc/ssh/sshd_config
```
🔁 PasswordAuthentication을 yes로 설정해야 비밀번호 로그인이 가능하다.
![](https://github.com/dididiri1/TIL/blob/main/Linux/images/02_01.png?raw=true)

### 6. SSH 서비스 재시작
```
sudo systemctl restart sshd
```

### ⚠️주의사항
> 인터넷에 노출된 EC2 인스턴스에서 비밀번호 로그인은 권장되지 않습니다.   
> 반드시 강력한 비밀번호를 설정하고, 가능하면 AllowUsers 등으로 접속 가능한 계정을 제한하세요.  
> 테스트 후에는 다시 PasswordAuthentication no로 되돌리는 것이 좋습니다.