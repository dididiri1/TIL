

## ✅ DBeaver

DBeaver는 오라클의 SQL Developer와 같이 DB 관리 툴이다. 데이터베이스를 시각적으로 관리할 수 있어 편리하며,
JDBC 기반이기에 거의 웬만한 주요 DB는 다 지원한다.


https://dbeaver.io/download/

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_01.png?raw=true)


## ✅ DB 성능을 개선하는 방법
### DB에 부하가 걸렸을 대 성능 개선하는 방법은 다양하다.
- sql 튜닝
- 캐싱 서버 활용 (Redis 등)
- 레플리케이션 (Master/Slave 구조)
- 샤딩
- 스케일업


![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_02.png?raw=true)


## ✅ MySQL SQL 실행 과정 및 SQL 튜닝 핵심

### 1. 클라이언트가 DB에 SQL 요청을 보냄

-> MySQL 엔진에서 옵티마이저가 SQL문을 분석한 뒤에 빠르고 효율적으로 데이터를 가져올 수 있는 계획을 세운다.  
어떤 순서로 테이블에 접근할 지, 인덱스를 사용할 지 어떤 인덱스를 사용할 지 등을 결정한다.

> (옵티마이저가 세운 계획은 완벽하지 않다. SQL 튜닝이 필요하다.)

---

### 2. 옵티마이저가 세운 계획 바탕으로 스토리지 엔진에서 데이터를 가져온다.

-> DB 성능에 문제가 생기는 대부분의 원인은 스토리지 엔진으로부터 데이터를 가져올 때 발생  
-> 데이터를 찾기가 어려워서 오래 걸리거나 가져올 데이터가 너무 많아 오래 걸린다.  
-> SQL 튜닝의 핵심은 스토리지 엔진으로부터 되도록 데이터를 찾기 쉽게 바꾸고, 적은 데이터를 가져오도록 바꾸는 것을 말함.

---

### 3. 옵티마이저가 세운 계획을 바탕으로 스토리지 엔진에서 데이터를 가져옴

-> DB 성능에 문제가 생기는 대부분의 원인은 스토리지 엔진으로부터 데이터 가져올 때 발생  
-> 데이터를 찾기가 어려워서 오래 걸리거나, 가져올 데이터가 너무 많아서 오래 걸림  
-> SQL 튜닝의 핵심은 스토리지 엔진으로부터 데이터를 찾기 쉽게 바꾸고, 적은 데이터를 가져오도록 바꾸는 것을 말한다.

---

### 4. MySQL 엔진에서 정렬, 필터링 등의 마지막 처리를 한 뒤에 클라이언트에게 SQL 결과를 응답한다.

## ✅ SQL 튜닝의 핵심은 인덱스를 적절하게 활용하여 DB 성능이 개선되게끔 하는것이다.

- 스토리지 엔진에서 데이터를 찾기 쉽게 바꾸기
- 스토리지 엔진으로부터 가져오는 데이터의 양 줄이기

그림 이 2가지를 어떻게 해결할 수 있을까?

여러가지 방법이 많지만 가장 많이 활용되는 방법이 인덱스 활용이다. 인덱스가 어떤 개념이길래 위 2가지 문제를 해결 할 수 있을까?

> 단순히 **인덱스만** 적용한다고 해서 무조건 해결 되는게 아니다. 인덱스를 **적절하게** 활용해야만 DB 성능이 개선 된다.  
> 이 방법에 대해 깊이 있게 다루기 전에 '인덱스'라는 개념에 대해 제대로 한 번 정리하고 넘어가자.

## ✅ 인덱스(index)란?
> 인덱스(index)는 데이터베이스 테이블에 대한 검색 성능의 속도를 높여주는 자료 구조를 뜻한다.

### 위의 정의보다는 아래의 읨미로 인덱스를 기억하자. 그애야 훨씬 직관적으로 이해하기가 쉽다.
> 인덱스(index) : 데이터를 빨리 찾기 위해 특정 컬럼을 기준으로 미리 정렬해놓는 표

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_03.png?raw=true)

