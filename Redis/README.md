# 실전! Redis 활용

## 섹션 1. Redis 알아보기

### Redis 소개
&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: red">Re</span>mote <span style="color: red">Di</span>ctionary <span style="color: red">S</span>erver  
&nbsp;&nbsp;&nbsp;&nbsp;Open Source In-Memory Data Store written in ANSI-C

### Redis 특징
- In-Memory : 모든 데이터를 RAM에 저장(백업 / 스냅샷 제외)
- Single Threaded : 단일 thread에서 모든 task 처리
- Cluster Mode : 다중 노드에 데이터를 분산 저장하여 안전성 & 고가용성 제공
- Persistence : RDB(Redis Database) + AOF(Append only file) 통해 영속성 옵션 제공
- Pub/Sub : Pub/Sub 패턴을 지원하여 손쉬운 어플리케이션 개발(e.g 채팅, 알림 등)

### Redis 장점
- 높은 성능 : 모든 데이터를 메모리에 저장하기 때문에 매우 빠른 읽기/쓰기 속도 보장
- Data Type 지원 : Redis에서 지원하는 Data type을 잘 활용하여 다양한 기능 구현
- 클라이언트 라이브러리 : Python, Java, JavaScript 등 다양한 언어로 작성된 클라이언트 라이브러리 지원
- 다양한 사례 / 강한 커뮤니티 : Redis를 활용하여 비슷한 문제를 해결한 사례가 많고, 커뮤니티 도움 받기 쉬움


### Redus 사용 사레
- Caching 
  - 임시 비밀번호(One-Time Password) 로그인 세션(Session)
- Rate 
  - Limiter Fixed-Window / Sliding-Window Rate Limiter(비율 계산기)
- Message Broker 
  - 메시지 큐(Message Queue)
- 실시간 분석 / 계산 
  - 순위표(Rank / Leaderboard)
  - 반경 탐색(Geofencing)
  - 방문자 수 계산(Visitors Count)
- 실시간 채팅 
  - Pub/Sub 패턴

## 섹션 2. Redis 설치

### Redis 설차
- [MacOS](https://redis.io/docs/install/install-redis/install-redis-on-mac-os/)
- [Windows](https://redis.io/docs/install/install-redis/install-redis-on-windows/)
  - [Redis 다운로드](https://github.com/microsoftarchive/redis/releases)
### Redis 실행

* `$ redis-cli`
* `$ ping`
```
```

### 데이터 저장/조회/삭제
#### 저장
* `$ SET lecture inflearn-redis`
#### 조회
* `$ GET lecture`
#### 삭제
* `$ DEL lecture`

## 섹션 3. 데이터 타입 알아보기

### Strings
- String
  - 문자열, 숫자, serialized object(JSON string) 등 저장
#### 저장

| 명령어                                  |                           예시                            | 설명                                            |
|:-------------------------------------|:-------------------------------------------------------:|:----------------------------------------------|
| set [key] [value]                    |              $ SET lecture inflearn-redis               | key, value 저장                                 |
| set [key] [value]                    | $ SET ‘{“lecture”: “inflearn-redis”, “language”: “en”}’ | json sring 형태로 저장                             |
| mset [key] [value] [key] [value] ... |               $ MSET name kangmin age 10                | 여러 개의 key, value를 한번에 저장                      |
| setex [key] [scond] [value]          |                 $ SETEX name 10 kangmin                 | key, seconds, value 저장(설정한 시간 *seconds 뒤에 소멸) |
| incr [key]                           |                      $ INCR price                       | 숫자형 스트링 값을 1로 올릴때 사용                          |
| INCRBY [key] [value]                 |                     $ INCR price 10                     | 숫자형 스트링 값에 특정 값을 더할 때 사용                      |


#### 조회

| 명령어                      |              예시               | 설명                                               |
|:-------------------------|:-----------------------------:|:-------------------------------------------------|
| keys *                   |           $ keys *            | 현재 저장된 키값 모두 확인(부하가 심한 명령어라 운영중인 서비스 사용 X        |
| get [key]                |         $ GET lecture         | 지정한 key 에 해당하는 value 를 가져온다                      |
| mget key [key] [key] ... | $ MSET price 100 name kangmin | 여러 개의 key 에 해당하는 value 를 한번에 가져온다                |
| ttl [key]                |          $ TTL name           | key 의 만료 시간을 초 단위로 보여준다(-1 만료시간 없음, -2 는 데이터 없음) |
| pttl [key]               |          $ PTTL name          | key 의 만료 시간을 밀리초 단위로 보여준다                        |
| type [key]               |          $ TYPE name          | 해당 key의 value 타입 확인                              |

#### 삭제
| 명령어       |       예시       | 설명           |
|:----------|:--------------:|:-------------|
| del [key] | $ DEL lecture  | 해당 key 들을 삭제 |
| flushall  | $ flushall  | 전체 삭제        |

#### 수정
| 명령어                   |         예시          | 설명               |
|:----------------------|:-------------------:|:-----------------|
| rename [key] [newKey] | $ rename name redis | key 이름 변경        |
| expire [key] [second] |  $ expire name 10   | 해당 키 값의 만료 시간 설정 |




