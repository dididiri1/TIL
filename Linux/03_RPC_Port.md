# [보안 점검] 웹 서버에 불필요한 111(RPC) 포트 닫는 방법

보안 점검에서 111 포트가 외부에 오픈되어 있음
Apache + Tomcat 웹 서버이며, 웹 서비스에는 불필요한 포트.

## 📌 2. 111 포트 확인
```
netstat -tulpn | grep 111
```
```
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1/systemd
tcp6       0      0 :::111                  :::*                    LISTEN      1/systemd
udp        0      0 0.0.0.0:111             0.0.0.0:*                           1/systemd
udp6       0      0 :::111                  :::*                                1/systemd
```

### 1️⃣ 원인 분석
111 포트는 RPC(Remote Procedure Call) 서비스인 rpcbind가 사용하는 포트.
Apache/Tomcat 웹서비스와 무관.
CentOS 7+/RHEL 7+에서는 rpcbind가 systemd 소켓 기반으로 동작 → 서비스 꺼도 systemd가 포트를 잡고 있음.

### 2️⃣ rpcbind 서비스 상태 
```
systemctl status rpcbind
```
![](https://github.com/dididiri1/TIL/blob/main/Linux/images/03_01.png?raw=true)

## 📌 2. 111 포트 닫는 방법
### 1️⃣ rpcbind 서비스 중지 & 비활성화
```
systemctl stop rpcbind
systemctl disable rpcbind
```

### 2️⃣ systemd 소켓 중지 & 비활성화
```
systemctl stop rpcbind.socket
systemctl disable rpcbind.socket
```

### 3️⃣ 포트 확인
```
netstat -tulpn | grep 111
```
#### → 아무것도 출력되지 않으면 성공.


### 📌 3. 결과
- 불필요한 111 포트 제거 완료.
- 웹 서버(Apache, Tomcat) 동작에는 영향 없음.
- NFS/백업 서버 사용 환경이면 서비스는 유지하고, 외부 방화벽으로 111 포트 차단 권장.
- DB 서버에서도 같은 원리 적용 가능 (NFS/클러스터 미사용 시 안전).

> ✅ 정리:  
> 111 포트는 웹 서비스와 무관한 rpcbind 서비스에서 열리는 포트.  
> 웹 서버에서 NFS 등 특별한 서비스가 없다면 rpcbind와 rpcbind.socket을 중지/비활성화하여 닫아도 무방.

