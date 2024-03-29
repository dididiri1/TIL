# AWS CLI

## CLI 다운받기
* [AWS CLI 설치](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/getting-started-install.html)

### OS에 맞게 AWS CLI를 설치
![](https://github.com/dididiri1/TIL/blob/main/AWS/CLI/images/01.png?raw=true)

## 1. Windows CLI 설치

### CMD에서 버전 확인
``` log
C:\Users\USER>aws --version
aws-cli/2.15.30 Python/3.11.8 Windows/10 exe/AMD64 prompt/off
``` 

## 계정 액세스 키 발급하기
- 기존에 access key가 없었다면 아래의 docs를 참고해서 계정 키를 생성
* [AWS 계정 및 엑세스 키](https://docs.aws.amazon.com/ko_kr/powershell/latest/userguide/creds-idc.html)


## aws configure 값 입력하기
``` log
C:\Users\OOOOO> aws configure
AWS Access Key ID [None]: 
AWS Secret Access Key [None]: 
Default region name [None]:     # 안해도됨 엔터.
Default output format [None]:   # 안해도됨 엔터.
``` 

## 2. 우분투 CLI 설치 
``` 
sudo apt install curl unzip
``` 
- curl 명령 사용해서 awscliv2.zip 파일을 다운받고 압축을 푼다.
``` 
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```
``` 
unzip awscliv2.zip
``` 

- 설치 프로그램을 실행 설치 명령은 새로 압축을 푼 install 디렉터리의 aws이라는 이름의 파일을 사용한다.
``` 
sudo ./aws/install
``` 
- 다음 명령을 사용하여 설치를 확인한다.
``` 
aws --version
```

## aws configure 등록
``` log
aws configure
``` 

## S3 CLI 권한으로 다운받기
### 개별파일 말고 한번에 다운받으려면 recursive 설정을 붙여줘야 한다고 한다.
``` log
aws s3 cp s3://{버킷이름}/{디렉토리} C:\Users\USER\Downloads --recursive
``` 

## 파일에 저장된 값 확인하기(Power Shell)
``` 
cat ~/.aws/credentials
``` 


