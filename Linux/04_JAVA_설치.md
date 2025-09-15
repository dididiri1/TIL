# Linux 자바 수동 설치 및 여러 자바 버전 사용 

## 1. JDK7 수동 설치

### 1️⃣ 아키텍처 확인
- x86_64 → 64비트
```
[root@ip-172-31-40-000 ~]# uname -m
x86_64
```

### 2️⃣ JDK 7 파일 다운로드
![](https://github.com/dididiri1/TIL/blob/main/Linux/images/04_01.png?raw=true)

#### 로컬 PC에서 JDK 파일 다운로드
- JDK7: Oracle 아카이브 → jdk-7u80-linux-x64.tar.gz
https://www.oracle.com/java/technologies/javase/javase7-archive-downloads.html
→ Oracle 로그인 필요

### 1️⃣ 파일 업로드
- 로컬에서 FTP로 서버에 jdk-7u80-linux-x64.tar.gz를 업로드했다고 가정
- 서버 경로 예: /home/ec2-user/jdk-7u80-linux-x64.tar.gz

### 2️⃣ 설치 디렉토리 생성
```
sudo mkdir -p /usr/lib/jvm
```

### 3️⃣ 압축 풀기
```
sudo tar -xvzf /home/ec2-user/jdk-7u80-linux-x64.tar.gz -C /usr/lib/jvm
```
- 압축 풀리면 /usr/lib/jvm/jdk1.7.0_80 디렉토리 생성됨

### 4️⃣ 환경변수 등록
시스템 전체 적용하려면 /etc/profile.d에 스크립트 생성
```
sudo vi /etc/profile.d/java7.sh
```
- 내용:
```
export JAVA_HOME=/usr/lib/jvm/jdk1.7.0_80
export PATH=$JAVA_HOME/bin:$PATH
```

- 적용:
```
source /etc/profile.d/java7.sh
```

### 5️⃣ 설치 확인
```
[root@ip-172-31-40-000 jvm]# javac -version
javac 1.7.0_80
```
```
[root@ip-172-31-40-000 jvm]# java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
```

## 2. 기존 JDK7에서 JDK8로 변경
```
sudo mkdir -p /usr/lib/jvm/java8
sudo unzip jdk-8u322-ojdkbuild-linux-x64.zip -d /usr/lib/jvm/java8
```