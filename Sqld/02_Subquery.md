# 서브쿼리

## 서브쿼리  
- 하나의 SQL문안에 포함되어 있는 또 다른 SQL문을 말함 - 반드시 괄호로 묶어야 함  
   ex) SELECT 안에 SELECT문, INSERT, UPDATE, DELETE 안의 SELECT문 

## 서브쿼리 종류
### 1. 스칼라 서브쿼리
- SELECT 에 사용하는 서브 쿼리
- 서브쿼리 결과를 마치 **하나의 컬럼처럼 사용하기 위해 주로 사용**

### 2. 인라인뷰
- FROM 절에 사용하는 서브쿼리
- **서브쿼리 결과를 테이블 처럼 사용하기 위해 주로 사용**

### 3. WHERE절 서브쿼리
- 가장 일반적인 서브쿼리
- 비교 상수 자리에 **값을 전달하기 위한 모적으로 주요 사용**

## WHERE 절 서브쿼리 종류
### 1. 단일행 서브 쿼리
- **서브쿼리 결과가 1개의 행이 리턴**되는 형태 
- 단일행 서브쿼리 연산자 종류

| 연산자 |   의미   |
|:----|:------:|
| =   |   같다   |
| <>  | 같지 않다  |
| >   |   크다   |
| >=  | 크거나 같다 |
| <   |   작다   |
| <=  | 작거나 같다 |


### 에시
```
SELECT EMPNO, ENAME, SAL
FROM EMP
WHERE SAL > (SELECT AVG(SAL)
             FROM EMP);
```

### 2. 다중행 서브쿼리
- **서브쿼리 결과가 여러 행이 리턴되는 형태**
- ‘=’, ‘>’, ‘<’와 같은 비교 연산자 사용불가(여러 값이랑 비교할 수 없는 연산자들) 
- - 서브쿼리 결과를 하나로 요약하거나 다중행 서브쿼리 연산자를 사용

| 연산자   |    의미    |
|:------|:--------:|
| IN    | 같은 값을 찾음 |
| > ANY | 최소값을 반환함 |
| < ANY | 최대값을 반환함 |
| < ALL | 최소값을 반환함 |
| > ALL | 최대값을 반환함 |

### 예제) ALL과 ANY 비교
> > ALL(2000, 3000) : 최대값(3000)보다 큰 행들 반환  
> < ALL(2000, 3000) : 최소값(2000)보다 작은 행들 반환  
> > ANY(2000, 3000) : 최소값(2000)보다 큰 행들 반환  
> < ANY(2000, 3000) : 최대값(3000)보다 작은 행들 반환

### 3. 다중컬럼 서브쿼리  
- 서브쿼리 결과가 여러 컬럼이 리턴되는 형태 
- 메인쿼리와의 비교 컬럼이 2개 이상 
- 대소 비교 전달 불가(두 값을 동시에 묶어 대소비교 할 수 없음)

### 예시 (메인쿼리에 비교 대상으로 서브쿼리 결과 전달)
```
SELECT EMPNO, ENAME, SAL, DEPTNO
FROM EMP
WHERE (DEPTNO, SAL) IN (SELECT DEPTNO, MAX(SAL)
                        FROM EMP
                        GROUP BY DEPTNO);
```

