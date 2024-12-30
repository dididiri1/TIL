## DML(Data Manipulation Language) 
- 데이터의 삽입(INSERT), 수정(UPDATE), 삭제(DELETE), 병합(MERGE) 
- **저장(commit) 혹은 취소(rollback) 반드시**

### INSERT 
- 테이블에 행을 삽입할 때 사용 
- 한 번에 한 행만 입력가능(SQL Linux. 여러 행 동시 삽입 가능) 
- 하나의 컬럼에는 한 값만 삽입 가능 
- 컬럼별 데이터타입과 사이즈에 맞게 삽입  
- INTO절에 컬럼명을 명시하여 일부 컬럼만 입력 가능. 작성하지 않은 컬럼은 NULL 이 입력됨
- 전체 컬럼에 대한 데이터 입력시 테이블명 뒤의 컬럼명 생략 가능 

#### 문법
```
INSERT INTO EMP VALUES(1, 'KIM', 3000)
```

### UPDATE 
- 데이터 수정할때 사용 
- 컬럼 단위 수행 
- 다중 컬럼 수정 가능

#### 문법
```
UPDATE EMP
   SET NAME = "KIM", PRICE = 3000
WHERE EMPNO = 1;
```

### DELETE 
- 데이터를 삭제할 때 사용 
- 행 단위 실행
#### 문법
```
DELETE FROM EMP
WHERE EMPNO = 1;
```

## TCL

### TCL(Transaction Control Language) 
- 트랜잭션 제어어로 COMMIT, ROLLBACK이 포함됨 
- DML에 의해 조작된 결과를 작업단위(트랜잭션) 별로 제어하는 명령어 
- DML 수행 후 트랜잭션을 정상 종료하지 않는 경우 LOCK 발생할 수 있음 

