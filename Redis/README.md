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
- 위치를 추가한다 (경도, 위도)
- GEOADD [key] [longitude] [latitude] [member]
``` log
127.0.0.1:6379> GEOADD seoul:station 126.923917 37.556944 hong-dae 127.027583 37.497928 gang-nam
(integer) 2
```
#### GEODIST
- 두 맴버간 거리를 반환한다.  unit : [m, km, pt, mi]
- GEODIST [key] [member1] [member2] [unit]
``` log
127.0.0.1:6379> GEODIST seoul:station hong-dae gang-nam km
"11.2561"
```

### Bitmaps
- 실제 데이터 타입은 아니고, String에 binary operation 쉽게 사용할 수 있도록 만들어놓은 인터페이스
- 최대 42억개 binary 데이터 표현 = 2^32(4,294,967,296) 

#### SETBIT
- 비트 값 추가. key: 해당 비트맵을 칭할 값, offset: 0 보다 큰 정수의 값, value: 0 또는 1의 비트 값
- SETBIT [key] [offset] [value]
``` log
127.0.0.1:6379> SETBIT user:log-in:23-01-01 123 1
(integer) 0
127.0.0.1:6379> SETBIT user:log-in:23-01-01 456 1
(integer) 0
127.0.0.1:6379> SETBIT user:log-in:23-01-02 123 1
(integer) 0
```

#### BITCOUNT
- 범위 내의 1로 설정된 bit의 개수를 반환
- SETBIT [key] [offset] [value]
``` log
127.0.0.1:6379> BITCOUNT user:log-in:23-01-01
(integer) 2
```

#### BITOP
- 두개의 공통된 비트를 확인할때 사용. bit 연산(AND, OR, XOR, NOT) 실행
- Destination Key에 비트맵으로 생성 한다.
- BITOP [operation] [destkey] [key] .. [key]
``` log
127.0.0.1:6379> BITOP AND result user:log-in:23-01-01 user:log-in:23-01-02
(integer) 58
```

#### GETBIT
- 비트 값 조회
- GETBIT [key] [offset] 
``` log
127.0.0.1:6379> GETBIT result 123
(integer) 1
127.0.0.1:6379> GETBIT result 456
(integer) 0
```

### HyperLogLog
- 집합의 cardinality를 추정할 수 있는 확률형 자료구조
- 정확성을 일부 포기하는 대신 저장공간을 효율적으로 사용(평균 에러 0.81%)
- HeyperLogLog를 이용하면 상대적으로 매우 적은 메모리로 카디널리티를 계산할수 있다.
- 다만 HeyperLogLog는 실제 값을 저장하지 않기 때문에 모든 아이템을 다시 출력하는 경우에는 활용할 수 없음

> 참고: 효율적인 unique item count 기능을 제공해주는 기능이다.  
> 예를 들어 아래와 같이 특정 사이트에 방문자수를 count 해주는 기능이 있을때  
> 정확한 방문자수의 집계를 위해선, 동일 정보를 가진 방문자는 count에서 제외해야 합니다.
> 
> 정확도를 완벽하게 알 필요는 없고 근사치만 알아도 되는 경우에 HyperLogLog를 이용하면  
> 메모리를 적게 사용하면서 카디널리티를 계산할 수 있다.

 

#### PFADD
- key의 item를 추가 한다.
- PFADD [key] [item] .. [item]
``` log
127.0.0.1:6379>  PFADD fruits apple orange grape kiwi
(integer) 1
```

#### PFCOUNT
- key의 item 갯수를 반환.
- PFCOUNT [key] [item] .. [item]
``` log
127.0.0.1:6379> PFCOUNT fruits
(integer) 4
```

## BloomFilter
- Bloom Filter는 어떠한 값이 특정 데이터 집합에 속하는지를 판단하는데 사용하는 확률적 자료구조이다.   
  주로 데이터 집합의 크기가 매우 커서 데이터가 집합에 속해있는지 판단하는데 오래걸리는 경우 Bloom Filter를 사용한다.
- HyperLogLog 처럼 정확성을 일부 포기하는 대신 저장공간을 효율적으로 사용
- element가 집합에 실제로 포함되지 않은데 포함되었다고 잘못 예측하는 경우
- 실제 값을 저장하지 않기 때문에 매우 적은 메모리 사용

Docker Desktop 설치
BloomFilter 실습 진행을 위해서는 Docker가 필요합니다.
Docker Desktop을 이용하면 Docker를 쉽게 다운 받을 수 있습니다.