### 4. 상호연관 서브쿼리
- 메인쿼리와 서브쿼리의 비교를 수행하는 형태 
- 비교할 집단이나 조건은 서브쿼리에 명시(메인쿼리절에는 서브쿼리 컬럼이 정의되지 않았기 때문에 에러 발생

### 예시 ( 대소 비교할 컬럼을 메인쿼리에, 일치 조건을 서브쿼리에 전달)
```
SELECT EMPNO, ENAME, SAL, DEPTNO
FROM EMP E1
WHERE SAL > (SELECT AVG(SAL)
             FROM EMP E2
             WHERE E1.DEPTNO = E2.DEPTNO
             GROUP BY DEPTNO);
```

## 인라인뷰(Inline View)

- **쿼리 안의 뷰의 형태로 테이블처럼 조회할 데이터를 정의하기 위해 사용** 
- 테이블명이 존재하지 않기 때문에 다른 테이블과 조인 시 반드시 테이블 별칭 명시(단독으로 사용하는 경우 불필요)  
- WHERE절 서브쿼리와 다르게 **서브쿼리 결과를 메인 쿼리의 어느 절에서도 사용할 수 있음** 
- 인라인뷰의 결과와 메인쿼리 테이블과 조인할 목적으로 주로 사용 
- 모든 연산자 사용 가능

## 스칼라 서브쿼리 
- SELECT절에 사용하는 쿼리로, 마치 하나의 컬럼처럼 표현하기 위해 사용 (단 하나의 출력 대상만 표현 가능) 
- 각 행마다 스칼라 서브쿼리 결과가 하나여야 함(단일행 서브쿼리 형태) 
- 조인의 대체 연산 

# 집합 연산자

## 집합 연산자 
- SELECT문 결과를 하나의 집합으로 간주, 그 집합에 대한 합집합, 교집합, 차집합 연산 
- SELECT문과 SELECT문 사이에 집합 연산자 정의 
- 두 집합의 컬럼이 동일하게 구성되어야 함(각 컬럼의 데이터 타입과 순서 일치 필요) 
- 전체 집합의 데이터타입과 컬럼명은 첫번째 집합에 의해 결정됨
- 
## 합집합 
- 두 집합의 총 합(전체) 출력 
- UNION과 UNION ALL 사용 가능
### 1. UNION 
- 중복된 데이터는 한 번만 출력 
- 중복된 데이터를 제거하기 위해 내부적으로 정렬 수행 
- 중복된 데이터가 없을경우는 UNION 사용 대신 UNION ALL 사용(불필요한 정렬 발생할 수 있으므로)
### 2. UNION ALL 
- 중복된 데이터도 전체 출력

## 교집합 
- 두 집합 사이에 INTERSECT 
- - 두집합의 교집합(공통으로 있는 행) 출력

## 차집합 
- 두 집합 사이에 MINUS 전달 
- 두 집합의 차집합(한 쪽 집합에만 존재하는 행) 출력 
- A-B와 B-A는 다르므로 집합의 순서 주의

# 그룹 함수 
## 그룹함수 
- 숫자함수 중 여러값을 전달하여 하나의 요약값을 출력하는 다중행 함수 
- 수학/통계 함수들(기술통계 함수) 
- GROUP BY절에 의해 그룹별 연산 결과를 리턴 함 
- 반드시 한 컬럼만 전달 - NULL은 무시하고 연산
- 
### COUNT 
- 행의 수를 세는 함수 
- 대상 컬럼은 * 또는 단 하나의 컬럼만 전달 가능(* 사용 시 모든 컬럼의 값이 널일 때만 COUNT 제외) 
- **문자, 숫자, 날짜 컬럼 모두 전달 가능** 
- 행의 수를 세는 경우 NOT NULL 컬럼을 찾아 세는 것이 좋음(PK 컬럼) 

```
SELECT COUNT(*),
       COUNT(EMPNO)
FROM EMP;
```

### SUM
- 총 합 출력 
- 숫자 컬럼만 전달 가능

```
SELECT SUM(*)
FROM EMP;
```

### AVG 
- 평균 출력 
- 숫자 컬럼만 전달 가능 
- **NULL을 제외한 대상의 평균을 리턴하므로 전체 대상 평균 연산 시 주의 **

```
SELECT AVG(*)
FROM EMP;
```

### MIN / MAX 
- 최대, 최소 출력 
- **날짜,숫자,문자 모두 가능(오름차순 순서대로 최소, 최대 출력)**
```
SELECT MIN(SAL), MAX(SAL)
FROM EMP;
```

### VARIANCE / STDDEV 
- 평균과 표준편차
- 표준편차는 분산의 루트값 
```
SELECT VARIANCE(SAL), STDDEV(SAL)
FROM EMP;
```

### GROUP BY FUNCTION 
- GROUP BY절에 사용하는 함수 
- 여러 GROUP BY결과를 동시에 출력(합집합)하는 기능 
- 그룹핑 할 그룹을 정의(전체 소계 등) 

```
SELECT DEPTNO, SUM(SAL)
FROM EMP
GROUP BY DEPTNO;
```

## 1. GROUPING SETS
- **A별, B별 그룹 연산 결과 출력** 
- 나열 순서 중요하지 X 
- 기본 출력에 전체 총계는 출력되지 X 
- NULL 혹은 () 사용하여 전체 총 합 출력 가능

```
SELECT DEPTNO, JOB, SUM(SAL)
FROM EMP
GROUP BY GROUPING SETS(DEPTNO, JOB;
```
>  GROUPING SETS에 나열한 대상에 대해 각 GROUP BY의 결과를 출력해 줌

## 2. ROLLUP
- **A별, (A,B)별, 전체 그룹 연산 결과 출력** 
- 나열 대상의 순서가 중요함 
- 기본적으로 전체 총 계가 출력됨
```
SELECT DEPTNO, JOB, SUM(SAL)
FROM EMP
GROUP BY ROLLUP(DEPTNO, JOB;
```

## 3. CUBE
- **A별, B별, (A,B)별, 전체 그룹 연산 결과 출력됨** 
- 그룹으로 묶을 대상의 나열 순서 중요하지 않음 
- 기본적으로 전체 총 계가 출력
```
SELECT DEPTNO, JOB, SUM(SAL)
FROM EMP
GROUP BY CUBE(DEPTNO, JOB;
```

# 윈도우 함수
## WINDOW FUNCTION 
- 서로 다른 행의 비교나 연산을 위해 만든 함수 
- GROUP BY를 쓰지 않고 그룹 연산 가능 
- LAG, LEAD, SUM, AVG, MIN, MAX, COUNT, RANK

```
SELECT 윈도우함수([대상]) OVER([PARTITION BY 칼럼]
                             [ORDER BY 컬럼 ASC|DESC]
                             [ROW|RANGE BETWEEN A AND B]);
```
### ** PARTITION BY 절 : 출력할 총 데이터 수 변화 없이 그룹연산 수행할 GROUP BY 컬럼

### ** ORDER BY절 - RANK의 경우 필수(정렬 컬럼 및 정렬 순서에 따라 순위 변화) - SUM, AVG, MIN, MAX, COUNT 등은 누적값 출력 시 사용

### ** ROWS|RANGE BETWEEN A AND B - 연산 범위 설정 - ORDER BY절 필수

## 그룹 함수의 형태 
- SUM, COUNT, AVG, MIN, MAX 등 
- OVER절을 사용하여 윈도우 함수로 사용 가능 
- 반드시 연산할 대상을 그룹함수의 입력값으로 전달 
```
SELECT SUM([대상]) OVER([PARTITION BY 칼럼]
                             [ORDER BY 컬럼 ASC|DESC]
                             [ROW|RANGE BETWEEN A AND B]);
```

## 1. ROWS, RANGE 차이
- ROWS : 값이 같더라도 각 행씩 연산 
- RANGE : 같은 값의 경우 하나의 RANGE로 묶어서 동시 연산(DEFAULT)

## 2. BETWEEN A AND B
- 시작점 정의 
  - CURRENT ROW : 현재행부터 
  - UNBOUNDED PRECEDING : 처음부터(DEFAULT) 
  - N PRECEDING : N 이전부터
- 마지막 시점 정의
  - CURRENT ROW : 현재행까지(DEFAULT) 
  - UNBOUNDED FOLLOWING : 마지막까지 
  - N FOLLOWING : N 이후까지

### ROWS 범위 설정 시 : 각 행 별로 연산 수행
```
SELECT R.*,
       SUM(SAL) OVER(ORDER BY SAL
                     ROWS BETWEEN UNBOUNDED PRECEDING
                     AND CURRENT ROW) AS RESULT1
FROM EMP;                     
```

### BETWEEN A AND B 수정 시
```
SELECT R.*,
       SUM(SAL) OVER(ORDER BY SAL
                     ROWS BETWEEN UNBOUNDED PRECEDING
                     AND 1 FOLLOWING) AS RESULT1
FROM EMP;                     
```

## 순위 관련 함수

### 1. RANK
- 전체 중/특정 그룹 중 값의 순위 확인 
- ORDER BY절 필수 
- 순위를 구할 대상을 ORDER BY절에 명시(여러 개 나열 가능) 
- 그룹 내 순위 구할 시 PARTITION BY 절 사용

### 문법
```
SELECT RANK() OVER([PARTITION BY 컬럼]
                    ORDER BY 컬럼 ASC|DESC);                  
```

### 각 직원 이름, 부서번호, 급여, 부서별 급여 순위(큰순서대로)
```
SELECT ENAME, DEPTNO, SAL
       RANK() OVER(PARTITION BY DEPTNO
                    ORDER BY SAL ADESC) AS RANK1
FROM EMP:                                
```

### 2. DENSE_RANK 
- 누적순위 
- 값이 같을 때 동일한 순위 부여 후 다음 순위가 바로 이어지는 순위 부여 방식
ex) 1 등이 5명이더라도 그 다음 순위가 2등

