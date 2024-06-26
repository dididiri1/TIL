# [JPA] 엔티티의 생명주기 - 비영속, 영속, 준영속, 삭제

## 엔티티의 생명주기
### 엔티티는 다음과 같이 4단계가 있음
- 비영속 (new/transient)
- 영속 (managed)
- 준영속 (detached)
- 삭제 (removed)

## 비영속(new/transient)
- 비영속 상태는 객체를 새로 생성한 상태
```
Member member = new Member(1L, "홍길동"); 
```