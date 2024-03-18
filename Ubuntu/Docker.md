# 우분투 Docker 설치

![](https://github.com/dididiri1/TIL/blob/main/Ubuntu/images/01.png?raw=true)

### 1. 우분투 업데이트
``` commend
apt update
```

### 2. 패키지 설치
``` commend
apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

### 3. GPG키를 추가
``` commend
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

### 4. apt 저장소를 추가
``` commend
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

### 5. 우분투 업데이트
``` commend
apt update
```

### 6. Docker 설치
``` commend
apt install docker-ce docker-ce-cli containerd.io
```

### 7. Docker가 설치 확인
``` commend
sudo systemctl status docker
```

