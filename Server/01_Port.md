# [Linux] ssh 포트 변경하기

### 1) SSH 설정 파일 수정
```
vi /etc/ssh/sshd_config
```

### 2) sshd_config 파일에서 Port 22를 찾아 Port 2024로 변경

![](https://github.com/dididiri1/TIL/blob/main/Server/images/01_01.png?raw=true)

### 3) SSH 서비스 재시작
```
sudo systemctl restart sshd
```


```
[root@ip-172-01-01-01 ~]# sudo systemctl restart sshd  
Job for sshd.service failed because the control process exited with error code.  
See "systemctl status sshd.service" and "journalctl -xe" for details.  
```

>문제 원인: SELinux는 기본적으로 보안 정책을 엄격하게 적용하며, SSH 데몬이 새로운 포트(여기서는 2024번 포트)를 바인딩하려고 할 때 
> 이를 차단하는 정책을 가지고 있다. 이 경우, name_bind 액세스 권한이 부족하여 발생한 오류이다.

### 4) 해결 방법
- SELinux의 정책을 수정하여 SSH 데몬이 포트 2024에서 바인딩할 수 있도록 허용한다.

#### 1. semanage 명령어 실행
```
sudo semanage port -m -t ssh_port_t -p tcp 2024
```

#### 2. 포트가 정상적으로 열렸는지 확인
```
sudo semanage port -l | grep ssh
```
```
ssh_port_t       tcp      2024, 22
```

### 5) SSH 서비스 재시작
```
sudo systemctl restart sshd
```
### 6) SSH 서비스 상태 확인
```
sudo systemctl restart sshd
```

![](https://github.com/dididiri1/TIL/blob/main/Server/images/01_02.png?raw=true)
