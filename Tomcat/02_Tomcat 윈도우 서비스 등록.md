# Tomcat 윈도우 서비스 등록 방법
![](https://github.com/dididiri1/TIL/blob/main/Tomcat/images/01_01.jpeg?raw=true)

## 1. service.bat 설치
```
C:\tomcat8\bin>service.bat install
Installing the service 'Tomcat8' ...
Using CATALINA_HOME:    "C:\tomcat8"
Using CATALINA_BASE:    "C:\tomcat8"
Using JAVA_HOME:        "C:\Program Files\java\jdk-16.0.2"
Using JRE_HOME:         "C:\Program Files\java\jdk-16.0.2"
Using JVM:              "C:\Program Files\java\jdk-16.0.2\bin\server\jvm.dll"
The service 'Tomcat7' has been installed.
```

## 2.  서비스 등록 확인
- 설치가 완료되면, 윈도우 실행창에서 services.msc 를 실행하여 윈도우 서비스로 이동한다.
![](https://github.com/dididiri1/TIL/blob/main/React/images/02_01.png?raw=true)


## 3. 서비스 관리
- Tomcat을 서비스로 등록한 후, 서비스를 관리하는 방법은 다음과 같다
#### 서비스 시작
```
C:\tomcat8\bin>net start Tomcat8
Apache Tomcat 8 Tomcat8 서비스를 시작합니다..
Apache Tomcat 8 Tomcat8 서비스가 잘 시작되었습니다.
```

#### 서비스 중지
```
C:\tomcat8\bin>net stop Tomcat8
Apache Tomcat 8 Tomcat8 서비스를 멈춥니다..
Apache Tomcat 8 Tomcat8 서비스를 잘 멈추었습니다.
```

## 4. 서비스 제거
```
C:\tomcat8\bin>service.bat remove
```
