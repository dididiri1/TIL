![](https://github.com/dididiri1/TIL/blob/main/Mysql/images/01_00.png?raw=true)


# [MYSQL] AWS EC2 Linux) - MySQL 설치 및 접속

## 1. MySQL 저장소 설치
- 최신 버전을 설치하기 위해 MySQL의 공식 저장소를 추가해야 한다.
- yum repository에 등록
```
sudo dnf install https://dev.mysql.com/get/mysql80-community-release-el8-1.noarch.rpm
```

## 2. MySQL 설치
- MySQL 저장소를 추가한 후, MySQL 서버를 설치
```
sudo dnf install mysql-server
```

## 3. MySQL 서비스 시작 & 중지

```
sudo systemctl start mysqld

sudo systemctl stop mysqld

sudo systemctl status mysqld
```

## 4. MySQL 설치 버전 확인
```
mysql --version
```

## 5. MySQL 초기 설정
- MySQL을 처음 실행하면 임시 비밀번호가 생성된다. 이를 확인하려면 명령어 실행 
- 만약 임시 비멀번호가 생성이 안되면, root로 그냥 접속하기
```
sudo grep 'temporary password' /var/log/mysqld.log
```

## 6. MySQL 접속 확인
### root 계정 접속
```
mysql -u root -p
```

### root 계정 비밀번호 설정
```
ALTER USER 'root'@'localhost' IDENTIFIED BY '새로운비밀번호';
```

### Mysql 3306 포트 오픈 
![](https://github.com/dididiri1/TIL/blob/main/Mysql/images/01_01.png?raw=true)


## 7. Mysql Workbench 접속
- 외부 접속 계정 생성
```
create user '{사용자명}'@'%' identified by '{패스워드}';

FLUSH PRIVILEGES;
```

```
mysql> SELECT user, host FROM mysql.user;
+------------------+-----------+
| user             | host      |
+------------------+-----------+
| root             | %         |
| mysql.infoschema | localhost |
| mysql.session    | localhost |
| mysql.sys        | localhost |
| root             | localhost |
+------------------+-----------+
5 rows in set (0.00 sec)

```

### 외부 접속 성공!
![](https://github.com/dididiri1/TIL/blob/main/Mysql/images/01_02.png?raw=true)