나이가 23살인 사용자를 뽑아내려면 위의 표에서 23살로 시작하는 지점과 24살로 시작되는 지점만 찾은 뒤 
그 사이에 있는 모든 값을 가져오면 된다. 정렬을 해놓으니 모든 데이터를 일일이 다 확인할 필요가 없어서 
훨씬 효율적으로 데이터를 조히할 수 있게 된다,

위의 예시에서 **나이 순으로 정렬된 표**가 **인덱스**이다. 정확히 말하자면 나이 컬럼을 기준으로 인덱스
를 생성한 것이다. 이러한 특징 때문에 데이터를 찾는 속도를 빠르게 만들기 위해서 인덱스를 많이 활용한다.

참고로 실제 DB에서는 인덱스를 생성한다고 해서 졍렬된 표를 직접 눈으로 확인할 수는 없다. 시스템 내부족으로
생성될 뿐이다.

```
CREAtE TABLE users  (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100),
  age INT
)
```

```
SET SESSION cte_max_recursion_depth = 1000000;

-- 더미 데이터 삽입 쿼리
INSERT INTO users (name, age)
WITH RECURSIVE cte (n) AS (
    SELECT 1
    UNION ALL
    SELECT n + 1 FROM cte WHERE n < 1000000
)
SELECT 
    CONCAT('User', LPAD(n, 7, '0')) AS name,
    FLOOR(1 + RAND() * 1000) AS age 
FROM cte;
```

### 인덱스의 장점
- 검색 성능 향상: **데이터를 빠르게 검색할 수 있다.** 특히, 큰 테이블에서 특정 조건을 만족하는 데이터를 찾을 때 매우 유용하다.
- 정렬 및 그룹화 속도 향상: ORDER BY, GROUP BY와 같은 작업을 수행할 때도 인덱스를 활용하면 빠르게 처리할 수 있다.

### 인덱스의 단점
- 추가적인 저장 공간 필요: 인덱스 자체가 별도의 데이터 구조이기 때문에, 이를 저장할 추가 공간이 필요하다.
- 데이터 수정 시 성능 저하: **데이터를 INSERT, UPDATE, DELETE 할 때** 인덱스도 함께 수정해야 하므로 성능이 떨어질 수 있다. 
  따라서 너무 많은 인덱스를 사용하면 오히려 성능이 저하될 수 있다.
- 인덱스는 주로 데이터 검색 시 유리하지만, 자주 변경되는 데이터를 다룰 때는 오히려 성능에 악영향을 미칠 수 있다.

### 언제 인덱스를 사용해야 하는가?
- 많은 조회가 이루어지는 열: **데이터 조회가 자주 발생하는 열에 인덱스를 사용하는 것이 좋다.** 예를 들어, 
  SELECT 쿼리에서 자주 조건으로 사용하는 열(WHERE 절에 자주 등장하는 열).
- 유일한 값이 많은 열: **인덱스는 중복 값이 많을수록 성능이 떨어질 수 있다.** 따라서 유일한 값이 많을수록 인덱스 효과가 크다. 
  예를 들어, 고유한 ID를 저장하는 primary key 열.
- 정렬과 그룹화에 자주 사용되는 열: ORDER BY나 GROUP BY 절에서 자주 사용되는 열에도 인덱스를 사용할 수 있다.

### 인덱스 설정 방법
#### 인덱스 생성
```
CREATE INDEX idx_age ON users(age);
```

#### 인덱스 확인
```
SHOW INDEX FROM users;
```
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_04.png?raw=true)

```
SELECT * from users
WHERE age = 23
```

## 12. 기본으로 설정되는 인덱스 (PK)

### ✅ 기본키 (Primary Key, PK)
테이블에서 특정 데이터를 식별하기 위한 키를 보고 기본키(Primary Key, PK)라고 부른다. 대부분의 경우에
테이블을 생성할 때 PK를 설정한다. PK의 특징 중 하나는 **PK를 기준으로 정렬을 해서 데이터를 보관한다.**

