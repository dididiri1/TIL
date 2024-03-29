# Jenkins와 GitHub 연동하기

## 1. GitHub에서 Personal Access Token 생성하기
- 만료 시간은 무제한으로 설정
- 해당 토큰으로 접근할 수 있는 스코프 지정
  - repo
  - admin:repo_hook

![](https://github.com/dididiri1/TIL/blob/main/Jenkins/images/03_01.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/Jenkins/images/03_02.png?raw=true)



## 2. GitHub Webhook 설정
- GitHub에서 해당 리포지토리로 이동하여 "Settings" > "Webhooks"로 이동
- "Add webhook"을 클릭
![](https://github.com/dididiri1/TIL/blob/main/Jenkins/images/03_03.png?raw=true)

- Payload URL"에 Jenkins 서버의 URL을 입력하고, "/github-webhook/"를 끝에 추가합니다.   
 예: 'http://your_jenkins_server.com/github-webhook/'
- Content type"을 "application/json"으로 설정
![](https://github.com/dididiri1/TIL/blob/main/Jenkins/images/03_04.png?raw=true)

## 3. Credentials 만들기
- (global) 링크를 눌러 Credentials 등록 화면으로 이동
- Credentials 등록 화면에서 Add Credentials 클릭

- Username: Github 사용자 이름
- Password: Github 엑세스 토큰
- ID: 젠킨스 Credential ID
![](https://github.com/dididiri1/TIL/blob/main/Jenkins/images/03_05.png?raw=true)

## 4. Jenkins에서 새로운 Item 생성
![](https://github.com/dididiri1/TIL/blob/main/Jenkins/images/03_09.png?raw=true)

- Git"을 선택하고, GitHub 프로젝트의 URL을 입력
![](https://github.com/dididiri1/TIL/blob/main/Jenkins/images/03_07.png?raw=true)

- Build Triggers 에서 GitHub hook trigger for GITScm polling을 선택
![](https://github.com/dididiri1/TIL/blob/main/Jenkins/images/03_08.png?raw=true)

## 5. Jenkins 워크스페이스 확인
* `cd /var/lib/jenkins/workspace`

## 6. Execute shell에서 deploy.sh 실행하기
- Build 섹션으로 이동하여 Add build step을 클릭하고, Execute shell을 선택
- deploy.sh 쉘 스크립트 실행 명령어 추가
![](https://github.com/dididiri1/TIL/blob/main/Jenkins/images/03_10.png?raw=true)

## 7. 배포 완료
![](https://github.com/dididiri1/TIL/blob/main/Jenkins/images/03_11.png?raw=true)