### 3. ROW_NUMBER 
- 연속된 행 번호 
- 동일한 순위를 인정하지 않고 단순히 순서대로 나열한대로의 순서 값 리턴

### LAG, LEAD  
- 행 순서대로 각각 이전 값(LAG), 이후 값(LEAD) 가져오기 
- ORDER BY절 필수 

### 예제) EMP에서 바로 이전 입사자와 급여 비교 
```
SELECT ENAME, DEPTNO, SAL
       LAG(SAL) OVER(PARTITION BY DEPTNO) AS 바로직전상사급여
FROM EMP:                                
```

### FIRST_VALUE, LAST_VALUE 
- 정렬 순서대로 정해진 **범위에서의 처음 값, 마지막 값 출력** 
- 순서와 범위 정의에 따라 최솟값과 최댓값 리턴 가능 
- PARTITION BY, ORDER BY절 생략 가능

### NTILE 
- 행을 특정 컬럼 순서에 따라 정해진 수의 그룹으로 나누기 위함 함수 
- 그룹 번호가 리턴됨 - ORDER BY 필수 
- PARTITION BY를 사용하여 특정 그룹을 또 원하는 수 만큼 그룹 분리 가능

### 예제) NTILE을 사용한 그룹 분리
```
SELECT ENAME, SAL, DEPTNO
       NTILE(2) OVER (ORDER BY SAL) AS GROUP_NUMBER
FROM EMP:                         
```

