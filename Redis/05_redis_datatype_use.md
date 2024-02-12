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

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_06.png?raw=true)

### Rate Limiter
#### Rate Limiter
- 시스템 안정성/보안을 위해 요청의 수를 제한하는 기술이다.
- IP-Based, User-Based, Application-Based, etc.
- 예를 들어 특정 IP별로 초당 요청할 수 있는 요청수를 제한한다거나 특정 유저별로 횟수를 제한한다거나  
  특정 어플리케이션 별로 요청을 제한하는 식의 방법이 있다.

#### Fixed-window Rate Limiting
- 고정된 시간(e.g. 1분) 안에 요청 수를 제한하는 방법
- Fixed-window 방식의 특징은 몇 분인지에 따라 요청수가 제한되기 때문에 만약 11분이 되는 경우 요청 횟수를  
  처음부터 다시 사용할 수 있게 된다.

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_08.png?raw=true)

### SNS Activity Feed
#### 활동 피드(Activity Feed)
- 활동 피드란 사용자 또는 시스템과 관련된 활동이나 업데이트를 시간순으로 정렬하여 보여주는 기능.
#### Fan-Out
- 소셜 피드에서는 Fan-Out 기술이 사용된다.
- 단일 데이터를 한 소스에서 여러 목적지로 전달하는 메시징 패턴을 의미한다.
- 하나의 메세지가 여러 개의 목적지로 확산되는 구조를 생각하면 됨.

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_09.png?raw=true)

### Login Session
- 사용자의 로그인 상태를 유지하기 위한 기술
- 로그인시 세션의 개수를 제한하여, 동시에 로그인 가능한 디바이스 개수
  ![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_10.png?raw=true)


### Sliding Window Rate Limiter
- Sliding Window Rate Limiter시간에 따라 Window를 이동시켜 동적으로 요청수를 조절하는 기술
- vs. Fixed Window Fixed Window는 window 시간마다 허용량이 초기화 되지만,  
  Sliding Window는 시간이 경과함에 따라 window가 같이 움직인다.

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_11.png?raw=true)

### Geofencing
- 위치를 활용하여 지도 상의 가상의 경계 또는 지리적 영역을 정의하는 기술
``` commend
127.0.0.1:6379> GEOADD gang-nam:burgers
                 127.025705 37.501272 five-guys
                 127.025699 37.502775 shake-shack
                 127.028747 37.498668 mc-donalds
                 127.027531 37.498847 burger-king
(integer) 4
127.0.0.1:6379> GEORADIUS gang-nam:burgers 127.027583 37.497928 0.5 km
1) "burger-king"
2) "mc-donalds"
3) "five-guys"
127.0.0.1:6379> GEORADIUS gang-nam:burgers 127.027583 37.497928 500 m
1) "burger-king"
2) "mc-donalds"
3) "five-guys"
127.0.0.1:6379> GEORADIUS gang-nam:burgers 127.027583 37.497928 600 m
1) "burger-king"
2) "mc-donalds"
3) "five-guys"
4) "shake-shack"
```

### Online Status
- 소셜 서비스나 게임을 보면 사용자의 현재 상태가 표시되는 서비스들이 있다.
- 해당 사용자가 현재 온라인 상태인지 오프라인 상태인지를 표시하는 기능이다.
- Redis 비트맵을 이용하면 이런 데이터를 효율적으로 관리할 수 있다.
- 특징
    - 실시간성을 완벽히 보장하지는 않는다. 수시로 변경되는 값이다.

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_12.png?raw=true)

> Redis 비트맵을 이용하면 이런 데이터를 효율적으로 관리할 수 있다.  
> 앞서 FixedWindowRateLimiter를 구현했던 것처럼 현재 시간의 분을 기준으로  
> 키를 생성해서 해당 유저가 서브와 통신을 주고받고 있는 경우 비트값을 1로 업데이트한다.  
> 그리고 이를 표시해주는 부분에서는 동일한 방법으로 키를 생성해 해당 유저의 온라인 상태를 조회 할 수 있다.  
> 이후 해당 유저가 더 이상 서버와 통신을 하고 있지 않으면 비트값이 추가로 업데이트 되지 않는다.  
> 유저의 상태를 조회했을 때 해당 유저는 오프라인으로 표시 된다.

### Visitors Coun
- HyperLogLog를 이용하여 방문자 수를 추정하는 기능을 구현하는 방법이다.
- Visitors Count Approximation
    - 방문자 수(또는 특정 횟수)를 대략적으로 추정하는 경우
    - 정확한 횟수를 셀 필요 없이 대략적인 어림치만 알고자 하는 경우  
      HyperLogLog를 이용하면 해당 기능을 쉽게 구현할 수 있다.

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_13.png?raw=true)

> HyperLogLog는 기본적으로 동일한 값을 한 번만 카운팅한다.  
> 따라서 값을 어떻케 추가하는지에 따라 카운팅 방식을 조절할 수 있다.  
> 예를 들어 오늘의 방문자를 셀 때 같은 유저의 요청을 중복으로 한 번만 카운팅할 수도 있고  
> 유닉스 타임을 함께 추가해서 초당 요청을 다르게 셀 수도 있다.

``` log
127.0.0.1:6379> PFADD today:users 
                user:1:1693494070 
                user:1:1693494071 
                user:2:1693494071
(integer) 1
127.0.0.1:6379> PFCOUNT today:users
(integer) 3
```

### Unique Events
- Bloom 필터를 이용해서 동일한 요청의 중복 처리를 방지하는 방법이다.
- 동일 요청이 중복으로 처리되지 않기 위해 빠르게 해당 item이 중복인지 확인하는 방법
    - 클라이언트에서 발생하는 이벤트를 수집하는 서버가 있다고 할 때 클라이언트의 실수로 중복되는 이벤트를
    - 여러번 요청할 수 있다. 이벤트 수집 서버는 이런 경우 대비해서 중복 데이터를 제거하는 기능을 가지고 있어야 한다.

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_14.png?raw=true)

> 이벤트의 데이터를 기준으로 해싱하거나 아니면 이벤트의 발생 시간에 따라 고유한 아이디를 발급 한다.  
> 이렇게 생성한 고유 아이디는 중복 데이터의 경우 동일하게 생성될 것이고 이벤트를 처리하기 전에  
> 고유 아이디를 기준으로 Bloom 필터에 먼저 조회를 요청하고 Bloom 필터에 존재하지 않는다면 해당 데이터는  
> 처리 되지 않은것이기 때문에 데이터를 저장한다. 이후 Bloom 필터에 아이디를 추가 한다.

### Reference
- [인프런 | 실전! Redis 활용](https://inf.run/7ctks)