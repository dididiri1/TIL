## 섹션 6. Redis 사용시 주의사항

### O(N) 명령어
- Redis의 대부분의 명령어는 O(1)의 시간복잡도를 갖고 있어 빠르게 동작한다. 일부 명령어의 경우 O(N)
- Redis는 Single Thread로 명령어를 순차적으로 수행하기 때문에, 오래 걸리는 O(N) 명령어 수행시, 전체적인 어플리케이션 성능 저하

### O(N) 명령어 에시
| 명령어      |                                  설명                                  |
|:---------|:--------------------------------------------------------------------:|
| KEYS *   | 지정된 패턴과 일치하는 모든 키 Key 조회 <br/> Production 환경에서 사용 금지 -> SCAN 명령어로 대체 |
| SMEMBERS |                Set의 모든 member 반환 (N = Set Cardinality                |
| HGETALL  |                 Hash의 모든 field 반환(N = Size of Hash)                  |
| SORT     |                    List, Set, ZSet의 item 정렬하여 반환                     |

### Thundering Herd Problem
- 병렬 요청이 공유 자원에 대해서 접근할 때, 급격한 과부하가 발생하는 문제

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_16.png?raw=true)

> 별도의 프로세스에서 cron job을 통해 통계 캐시를 주기적으로 최신화 시켜야 된다.  
> 대규모 트래픽을 처리하는 서비스에서는 캐싱을 적용하는 것도 중요하지만 캐시를 어떻케 관리하는 것도한 중요.

### Stale Cache Invalidation
- Cache Invalidation 
  -  캐시의 유효성이 손실되었거나 변경되었을 때, 캐시를 변경하거나 삭제하는 기술

> 캐시는 임시 데이터이기 때문에 잘못하면 원본 데이터와 싱크가 안 맞을 수 있다.  
> 캐시의 유효성이 손실되었거나 데이터 자체가 변경 되었을 때는 캐시를 변경하가너 삭제하는 등 관리가 필요함  
> 유효하지 않은 캐시를 계속 들고 있으면 어플리케이션에서는 잘못된 데이터를 내려줄 수 있기 때문이다.

![](https://github.com/dididiri1/TIL/blob/main/Redis/images/01_17.png?raw=true)

> 위에 원본 잔액이 이미 80으로 업데이트 되었지만 캐시의 100으로 남아있게 되는 경우 서버는  
> 유효 하지 않은 캐시 데이터를 잘못 읽어서 클라이언트에게 잘못된 데이터를 줄 수 있다.


### Reference
- [인프런 | 실전! Redis 활용](https://inf.run/7ctks)






