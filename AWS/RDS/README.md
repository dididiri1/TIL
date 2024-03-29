# AWS - RDS 사용하기

![](https://github.com/dididiri1/TIL/blob/main/AWS/RDS/images/aws-rds-logo.pngraw=true)


## RDS(Relational Database Service)란
> 간단히 말하면 관계형 데이터베이스를 제공하는 AWS의 서비스이다.     
> 유저가 사용하기 쉽도록 인프라 등을 자동화 시켜주고 유저들은 앤드포인트로   
> 접속할 수 있는 데이터베이스를 제공받는다.

## 1. RDS 인스턴스 생성
### 1. RDS 대시보드에서 데이터베이스 생성을 선택
![](https://github.com/dididiri1/TIL/blob/main/AWS/RDS/images/01.png?raw=true)


### 2. 엔진 옵션 - MySQL 선택
![](https://github.com/dididiri1/TIL/blob/main/AWS/RDS/images/02.png?raw=true)

### 3. 템플릿 프리티어 선택 프로덕션 디폴트값으로 체크 되어있어 과금주의!
![](https://github.com/dididiri1/TIL/blob/main/AWS/RDS/images/03.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/AWS/RDS/images/04.png?raw=true)

### 4. 퍼블릭 액세스를 "예"로 선택 보안그룹 생성
![](https://github.com/dididiri1/TIL/blob/main/AWS/RDS/images/05.png?raw=true)


### 5. 왼쪽 메뉴에서 파라미터 그룹을 선택
![](https://github.com/dididiri1/TIL/blob/main/AWS/RDS/images/06.png?raw=true)

### 6. 파라미터 그룹을 생성한다.
![](https://github.com/dididiri1/TIL/blob/main/AWS/RDS/images/07.png?raw=true)

### 7. 생성된 파라미터 그룹을 확인하고 해당 그룹을 클릭
![](https://github.com/dididiri1/TIL/blob/main/AWS/RDS/images/08.png?raw=true)


### 8. 보안그룹 수정
![](https://github.com/dididiri1/TIL/blob/main/AWS/RDS/images/09.png?raw=true)

### 9. 인바운드 규칙 IP(any) 허용
![](https://github.com/dididiri1/TIL/blob/main/AWS/RDS/images/10.png?raw=true)

### 10. 연결 및 테스트
![](https://github.com/dididiri1/TIL/blob/main/AWS/RDS/images/10.png?raw=true)