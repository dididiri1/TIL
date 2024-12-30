# 실전! Redis 활용

## 섹션 1. Redis 알아보기

### Redis 소개
&nbsp;&nbsp;&nbsp;&nbsp;Remote Dictionary Linux  
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

### Reference
- [인프런 | 실전! Redis 활용](https://inf.run/7ctks)