### 트랜잭션 
- 트랜잭션은 데이터베이스의 논리적 연산 단위(하나의 연속적인 업무 단위) 
- 하나의 트랜잭션에는 하나 이상의 SQL 문장이 포함 
- 분할 할 수 없는 최소의 단위 
- ALL OR NOTHING 개념(모두 COMMIT하거나 ROLLBACK 처리 해야 함

### 트랜잭션의 특성
- 원자성(atomicity) : 트랜잭션 정의된 연산들 모두 성공적으로 실행되던지 아니면 전혀 실행되지 않은 상태로 남아 있어야 함 
- 일관성(consistency) : 트랜잭션 실행 전 데이터베이스 내용이 잘못되어 있지 않다면 트랜잭션 실행 이후에도 데이터베이스 내용의 잘못이 있으면 안됨 
- 고립성(isolation) : 트랜잭션 실행도중 다른 트랜잭션의 영향을 받아 잘못된 결과를 만들어서는 안됨 
- 지속성(durability) : 트랜잭션이 성공적으로 수행되면 갱신한 데이터베이스 내용이 영구적으로 저장

### COMMIT 
- 입력, 수정, 삭제한 데이터에 이상이 없을 경우 우 데이터를 저장하는 명령어 
- 한 번 COMMIT을 수행하면 COMMIT 이전에 수행된 DML은 모두 저장되며 되돌릴 수 없음 
- ORACLE은 DDL시 AUTO COMMIT(23c버전부터 비활성화 가능) 이지만 SQL Server는 AUTO COMMIT비활성화 설정 가능 

### ROLLBACK 
- 테이블 내 입력한 데이터나 수정한 데이터, 삭제한 데이터에 대해 변경을 취소하는 명령어 
- 데이터베이스에 저장되지 않고 최종 COMMIT 지점/변경 전/특정 SAVEPOINT 지점으로 원복됨 
- 최종 COMMIT 시점 이전까지 ROLLBACK 가능 
- SAVEPOINT를 설정하여 최종 COMMIT 시점이 아닌, 그 이후의 원하는 시점으로의 원복 

### SAVEPOINT 
- 트랜잭션 내에서 롤백을 부분적으로 수행하기 위해 사용되는 지점을 지정하는 데 사용 
- 사용자가 원하는 위치에 원하는 이름으로 설정 가능
- ROLLBACK TO savepoint_name 으로 원하는 지점으로 원복 가능(단, COMMIT 이전으로는 원복 불가)

#### 문법
```
SAVEPOINT P1;
```

## DDL

### DDL(Data Definition Language) 
- 데이터 정의어 
- 데이터 구조 정의(객체 생성, 삭제, 변경) 언어 
- CERATE(객체 생성), ALTER(객체 변경), DROP(객체 삭제), TRUNCATE(데이터 삭제) 
- AUTO COMMIT(명령어 수행하면 즉시 저장, 원복 불가)

### CREATE  
- 테이블이나 인덱스와 같은 객체를 생성하는 명령어 
- 테이블 생성 시 테이블명, 컬럼명, 컬럼순서, 컬럼크기, 컬럼의 데이터타입 정의 필수 
- 테이블 생성 시 각 컬럼의 제약조건 및 기본값은 생략 가능 
- 테이블 생성 시 소유자 명시 가능(생략 시 명령어 수행 계정 소유) 

#### 문법
```
CREATE TABLE EMP
EMPNO NUMBER,
NAME VARCHAR2(10)
PRICE NUMBER);
```

### ALTER 
- 테이블 구조 변경(컬럼명, 컬럼 데이터타입, 컬럼사이즈, default값, 컬럼삭제, 컬럼추가, 제약조건) 
- 컬럼순서 변경 불가(재생성으로 해결) 

#### 1. 컬럼 추가 
- 새로 추가된 컬럼위치는 맨 마지막(절대 중간 위치에 추가 불가) 
- 컬럼 추가 시 데이터타입 필수, default값, 제약조건을 명시할 수 있음 
- 여러 컬럼 동시 추가 가능(반드시 괄호 사용) 

#### 문법
```
ALTER TABLE 테이블명 ADD 칼럼명 데이터타입 [DEFAULT] [제약조건];
```

#### 예제) 컬럼 추가
```
ALTER TABLE EMP ADD BIRTHDAY DATE;
```

#### 2. 컬럼(속성) 변경 
- 컬럼 사이즈, 데이터타입, default값 변경 가능 
- 여러 컬럼 동시 변경 가능

#### 문법 (괄호 생략 가능 )
```
ALTER TABLE 테이블명 MODIFY (칼럼명 DEFAULT 값);
```

#### 예제) 컬럼 변경
```
ALTER TABLE EMP MODIFY (COL_A NUMBER(10), COL_B VARCHAR(6));
```

#### 3. 컬럼 이름 변경 - 항상 가능 
- **동시 여러 컬럼 이름 변경 불가(괄호 전달 불가)**
- ALTER ... RENAME 명령어로 처리
#### 문법 
```
ALTER TABLE 테이블명 RENAME COLUMN 기존칼럼명 TO 새 컬럼명;
```

#### 예제) 컬럼 이름 변경
```
ALTER TABLE EMP RENAME COLUMN ENAME TO NAME;
```

4. 컬럼 삭제 
- **데이터 존재 여부와 상관없이 언제나 가능** 
- RECYCLEBIN에 남지 X(FLASHBACK으로 복구 불가) 
- **동시 삭제 불가**

#### 예제) COL_A 컬럼의 삭제 
```
ALTER TABLE TEST DROP COLUMN COL_A;           -- 가능
ALTER TABLE TEST DROP COLUMN COL_A, COL_B;    -- 에러
```

### DROP 
- 객체(테이블, 인덱스 등) 삭제
- DROP 후에는 조회 불가

#### 문법
```
DROP TABLE 테이블명 [PURGE];
```
> PURGE로 테이블 삭제시 RECUCLEBIN 에서 조회 불가

### TRUNCATE 
- 구조 남기고 데이터만 즉시 삭제, 즉시 반영(AUTO COMMIT) 
- RECYCLEBIN에 남지 않음
#### 문법
```
TRUNCATE TABLE 테이블명;
```

### DELETE / DROP / TRUNCATE 차이 
- DELETE : 데이터 일부 또는 전체 삭제, 롤백 가능 
- TRUNCATE : 데이터 전체 삭제만 가능(일부 삭제 불가), 즉시 반영(롤백 불가) 
- DROP : 데이터와 구조를 동시 삭제, 즉시 반영(롤백 불가)

## DCL
### DCL(Data Contol Language) 
- 데이터 제어어로 객체에 대한 권한을 부여(GRANT)하거나 회수(REVOKE)하는 기능 
- 테이블 소유자는 타계정에 테이블 조회 및 수정 권한 부여 및 회수 가능

### 권한 
- 일반적으로 본인(접속한 계정) 소유가 아닌 테이블은 원칙적으로 조회 불가(권한 통제) 
- 업무적으로 필요시 테이블 소유자가 아닌 계정에 테이블 조회, 수정 권한 부여 가능

### 권한 종류
#### 1) 오브젝트권한 
- 테이블에 대한 권한 제어(SELECT, INSERT, UPDATE, DELETE, MERGE 권한) 
- 테이블 소유자는 타계정에 소유 테이블에 대한 조회 및 수정 권한 부여 및 회수 가능
#### 2) 시스템권한  
- 시스템 작업(테이블생성 등)등을 제어(테이블 생성 권한, 인덱스 삭제 권한)
- 관리자 권한만 권한 부여 및 회수 가

### GRANT 
- 권한 부여 시 반드시 테이블 소유자나 관리자계정(SYS, SYSTEM)으로 접속하여 권한을 부여하여야 함 
- 동시에 여러 유저에 대한 권한 부여 가능 
- 동시 여러 권한 부여 가능 -  동시 여러 객체 권한 부여 불가
#### 문법
```
GRANT 권한 ON 테이블명 TO 유저;
```

#### 예제) 오브젝트 권한 부여(PROFESSOR 소유자 실행)
```
GRANT SELECT ON PROFESSOR TO HR;                   -- 가능
GRANT SELECT ON PROFESSOR TO HR, BI;               -- 가능
GRANT SELECT, UPDATE, INSERT ON PROFESSOR TO HR;   -- 가능
GRANT SELECT ON PROFESSOR, DEPT TO HR;             -- 에러
```

#### 예제) 시스템 권한 부여(관리자 권한으로 실행) 
```
GRANT CREATE ON TABLE TO HR;                   -- 가능
GRANT CREATE ON TABLE TO HR, BI;               -- 가능
GRANT CREATE ON TABLE, DROP TABLE TO HR;       -- 에러
```

### REVOKE 
- **동시 여러 권한 회수 가능**
- 이미 회수된 권한 재회수 불가 
- **동시 여러 유저로부터의 권한 회수 가능**
#### 문법
```
REVOKE 권한 ON 테이블명 FROM 유저;
```

#### 예제) 오브젝트 권한 부여(PROFESSOR 소유자 실행)
```
REVOKE SELECT ON PROFESSOR FROM HR, BI;
REVOKE SELECT, UPDATE, INSERT ON PROFESSOR FROM HR;           
```

### 롤(ROLE) 
- 권한의 묶음(생성 가능한 객체)
- SYSTEM 계정에서 ROLE 생성 가능 
#### 문법
```
CREATE ROLE 롤이름;
```
#### 예제
```
CREATE ROLE ROLE_SEL;                      -- 생성

GRANT SELECT ON EMP TO ROLE_SEL;           -- 롤에 권한 담기

GRANT ROLE_SEL TO HR                       -- 롤 부여

REVOKE SELECT ON DEPARTMENT FORM ROLE_SEL; -- 롤에서 권한 빼기
```

### 권한부여 옵션(중간관리자의 권한)
#### 1. WITH GRANT OPTION
- WITH GRANT OPTION으로 받은 **오브젝트 권한을 다른 사용자에게 부여할 수 있음** 
- 중간관리자(WITH GRANT OPTION으로 권한을 부여받은 자)가 부여한 권한은 **중간관리자만 회수 가능** 
- **중간관리자에게 부여된 권한 회수 시 제 3자에게 부여된 권한도 함께 회수됨**

#### 2. WITH ADMIN OPTION
- WITH ADMIN OPTION을 통해 부여 받은 **시스템 권한/롤 권한을 다른 사용자에게 부여할 수 있음** 
- 중간관리자를 거치지 않고 **직접 회수 가능** 
- **중간관리자 권한 회수시 제 3자에게 부여된 권한도 함께 회수X(남아있음)**


### Reference
- [SQLD - 홍쌤의 데이터 랩](https://www.youtube.com/@hongdatalab)