**id** 컬럼을 기준으로 정렬되어 있는 체로 데이터가 조회된다. 왜냐하면 PK가 **인덱스**의 일종이기 때문이다.
이렇게 원본 데이터 자체가 정렬되는 인덱스를 보고 **클러스터링 인덱스**라고 부른다. PK(Primary Key) = 클러스터링
인덱스라고 생각해도 된다. 클러스터링 인덱스는 PK(Primary Key) 밖에 없기 떄문이다.

### 클러스터 인덱스 (Clustered Index)
영어사전은 단어들이 알파벳 순서로 정렬되어 있다. 이 정렬된 상태 덕분에 우리는 특정 단어를 빠르게 찾을 수 있다. 
예를 들어, “apple”을 찾으려면 사전의 앞부분에서부터 찾고, “zebra”는 사전의 뒷부분을 바로 열어 찾으면 된다.

이 사전의 알파벳 순서로 정렬된 방식이 클러스터 인덱스와 비슷하다.

- 클러스터 인덱스는 테이블의 데이터 자체가 정렬되는 방식이다.
- 즉, 테이블의 행(row)들이 인덱스 기준으로 실제 데이터 파일에 정렬되어 저장된다.
- 하나의 테이블에 하나의 클러스터 인덱스만 존재할 수 있다, 왜냐하면 테이블 자체가 해당 인덱스를 기준으로 정렬되어 저장되기 때문이다.
- 클러스터 인덱스가 설정된 열(column)에 따라 데이터가 물리적으로 정렬된다. 이로 인해 해당 열로 데이터를 검색할 때 매우 빠르게 찾을 수 있다.
- Primary Key는 일반적으로 클러스터 인덱스로 사용된다. 예를 들어, 학생 테이블에서 학생 ID가 Primary Key라면, 테이블은 학생 ID 순서대로 정렬되어 저장된다.

## 제약 조건을 추가하면 자동으로 생성되는 인덱스 (UNIQUE)

### 보조 인덱스 (Non-clustered Index)
보조 인덱스는 책의 목차와 비슷한 개념이다.
목차는 본문의 내용과는 별도로 위치 정보(페이지 번호)를 제공하는데, 목차에서 내용을 찾으면 바로 해당 페이지로 이동할 수 있다. 
목차 자체는 본문과 별도로 관리된다.


### ✅ UNIQUE 제약 조건을 추가하면 자동으로 인덱스가 생성된다.
- MySQL은 UNIQUE 제약 조건을 추가하면 자동으로 인덱스가 생성된다.

#### 1. 테이블 생성
```
CREAtE TABLE users  (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) UNIQUE
);
```

### 2. 인덱스 확인
```
SHOW INDEX FROM users
```
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_05.png?raw=true)
- 이런 특징 떄문에 UNIQUE 특징으로 인해 생성되는 인덱스를 보고 **고유 인덱스(Unique index)**라고 부른다.
- Unique 제약 조건이 걸린 컬럼은 일반적으로 보조 인덱스로 사용된다.

> 💡[이것만은 기억해두자!]
> UNIQUE 옵션을 사용하면 인덱스가 같이 생성되기 때문에 조회 성능이 향상된다.

### 인덱스를 많이 사용하면 무조건 좋은가?
인덱스의 자료구조는 B-Tree이다.
B-Tree는 인덱스를 구현하는 데 매우 효율적인 자료구조지만, 인덱스를 너무 많이 사용하면 다음과 같은 문제점이 발생할 수 있다.

