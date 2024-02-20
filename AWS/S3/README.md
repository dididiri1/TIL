# AWS - S3 사용하기

## S3(Simple,Storage, Service)란
> S3는 AWS(Amazon Web Service)에서 제공하는 인터넷 스토리지 서비스이다.  
> S3(Simple Storage Service) 를 뜻함.

## 1. S3 버킷 만들기

### AWS 리전, 버킷 이름 설정 [1/1]
- 원하는 버킷 이름을 입력하고, AWS 리전을 선택한 후 "Next"를 클릭한다.
![](https://github.com/dididiri1/TIL/blob/main/AWS/S3/images/01.png?raw=true)

### 퍼블릭 엑세스 설정 [1/2]
![](https://github.com/dididiri1/TIL/blob/main/AWS/S3/images/02.png?raw=true)


### 버킷 생성 [1/3]
![](https://github.com/dididiri1/TIL/blob/main/AWS/S3/images/03.png?raw=true)

### 파일 업로드 [1/4]
- 생성한 버킷을 선택하고 "Upload" 버튼을 클릭합니다.
![](https://github.com/dididiri1/TIL/blob/main/AWS/S3/images/04.png?raw=true)

### 업로드한 파일 확인  [1/5]
![](https://github.com/dididiri1/TIL/blob/main/AWS/S3/images/05.png?raw=true)

### AccessDenied [1/6]
- AWS S3 버킷에서 AccessDenied 오류가 발생하는 경우, 버킷 정책을 확인하고 필요한 권한을 추가해야 한다.

![](https://github.com/dididiri1/TIL/blob/main/AWS/S3/images/06.png?raw=true)

## 2. 버킷 정책 설정(Public 읽기 권한 부여) [1/1]
- "권한" 탭으로 이동.
- "버킷 정책"을 클릭하여 버킷 정책 편집 페이지로 이동.
- "정책 생성기"을 클릭하여 권한을 설정한다.

![](https://github.com/dididiri1/TIL/blob/main/AWS/S3/images/07.png?raw=true)

- "s3:GetObject": 객체(파일) 읽기 권한
- "s3:PutObject": 객체(파일) 쓰기(업로드) 권한
- "s3:ListBucket": 버킷 내 객체 목록 조회 권한

## 3. 이미지 확인
![](https://github.com/dididiri1/TIL/blob/main/AWS/S3/images/08.png?raw=true)
![](https://github.com/dididiri1/TIL/blob/main/AWS/S3/images/09.png?raw=true)






