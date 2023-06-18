
### IAM 개요

* AWS 계정 및 권한 관리 서비스
* AWS 서비스와 리스소에 대한 엑세스 관리
* 사용자, 그룹, 역활, 정책으로 구성
* 리전에 속하는 서비스가 아닌 글로버 서비스
* 계정 보안 강화를 위해
  - 루트 계정(AWS 회원가입시 만들어지는 계정)은 최초 사용자 계정 생성 이후 가능하면 사용하지 말것
  - 사용자 계정(IAM 계정)으로 서비스를 사용하고 사용자는 필요한 최소함의 권한만 부여(최소권한의 원칙)
  - 루트계정과 개별 사용자 계정에 강력한 암호 정책과 멀티팩터 인증(MFA) 적용
  - 사용자의 암호에 대한 복잡성 요구 사항과 의무 교체 주기를 정의

### IAM - 엑세스 관리

[01_01.png]


### IAM - 정책

- AWS 리소스에 대한 엑세스 권한을 정의 한 것
- 사용자, 그룹, 역할에 정책을 연결하여 사용
- JSON 문서 형식으로 이루어짐
- 정책이 명시되지 않은 경우 기본적으로 모든 요청이 거부(Deny) 됨
  
  - IAM 정책 JSON 문서 구조
    - Effect: Statement에 대한 Access 또는 Deny
    - Action: 권한에 대한 작업 목록
    - Resource: 권한이 적용되는 리소스
    - Condition: 정책이 적용되는 세부 조건(옵션사항)
      - 특정 리전, 특정 IP에 정책을 적용할 수 있음
      
    ``` json
        {
            "Statement": [{
                "Effect": "effect",
                "Action": "action",
                "Resource": "arn",
                "Condition": {
                    "condition": {
                        "key": "value"
                    }
        
                }
            }]
        }
    ``` 

### IAM - 권한 경계

- IAM 사용자 또는 역할에 최대 권한을 제한 하는 기능
- 예를 들어 IAM 사용자에게 s3, CloudWatch 및 EC2만 관리할 수 있게 하래와 같은 정책을 적용

    ``` json
        {
            "Statement": [{
                "Effect": "effect",
                "Action": "action",
                "Resource": "arn",
                "Condition": {
                    "condition": {
                        "key": "value"
                    }
        
                }
            }]
        }
    ``` 