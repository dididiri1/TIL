

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

## 멀티 컬럼 인덱스(Multiple - Column Index)
### 멀티 컬럼 인덱스 개념
- 멀티 컬럼 인덱스는 여러 개의 컬럼(열)을 하나의 인덱스로 묶어서 관리하는 인덱스이다.
- 이를 통해 여러 컬럼에 걸친 데이터 조회를 최저화할 수 있디. 즉 하나의 인덱스가 여러 컬럼을 포하함으로써
  특정 쿼리가 여러 컬럼을 동시에 검색할 때 성능을 높이는 역할을 한다.

멀티 컬럼 인덱스 생성
```
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    dept VARCHAR(100),
    age INT
);
```
```
CREATE INDEX idx_dept_name ON users (dept, name);

DROP INDEX idx_dept_name ON users;
```

> 💡[이것만은 기억해두자!]
> 멀티 컬럼 인덱스란, 2개 이상의 컬럼을 묶어서 설정하는 인덱스를 뜻한다.
> 즉, 데이터를 빨리 찾기 위해 2개 이상의 컬럼을 기준으로 미리 정렬해놓은 표이다.

### 아래와 같은 users 테이블이 있다고 가정하자.
```
SET SESSION cte_max_recursion_depth = 1000000;

-- 더미 데이터 삽입 쿼리
INSERT INTO users (dept, name, age)
WITH RECURSIVE cte (n) AS (
    SELECT 1
    UNION ALL
    SELECT n + 1 FROM cte WHERE n < 1000000
)
SELECT 
    CASE
        WHEN n <= 333333 THEN '운영'
        WHEN n <= 666666 THEN '인사'
        ELSE '회계'
    END AS dept,
    CONCAT('User', LPAD(n, 7, '0')) AS name,
    FLOOR(1 + RAND() * 1000) AS age 
FROM cte;

```
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_06.png?raw=true)

이 때, 부서와 이름의 컬럼을 활용해 멀티 컬럼 인덱스를 만들면 아래와 같은 표가 시스템 내부에 생성된다.  
부서를 기준으로 먼저 오름차순으로 정렬한 뒤, 같은 부서의 값을 가진 데이터들 사이에서 이름을 기준으로 오름차순 정렬을 한다.
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_07.png?raw=true)
위의 예시에서는 컬럼 2개를 가지고 인덱스를 생성했지만, 2개 이상의 컬럼을 가지고 인덱스를 생성할 수도 있다.

### 멀티 컬럼 인덱스 주의점
#### 일반 인덱스처럼 활용 가능
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_08.png?raw=true)

**부서를 기준으로 먼저 정렬이 되어 있고, 그 다음 같은 부서 내에서 이름을 기준으로 정렬**되어 있다.
이런 구조로 되어 있기 때문에 부서 컬럼만 놓고 봤을 때는 부서 인덱스와 동일한 정렬 상태를 갖고 있다.
따라서 위의 멀티 컬럼 인덱스의 구조를 활용하면 부서의 인덱스를 활용하듯이 쓸 수도 있다.

정렬을 자세히 잘 살펴보면 이름 기준으로 정렬이 되어 있지는 않다.
왜냐면 같은 부서를 가진 데이터끼리만 정렬을 시켰기 때문이다. 실제로 아래 SQL문을 실행시킬 때 인덱스를 활용하지 못한다.
```
SELECT * FROM users
WHERE name = '홍길동';
```
따라서 멀티 컬럼 인덱스에서 일반 인덱스처럼 활용할 수 있는 건 처음에 배치된 컬럼들뿐이다.

소분류 → 중분류 → 대분류 컬럼순으로 구성
멀티 컬럼 인ㄷ게스를 만들 떄는 순서의 주의해야 한다.
왜나하면 순서를 어떻게 정해서 인덱스를 만드느냐에 따라서 성능 차이가 나기 때문이다.

멀티 컬럼 인덱스에서도 배치한 컬럼의 순서대로 데이터를 탐색한다. (name, dept)의 순서대로 멀티 컬럼 인덱
를 구성했다면 먼저 일치하는 이름을 찾은 뒤, 일치하는 이름에서 부서를 찾는 식으로 처리한다.

- 소분류가 가장 구체적이므로 선택도가 높음
- 선택도가 높은 컬럼을 앞에 두면 검색 범위를 빠르게 좁힐 수 있음

따라서 멀티 컬럼 인덱스를 구성할 때는 데이터 중복도가 낮은(≒ 카디널리티가 높은) 컬럼이 앞쪽으로 오는 게 좋은 경우가 많다.
- 소분류는 카디널리티가 높음(중복이 적음)
- 대분류는 카디널리티가 낮음(중복이 많음)
- 카디널리티가 높은 컬럼을 앞에 두는 것이 효율적
  데이터베이스는 인덱스를 통해 가능한 한 빨리 검색 대상을 좁히는 것이 중요하므로, 더 구체적이고 선택도가 높은 컬럼을 앞에 두는 것이 효율적이다.

> 💡[이것만은 기억해두자!]
> 멀티 컬럼 인덱스 컬럼의 순서는 매우 중요하다.
> 멀티 컬럼 인덱스에서 처음에 배치된 컬럼들은 일반 인덱스처럼 활용할 수 있다.
> 멀티 컬럼 인덱스를 구성할 때 데이터 중복도가 낮은 컬럼이 앞쪽으로 오는게 좋다.

## 커버링 인덱스(Covering Index)
### SQL문을 실행시킬 때 필요한 모든 컬럼을 갖고 있는 인덱스를 커버링 인덱스(Covering Index)라고 한다.

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_09.png?raw=true)

위와 같은 users 테이블이 있고, name 인덱스가 있다고 가정하자. 그리고 아래 2개의 SQL문을 실행해야 한다고 가정하자.
```
SELECT id, created_at FROM users;

SELECT id, name FROM users;
```
1번째 SQL문을 보면 id, created_at라는 컬럼만 조회한다고 하더라도 실제 테이블의 데이터에 접근해야 한다.

하지만 2번째 SQL문에서는 id, name 컬럼은 실제 테이블에 접근하지 않고 인덱스에만 접근해서 알아낼 수 있는 정보들이다.
따라서 실제 테이블에 접근하지 않고 데이터를 조회할 수 있다. 실제 테이블에 접근하는 것 자체가 인덱스에 접근하는 것보다 속도가 느리다.
이 상황에서 SQL문을 실행시킬 때 필요한 모든 컬럼을 갖고 있는 인덱스를 보고 커버링 인덱스(Covering Index)라고 표현한다.

```
-- 커버링 인덱스
CREATE INDEX idx_user_query ON users (dept, name, age);

-- 커버링 인덱스가 사용되는 쿼리
EXPLAIN SELECT name, age FROM users WHERE dept = '운영';
```
- Extra 컬럼에 Using index 가 나오면 → 커버링 인덱스 사용 중
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_10.png?raw=true)


| 항목             | 일반 인덱스  | 커버링 인덱스 |
|:----------------|:------------:|:-------------:|
| 인덱스 컬럼만 사용? | ❌         | ✅           |
| 테이블 접근 필요?  | ✅         | ❌           |
| 성능             | 보통        | 더 빠름       |
| EXPLAIN 표시     | Using where | Using index   | 


