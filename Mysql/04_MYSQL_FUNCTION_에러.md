# MySQL 함수 생성 시 log_bin_trust_function_creators 관련 이슈 및 해결 방법 정리

1. 이슈 상황
- MySQL Workbench Export 덤프 사용시 function 추출 안되는 형상 발생
- MySQL에서 함수(FUNCTION)를 생성할 때 다음과 같은 에러가 발생할 수 있음
```
Error Code: 1418
This function has none of DETERMINISTIC, NO SQL, or READS SQL DATA in its declaration and binary logging is enabled
```

- MySQL 서버에서 binary logging이 켜져 있음
- 함수 생성 시 DETERMINISTIC / SQL 데이터 읽기 속성을 지정하지 않음
- log_bin_trust_function_creators 설정이 OFF(0) 상태임

## 방법 1: 서버 설정 변경 (운영 서버에서는 주의)
- 1 → ON → 함수 생성 시 DETERMINISTIC/READS SQL DATA 없이도 허용
```
-- 일시적 변경 (서버 재시작 시 원래 값으로 복구)
SET GLOBAL log_bin_trust_function_creators = 1;
```
- 0 → OFF → 안전하게 명시하지 않으면 Error 1418 발생
```
SET GLOBAL log_bin_trust_function_creators = 0;
```

### 확인
```
log_bin_trust_function_creators	ON

```
![](https://github.com/dididiri1/TIL/blob/main/Mysql/images/04_01.png?raw=true)

