# JAVA 8 설치

- 누리2 모듈은 JDK8 ~ JDK11 까지 지원 
- 누리2 설치 환경: '/home/nuri2/'


## JAVA 8 환경 사용 방법

### 디렉토리 생성
```
sudo mkdir -p /home/nuri2
```

### 압축 해제
```
sudo unzip jdk-8u322-ojdkbuild-linux-x64.zip -d /home/nuri2
```

### 권한 설정(필요시)
```
chmod 755 -R jdk-8u322-ojdkbuild-linux-x64
```


### 실행 확인
```
./bin/java -version

openjdk version "1.8.0_322"
OpenJDK Runtime Environment (build 1.8.0_322-06)
OpenJDK 64-Bit Server VM (build 25.322-b06, mixed mode)
```

1. nuri2_launcher.sh 수정
---------------------------
#!/bin/bash
..
JAVA_HOME=/home/nuri2/java8/bin
# nuri2 path
NURI2_HOME=/home/nuri2
NURI2_NAMES=nuri2.jar
...
---------------------------