### ✅ 왜 빠른가?
- 일반 인덱스는:
    - 인덱스로 위치 확인 → 테이블에서 실제 데이터 읽기 (Extra I/O)
- 커버링 인덱스는:
    - 인덱스만 읽고 끝남 (테이블 row 접근 없음)
      → 디스크 I/O를 줄이므로 훨씬 빠름

## SQL문의 '실행 계획' 사용해보기(EXPLAIN)

### ✅실행 계획이란 
옵티마이저가 SQL문을 어떤 방식으로 어떻게 처리할 지를 계획한 걸 의미한다.
이 실행 계획을 보고 비효율적으로 처리하는 방식이 있는 지 점검하고, 비효율적인 부분이 있다면 
더 효율적인 방법으로 SQL문을 실행하게끔 튜닝을 하는 게 목표다. 
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_11.png?raw=true)

쉽게 설명하자면

- 데이터베이스에 어떤 SQL 쿼리를 보내면, 데이터베이스는 그 쿼리를 실행할 때 최적의 경로를 선택해야 한다.
- 이 경로는 여러 가지 요소에 따라 달라진다. 예를 들어, 테이블이 크다면 인덱스를 사용할지, 아니면 테이블 전체를 스캔할지 등을 결정해야 한다.
- 실행 계획(Explain)을 사용하면 데이터베이스가 쿼리를 처리하는 과정을 미리 알 수 있고, 이를 통해 성능을 최적화할 수 있다.
- 예를 들어, 실행 계획을 보면 쿼리가 테이블 전체를 스캔하고 있는지, 아니면 인덱스를 잘 활용하고 있는지를 확인할 수 있다.

### ✅ 실행 계획을 확인하는 방법
```
# 실행 계획 조회하기
EXPLAIN [SQL문]

# 실행 계획에 대한 자세한 정보 조회하기
EXPLAIN ANALYZE [SQL문]
```

#### 테스트 데이터
```
INSERT INTO users(name, age) VALUES
('송혜교', 26),
('고윤정', 23),
('하정우', 21),
('이민정', 24),
('이병헌', 23),
('하지원', 22),
('최지우', 22)
```
### 실행 계획 조회하기
```
EXPLAIN SELECT * FROM users
WHERE age = 23;
```

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_12.png?raw=true)

## EXPLAIN 주요 항목 요약

### 🔹 id
실행 계획의 순서. 숫자가 높을수록 나중에 실행됨.

### 🔹 table
조회된 테이블 이름.

### 🔹 type
테이블 접근 방식 (ALL, ref, const 등).  
`ALL`은 Full Scan으로 느림. `ref`, `eq_ref`가 성능 좋음.

### 🔹 possible_keys ⭐️
MySQL이 사용할 수 있다고 판단한 인덱스 목록.  
**여기에 나온다고 꼭 사용하는 건 아님.**

### 🔹 key ⭐️
실제로 사용된 인덱스.  
`NULL`이면 인덱스를 사용하지 않은 것.

### 🔹 ref
조인 시 어떤 컬럼이나 상수를 기준으로 데이터를 찾았는지 보여줌.

### 🔹 rows ⭐️⭐️⭐️
접근하는 예상 행 수.  
**튜닝 핵심 지표 → 이 값이 작을수록 좋음.**

### 🔹 filtered
조건에 만족하는 행의 비율(%).  
낮을수록 불필요한 데이터를 더 많이 읽은 것.

### 🔹 Extra ⭐️
추가 정보 표시.  
예시:
- `Using where`: WHERE 조건을 적용 중
- `Using index`: 테이블 접근 없이 인덱스만으로 결과 추출 (커버링 인덱스)

> 💡[이것만은 기억해두자!]
> rows, filtered의 값은 정확한 수치가 아닌 추정값이기 때문에 오차가 있을 수 있다.

### 실행 계획에 대한 자세한 정보 조회
```
EXPLAIN ANALYZE SELECT * FROM users
WHERE age = 23;
```
```
-> Filter: (users.age = 23)  (cost=0.95 rows=1) (actual time=0.0704..0.0786 rows=2 loops=1)
    -> Table scan on users  (cost=0.95 rows=7) (actual time=0.0654..0.0742 rows=7 loops=1)  
```
#### Table scan on users : users 테이블을 풀 스캔했다.
- rows : 접근한 데이터의 행의 수
- actual time=0.0437..0.0502
   ->0.0654 (앞에 있는 숫자) : 첫 번째 데이터에 접근하기까지의 시간 (불필요!)
   ->0.0742 (뒤에 있는 숫자) : 마지막 데이터까지 접근한 시간 (중요!)
- Filter(뒤에있는 숫자)0.0786 - Table(뒤에있는 숫자)0.0742 =  0.0044
   -> 의미 : 테이블 스캔 이후 필터 작업에 추가로 소요된 시간

## 실행 계획에서 Type 의미 분석
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_13.png?raw=true)
실행 계획(EXPLAIN)을 조회했을 때 나오는 결과값 중 하나인 type은 성능 최적화에 있어서 이 값의 의미를 
파악하는 게 굉장히 중요하다.

### ✅ ALL: 풀 테이블 스캔
풀 테이블 스캔(Full Table Scan)이란 인덱스를 활용하지 않고 테이블을 처음부터 끝까지 전부 다 뒤져서 데이터를
찾는 방식이다. 처음부터 끝까지 전부 다 뒤져서 필요한 데이터를 찾는 방식이다보니 **비효율적이다.**

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_14.png?raw=true)

```
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    age INT
);
```

```
INSERT INTO users (name, age) VALUES 
('Alice', 30),
('Bob', 23),
('Charlie', 35);
```

```
EXPLAIN SELECT * FROM users WHERE age = 23; # type : ALL
```

### ✅ INDEX : 풀 인덱스 스캔
**풀 인덱스 스캔(Full Index Scan)**이란 인덱스 테이블을 처음부터 끝까지 다 뒤져서 데이터를 찾는 방식이다.
인덱스의 테이블은 실제 테이블보다 크기가 작기 때문에, **폴 테이블 스캔(Full Table Scan)** 보다 효율적이다.
하지만 인덱스 테이블 전체를 읽어야 하기 때문에 **아주 효율적이라고는 볼 수는 없다.**

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_15.png?raw=true)

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

```
CREATE INDEX idx_name ON users (name);

```
```
SELECT * FROM users
ORDER BY name
LIMIT 10;
```

> type이 index인걸로 봐서 풀 인덱스 스캔을 한것을 확인할 수 있다.   
> 즉, 인덱스를 처음부터 끝까지 다 읽어서 필요한 데이터를 뽑아냈다.

## Const

### ✅ Const란?
조회하고자 하는 1건의 데이터를 헤매지 않고 단번에 찾아올 수 있을 때 const가 출력된다.
그러다보니 고유 인덱스 또는 기본 키를 사용해서 1건의 데이터만 조회한 경우에 const가 출력된다.
이 방식은 아주 효율적인 방식이다.

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_16.png?raw=true)

