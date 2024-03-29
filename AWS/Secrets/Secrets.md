# AWS Secrets Manager
![](https://github.com/dididiri1/TIL/blob/main/AWS/Secrets/images/aws-secrets-logo.png?raw=true)

## AWS Secrets Manager란?
> AWS Secrets Manager는 AWS(Amazon Web Services)에서 제공하는 관리형 서비스로, 애플리케이션, 서비스,    
> IT 리소스를 위한 비밀(예: 데이터베이스 비밀번호, API 키, 액세스 키 등)을 안전하게 저장, 관리 및 검색할 수 있게 해줍니다.  
> 이 서비스는 중요한 정보를 암호화하여 저장하는 동시에, 필요할 때 이러한 비밀을 안전하게 검색할 수 있는 기능을 제공합니다.


## 1. AWS Secrets Manager 생성

### 1) AWS Secrets Manager에 비밀 생성
- Secret Manager에 들어가 "새 보안 암호 저장"을 선택한다.
- 저장하고 싶은 키와 키값을 입력 후  "다음"을 누른다.
![](https://github.com/dididiri1/TIL/blob/main/AWS/Secrets/images/01.png?raw=true)


### 2) 보안 암호 구성
- 보안 암호 이름은 '/secret/{어플리케이션}'으로 설정한다 (필수)
![](https://github.com/dididiri1/TIL/blob/main/AWS/Secrets/images/02.png?raw=true)

### 3) 교체 구성 - 선택 사항
- 자동 교체 구성은 비활성으로 다음, 활성화 할 경우 Lambda를 이용해 주기적으로 새로운 키를 발급해서 교체 가능
![](https://github.com/dididiri1/TIL/blob/main/AWS/Secrets/images/03.png?raw=true)


## 2. IAM 설정
- 사용자 이름을 입력해준 뒤 "다음"
![](https://github.com/dididiri1/TIL/blob/main/AWS/Secrets/images/04.png?raw=true)

- Secrets Manager 사용 권한을 등록
![](https://github.com/dididiri1/TIL/blob/main/AWS/Secrets/images/05.png?raw=true)

- 엑세스키 만들기
![](https://github.com/dididiri1/TIL/blob/main/AWS/Secrets/images/06.png?raw=true)

- CLI에서 사용할 것이기 때문에 CLI 선택한다.
![](https://github.com/dididiri1/TIL/blob/main/AWS/Secrets/images/07.png?raw=true)

-  생성된 엑세스 키는 ".csv 파일 다운로드" 버튼을 눌러 csv파일로 관리한다.
![](https://github.com/dididiri1/TIL/blob/main/AWS/Secrets/images/08.png?raw=true)


## 3. CLI 설치
[AWS CLI 설치](https://github.com/dididiri1/TIL/blob/main/AWS/CLI/CLI.md)

- 인텔리제이의 또는 PowerShell 아무거나 터미널 사용하면 된다.
- aws configure 명령어 실행 Access Key, Secret Access Key 등록 한다.
``` log
aws configure
``` 
![](https://github.com/dididiri1/TIL/blob/main/AWS/Secrets/images/09.png?raw=true)


## 4. Spring Boot 연결하기 
### bootstrap.yml 세팅
- resources - bootstrap.yml 파일 생성후 밑에 내용 붙쳐넣기.
- 시크릿매니저 이름은 생성한 "/secret/instagram"에서 instagram만 입력한다.
``` ymi
aws:
  secretsmanager:
    name: {시크릿매니저 이름}
cloud:
  aws:
    region:
      static: ap-northeast-2
``` 


### 의존성 추가
- build.gradle에 해당 implement를 추가해주면 AWS SecretManger를 사용할 수 있다.
``` log
// AWS Secrets
implementation 'org.springframework.cloud:spring-cloud-starter-bootstrap:3.1.3'
implementation 'org.springframework.cloud:spring-cloud-starter-aws-secrets-manager-config:2.2.6.RELEASE'
``` 

### application.yml에 secrets Manager키값들 넣어준다.
![](https://github.com/dididiri1/TIL/blob/main/AWS/Secrets/images/10.png?raw=true)

### 최종 테스트
- AWS SecretManger 값들이 출력된다.
![](https://github.com/dididiri1/TIL/blob/main/AWS/Secrets/images/11.png?raw=true)