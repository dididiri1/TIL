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

#### SADD
- 셋의 key에 value 삽입
- SADD [key] [value]
``` log
127.0.0.1:6379> SADD user:1:fruist apple banana orange orange
(integer) 3
```
#### SMEMBERS
- 셋의 모든 멤버 출력
- SMEMBERS [key]
``` log
127.0.0.1:6379> SMEMBERS user:1:fruits
1) "orange"
2) "banana"
3) "apple"
```
#### SCARD
- 셋의 카디널리티 출력 
- SCARD [key]
``` log
127.0.0.1:6379> SCARD user:1:fruits
(integer) 3
```
#### SISMEMBER
- 특정 아이템이 셋이 포함되어있는지 확인
- SISMEMBER [key]
``` log
127.0.0.1:6379> SISMEMBER user:1:fruits banana
(integer) 1
```
#### SUNION
- 주어진 키에 저장된 요소들의 합집합을 돌려줌
- SUNION [key] .. [key]
``` log
127.0.0.1:6379> sunion user:1:fruits user:2:fruits
1) "orange"
2) "lemon"
3) "banana"
4) "apple"
```
#### SINTER
- 주어진 키에 저장된 요소들의 교집합을 돌려줌
- SINTER [key] .. [key]
``` log
127.0.0.1:6379> SINTER user:1:fruits user:2:fruits
1) "apple"
```
#### SDIFF
- 주어진 키에 저장된 요소들의 차집합을 돌려줌
- SDIFF [key] .. [key]
``` log
127.0.0.1:6379> sdiff user:1:fruits user:2:fruits
1) "orange"
2) "banana"
```

### Hashes
- filed-value 구조를 갖는 데이터 타입
- 다양한 속성을 갖는 객체의 데이터를 저장할 때 유용

#### HSET
- 해시의 key의 filed에 value 저장하고 필드 값이 존재하면 덮어쓴다
- HSET [key] [field] [value]
``` log
127.0.0.1:6379> HSET lecutre name inflean-redis
(integer) 1
```

#### HMSET
- 해시의 key의 **여러 filed**에 value 저장하고 필드 값이 존재하면 덮어쓴다
- HMSET [key] [field] [value] .. [value]
``` log
HMSET lecutre name inflean-redis price 100
OK
```

#### HGET
- 해시의 key의 field값을 반환한다. 
- HGET [key] [field]
``` log
127.0.0.1:6379> HGET lecutre name
"inflean-redis"
```

#### HMGET
- 해시의 key의 하나 이상의 field값을 반환한다.
- HMGET [key] [field] .. [field]
``` log
127.0.0.1:6379> HMGET lecutre name price
1) "inflean-redis"
2) "100"
```

#### HKEYS
- 해시의 key의 모든 field의 name을 반환한다.
- HKEYS [key] [field] .. [field]
``` log
127.0.0.1:6379> HKEYS lecutre
1) "name"
2) "price"
```

#### HVALS
- 해시의 key의 모든 value를 반환한다.
- HVALS [key] [field] .. [field]
``` log
127.0.0.1:6379> HVALS lecutre
1) "inflean-redis"
2) "100"
```

#### HGETALL
- 해시의 key의 모든 field와 value값을 반환한다.
- HGETALL [key]
``` log
127.0.0.1:6379> HGETALL lecutre
1) "name"
2) "inflean-redis"
3) "price"
4) "100"
```

#### HEXISTS
- 해시의 key의 field가 있는지 확인. 있으면 1, 없으면 0을 리턴한다.
- HEXISTS [key] [field]
``` log
127.0.0.1:6379> HEXISTS lecutre name
(integer) 1
```

#### HDEL
- 해시의 key의 field 삭제
- HDEL [key] [field]
``` log
127.0.0.1:6379> HDEL lecutre price
(integer) 1
```

#### DEL
- 해시의 key를 삭제
- DEL [key]
``` log
127.0.0.1:6379> DEL lecutre
(integer) 1
```

### Sorted Sets
- Set과 Hash가 혼합된 타입이다.
- 하나의 Key에 Socre와 Value로 구성이된다.
- Unique string을 연관된 score를 통해 정렬된 집합(Set의 기능 + 추가로 score 속성 저장)
- 내부적으로 Skip List + Hash Table로 이루어져 있고, score 값에 따라 정렬 유지
- 만약 score가 동일하면 lexicographically(사전 편찬 순) 정렬됨
- SortedSet은 줄여서 ZSet이라고 불린다