- 인덱스가 없다면 특정 값을 일일이 다 뒤져야 한다. 그래서 1건의 데이터를 바로 찾을 수 없다.
- 인덱스가 있는데 고유하지 않다면(NOT UNIQUE) 원하는 1건의 데이터를 찾았다고 하더라도, 나머지 데이터에 같은 값이 있을 지도 모르므로 다른 데이터들도 체크해봐야 한다.
- 고유하다면(UNIQUE) 1건의 데이터를 찾는 순간, 나머지 데이터는 아예 볼 필요가 없어진다. 왜냐하면 찾고자 하는 데이터가 유일한 데이터이기 때문이다.  
  → UNIQUE 제약조건이 있는 컬럼과 기본 키는 전부 UNIQUE한 특성을 가지고 있다.

```
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    account VARCHAR(100) UNIQUE
);

INSERT INTO users(account) VALUE 
('user1@example.com'),
('user2@example.com'),
('user3@example.com')
```

```
EXPLAIN SELECT * FROM users WHERE id = 3;
EXPLAIN SELECT * FROM users WHERE account = 'user3@example.com';
```

UNIQUE 속성을 가진 컬럼은 인덱스가 자동으로 생성된다.

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_17.png?raw=true)

## Range(Index Range Scan)

### ✅ range: 인덱스 레인지 스캔(index Range Scan) 
인덱스 레인지 스캔(Index Range Scan)은 인덱스를 활용해 범위 형태의 데이터를 조회한 경우를 의미한다.
범위 형태란 **BETWEEN, 부등호(<, >, ≤, ≥), IN, LIKE**를 활용한 데이터 조회를 뜻한다.
이 방식은 인덱스를 활용하기 때문에 **효율적인 방식이다.**
하지만 인덱스를 사용하더라도 데이터를 조회하는 범위가 클 경우 성능 저하의 원인이 되기도 한다.

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_18.png?raw=true)

```
DROP TABLE IF EXISTS users;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    account INT
);
```
```
SET SESSION cte_max_recursion_depth = 1000000;

-- 더미 데이터 삽입 쿼리
INSERT INTO users (age)
WITH RECURSIVE cte (n) AS (
    SELECT 1
    UNION ALL
    SELECT n + 1 FROM cte WHERE n < 1000000
)
SELECT 
    FLOOR(1 + RAND() * 1000) AS age 
FROM cte;
``` 
``` 
CREATE INDEX idx_age ON users(age);
``` 

``` 
EXPLAIN SELECT * FROM users
WHERE age BETWEEN 10 and 20;

EXPLAIN SELECT * FROM users
WHERE age IN (10, 20, 30);

EXPLAIN SELECT * FROM users
WHERE age < 20;
``` 
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_19.png?raw=true)

## Ref
### ✅ ref : 비고유 인덱스를 활용하는 경우
비고유 인덱스를 사용한 경우(=UNIQUE가 아닌 컬럼의 인덱스를 사용한 경우) type 에 ref가 출력된다.
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_20.png?raw=true)

``` 
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100)
);
```
``` 
CREATE INDEX idx_name ON users(name);
```
``` 
EXPLAIN SELECT * FROM users WHERE name = '홍길동';
``` 

### ✅이 외의 type들
eq_ref, index_merge, ref_or_null 등 다양한 타입들이 존재한다. 하지만 모든 타입들을 다 미리 공부할 필요는 없다.
자주는 나오는 type에 먼저 익숙해진 다음에 더 딮이 공부하고 싶을 떄 다른 type들도 찾아서 공부 하면 된다.

## [실습] 한 번에 너무 많은 데이터를 조회하는 SQL문 튜닝하기
- 데이터를 조회할 때 한 번에 너무 많은 데이터를 조회하는 건 아닌지 체크
- LIMIT, WHERE문 등을 활용해서 한 번에 조회하는 데이터의 수를 줄이는 방법을 고려

```
DROP TABLE IF EXISTS users; # 기존 테이블 삭제
 
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    age INT
);
``` 
``` 
-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 개수와 맞춰서 작성하면 된다.)
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
#### 데이터 조회
``` 
SELECT * FROM users LIMIT 10000; -- 200ms 

SELECT * FROM users LIMIT 10; -- 20ms
``` 
### ✅ 조회 결과 데이터의 개수 줄이기
실제 페이스북, 인스타그램의 서비스를 보더라도 한 번에 모든 게시글의 데이터를 불러오지 않는다.
스크롤을 내리면서 필요한 데이터를 그때그때 로딩하는 방식이다. 다른 커뮤니티 서비스의 게시판을 보면 페이지네이션을 적용시켜서 일부 데이터만 조회하려고 한다.
그 이유가 **조회하는 데이터의 개수가 성능에 많은 영향을 끼치기 때문이다.**

직관적으로 생각해보면 100만개의 데이터에서 1개의 데이터를 찾는 것보다 10,000개의 데이터를 찾는 게 오래 걸릴 수 밖에 없다.


> 💡[이것만은 기억해두자!]
> 데이터를 조회할 때 한 번에 너무 많은 데이터를 조죄하는 건 아닌 지 체크해봐라.
> LIMIT, WHERE문 등을 활용해서 한 번에 조회하는 데이터의 수를 줄이는 방법을 고혀해봐라.


## [실습] WHERE문이 사용된 SQL문 튜닝하기 - 1
### ✅ 최근 3일 이내에 가입한 유저 조회하기

### WHERE문 튜닝
#### 테이블 생성
``` 
DROP TABLE IF EXISTS users; 

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
``` 

#### 100만건의 랜덤 데이터 삽입
``` 
-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 개수와 맞춰서 작성하면 된다.)
SET SESSION cte_max_recursion_depth = 1000000; 

-- 더미 데이터 삽입 쿼리
INSERT INTO users (name, department, created_at)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    CONCAT('User', LPAD(n, 7, '0')) AS name,  -- 'User' 다음에 7자리 숫자로 구성된 이름 생성
    CASE 
        WHEN n % 10 = 1 THEN 'Engineering'
        WHEN n % 10 = 2 THEN 'Marketing'
        WHEN n % 10 = 3 THEN 'Sales'
        WHEN n % 10 = 4 THEN 'Finance'
        WHEN n % 10 = 5 THEN 'HR'
        WHEN n % 10 = 6 THEN 'Operations'
        WHEN n % 10 = 7 THEN 'IT'
        WHEN n % 10 = 8 THEN 'Customer Service'
        WHEN n % 10 = 9 THEN 'Research and Development'
        ELSE 'Product Management'
    END AS department,  -- 의미 있는 단어 조합으로 부서 이름 생성
    TIMESTAMP(DATE_SUB(NOW(), INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND) AS created_at -- 최근 10년 내의 임의의 날짜와 시간 생성
FROM cte;

``` 

#### 성능 측정
``` 
EXPLAIN SELECT * FROM users
WHERE created_at >= DATE_SUB(NOW(), INTERVAL 3 DAY);
```
- 풀 테이블 스캔은 성능상으로 비효율적이다. 

```
CREATE INDEX idx_created_at ON users(create_at);
```

``` 
EXPLAIN SELECT * FROM users
WHERE created_at >= DATE_SUB(NOW(), INTERVAL 3 DAY);
```
### 다시 실행 계획을 조회하면 인덱스 레인지 스캔을 한 것을 확인할 수 있다.
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_21.png?raw=true)


![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_22.png?raw=true)


> 💡[이것만은 기억해두자!]
> WHERE문의 부등호(>, <, ≤, ≥, =), IN, BETWEEN, LIKE와 같은 곳에서 사용되는 컬럼은 인덱스를 사용했을 때 성능이 향상될 가능성이 높다.
> 데이터 액세스(rows)를 크게 줄일 수 있는 컬럼은 중복 정도가 낮은 컬럼이다. 따라서 중복 정도가 낮은 컬럼을 골라서 인덱스를 생성
> **단일 컬럼에 설정하는 일반 인덱스**를 설정했을 때와 "멀티 컬럼 인덱스"를 설정했을 때의 성능 차이가 별로 나지 않는다면, 멀티 컬럼 인덱스를 사용하지 말고 일반 인덱스를 활용하자.


## [실습] WHERE문이 사용된 SQL문 튜닝하기 - 2
### ✅ Sales 부서이면서 최근 3일 이내에 가입한 유저 조회하기

#### 테이블 생성
``` 
DROP TABLE IF EXISTS users; 

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
``` 
#### 100만건의 랜덤 데이터 삽입
``` 
-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 개수와 맞춰서 작성하면 된다.)
SET SESSION cte_max_recursion_depth = 1000000; 

