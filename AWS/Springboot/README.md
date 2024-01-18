# Spring boot AWS S3 연동

## AWS S3 설정
### 사용자 생성 [1/1]
- AWS console > IAM > 엑세스 관리 > 사용자 > 사용자 생성 클릭
- 
![](https://github.com/dididiri1/TIL/blob/main/AWS/Springboot/images/01.png?raw=true)

- 사용자 이름을 입력하고 다음을 클릭한다.
![](https://github.com/dididiri1/TIL/blob/main/AWS/Springboot/images/02.png?raw=true)

- 직접 정책 연결을 선택하고, AmozonS3FullAccess 를 선택하고 다음을 클릭한다.
![](https://github.com/dididiri1/TIL/blob/main/AWS/Springboot/images/03.png?raw=true)

- 검토 및 사용자 생성
![](https://github.com/dididiri1/TIL/blob/main/AWS/Springboot/images/04.png?raw=true)

## 엑세스 키 발급 [1/2]
- AWS Console > IAM > 엑세스 관리자 > 사용자 > 생성한 사용자 이름 클릭 > 엑세스키 만들기
![](https://github.com/dididiri1/TIL/blob/main/AWS/Springboot/images/06.png?raw=true)

## Spring boot 설정
### build.gradle에 의존성 추가 [1/1]
``` gradle
// AWS s3
implementation 'com.amazonaws:aws-java-sdk-s3:1.12.338'
```

### application.yml 설정 [1/2]
``` yml
cloud:
  aws:
    credentials:
      instance-profile: false
      accessKey: {accessKey}
      secretKey: {secretKey}
    region:
      auto: false
      static: ap-northeast-2
    stack:
      auto: false
    s3:
      bucket: {buckey}
      endPoint: https://s3.ap-northeast-2.amazonaws.com
      folder: {folder}
```

### AwsS3Config 설정 [1/1]
``` java
import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.services.s3.AmazonS3Client;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AwsS3Config {

    @Value("${cloud.aws.credentials.access-key}")
    private String accessKey;

    @Value("${cloud.aws.credentials.secret-key}")
    private String secretKey;

    @Value("${cloud.aws.region.static}")
    private String region;


    @Bean
    public AmazonS3Client amazonS3Client() {
        BasicAWSCredentials basicAWSCredentials = new BasicAWSCredentials(accessKey, secretKey);
        return (AmazonS3Client) AmazonS3ClientBuilder.standard()
                .withRegion(region)
                .withCredentials(new AWSStaticCredentialsProvider(basicAWSCredentials))
                .build();
    }
}
``` 

## AmazonS3Exception 400 해결
- API 만들어서 업로드 했는데 에러남
- S3에 버킷의 ACL권한을 안줘서 그렇다 S3 로가서 ACL 권한 설정 줘야함
``` log
com.amazonaws.services.s3.model.AmazonS3Exception: The bucket does not allow ACLs   
(Service: Amazon S3; Status Code: 400; Error Code: AccessControlListNotSupported;   
Request ID: SRZ5FSW2F6R161RP; S3 Extended Request ID: 0ebc/Ruzbt8on+EgWj8TbVIDWiGdXbZd6m2F0zDMcoBbsJRHltv5nml9xXHISwlclKor9zWuxMDU4tvOoYYJzA==; Proxy: null)
``` 

![](https://github.com/dididiri1/TIL/blob/main/AWS/Springboot/images/08.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/AWS/Springboot/images/09.png?raw=true)