- [Mac](https://docs.docker.com/desktop/install/mac-install/)
- [Windows](https://docs.docker.com/desktop/install/windows-install/)

### 컨테이너 실행(Windows PowerShell)
* `$ docker run -p 63790:6379 -d --rm redis/redis-stack-server`
* `$ docker exec -it [CONTAINER ID] redis-cli`

#### BF.MADD
- key의 item를 추가 한다.
- BF.MADD [key] [item] .. [item]
``` log
127.0.0.1:6379> BF.MADD fruits apple orange grape
1) (integer) 1
2) (integer) 1
3) (integer) 1
```

#### BF.EXISTS
- 해당 아이템이 집합에 이미 포함되어있는지 여부 확인
- BF.EXISTS [key] [item] .. [item]
``` log
127.0.0.1:6379> BF.EXISTS fruits apple
(integer) 1
127.0.0.1:6379> BF.EXISTS fruits banana
(integer) 0
```

#### BF.MEXISTS
- 해당 여러 아이템이 집합에 이미 포함되어있는지 여부 확인
- BF.MEXISTS [key] [item] .. [item]
``` log
127.0.0.1:6379> BF.MEXISTS fruits apple banana
1) (integer) 1
2) (integer) 0
```

## 섹션 4. Redis 특수 명령어

### Expiration
- 데이터를 특정시간 이후에 만료 시키는 기능
- TTL(Time To Live) 데이터가 유효한 시간(초 단위)
  - TTL60이라고 하면 해당 데이터가 저장된 이후 60초만 유효하다는 의미이다.
- 특징
  - 데이터 조회 요청시에 만료된 데이터는 조회되지 않음
  - 데이터가 만료되자마자 삭제하지 않고, 만료로 표시했다가 백그라운드에서 주기적으로 삭제됨.
> 참고: Redis에서 데이터를 저장한 이후에 만료시키지 않게 되면 해당 데이터가 계속 저장공간을  
> 차지하고 있기 때문에 데이터를 삭제하거나 만료시키는게 중요.

#### EXPIRE
- Key 만료 시간 설정
- EXPIRE [key] [second]
``` log
127.0.0.1:6379> SET greeting hello
OK
127.0.0.1:6379> TTL greeting /*-1은 데이터 만료가 설정되어 있지 않다는 의미*/
(integer) -1
127.0.0.1:6379> EXPIRE greeting 10
(integer) 1
127.0.0.1:6379> GET greeting /*만료 전**
"hello"
127.0.0.1:6379> GET greeting /*만료 후*/
(nil)
```

#### TTL 
- Key 만료 시간 확인
- TTL [key] 
``` log
127.0.0.1:6379> TTL greeting
(integer) 5
```

#### SETEX
- Key 설정 및 만료 시간 설정
- SETEX [key] [second] [member]
``` log
127.0.0.1:6379> SETEX greeting 10 hello
OK
```

## SET NX/XX
- NX
  - 해당 Key가 존재하지 않는 경우에만 SET
- XX
  - 해당 Key가 이미 존재하는 경우에만 SET
- Null Reply
  - SET이 동작하지 않은 경우 (nil) 응답

> 참고 : 만약 이런 옵션이 없다면 직접 키를 get 해서 데이터 존재 여부를 확인하고 이후 set  
> 명령어를 별도로 해야되는데 nx, xx 옵션을 활용하면 좋음

#### SET NX
- SET [key] [value] [NX|XX]
``` log
127.0.0.1:6379> SET greeting hello NX
OK
127.0.0.1:6379> SET greeting hi NX /*이미 존재하는 key라 nil 반환*/
(nil)
```

#### SET XX
- SET [key] [value] [NX|XX]
``` log
127.0.0.1:6379> SET greeting hello XX
OK
127.0.0.1:6379> SET invalid abcd XX /*존재 하지 않는 key라 nil 반환*/
(nil)
```

### Pub/Sub
- Publisher와 Subscriber가 서로 알지 못해도 통신이 가능한 메세지 패턴, decoupling 된 패턴
- Pub/Sub의 최대 장접은 두 시스템 간의 강한 커플링을 줄일 수 있다.
- **Publisher**는 Subscriber에게 직접 메시지를 보내지 않고, Channel에 Publish하게 되고  
- **Subscriber**는 관심이 있는 Channel을 필요에 따라 Subscribe하며 메시지 수신을 한다.

- vs. Stream 메시지가 보관되는 Stream과 달리 Pub/Sub은 Subscribe 하지 않을 때 발행된 메시지 수신 불가

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_03.png?raw=true)
> 참고: 창고를 관리하는 stock 서비스는 주문 상태에 따라서만 영향을 받기 때문에 Order 채널만 구독하면 되고  
> 알림을 전송하는 Notification 서비스는 Order(주문), Payment(결제), Delivery(배송) 이벤트를 추척하기 위해  
> 여러 개의 채널을 모두 구독할 수 있다.

#### SUBSCRIBE
- SUBSCRIBE [channel] .. [channel] 
``` log
127.0.0.1:6379> SUBSCRIBE ch:order ch:payment
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "ch:order"
3) (integer) 1
1) "subscribe"
2) "ch:payment"
3) (integer) 2
1) "message"
2) "ch:order"
3) "new-order"
1) "message"
2) "ch:payment"
3) "new-payment"
```

#### PUBLISH
- PUBLISH [channel] [message]
``` log
127.0.0.1:6379> PUBLISH ch:order new-order
(integer) 1
127.0.0.1:6379> PUBLISH ch:payment new-payment
(integer) 1
```

### Pipeline
- 다수의 commands를 한 번에 요청하여 네트워크 성능을 향상 시키는 기술
- 파이프 라인은 Round-Trip 횟수를 줄여서 Round-Trip Times 최소화하는 기술이며,  
  대부분 Redis 클라이언트 라이브러리에 구현이 되어 있다.
> 참고: RTT(Round-Trip Time, 왕복시간)는 네트워크 요청이 시작점에서 목적지로 갔다가 다시 시작점으로  
> 돌아오는데 걸리는 시간(밀리초)이다. 즉 RTT는 패킷 왕복시간이다.

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_04.png?raw=true)