-- 더미 데이터 삽입 쿼리
INSERT INTO users (name, department, created_at)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    CONCAT('User', LPAD(n, 7, '0')) AS name,  -- 'User' 다음에 7자리 숫자로 구성된 이름 생성
    CASE 
        WHEN n % 10 = 1 THEN 'Engineering'
        WHEN n % 10 = 2 THEN 'Marketing'
        WHEN n % 10 = 3 THEN 'Sales'
        WHEN n % 10 = 4 THEN 'Finance'
        WHEN n % 10 = 5 THEN 'HR'
        WHEN n % 10 = 6 THEN 'Operations'
        WHEN n % 10 = 7 THEN 'IT'
        WHEN n % 10 = 8 THEN 'Customer Service'
        WHEN n % 10 = 9 THEN 'Research and Development'
        ELSE 'Product Management'
    END AS department,  -- 의미 있는 단어 조합으로 부서 이름 생성
    TIMESTAMP(DATE_SUB(NOW(), INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND) AS created_at -- 최근 10년 내의 임의의 날짜와 시간 생성
FROM cte;
``` 

#### 성능 측정

``` 
EXPLAIN ANALYZE SELECT * FROM users
WHERE department = 'Sales'
AND created_at >= DATE_SUB(NOW(), INTERVAL 3 DAY);
``` 

``` 
-> Filter: ((users.department = 'Sales') and (users.created_at >= <cache>((now() - interval 3 day))))  (cost=93877 rows=33224) (actual time=0.813..234 rows=121 loops=1)
    -> Table scan on users  (cost=93877 rows=996810) (actual time=0.206..192 rows=1e+6 loops=1)
```
- 액세스한 데이터의 개수는 1e+6(= 10의 6제곱 = 1,000,000)개 이다.
- 액세스한 1,000,000개의 데이터 중 department = Sales와 created_at >= DATE_SUB(NOW(), INTERVAL 3 DAY)을 만족하는 데이터를 필터링해온다.  
  → 조건을 만족한 데이터의 개수는 rows는 121개이다.

### 성능 개선을 위한 인덱스 추가

#### 인덱스를 추가하는 방법이 3가지가 있다.
- created_at 컬럼을 기준으로 인덱스 생성
- department 컬럼을 기준으로 인덱스 생성
- department, created_at 둘 다 인덱스 생성

### **department** 컬럼보다 **created_at** 컬럼에 인덱스를 설정하는 것이 더 효율적이다. 이유는 다음과 같다.

## ✅ department 컬럼
- 고정된 값(Sales, Engineering 등)이 반복된다.
- 특정 값을 조회하면 많은 행이 중복 선택된다.
- 인덱스를 사용해도 선택되는 데이터가 많아 성능 이점이 줄어든다.

## ✅ created_at 컬럼
- 시간 순으로 연속된 값이 저장된다.
- "최근 3일", "최근 1주일"과 같은 범위 조건으로 조회 시, 적은 데이터만 선택된다.
- 인덱스를 설정하면 범위 내에서만 데이터를 읽어와 성능이 향상된다.

## ⚠️ 인덱스는 최소한으로
- 인덱스가 많을수록 쓰기(INSERT/UPDATE) 성능은 저하된다.
- **created_at**에 인덱스를 설정하면 대부분의 조회 성능을 커버할 수 있다.
- 불필요한 인덱스를 줄이기 위해 **department**에는 인덱스를 설정하지 않아도 된다.

> 💡[이것만은 기억해두자!]
> 데이터 엑세스(rows)를 크게 줄일 수 있는 컬ㄹ머은 중복 정도가 낮은 컬럼이다. 따라서 중복 정도가 낮은 컬럼을 골라서 인덱스를 생성해라.

## [실습] 인덱스를 걸었는데도 인덱스가 작동하지 않는 경우 - 1

### ✅ 조회 범위가 너무 넓은 경우

#### 테이블 생성
``` 
DROP TABLE IF EXISTS users; # 기존 테이블 삭제

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    age INT
);
```
#### 데이터 삽입
``` 
-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 개수와 맞춰서 작성하면 된다.)
SET SESSION cte_max_recursion_depth = 1000000; 

-- 더미 데이터 삽입 쿼리
INSERT INTO users (name, age)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    CONCAT('User', LPAD(n, 7, '0')),   -- 'User' 다음에 7자리 숫자로 구성된 이름 생성
    FLOOR(1 + RAND() * 1000) AS age    -- 1부터 1000 사이의 난수로 나이 생성
FROM cte;
``` 

#### 인덱스 설정
``` 
CREATE INDEX idx_name ON users (name);
``` 
#### 실행 계획 조회
``` 
EXPLAIN SELECT * FROM users
ORDER BY name DESC;
``` 

- 분명 인덱스를 걸었음에도 왜 풀 테이블 스캔으로 데이터를 조회된다.
- 그 이유는 옵티마이저가 넓은 범위의 데이터를 조회할 때는 인덱스를 활용하는 것이 비효율적이라고 판단한다. 
  인덱스를 활용하지 않고 풀 테이블 스캔으로 데이터를 찾을 때 훨씬 효율적이라고 판단한다.
- 즉, 굳이 인덱스를 거쳤다가 각 원래 테이블의 데이터를 일일이 하나씩 찾아내는 것보다, 바로 원래 테이블에 접근해서 
  모든 데이터를 통째로 가져와서 정렬하는 게 효율적이라고 판단한 것이다. 실제 성능상으로도 풀 테이블 스캔을 통해 데이터를 가져오는 게 효율적이다.

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_23.png?raw=true)

> 💡[이것만은 기억해두자!]
> 넓은 범위의 데이터를 조회하는 경우, MySQL은 인덱스를 사용해서 조회하는 것보다 풀 테이블 스캔이 효과적이라고 판단한다.    
> 인덱스 컬럼을 가공(함수 적용, 산술 연산, 문자역 조작 등)하면, MySQL은 해당 인덱스를 사용하지 못하는 경우가 많다.     
> 따라서 인덱스를 적극 활용하기 위해서는 인덱스 컬럼 자체를 최대한 가공하지 않아야 한다.

## 인덱스를 걸었는데도 인덱스가 작동하지 않는 경우 - 2

### ✅ 인덱스 컬럼을 가공한 경우
#### 테이블 생성
``` 
DROP TABLE IF EXISTS users; 

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    salary INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
``` 
#### 데이터 삽입
``` 
-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 개수와 맞춰서 작성하면 된다.)
SET SESSION cte_max_recursion_depth = 1000000; 