- 데이터 수정 시 성능 저하: 인덱스가 많을수록 데이터 수정 시 더 많은 B-Tree가 업데이트되어 성능이 떨어짐.
- 추가적인 저장 공간 필요: 인덱스가 많을수록 더 많은 B-Tree 구조를 저장해야 하므로 디스크 공간이 많이 사용됨.
- 인덱스 선택 문제: 너무 많은 인덱스가 있으면 최적의 인덱스를 선택하는 데 시간이 소요될 수 있음.
- Full Table Scan 발생 가능성: 너무 많은 인덱스가 있으면 오히려 인덱스를 사용하지 않고 테이블 전체를 스캔하는 경우도 발생할 수 있음.

```
-- 테이블 A: 인덱스가 없는 테이블
CREATE TABLE test_table_no_index (
  id INT AUTO_INCREMENT PRIMARY KEY,
  column1 INT,
  column2 INT,
  column3 INT,
  column4 INT,
  column5 INT,
  column6 INT,
  column7 INT,
  column8 INT,
  column9 INT,
  column10 INT
);
```
```
-- 테이블 B: 인덱스가 많은 테이블
CREATE TABLE test_table_many_index (
  id INT AUTO_INCREMENT PRIMARY KEY,
  column1 INT,
  column2 INT,
  column3 INT,
  column4 INT,
  column5 INT,
  column6 INT,
  column7 INT,
  column8 INT,
  column9 INT,
  column10 INT
);
```
```
-- 각 컬럼에 인덱스를 추가
CREATE INDEX idx_column1 ON test_table_many_index (column1);
CREATE INDEX idx_column2 ON test_table_many_index (column2);
CREATE INDEX idx_column3 ON test_table_many_index (column3);
CREATE INDEX idx_column4 ON test_table_many_index (column4);
CREATE INDEX idx_column5 ON test_table_many_index (column5);
CREATE INDEX idx_column6 ON test_table_many_index (column6);
CREATE INDEX idx_column7 ON test_table_many_index (column7);
CREATE INDEX idx_column8 ON test_table_many_index (column8);
CREATE INDEX idx_column9 ON test_table_many_index (column9);
CREATE INDEX idx_column10 ON test_table_many_index (column10);
```

```

-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 갯수와 맞춰서 작성하면 된다.)
SET SESSION cte_max_recursion_depth = 1000000;

-- 인덱스가 없는 테이블에 데이터 10만개 삽입
INSERT INTO test_table_no_index (column1,column2,column3,column4,column5,column6,column7,column8,column9,column10)
WITH RECURSIVE cte (n) AS (
    SELECT 1
    UNION ALL
    SELECT n + 1 FROM cte WHERE n < 1000000
)
SELECT 
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000)
FROM cte;
```

```
-- 인덱스가 많은 테이블에 데이터 10만개 삽입
INSERT INTO test_table_many_index (column1,column2,column3,column4,column5,column6,column7,column8,column9,column10)
WITH RECURSIVE cte (n) AS (
    SELECT 1
    UNION ALL
    SELECT n + 1 FROM cte WHERE n < 1000000
)
SELECT 
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000),
    FLOOR(RAND() * 1000)
FROM cte;
```
### 인덱스가 없이 생성한 테이블에 10만개 데이터 삽입시 걸린 속도
- 약 3.1114 sec 정도의 시간이 소요된다.
### 인덱스가 1개 생성한 테이블에 10만개 데이터 삽입시 걸린 속도
- 약 4.354 sec 정도의 시간이 소요된다.
### 인덱스가 2개 생성한 테이블에 10만개 데이터 삽입시 걸린 속도
- 약 5.263 sec 정도의 시간이 소요된다.
### 인덱스가 10개 생성한 테이블에 10만개 데이터 삽입시 걸린 속도
- 약 30.0001 sec 정도의 시간이 소요되고, 데이터가 많아지면 많아질수록 점점 삽입 속도가 느려지는 걸 확인할 수 있다.
> 💡[이것만은 기억해두자!]
> 최소한의 인덱스만 사용하려고 하자.
> 인덱스를 추가하면 조회 속도는 빨라지나, 쓰기 (삽입,수정,삭제) 속도는 느려짐을 기억하자!.

