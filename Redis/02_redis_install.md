## 섹션 2. Redis 설치

### Redis 설차
- [MacOS](https://redis.io/docs/install/install-redis/install-redis-on-mac-os/)
- [Windows](https://redis.io/docs/install/install-redis/install-redis-on-windows/)
    - [Redis 다운로드](https://github.com/microsoftarchive/redis/releases)
### Redis 실행
- 파일 설치경로: C:\Program Files\Redis\redis-cli.exe 실행
* `$ redis-cli`
* `$ ping`

## Docker Desktop 설치
- [Mac](https://docs.docker.com/desktop/install/mac-install/)
- [Windows](https://docs.docker.com/desktop/install/windows-install/)

### 컨테이너 실행(Windows Power Shell)
* `$ docker run -p 6379:6379 -d --rm redis/redis-stack-server`

### Docker
- WSL 실행 또는 인텔리제이
``` commend
#> docker pull redis
#> docker run --name [서버이름] -d -p 6379:6379 redis
#> docker exec -it [CONTAINER ID] /bin/bash
#> redis-cli
```
- redis 컨테이너 재시작
``` commend
#> docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED        STATUS                            PORTS                    NAMES
f2effc632237   redis     "docker-entrypoint.s…"   24 hours ago   Exited (255) About a minute ago   0.0.0.0:6379->6379/tcp   redis
#> docker start [컨테이너 ID]
```

### 데이터 저장/조회/삭제
#### 저장
* `$ SET lecture inflearn-redis`
#### 조회
* `$ GET lecture`
#### 삭제
* `$ DEL lecture`

### Reference
- [인프런 | 실전! Redis 활용](https://inf.run/7ctks)