-- users 테이블에 더미 데이터 삽입
INSERT INTO users (name, salary, created_at)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    CONCAT('User', LPAD(n, 7, '0')) AS name,  -- 'User' 다음에 7자리 숫자로 구성된 이름 생성
    FLOOR(1 + RAND() * 1000000) AS salary,    -- 1부터 1000000 사이의 난수로 급여 생성
    TIMESTAMP(DATE_SUB(NOW(), INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND) AS created_at -- 최근 10년 내의 임의의 날짜와 시간 생성
FROM cte;
``` 
#### 인덱스 설정
``` 
CREATE INDEX idx_name ON users (name);

CREATE INDEX idx_salary ON users (salary);
``` 

#### 실행 계획 조회
``` 
# User000000으로 시작하는 이름을 가진 유저 조회
EXPLAIN SELECT * FROM users
WHERE SUBSTRING(name, 1, 10) = 'User000000';

# 2달치 급여(salary)가 1000 이하인 유저 조회
EXPLAIN SELECT * FROM users
WHERE salary * 2 < 1000
ORDER BY salary;
``` 

- 위의 실행 계획들을 실행시켜보면 인덱스를 활용하지 않고 풀 테이블 스캔으로 탐색하는 걸 확인할 수 있다. 인덱스를 활용하지 않기 때문에 비효율적으로 데이터를 조회한다.
- SQL문을 작성할 인덱스 컬럼을 가공(함수 적용, 산술 연산, 문자역 조작 등)하면, MySQL은 해당 인덱스를 활용하지 못하는 경우가 많다.
- 따라서 인덱스를 적극 활용하기 위해서는 인덱스 컬럼 자체를 최대한 가공하지 않아야 한다. 

### 인덱스 컬럼을 가공하지 않은 SQL 문
``` 
# User000000으로 시작하는 이름을 가진 유저 조회
EXPLAIN SELECT * FROM users
WHERE name LIKE 'User000000%';

# 2달치 급여(salary)가 1000 이하인 유저 조회
EXPLAIN SELECT * FROM users
WHERE salary < 1000 / 2
ORDER BY salary;
``` 
- 인덱스 컬럼을 가공하지 않아야 인덱스를 제대로 활용할 수 있게 된다.

## [실습] ORDER BY문이 사용된 SQL문 튜닝하기

- ORDER BY는 시간이 오래걸리는 작업이므로 최대한 피해주는 것이 좋다. 인덱스를 사용하면 미리 정렬을 해둔 상태이기 때문에,   
  ORDER BY를 사용해서 정렬해야 하는 번거로운 작업을 피할 수 있다.
- LIMIT 없이 큰 범위의 데이터를 조회해오는 경우 옵티마이저가 인덱스를 활용하지 않고 풀 테이블 스캔을 해버릴 수도 있다.  
  따라서 성능 효율을 위해 LIMIT을 통해 작은 데이터의 범위를 조회해오도록 항상 신경쓰자. 

#### 테이블 생성
``` 
DROP TABLE IF EXISTS users; 

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(100),
    salary INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
``` 
#### 데이터 삽입
``` 
-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 개수와 맞춰서 작성하면 된다.)
SET SESSION cte_max_recursion_depth = 1000000; 