#### ZADD
- key의 member 추가하기
- ZADD [key] [score] [member] .. [score] [member]
``` log
127.0.0.1:6379> ZADD points 10 TeamA 10 TeamB 50 TeamC
(integer) 3
```

#### ZCARD
- key의 member 갯수 반환한다.
- ZCARD [key] 
``` log
127.0.0.1:6379> ZCARD points
(integer) 3
``` 

#### ZSCORE
- member의 score 값 조회하기
- ZSCORE [key] [member]
``` log
127.0.0.1:6379> ZSCORE points TeamA
"10"
```

#### ZREVRAGNE
- key에 member들을 score의 역정렬로 반환한다.
- ZREVRAGNE [key] [START INDEX] [END IDEX] [WITHSCORE]
``` log
127.0.0.1:6379> ZRANGE points 0 -1
1) "TeamA"
2) "TeamB"
127.0.0.1:6379> ZRANGE points 0 -1 WITHSCORES
1) "TeamA"
2) "10"
3) "TeamB"
4) "10"
5) "TeamC"
6) "50"
```

#### ZRANK
- key에 member의 score 순위를 반환한다.
- ZRANK  [key] [member]
``` log
127.0.0.1:6379> ZRANK points TeamA
(integer) 0
127.0.0.1:6379> ZRANK points TeamB
(integer) 1
127.0.0.1:6379> ZRANK points TeamC
(integer) 2
127.0.0.1:6379>
```

### Streams
- Stream은 Redis 5.0에 새롭게 도입된 데이터 유형
- 로그 데이터 구조를 모델링 함
- append-only log에 consumer groups과 같은 기능을 더한 자료 구조
- Consumer groups 개념은 메시징 시스템 Kafka에서 처음 도입됐지만 Redis의 consumer group은 완전히 다름.
- 추가 기능 
  - unique id를 통해 하나의 entry를 읽을 때, O(1) 시간 복잡도
  - Consumer Group을 통해 분산 시스템에서 다수의 consumer가 event 처리

> 참고 : append-only log란 데이터베이스나 분산 시스템에 주로 사용되는 데이터저장 알고리즘으로  
> 데이터가 수정되거나 삭제되지 않고 항상 추가만 되는 구조를 갖는다.

#### XADD
- 스트림에 데이터 추가
- XADD [key] [messsage-id] [filed] [value] .. [filed] [value]
``` log
127.0.0.1:6379> XADD events * action like user_id 1 product_id 1
"1703048194616-0"
127.0.0.1:6379> XADD events * action like user_id 2 product_id 1
"1703048200069-0"
```

#### XRANGE
- 스트림 이벤트 출력
- XRANGE [key] [start-id] [end-id]
``` log
127.0.0.1:6379> XRANGE events - +
1) 1) "1703048194616-0"
   2) 1) "action"
      2) "like"
      3) "user_id"
      4) "1"
      5) "product_id"
      6) "1"
2) 1) "1703048200069-0"
   2) 1) "action"
      2) "like"
      3) "user_id"
      4) "2"
      5) "product_id"
      6) "1"
```

#### XDEL
- 스트림 이벤트 삭제
- XDEL [key] [event-id] [end-id]
``` log
127.0.0.1:6379> XDEL events 1703048194616-0
(integer) 1
```

### Geospatials
- 좌표를 저장하고, 검색하는 데이터 타입
- 거리 계산, 범위 탐색 등 지원

> 예를 들어 서울에 있는 지하철 역들의 좌표를 알고 있을때 GEOADD를 이용하여 강남역과  
> 홍대역의 좌표를 저장하면 GEODIST 명령어를 이용하여 두 역의 거리를 구할 수 있다.

#### GEOADD
- 지리공간 인덱스 위치를 추가한다 (경도, 위도)
- GEOADD [key] [longitude] [latitude] [member]
``` log
127.0.0.1:6379> GEOADD seoul:station 126.923917 37.556944 hong-dae 127.027583 37.497928 gang-nam
(integer) 2
```
#### GEODIST
- -- geospatial 인덱스에서 두 맴버간 거리를 반환한다.  unit : [m, km, pt, mi]
- GEODIST [key] [member1] [member2] [unit]
``` log
127.0.0.1:6379> GEODIST seoul:station hong-dae gang-nam km
"11.2561"
```