## 비율관련 함수
### 1. RATIO_TO_REPORT 
- 각 값의 비율 리턴(전체 비율 또는 특정 그룹 내 비율 가능) 
- ORDER BY 사용 불가

### 문법
```
SELECT RATIO_TO_REPORT(대상) OVER([PARTITION BY 컬럼])                      
```

### 2. CUME_DIST : 누적비율 
- 각 값의 누적 비율 리턴(전체 비율 또는 특정 그룹 내 비율 가능) 
- ORDER BY를 사용하여 누적비율을 구하는 순서 정할 수 있음 
- ORDER BY 필수 - 특정 값의 비율이 아닌 행의 비율
- 특정 값의 비율이 아닌 행의 비율을 의미함

### 문법
```
SELECT CUME_DIST() OVER([PARTITION BY 컬럼]
                                  ORDER BY 컬럼 ASC|DESC)                      
```

### 3. PERCENT_RANK 
- PERCENTILE(분위수) 출력 
- 전체 COUNT중 상대적 위치 출력(0~1 범위 내) 
- ORDERY BY 필수 
- ### 문법
```
SELECT PERCENT_RANK() OVER([PARTITION BY 컬럼]
                                  ORDER BY 컬럼 ASC|DESC)                      
```

# TOP N QUERY

## TOP N QUERY 
- 페이징 처리를 효과적으로 수행하기 위해 사용 
- 전체 결과에서 특정 N개 추출  
  예) 성적 상위자 3명