-- 더미 데이터 삽입 쿼리
INSERT INTO users (name, department, salary, created_at)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    CONCAT('User', LPAD(n, 7, '0')) AS name,  -- 'User' 다음에 7자리 숫자로 구성된 이름 생성
    CASE 
        WHEN n % 10 = 1 THEN 'Engineering'
        WHEN n % 10 = 2 THEN 'Marketing'
        WHEN n % 10 = 3 THEN 'Sales'
        WHEN n % 10 = 4 THEN 'Finance'
        WHEN n % 10 = 5 THEN 'HR'
        WHEN n % 10 = 6 THEN 'Operations'
        WHEN n % 10 = 7 THEN 'IT'
        WHEN n % 10 = 8 THEN 'Customer Service'
        WHEN n % 10 = 9 THEN 'Research and Development'
        ELSE 'Product Management'
    END AS department,  -- 의미 있는 단어 조합으로 부서 이름 생성
    FLOOR(1 + RAND() * 1000000) AS salary,    -- 1부터 1000000 사이의 난수로 나이 생성
    TIMESTAMP(DATE_SUB(NOW(), INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND) AS created_at -- 최근 10년 내의 임의의 날짜와 시간 생성
FROM cte;
``` 

#### 성능 측정
``` 
EXPLAIN SELECT * FROM users
ORDER BY salary
LIMIT 100;
``` 

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_24.png?raw=true)

- type이 ALL이다. 풀 테이블 스캔을 했음을 뜻한다.
- ORDER BY는 시간이 오래걸리는 작업이므로 최대한 피해주는 것이 좋다. 왜냐하면 정렬이라는 작업 자체가 
  다른 작업에 비해서 부담스러운 작업이며 성능에 안 좋은 영향을 끼치는 요소 중 하나이기 때문이다.

#### 성능 개선을 위한 인덱스 추가
``` 
CREATE INDEX idx_salary ON users (salary);
``` 

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_25.png?raw=true)

- 풀 테이블 스캔(type: ALL)이 아닌 인덱스 풀 스캔(type: index)을 활용해서 빠르게 데이터를 정렬해서 조회해왔다.
- LIMIT 없이 큰 범위의 데이터를 조회해오는 경우 옵티마이저가 인덱스를 활용하지 않고 테이블 풀 스캔을 해버릴 수도 있다.    
  따라서 성능 효율을 위해 LIMIT을 통해 작은 데이터의 범위를 조회해오도록 항상 신경써야 한다.

#### 실행 계획 세부 내용 조회
``` 
EXPLAIN ANALYZE SELECT * FROM users
ORDER BY salary
LIMIT 100;
``` 

``` 
-> Limit: 100 row(s)  (cost=0.0918 rows=100) (actual time=0.916..1.81 rows=100 loops=1)
    -> Index scan on users using idx_salary  (cost=0.0918 rows=100) (actual time=0.914..1.79 rows=100 loops=1)
``` 

- 인덱스 스캔으로 100개의 데이터에 대해서만 액세스 했다.  
  (정렬 작업을 따로 하지 않았다. 왜냐하면 인덱스라서 이미 정렬이 되어 있기 때문이다.)
- Limit에 의해 100개의 데이터만 조회했다.

## [실습] WHERE문에 인덱스를 걸기 vs ORDER BY문에 인덱스를 걸기

- ORDER BY의 특징상 모든 데이터를 바탕으로 정렬을 해야 하기 때문에, 인덱스 풀 스캔 또는 테이블 풀 스캔을 활용할 수 밖에 없다.
- 이 때문에 ORDER BY문보다 WHERE문에 있는 컬럼에 인덱스를 걸었을 때 성능이 향상되는 경우가 많다.  
  (항상 그런 것은 아니니까 성능 측정과 실행 계획을 살펴야한다.)
``` 
DROP TABLE IF EXISTS users; 

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(100),
    salary INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
``` 
-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 개수와 맞춰서 작성하면 된다.)
SET SESSION cte_max_recursion_depth = 1000000; 

-- 더미 데이터 삽입 쿼리
INSERT INTO users (name, department, salary, created_at)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    CONCAT('User', LPAD(n, 7, '0')) AS name,  -- 'User' 다음에 7자리 숫자로 구성된 이름 생성
    CASE 
        WHEN n % 10 = 1 THEN 'Engineering'
        WHEN n % 10 = 2 THEN 'Marketing'
        WHEN n % 10 = 3 THEN 'Sales'
        WHEN n % 10 = 4 THEN 'Finance'
        WHEN n % 10 = 5 THEN 'HR'
        WHEN n % 10 = 6 THEN 'Operations'
        WHEN n % 10 = 7 THEN 'IT'
        WHEN n % 10 = 8 THEN 'Customer Service'
        WHEN n % 10 = 9 THEN 'Research and Development'
        ELSE 'Product Management'
    END AS department,  -- 의미 있는 단어 조합으로 부서 이름 생성
    FLOOR(1 + RAND() * 1000000) AS salary,    -- 1부터 1000000 사이의 난수로 나이 생성
    TIMESTAMP(DATE_SUB(NOW(), INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND) AS created_at -- 최근 10년 내의 임의의 날짜와 시간 생성
FROM cte;
``` 
#### 데이터 조회 성능 확인
```
EXPLAIN SELECT * FROM users
WHERE created_at >= DATE_SUB(NOW(), INTERVAL 3 DAY)
AND department = 'Sales'
ORDER BY salary
LIMIT 100;
```
- 풀 테이블 스캔을 했다. 비효율적이다.

### ✅ 성능 개선을 위한 인덱스 추가

- SQL문만 봤을 때는 created_at, department, salary 컬럼에 인덱스를 걸 수 있는 선택지가 있다는 걸 알 수 있다.
- 어떤 컬럼에 거는 게 효율적인지는 하나씩 걸어보고 SQL문의 성능을 측정해서 판단해도 된다.

# 성능 개선을 위한 인덱스 선택 전략

SQL문을 보면 `created_at`, `department`, `salary` 컬럼에 인덱스를 설정할 수 있는 후보가 존재한다.  
어떤 컬럼에 인덱스를 거는 것이 효율적인지는 하나씩 적용해 보고, 성능을 비교하며 판단할 수 있다.

## ✅ created_at vs department

먼저 `created_at`과 `department` 중 어떤 컬럼에 인덱스를 거는 것이 더 효율적인지 예상해보자.

- `department = 'Sales'` 조건은 해당 값이 자주 등장할 수 있어, 데이터 액세스 건수가 많아질 가능성이 크다.
- 반면, `created_at >= DATE_SUB(NOW(), INTERVAL 3 DAY)` 조건은 **최근 며칠의 데이터만 조회**하므로 액세스 건수가 적다.

**데이터 액세스 수를 줄이는 것이 성능 향상의 핵심**이기 때문에,  
`department`보다 `created_at`에 인덱스를 설정하는 것이 더 유리하다고 볼 수 있다.

## ✅ created_at vs salary

이번엔 `WHERE`절의 `created_at`과 `ORDER BY`절의 `salary` 중 어떤 컬럼에 인덱스를 걸어야 할지 비교해보자.

결론적으로는, **`salary`보다 `created_at`에 인덱스를 거는 것이 더 효율적**이다.  
왜냐하면 `WHERE`절에서 조건을 통해 데이터를 **필터링하는 단계에서 인덱스가 가장 큰 효과**를 발휘하기 때문이다.


## 🧠 정리
- **`WHERE`절의 조건 필터링**이 가장 우선순위가 높다.
- **선택도 높은(created_at처럼)** 컬럼에 인덱스를 걸면 성능 향상이 크다.
- `ORDER BY`절도 인덱스를 사용할 수 있지만, **우선순위는 낮다**.

### salary에 인덱스
```
CREATE INDEX idx_salary ON users (salary);
```
```
-- 실행 계획
EXPLAIN SELECT * FROM users
WHERE created_at >= DATE_SUB(NOW(), INTERVAL 3 DAY)
AND department = 'Sales'
ORDER BY salary
LIMIT 100;
```
- 인덱스를 걸기 전보다 약 5배정도 느려진 것을 확인할 수 있다.
- 위의 실행 계획에서 type이 index이면서 salary 인덱스를 사용한 걸로 봐서 인덱스 풀 스캔을 했음을 알 수 있다.

### created_at에 인덱스
```
ALTER TABLE users DROP INDEX idx_salary; -- 기존 인덱스 삭제
CREATE INDEX idx_created_at ON users (created_at);
```
```
-- 실행 계획
EXPLAIN SELECT * FROM users
WHERE created_at >= DATE_SUB(NOW(), INTERVAL 3 DAY)
AND department = 'Sales'
ORDER BY salary
LIMIT 100;
```
- 인덱스를 걸지 않은 200ms보다 약 6~7배 정도 빨라졌음을 알 수 있다. 
- type이 range인걸 보니 인덱스 레인지 스캔을 활용했음을 알 수 있다. 


## [실습] HAVING문이 사용된 SQL문 튜닝하기

```
DROP TABLE IF EXISTS users; 

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    age INT,
    department VARCHAR(100),
    salary INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
```
-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 개수와 맞춰서 작성하면 된다.)
SET SESSION cte_max_recursion_depth = 1000000; 

