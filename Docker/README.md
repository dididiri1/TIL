## Docker 명령어

### 이미지 생성
* `$ docker build -t ubuntu:18.04`

### 이미지 Pull 받기

* `$ docker pull ubuntu:18.04`

### 이미지 목록

* `$ docker images`

### 이미지 실행

* `$ docker run -d -p 8080:80 tomcat`

### 이미지 삭제

* `$ docker rmi {이미지ID}`

### 이미지 한번에 모두 삭제

* `$ docker stop $(docker ps -q)`
* `$ docker rm $(docker ps -a -q)`
* `$ docker rmi -f $(docker images -q)`



### 컨테이너 목록

* `$ docker ps`

### 모든 컨테이너 목록 (종료 포함)

* `$ docker ps -a`

### 컨테이너 삭제

* `$ docker rm {컨테이너ID}`

### 컨테이너 종료

* `$ docker stop {컨테이너ID}`

## Dit 옵션과 Attach 명령어

### Dit 옵션 (터미널 모드로 상호작용 하겠다.)

* `$ docker run -dit run -name myubut ubunut`

### Attach 옵션 (컨테이너 내부 접근)

* `$ docker attach {컨테이너ID}`

### Exec 명령어
  - 우분투는 OS이기 떄문에 dit, bash를 해줘야된다.
 
* `$ docker exec -dit {컨테이너ID} bash`

### 정리
  - OS (ubunut)
    - exec, attach 둘가 가능함 근데 attach로 많이씀.
    
  - While process (https, nginx)
    - exec 사용

## Docker Hub
### 도커 파일 이미지 생성
- $ docker build -t {이미지ID} 
* `$ docker build -t instagram-service:0.0 ./`
``` log
#> docker images
REPOSITORY                                                TAG
IMAGE ID       CREATED         SIZE
instagram-service                                         0.0
``` 

### 도커 이미지 태그 변경
- $ docker tag {이미지ID} {DockerHub ID}/{이미지ID}
* `docker tag instagram-service:0.0 dididiri1/instagram-service:0.0`
``` log
#> docker images
REPOSITORY                                                TAG
IMAGE ID       CREATED         SIZE
dididiri1/instagram-service                               0.0
``` 

### 도커 이미지 Push
- $ docker push {이미지ID}
* `docker push dididiri1/instagram-service:0.0`

### 도커 이미지 Pull
- $ docker pull {이미지ID}
* `docker pull dididiri1/instagram-service:0.0`


### Reference

- [메타코딩 | 도커 입문](https://www.youtube.com/watch?v=BivQIVzsiTM&list=PL93mKxaRDidGMzIllhYKx1d6aMg6_5wW3)