##  TOP-N 행 추출 방법
#### 1. ROWNUM
#### 2. RANK
#### 3. FETCH

###  ROWNUM
- 출력된 데이터 기준으로 행 번호 부여 
- 절대적인 행 번호가 아닌 가상의 번호이므로 특정 행을 지정할 수 없음(=연산 붙가) 
- 첫번재 행이 증가한 이후 할당되므로 '>' 연산 사용 불가(0은

### 예제) ROWNUM을 출력 형태
```
SELECT ROWNUM, EMP.*
FROM EMP 
WHERE SAL >= 1000;                    
```

### ROWNUM 잘못된 사용
```
SELECT EMPNO, ENAME, DEPTNO, SAL
FROM EMP 
WHERE ROWNUM > 5;                        
```

### 예제) ROWNUM 올바른 사용
```
SELECT EMPNO, ENAME, DEPTNO, SAL
FROM EMP 
WHERE ROWNUM <= 5;                    
```
> EQAUL 비교 시 작다(<)와 함께 사용하면 1부터 순서대로 뽑을 수 있기 때문에 출력 가능함  
> 정렬 순서에 따라 출력되는 ROWNUM이 달라짐

### FETCH절 
- 출력될 행의 수를 제한하는 절 
- ORACLE 12C 이상부터 제공(이전버전에는 ROWNUM 주로 사용) 
- SQL-Linux 사용 가능 - ORDER BY절 뒤에 사용(내부 파싱 순서도 ORDER BY 뒤)

### 문법
```
SELECT *
FROM EMP 
WHERE 
GROUP BY 
HAVING
ORDER BY
OFFSET N {ROW|ORWS}
FETCH {FIRST|NEXT} N {ROW|ORWS} ONLY               
```
- OFFSET : 건너뛸 행의 수 ex) 성적 높은순 1등 제외, 나머지 3명 
- N : 출력할 행의 수 - FETCH : 출력할 행의 수를 전달하는 구문 - FIRST : OFFSET을 쓰지 않았을 때 처음부터 N 행 출력 명령 
- NEXT : OFFSET을 사용했을 경우 제외한 행 다음부터 N 행 출력 명령 
- ROW | ROWS : 행의 수에 따라 하나일 경우 단수, 여러값이면 복수형(특별히 구분하지 않아도 됨)

## 계층형 질의

### 계층형 질의 
- 하나의 테이블 내 각 행끼리 관계를 가질 때, 연결고리를 통해 행과 행 사이의 계층(depth)을 표현하는 기법
- **PRIOR의 위치에 따라 연결하는 데이터가 달라짐**

### 문법
```
SELECT *
FROM EMP 
START WITH 시작조건          -- 시작점을 지정하는 조건 전달
CONNECT BY PRIOR 연결조건;   -- 시작점 기준으로 하위 계급을 찾아가는 조건           
```
> ** START WITH : 데이터를 출력할 시작 지정하는 조건  
> ** CONNECT BY PRIOR : 행을 이어나갈 조건 

### 예시) DEPT2 테이블에 대해 각 부서의 레벨을 출력(최상위 부서가 1 레벨)
```
SELECT D.* LEVEL
FROM DEPT D
START WITH PDEPT IS NULL
CONNECT BY PRIOR DCODE = PDEPT;          
```

### 예시) 연결조건에 추가 조건이 붙는 경우
```
SELECT D.* LEVEL
FROM DEPT D
START WITH PDEPT IS NULL
CONNECT BY PRIOR DCODE = PDEPT AND AREA = '서울지사';          
```

### 계층형 질의 가상 컬럼
#### 1) LEVEL : 각 DEPTH를 표현(시작점부터 1)
#### 2) CONNECT_BY_ISLEAF : LEAF NODE(최하위노드) 여부(참:1, 거짓:0)