-- 더미 데이터 삽입 쿼리
INSERT INTO users (name, age, department, salary, created_at)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    CONCAT('User', LPAD(n, 7, '0')) AS name,  -- 'User' 다음에 7자리 숫자로 구성된 이름 생성
    FLOOR(1 + RAND() * 100) AS age, -- 1부터 100 사이의 난수로 생성
    CASE 
        WHEN n % 10 = 1 THEN 'Engineering'
        WHEN n % 10 = 2 THEN 'Marketing'
        WHEN n % 10 = 3 THEN 'Sales'
        WHEN n % 10 = 4 THEN 'Finance'
        WHEN n % 10 = 5 THEN 'HR'
        WHEN n % 10 = 6 THEN 'Operations'
        WHEN n % 10 = 7 THEN 'IT'
        WHEN n % 10 = 8 THEN 'Customer Service'
        WHEN n % 10 = 9 THEN 'Research and Development'
        ELSE 'Product Management'
    END AS department,  -- 의미 있는 단어 조합으로 부서 이름 생성
    FLOOR(1 + RAND() * 1000000) AS salary,    -- 1부터 1000000 사이의 난수로 생성
    TIMESTAMP(DATE_SUB(NOW(), INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND) AS created_at -- 최근 10년 내의 임의의 날짜와 시간 생성
FROM cte;
```

#### 인덱스 생성
```
CREATE INDEX idx_age ON users (age);
```

#### 조회 성능 확인
```
SELECT age, MAX(salary) FROM users
GROUP BY age
HAVING age >= 20 AND age < 30;
```

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_26.png?raw=true)

- 약 800ms 정도의 시간이 소요

#### 실행 계획 조회
```
EXPLAIN SELECT age, MAX(salary) FROM users
GROUP BY age
HAVING age >= 20 AND age < 30;
```

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_27.png?raw=true)

- type이 index인걸로 봐서 인덱스 풀 스캔을 하고 있다. 

#### 성능 개선
```
SELECT age, MAX(salary) FROM users
WHERE age >= 20 AND age < 30
GROUP BY age;
```
- HAVING 문 대신에 WHERE 문에 조건을 넣으니 약 150ms 정도 시간이 걸린다.

```
EXPLAIN SELECT age, MAX(salary) FROM users
WHERE age >= 20 AND age < 30
GROUP BY age;
```
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_28.png?raw=true)


```
EXPLAIN ANALYZE SELECT age, MAX(salary) FROM users
WHERE age >= 20 AND age < 30
GROUP BY age;
```
```
-> Group aggregate: max(users.salary)  (cost=133919 rows=94.7) (actual time=32.6..139 rows=10 loops=1)
    -> Index range scan on users using idx_age over (20 <= age < 30), with index condition: ((users.age >= 20) and (users.age < 30))  (cost=88569 rows=196820) (actual time=1.09..136 rows=99665 loops=1)
```
- HAVING 대신에 WHERE문을 사용함으로써 GROUP BY를 처리하기 전에 데이터를 필터링했다.
- 그런 뒤에 필터링 된 데이터를 기반으로 GROUP BY를 진행했다. 

## [실습] 유저 이름으로 특정 기간에 작성된 글 검색하는 SQL문 튜닝하기

#### 기본 테이블 생성
```
DROP TABLE IF EXISTS posts;
DROP TABLE IF EXISTS users;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE posts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

#### 더미 데이터 추가
```
-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 개수와 맞춰서 작성하면 된다.)
SET SESSION cte_max_recursion_depth = 1000000; 

-- users 테이블에 더미 데이터 삽입
INSERT INTO users (name, created_at)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    CONCAT('User', LPAD(n, 7, '0')) AS name,  -- 'User' 다음에 7자리 숫자로 구성된 이름 생성
    TIMESTAMP(DATE_SUB(NOW(), INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND) AS created_at -- 최근 10년 내의 임의의 날짜와 시간 생성
FROM cte;

-- posts 테이블에 더미 데이터 삽입
INSERT INTO posts (title, created_at, user_id)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    CONCAT('Post', LPAD(n, 7, '0')) AS name,  -- 'User' 다음에 7자리 숫자로 구성된 이름 생성
    TIMESTAMP(DATE_SUB(NOW(), INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND) AS created_at, -- 최근 10년 내의 임의의 날짜와 시간 생성
    FLOOR(1 + RAND() * 50000) AS user_id -- 1부터 50000 사이의 난수로 급여 생성
FROM cte;
```

#### 기존 SQL문 성능 측정
```
SELECT p.id, p.title, p.created_at
FROM posts p
JOIN users u ON p.user_id = u.id
WHERE u.name = 'User0000046'
AND p.created_at BETWEEN '2022-01-01' AND '2024-03-07';
```
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_29.png?raw=true)

- 약 0.70초 소요 (데이터가 많을수록 더 증가)

#### 실행 계획 조회
```
EXPLAIN SELECT p.id, p.title, p.created_at
FROM posts p
JOIN users u ON p.user_id = u.id
WHERE u.name = 'User0000046'
AND p.created_at BETWEEN '2021-01-01' AND '2025-03-07';
```

- 풀 테이블 스캔을 하기 때문에 인덱스를 추가해야 한다.
- 인덱스를 추가할 수 있는 컬럼이 users.name과 posts.created_at이 있다.

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_30.png?raw=true)

#### 성능 개선을 위한 인덱스 생성
```
CREATE INDEX idx_name ON users (name);
CREATE INDEX idx_created_at ON posts (created_at);
```
- 실행 계획에서 users.name 인덱스는 사용되었지만,
- posts.created_at 인덱스는 사용되지 않음
MySQL 옵티마이저는 조인 후 필터링이 더 효율적이라 판단해서 created_at 인덱스를 사용하지 않음.

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_31.png?raw=true)

- 인덱스를 추가한다고 무조건 성능이 좋아지는 건 아님
- 옵티마이저가 사용하지 않을 인덱스는 오히려 관리 오버헤드만 증가
```
ALTER TABLE posts DROP INDEX idx_created_at;
```

- 성능이 많이 개선되었다.
- 이유는 created_at 인덱스를 통해 데이터를 가져오는 것이 탐색량이 더 적기 때문이다.
  결론: 인덱스 튜닝 요약
  where/join 조건에 사용되는 컬럼에 인덱스를 고려하되,

> 💡[이것만은 기억해두자!]
> where/join 조건에 사용되는 컬럼에 인덱스를 고려하되,
> 실제로 사용되는지 EXPLAIN으로 꼭 확인
> 불필요한 인덱스는 제거해서 성능과 디스크 비용을 줄이자

## [실습] 특정 부서에서 최대 연봉을 가진 사용자들 조회하는 SQL문 튜닝하기

#### 테이블 생성
```
DROP TABLE IF EXISTS posts;
DROP TABLE IF EXISTS users; 

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(100),
    salary INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### 더미 데이터 삽입
```
-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 개수와 맞춰서 작성하면 된다.)
SET SESSION cte_max_recursion_depth = 1000000; 