> 정리: 구체적인 수치를 기억하기 보다는 파이프라이닝을 통해 네트워크 라운드 트립의 횟수를  
> 줄임으로써 전체적인 지연시간을 줄일 수 있다는 개념만 이해하면 된다.

### Transaction
#### Transaction
- 다수의 명령을 하나의 트랜잭션으로 처리 -> 원자성(Atomicity) 보장하는 기술이다.
- 트랜잭션은 관계형 데이터베이스에 주로 사용되는 기술이지만 레디스에서도 트랜잭션을 지원한다.
- 중간에 에러가 발생하면 모든 작업 Rollback 된다.
- 하나의 트랜잭션이 처리되는 동안 다른 클라이언트의 요청이 중간에 끼어들 수 없음
#### Atomicity
- All or Nothing / 모든 작업이 적용되거나 하나도 적용되지 않는 특성을 의미한다.
#### Transaction vs Pipeline
- Pipeline은 네트워크 퍼포먼스 향상을 위해 여러개의 명령어를 한 번에 전달하는 기술일뿐 다수의 명령어가 개별적으로 수행된다.
- Transcation은 작업의 원자성을 보장하기 위해 다수의 명령어 일괄적용되거나 모두 적용되지 않는다.
- Transaction과 Pipeline을 서로 동시에 사용할 수 도 있다.

#### MULTI
- 트랜잭션 진입, 트랜잭션을 시작하면 Redis는 이후 커맨드는 바로 실행되지 않고 queue에 쌓임
- MULTI
``` log
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> INCR foo  # 아직 증가하지는 않음 commit 안되어있는 상태.
QUEUED
127.0.0.1:6379> GET foo
(nil)
```

#### DISCARD
- 롤백, queue에 쌓여있는 명령어를 실괄적으로 폐기 또는 중단됨
- DISCARD
``` log
127.0.0.1:6379> DISCARD
OK
127.0.0.1:6379> GET foo
(nil)
```

#### EXEC
- 커밋, 정상적으로 처리되어 queue에 쌓여있는 명령어를 일괄적으로 실행함
- EXEC
``` log
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> INCR foo  # 아직 증가하지는 않음 commit 안되어있는 상태.
QUEUED
127.0.0.1:6379> EXEC   # commit 상태.
1) (integer) 1
127.0.0.1:6379> GET foo
"1"
```

## 섹션 5. 데이터 타입 활용

### One-Time Password
- 어떤 유저가 새로운 서비스에 회원가입할 때 인증번호 6자리 코드를 제공 할때 사용
- 인증을 위해 사용되는 임시 비밀번호(e.g. 6자리 랜덤 숫자)
- OTP는 일정 시간 동안만 유효하고 회원 가입 과정에서만 사용되는 데이터이기 때문에 Redis로 쉽게 구현 가능

> 참고: 휴대폰 번호에 맞게 캐시 키를 생성한 뒤 set 명령어를 통해 임시 코드 값을 저장하고 유효한 시간만큼  
> TTL을 설정한다.

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_05.png?raw=true)

### Distributed Lock
- 분산락이란 다수의 프로세스에서 동일한 자원을 접근할 때 동시성 문제를 해결하기 위해 사용되는 것.
- 분산 환경의 다수의 프로세스에서 동일한 자원에 접근할 때, 동시성 문제 해결


![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_07.png?raw=true)
> 참고: 예를 들어 은행 시스템에서 사용자의 잔고를 변경할 때 관계형 데이터베이스에 레코드 락을 걸어 해결할 수 있지만  
> Redis를 이용해서도 분산락을 구현하여 동시에 여러 프로세스에서 데이터를 변경하는 문제를 막을 수도 있다.  
> DB는 레코드가 존재하지 않는 경우에는 락을 사용할 수 없고 락으로 인한 DB 성능 저하도 발생할 수 있기 때문에  
> 필요에 맞게 분산락과 레코드 락을 사용하는 것이 필요하다.