### 계층형 질의 가상 함수
#### 1) CONNECT_BY_ROOT 컬럼명 : 루트노드의 해당 컬럼명의 값이 출력
#### 2) SYS_CONNECT_BY_PATH(컬럼, 구분자) : 이어지는 경로 출력
#### 3) ORDER SIBLINGS BY 컬럼 : 같은 LEVEL일 경우 정렬 수행

### 예제) 계층형 가상컬럼, 함수 결과
```
SELECT D.* LEVEL,
       LPAD('',(LEVEL-1)*4,'')||DNAME AS 학과명,
       CONNECT_BY_ROOT DNAME,
       SYS_CONNECT_BY_PATH(DNAME, '_')
FROM DEPT D
START WITH PDEPT IS NULL
CONNECT BY PRIOR DCODE = PDEPT
ORDER SIBLINGS BY DNAME;    
```

## PIVOT 과 UNPIVOT
#### 1) LONG DATA(Tidy data) 
- 하나의 속성이 하나의 컬럼으로 정의되어 값들이 여러 행으로 쌓이는 구조 
- RDBMS의 테이블 설계 방식 
- 다른 테이블과의 조인 연산이 가능한 구조
#### 2) WIDE DATA(Cross table)
   ** WIDE DATA - 행과 컬럼에 유의미한 정보 전달을 목적으로 작성하는 교차표 
- 하나의 속성값이 여러 컬럼으로 분리되어 표현 
- RDBMS에서 WIDE 형식으로 테이블 설계시 값이 추가될 때 마다 컬럼이 추가돼야 하므로 비효울적! 
- 다른 테이블과의 조인 연산이 불가함 
- 주로 데이터를 요약할 목적으로 사용 

## 정규 표현식
### 정규 표현식
- 문자열의 공통된 규칙을 보다 일반화 하여 표현하는 방법 
- 정규 표현식 사용 가능한 문자함수 제공(regexp_replace, regexp_substr, regexp_instr, ....)
  ex) 숫자를 포함하는, 숫자로 시작하는 4자리, 두번째 자리가 A인 5글자 

### REGEXP_REPLACE - 정규식 표현을 사용한 문자열 치환 가능
** 문법
(대상, 찾을문자열, [바꿀문자열], [검색위치], [발견횟수], [옵션])
#### 1. 특징 
- 바꿀문자열 생략 시 문자열 삭제 
- 검색위치 생략 시 1 
- 발견횟수 생략 시 0(모든)
#### 2. 옵션 
- c : 대소를 구분하여 검색 
- i : 대소를 구분하지 않고 검색 
- m : 패턴을 다중라인으로 선언 가능

### 예제) ID에서 숫자 삭제
```
SELECT ID,
       REGEXP_REPLACE(ID, '\d',''),
       REGEXP_REPLACE(ID, '[[:digit:]]','')
FROM EMP       
```

### 예제) ID에서 특수기호 삭제
```
SELECT ID,
       REGEXP_REPLACE(ID, '\w',''),
       REGEXP_REPLACE(ID, '\w|_',''),
       REGEXP_REPLACE(ID, '[[:punct:]]',''),
FROM EMP       
```

### REGEXP_SUBSTR 
- 정규식 표현식을 사용한 문자열 추출 
- 옵션은 REGEXP_SUBSTR과
#### ** 문법
#### REGEXP_SUBSTR(대상, 패턴, [검색위치], [발견횟수], [옵션], [추출그룹])
#### ** 특징  
- 검색위치 생략 시 1 
- 발견횟수 생략 시 1 
- 추출그룹은 서브패턴을 추출 시 그 중 추출할 서브패턴 번호  
  예제) 전화번호를 분리하여 지역번호 추출 
```
SELECT TEL,
       REGEXP_SUBSTR(TEL,                    -- 원본
                     '(\d+)\)(\d+)-(\d+)',   -- 패턴(서브패턴과함께표현)
                     1,                      -- 시작위치
                     1,                      -- 발견횟수
                     null,                   -- 옵션
                     1) AS 지역번호           -- 추출할 서브패턴(그룹)번호
FROM EMP       
```

### Reference
- [SQLD - 홍쌤의 데이터 랩](https://www.youtube.com/@hongdatalab)