## 조인

### JOIN(조인) 
- 여러 테이블의 데이터를 사용하여 동시 출력하거나 참조 할 경우 사용 
- FROM절에 조인할 테이블 나열 
- ORACLE표준은 테이블 나열 순서 중요하지X, ANSI표준은 OUTER JOIN시 순서 중요) 
- WHERE 절에서 조인 조건을 작성(ORACLE표준) - 동일한 열 이름이 여러 테이블에 존재할 경우 열 이름 앞에 테이블 이름이나 테이블 Alias 붙임 
- N개의 테이블을 조인하려면 최소 N-1개의 조인 조건이 필요 - ORACLE 표준과 ANSI표준이 서로 다름 

### 조인 종류
#### 1. 조건의 형태에 따라
1) EQUI JOIN(등가 JOIN) : JOIN 조건이 동등 조건인 경우
2) NON EQUI JOIN: JOIN조건이 동등 조건이 아닌 경우

#### 2. 조인 결과에 따라
1) INNER JOIN : JOIN 조건에 성립하는 데이터만 출력하는 경우
2) OUTER JOIN: JOIN 조건에 성립하지 않는 데이터도 출력하는 경우  
   (LEFT/RIGHT/FULL OUTER JOIN으로 나뉨)
3. NATURAL JOIN : 조인조건 생략 시 두 테이블에 같은 이름으로 자연 연결되는 조인
4. CROSS JOIN : 조인조건 생략 시 두 테이블의 발생 가능한 모든 행을 출력하는 조인
5. SELF JOIN: 하나의 테이블을 두 번 이상 참조하여 연결하는 조인

## 표준 조인 
### 표준조인 
- ANSI 표준으로 작성되는 INNER JOIN, CROSS JOIN, NATURAL JOIN, OUTER JOIN을 말함
### INNER JOIN 
- 내부 조인이라고 하며, 조인 조건이 일치하는 행만 추출(ORACLE 조인 기본)
- ANSI 표준의 경우 FROM절에 INNER JOIN 혹은 줄여서 JOIN을 명시 
- ANSI 표준의 경우 USING이나 ON 조건절을 필수적으로 사용

### ON절  
- 조인할 양 컬럼의 컬럼명이 서로 다르더라도 사용 가능 
- - ON 조건의 괄호는 옵션(생략가능) 
- - 컬럼명이 같을 경우 테이블 이름이나 별칭을 사용하여 명확하게 지정(테이블 출처 명확히) 
- ON 조건절에서 조인조건 명시, WHERE절에서는 일반조건 명시

### 예시
```
SELECT EMP.ENAME, DEPT.DNAME
FROM EMP JOIN DEPT
ON EMP.DEPTNO = DEPT.DEPTNO
```

### USING 조건절 
- 조인할 컬럼명이 같을 경우 사용 
- Alias 나 테이블 이름 같은 접두사 붙이기 불가 
- 괄호 필수 
### 예시
```
SELECT EMP.ENAME, DEPT.DNAME
FROM EMP JOIN DEPT
USING (DEPTNO);
```

### NATURAL JOIN 
- 두 테이블 간의 동일한 이름을 가지는 모든 컬럼들에 대해 EQUI JOIN을 수행 
- USING, ON, WHERE 절에서 조건 정의 불가 
- JOIN에 사용된 컬럼들은 데이터 유형이 동일해야 하며 접두사를 사용불가
### 예시
```
SELECT *
FROM EMP NATURAL JOIN DEPT;
```

### CROSS JOIN 
- 테이블 간 JOIN 조건이 없는 경우 생성 가능한 모든 데이터들의 조합
- Cartesian product(카타시안곱) 출력 
- 양쪽 테이블 행의 수의 곱한 수의 데이터 조합 발생(m * n)

### 예시
```
SELECT *
FROM EMP CROSS JOIN DEPT;
```

### OUTER JOIN 
- INNER JOIN과 대비되는 조인방식 
- JOIN조건에서 동일한 값이 없는 행도 반환할 때 사용 
- 두 테이블 중 한쪽에 NULL.을 가지면 EQUI JOIN시 출력되지 않음 -> 이를 출력 시 OUTER JOIN사용 
- 테이블 기준 방향에 따라 LEFT OUTER JOIN, RIGHT OUTER JOIN, FULL OUTER JOIN으로 구분
### OUTER JOIN 종류
1) LEFT OUTER JOIN
- FROM절에 나열된 왼쪽 테이블에 해당하는 데이터를 읽은 후, 우측 테이블에서 JOIN 대상 읽어옴 
- 즉, 왼쪽 테이블이 기준이 되어 오른쪽 데이터를 채우는 방식 
- 우측 값에서 같은 같이 없는 경우 NULL 값으로 출력
2) RIGHT OUTER JOIN  
- LEFT OUTER JOIN의 반대 
- 즉, 오른쪽 테이블 기준으로 왼쪽 테이블 데이터를 채우는 방식
- FROM절에 테이블 순서를 변경하면 LEFT OUTER JOIN으로 수행 가능
3) FULL OUTER JOIN  
- 두 테이블 전체 기준으로 결과를 생성하여 중복 데이터는 삭제 후 리턴 
- LEFT OUTER JOIN 결과와 RIGHT OUTER JOIN 결과의 UNION 연산 리턴과 동일함 
- ORACLE 표준에는 없음 

### ORACLE 표준
```
SELECT *
FROM STUDENT S, PROFESSOR P
WHERE S.PROFNO = P.PROFNO(+)
AND S.GRADE IN (1,4);
```

### ANSI 표준
```
SELECT *
FROM STUDENT S LEFT JOIN PROFESSOR P
ON S.PRFONO = P.PRFONO
WHERE S.GRADE IN (1,4);
```
