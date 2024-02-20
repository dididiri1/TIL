# AWS - EC2 인스턴스에 HTTPS 적용하기

## 1. Route 53 도메인 연결

- 호스팅 영역 생성
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/01.png?raw=true)

- 호스팅 영역 생성
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/03.png?raw=true)


## 2. 가비아 네임서버 설정
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/02.png?raw=true)


## 3. Certificate Manager 인증서 발급
- 퍼블릭 인증서 요청
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/04.png?raw=true)
- 도메인 이름 입력
  - 인증서를 적용할 도메인을 입력한다. (도메인 앞에 *를 붙여준다)
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/05.png?raw=true)

- 인증서 확인
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/06.png?raw=true)

- Route 53에서 레코드 생성
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/07.png?raw=true)

- 발급완료
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/08.png?raw=true)

## 4. 대상 그룹
- EC2 대시보드 왼쪽 하단 로드 밸런싱 > 대상 그룹
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/09.png?raw=true)

- 대상 유형 선택 - Instances 선택
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/10.png?raw=true)

- 대상 그룹 이름 입력
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/11.png?raw=true)

- EC2 대상 그룹 등록
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/12.png?raw=true)

- 대상 그룹 생성 완료
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/13.png?raw=true)

## 5. 로드 밸런서
- EC2 대시보드 왼쪽 하단 로드 밸런싱 > 로드밸런서
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/14.png?raw=true)

- Application Load Balancer 선택
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/15.png?raw=true)

.
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/16.png?raw=true)

### 네트워크 매핑
- 인스턴스와 같은 가용 영역 선택한다.
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/17.png?raw=true)

### 보안 그룹 & 리스너 및 라우팅
- 인스턴스와 같은 보안그룹 선택한다.
- 대상 그룹 연결
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/18.png?raw=true)

### 리스너 및 규칙 설정
- HTTP -> HTTPS 리다이렉트
- HTTPS -> Ec2 대상그룹 
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/19.png?raw=true)

## 6. Route 53 로드밸런서 설정
- 레코드 편집 로드밸런서 라우팅 대상 등록
![](https://github.com/dididiri1/TIL/blob/main/AWS/Https/images/20.png?raw=true)



















