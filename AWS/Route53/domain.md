# [AWS] EC2 와 도메인 연결하기 (feat. 가비아)


### 1. Route53 이동
- AWS Management Console에서 Route53 서비스로 이동
![](https://github.com/dididiri1/TIL/blob/main/AWS/Route53/images/01_01.png?raw=true)

### 2. 호스팅 영역 생성 설정
- 도메인 이름: 연결하려는 도메인 (예: example.com)
![](https://github.com/dididiri1/TIL/blob/main/AWS/Route53/images/01_02.png?raw=true)

### 3. 생성한 도메인의 레코드 생성

![](https://github.com/dididiri1/TIL/blob/main/AWS/Route53/images/01_03.png?raw=true)
- 생성한 호스팅 영역으로 이동하여 A 레코드를 추가 한다.
  - 레코드 이름: @ (루트 도메인) 또는 www (서브도메인)
  - 값: EC2의 Elastic IP
  - TTL: 기본값 유지 (300초)
![](https://github.com/dididiri1/TIL/blob/main/AWS/Route53/images/01_04.png?raw=true)
``` 

``` 
### 4. 가비아 네임서버 등록하기
![](https://github.com/dididiri1/TIL/blob/main/AWS/Route53/images/01_05.png?raw=true)

### 5. AWS EC2와 도메인 연결 완료!!

![](https://github.com/dididiri1/TIL/blob/main/AWS/Route53/images/01_06.png?raw=true)
