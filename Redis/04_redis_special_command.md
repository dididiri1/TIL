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

### Reference
- [인프런 | 실전! Redis 활용](https://inf.run/7ctks)