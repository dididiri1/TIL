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

## SELinux 비활성화 방법
### 1. getenforce 명령어
- SELinux의 현재 상태 확인
```
getenforce
```
- Enforcing: SELinux가 활성화
- Permissive: SELinux가 활성화되어 있지만, 정책 위반 시 경고만 발생하고 차단은 하지 않음.
- Disabled: SELinux가 비활성화
### 2. SELinux 설정 파일 수정:
- /etc/selinux/config 파일에서 SELINUX 값을 disabled로 변경
```

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing 
# SELINUXTYPE= can take one of these three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

### 3. 즉시 적용하기 위한 방법
> setenforce 명령어를 사용하여 SELinux를 즉시 활성화할 수도 있다.   
> 하지만, 이 방법은 시스템 재부팅 시 설정 파일에 지정된 값에 따라 다시 설정된다.

- enforcing 모드로 설정:
```
sudo setenforce 1
```
- permissive 모드로 설정:
```
sudo setenforce 0
```

### 4. 시스템 재부팅
- 설정을 적용하려면 시스템을 재부팅해야 합니다.
```
sudo reboot
```