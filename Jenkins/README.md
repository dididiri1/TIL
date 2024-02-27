# Jenkins

## 1. 서버 업데이트

## 2. Java 설치
* `apt-get install openjdk-11-jdk`

## 3. Jenkins 저장소 추가
### Jenkins 패키지 저장소의 GPG 키를 시스템에 추가한다.
* `wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -`

### Jenkins 패키지 저장소를 시스템의 저장소 목록에 추가한다.
* `sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list`

### sudo apt-get update 시, public key 를 사용할 수 없다는 에러 경우
* `ssudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys <PUBKEY>`

## 4. Jenkins 설치
### 저장소를 추가한 후, 다시 패키지 목록을 업데이트하고 Jenkins를 설치합니다.
* `sudo apt update`
* `apt-get install jenkins`


## 5. Jenkins 서비스 시작 및 활성화
* `service jenkins start`

## 6. Jenkins 설정
- 브라우저를 통해 Jenkins에 접속한다. 
- http://your_ec2_instance_public_ip_or_dns:8080
![](https://github.com/dididiri1/TIL/blob/main/Jnekins/images/01_01.png?raw=true)

- 최초 접속시 초기 암호를 입력하라고 요청한다. 암호는 명령어를 통해 서버에서 확인할 수 있다.
- 이 암호를 입력한 후 Jenkins의 초기 설정을 완료하면 Jenkins 설치 및 설정이 완료됩니다.
* `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

## 7. Jenkins 포트 변경 두가지 방법

### EC2 방화벽 9090 포트 개방
![](https://github.com/dididiri1/TIL/blob/main/Jnekins/images/01_03.png?raw=true)

### 1. Jenkins 설정 파일을 수정
- 파일 내에서 '--httpPort=9090' 변경한다.
* `vi /etc/default/jenkins`

### 2. Jenkins 설정 파일을 수정
- 파일 내에서 'Environment="JENKINS_PORT=9090' 변경한다.
* `vi /lib/systemd/system/jenkins.service`

### 설정 변경사항을 적용하기 위해 Jenkins 서비스를 재시작한다.
* `service jenkins restart`

### Jenkins 상태 확인 & 포트 확인
* `service jenkins status`

``` log
● jenkins.service - Jenkins Continuous Integration Server
     Loaded: loaded (/lib/systemd/system/jenkins.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2024-00-00 13:40:41 KST; 42s ago
  Main PID: 115526 (java)
      Tasks: 47 (limit: 1121)
     Memory: 311.5M
        CPU: 17.399s
     CGroup: /system.slice/jenkins.service
             └─115526 /usr/bin/java -Djava.awt.headless=true -jar /usr/share/java/jenkins.war --webroot=/var/cache/jenkins/war --httpPort=9090
``` 

### 웹 브라우저에서 변경된 포트를 사용하여 Jenkins에 접속해 확인
