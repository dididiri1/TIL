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

