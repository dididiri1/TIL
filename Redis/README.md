# 실전! Redis 활용

## 섹션 1. Redis 알아보기

### Redis 소개
&nbsp;&nbsp;&nbsp;&nbsp;Remote Dictionary Server  
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


### 데이터 저장/조회/삭제
#### 저장
* `$ SET lecture inflearn-redis`
#### 조회
* `$ GET lecture`
#### 삭제
* `$ DEL lecture`

## 섹션 3. 데이터 타입 알아보기

### Strings
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

| 명령어                          |         예시          | 설명                                               |
|:-----------------------------|:-------------------:|:-------------------------------------------------|
| keys *                       |      $ keys *       | 현재 저장된 키값 모두 확인(부하가 심한 명령어라 운영중인 서비스 사용 X        |
| get [key]                    |    $ GET lecture    | 지정한 key 에 해당하는 value 를 가져온다                      |
| mget [key] [key] [key] ...   | $ MGET name kangmin | 여러 개의 key 에 해당하는 value 를 한번에 가져온다                |
| ttl [key]                    |     $ TTL name      | key 의 만료 시간을 초 단위로 보여준다(-1 만료시간 없음, -2 는 데이터 없음) |
| pttl [key]                   |     $ PTTL name     | key 의 만료 시간을 밀리초 단위로 보여준다                        |
| type [key]                   |     $ TYPE name     | 해당 key의 value 타입 확인                              |

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

### Lists
- String을 Linked List로 저장 -> push / pop에 최적화 O(1)
- Queue(FIFO) / Stack(FILO) 구현에 사용

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_01.png?raw=true)

| 명령어                            |              예시              | 설명                            |
|:-------------------------------|:----------------------------:|:------------------------------|
| lpush [key] [value] [value] .. | $ LPUSH queue job1 job2 job3 | 왼쪽에서 리스트의 오른쪽에 데이터를 저장        |
| rpop [key]                     |         $ RPOP queue         | 리스트 오른쪽에서 데이터를 꺼내오고, 리스트에서 제거 |
| lpop [key]                     |         $ LPOP stack         | 리스트 왼쪽에서 데이터를 꺼내오고, 리스트에서 제거  |
| rpush [key] [value] [value] .. | $ RPUSH queue job1 job2 job3 | 오른쪽에서 리스트의 왼쪽에 데이터를 저장        |
| lrange [key]                   |     $ LRANGE queue 0 -4      | 인덱스로 범위를 지정해서 리스트 조회          |
| ltrim [key]                    |      $ LTRIM queue 0 1       | 값을 지정해서 삭제                    |

### Sets
- Unique String을 저장하는 정렬되지 않은 집합 순서X, 중복X
- Set Operation 사용 가능(e.g. intersection, union, difference

| 명령어                         |                       예시                        | 설명                                      |
|:----------------------------|:-----------------------------------------------:|:----------------------------------------|
| sadd [key] [value]          | $ SADD user:1:fruits apple banana orange orange | 셋의 key에 value 삽입                        |
| smembers [key]              |            $ SMEMBERS user:1:fruits             | 셋의 모든 멤버 출력                             |
| scard [key]                 |              $ SCARD user:1:fruits              | 셋의 카디널리티 출력 (아이템 갯수)                    |
| sismember [key]             |        $ SISMEMBER user:1:fruits banana         | 특정 아이템이 셋이 포함되어있는지 확인(없으면 0, 있으면 1 반환)  |
| sunion [key1] [key2]        |          $ SUNION user:1:fruits banana          | 주어진 키에 저장된 요소들의 합집합을 돌려줌  |
| sinter [key1] [key2]        |      $ SINTER user:1:fruits user:2:fruits       | user1과 user2가 공통으로 좋아하는 과일 출력 (교집합)     |
| sdiff [key1] [key2]         |       $ SDIFF user:1:fruits user:2:fruits       | user1은 좋아하지만 user2가 좋아하지 않는 과일 출력 (차집합) |

#### SADD
- sadd
``` log
127.0.0.1:6379> SADD user:1:fruist apple banana orange orange
(integer) 3
```
#### SMEMBERS
- smembers 명령어는 셋의 모든 멤버 출력
``` log
127.0.0.1:6379> SMEMBERS user:1:fruits
1) "orange"
2) "banana"
3) "apple"
```
#### SCARD
- scard는 셋의 카디널리티 출력 
``` log
127.0.0.1:6379> SCARD user:1:fruits
(integer) 3
```
#### SISMEMBER
- scard는 셋의 카디널리티 출력
``` log
127.0.0.1:6379> SISMEMBER user:1:fruits banana
(integer) 1
```
#### SUNION
- 주어진 키에 저장된 요소들의 합집합을 돌려줌
``` log
127.0.0.1:6379> sunion user:1:fruits user:2:fruits
1) "orange"
2) "lemon"
3) "banana"
4) "apple"
```
#### SINTER
- 주어진 키에 저장된 요소들의 교집합을 돌려줌
``` log
127.0.0.1:6379> SINTER user:1:fruits user:2:fruits
1) "apple"
```
#### SDIFF
- 주어진 키에 저장된 요소들의 차집합을 돌려줌
``` log
127.0.0.1:6379> sdiff user:1:fruits user:2:fruits
1) "orange"
2) "banana"
```