-- 더미 데이터 삽입 쿼리
INSERT INTO users (name, department, salary, created_at)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    CONCAT('User', LPAD(n, 7, '0')) AS name,  -- 'User' 다음에 7자리 숫자로 구성된 이름 생성
    CASE 
        WHEN n % 10 = 1 THEN 'Engineering'
        WHEN n % 10 = 2 THEN 'Marketing'
        WHEN n % 10 = 3 THEN 'Sales'
        WHEN n % 10 = 4 THEN 'Finance'
        WHEN n % 10 = 5 THEN 'HR'
        WHEN n % 10 = 6 THEN 'Operations'
        WHEN n % 10 = 7 THEN 'IT'
        WHEN n % 10 = 8 THEN 'Customer Service'
        WHEN n % 10 = 9 THEN 'Research and Development'
        ELSE 'Product Management'
    END AS department,  -- 의미 있는 단어 조합으로 부서 이름 생성
    FLOOR(1 + RAND() * 100000) AS salary,    -- 1부터 100000 사이의 난수로 나이 생성
    TIMESTAMP(DATE_SUB(NOW(), INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND) AS created_at -- 최근 10년 내의 임의의 날짜와 시간 생성
FROM cte;
```

#### 기존 SQL문 성능 측정
```
SELECT *
FROM users
WHERE salary = (SELECT MAX(salary) FROM users)
AND department IN ('Sales', 'Marketing', 'IT');
```
- 약 0.30초 소요 (데이터가 많을수록 더 증가)

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_32.png?raw=true)

- type이 ALL -> 풀 테이블 스캔
- 인덱스를 활용해서 풀 테이블 스캔을 하지 않도록 바꿔보자.

#### 인덱스 생성
- 데이터 액세스 수를 크게 줄일 수 있는 컬럼은 중복 정도가 낮은 컬럼이다.
- 따라서 salary로 인덱스를 생성
```
CREATE INDEX idx_salary ON users (salary);
```

#### 성능 측정
```
EXPLAIN SELECT *
FROM users
WHERE salary = (SELECT MAX(salary) FROM users)
AND department IN ('Sales', 'Marketing', 'IT');
```

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_33.png?raw=true)

- 인덱스를 활용해서 데이터를 액세스 했고, 액세스 수도 11개로 확 줄었다. 

## [실습] 부서별 최대 연봉을 가진 사용자들 조회하는 SQL문 튜닝하기

#### 테이블 생성
```
DROP TABLE IF EXISTS users; 

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(100),
    salary INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
#### 더미 데이터 생성
```
-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 개수와 맞춰서 작성하면 된다.)
SET SESSION cte_max_recursion_depth = 1000000; 

-- 더미 데이터 삽입 쿼리
INSERT INTO users (name, department, salary, created_at)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    CONCAT('User', LPAD(n, 7, '0')) AS name,  -- 'User' 다음에 7자리 숫자로 구성된 이름 생성
    CASE 
        WHEN n % 10 = 1 THEN 'Engineering'
        WHEN n % 10 = 2 THEN 'Marketing'
        WHEN n % 10 = 3 THEN 'Sales'
        WHEN n % 10 = 4 THEN 'Finance'
        WHEN n % 10 = 5 THEN 'HR'
        WHEN n % 10 = 6 THEN 'Operations'
        WHEN n % 10 = 7 THEN 'IT'
        WHEN n % 10 = 8 THEN 'Customer Service'
        WHEN n % 10 = 9 THEN 'Research and Development'
        ELSE 'Product Management'
    END AS department,  -- 의미 있는 단어 조합으로 부서 이름 생성
    FLOOR(1 + RAND() * 100000) AS salary,    -- 1부터 100000 사이의 난수로 나이 생성
    TIMESTAMP(DATE_SUB(NOW(), INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND) AS created_at -- 최근 10년 내의 임의의 날짜와 시간 생성
FROM cte;
```

#### 성능 측정
```
SELECT u.id, u.name, u.department, u.salary, u.created_at
FROM users u
JOIN (
    SELECT department, MAX(salary) AS max_salary
    FROM users
    GROUP BY department
) d ON u.department = d.department AND u.salary = d.max_salary;
```

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_34.png?raw=true)

- 약 0.7초 정도 걸린다. 

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_35.png?raw=true)

- JOIN 문 내부에 있는 서브쿼리를 실행시킬 때 풀 테이블 스캔이 이뤄어졌음을 알 수 있다.

#### 성능 개선
- GROUP BY department는 department를 기준으로 정렬을 시킨 뒤에 MAX(salary) 값을 구하게 된다.  
  이 때, MAX(salary)를 구하기 위해 이리저리 찾아다닐 수 밖에 없다.
- 이를 해결하기 위해 (department, salary)의 멀티 컬럼 인덱스가 있으면 department를 기준으로 정렬을   
  시키는 작업을 하지 않아도 되고, 심지어 MAX(salary)도 빠르게 찾을 수 있다. 멀티 컬럼 인덱스를 생성해보자.
```
CREATE INDEX idx_department_salary ON users (department, salary);
```
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_36.png?raw=true)

- 약 0.7초에서 0.0017초 성능이 향상 되었다.

#### 실행 계획을 조회
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_37.png?raw=true)
- 실행 계획을 조회해봐도 인덱스를 잘 활용해서 데이터를 찾고 있고, 접근한 rows 자체도 훨씬 적어졌다. 

## [실습] 2023년 주문 데이터 조회하는 SQL문 튜닝하기

#### 테이블 생성
```
DROP TABLE IF EXISTS users; 
DROP TABLE IF EXISTS orders; 

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    ordered_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```


#### 더미 데이터 생성
```
-- 높은 재귀(반복) 횟수를 허용하도록 설정
-- (아래에서 생성할 더미 데이터의 개수와 맞춰서 작성하면 된다.)
SET SESSION cte_max_recursion_depth = 1000000; 

-- users 테이블에 더미 데이터 삽입
INSERT INTO users (name, created_at)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    CONCAT('User', LPAD(n, 7, '0')) AS name,  -- 'User' 다음에 7자리 숫자로 구성된 이름 생성
    TIMESTAMP(DATE_SUB(NOW(), INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND) AS created_at -- 최근 10년 내의 임의의 날짜와 시간 생성
FROM cte;

-- orders 테이블에 더미 데이터 삽입
INSERT INTO orders (ordered_at, user_id)
WITH RECURSIVE cte (n) AS
(
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM cte WHERE n < 1000000 -- 생성하고 싶은 더미 데이터의 개수
)
SELECT 
    TIMESTAMP(DATE_SUB(NOW(), INTERVAL FLOOR(RAND() * 3650) DAY) + INTERVAL FLOOR(RAND() * 86400) SECOND) AS ordered_at, -- 최근 10년 내의 임의의 날짜와 시간 생성
    FLOOR(1 + RAND() * 1000000) AS user_id    -- 1부터 1000000 사이의 난수로 급여 생성
FROM cte;
```
#### 기존 SQL문 성능 조회
```
EXPLAIN SELECT *
FROM orders
WHERE YEAR(ordered_at) = 2023
ORDER BY ordered_at
LIMIT 30;
```

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_39.png?raw=true)

#### 성능 개선

- ordered_at에 인덱스를 추가하면 풀 테이블 스캔을 막을 수 있을 것 같다.
```
CREATE INDEX idx_ordered_at ON orders (ordered_at);
```
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_40.png?raw=true)

- 0.7초로 더 느려졌다.

#### 실행계획을 살펴보면
![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_41.png?raw=true)

- 인덱스 풀 스캔을 했다. 풀 테이블 스캔 대신에 인덱스 풀 스캔을 하면 더 빨라져야 한다.
- 또한 WHERE문으로 특정 범위의 데이터만 접근하면 인덱스 풀 스캔이 아니라 인덱스 레인지 스캔이 나와야한다.

> 문제는 인덱스의 컬럼을 가공해서 사용했기 때문이다.
> 그래서 인덱스를 제대로 활용 하지 못한 것이다. 인덱스의 컬럼을 가공하지 않게 SQL문을 다시 수정해보자. 

```
SELECT *
FROM orders
WHERE ordered_at >= '2023-01-01 00:00:00' 
  AND ordered_at < '2024-01-01 00:00:00'
ORDER BY ordered_at
LIMIT 30;
```

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_42.png?raw=true)

- 0.7초에서 0.04초로 성능이 향상되었다. 

#### 실행 계획도 인덱스 레인지 스캔으로 바뀌었다.

![](https://github.com/dididiri1/TIL/blob/main/Mysql2/images/03_44.png?raw=true)

