# AWS CLI 설치 및 S3 다운받기

## 1. CLI 다운받기
* [AWS CLI 설치](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/getting-started-install.html)

### OS에 맞게 AWS CLI를 설치
![](https://github.com/dididiri1/TIL/blob/main/AWS/S3/images/10.png?raw=true)

### CMD에서 정상 설치여부를 확인
``` log
C:\Users\USER>aws --version
aws-cli/2.15.30 Python/3.11.8 Windows/10 exe/AMD64 prompt/off
``` 

## 2. 계정 액세스 키 발급하기
### 기존에 access key가 없었다면 아래의 docs를 참고해서 계정 키를 생성
* [AWS 계정 및 엑세스 키](https://docs.aws.amazon.com/ko_kr/powershell/latest/userguide/creds-idc.html)


## 3. aws configure 값 입력하기
``` log
C:\Users\OOOOO> aws configure
AWS Access Key ID [None]: 
AWS Secret Access Key [None]: 
Default region name [None]:     # 안해도됨 엔터.
Default output format [None]:   # 안해도됨 엔터.
``` 

## 4. 다운받기
### 개별파일 말고 한번에 다운받으려면 recursive 설정을 붙여줘야 한다고 한다.
``` log
aws s3 cp s3://{버킷이름}/{디렉토리} C:\Users\USER\Downloads --recursive
``` 

## 5. 파일에 저장된 값 확인하기(Power Shell)
``` 
cat ~/.aws/credentials
``` 