# 🧠 Java instanceof 패턴 매칭 완전 정리 (Java 16+)

## 📌 1. 기존 instanceof 방식 (Java 15 이하)
- 이전에는 타입을 확인한 뒤, **명시적 캐스팅**을 해야 했다.

```java
public class InstanceofMainV0 {

    public static void main(String[] args) {
        Object obj = "Hello Java";

        if (obj instanceof String) {
            String str = (String) obj; // 명시적 캐스팅 필요
            System.out.println(str.toUpperCase());
        }
    }
}
```
### 🔸 단점
- 코드가 길고 중복됨
- instanceof 검사 후 다시 캐스팅해야 해서 불편


## 📌 2. 패턴 매칭 방식 (Java 16 이후)
Java 16부터는 instanceof 패턴 매칭이 도입되어 검사와 캐스팅을 한 번에 처리할 수 있다.
```java
public class InstanceofMainV1 {

    public static void main(String[] args) {
        Object obj = "Hello Pattern Matching";

        if (obj instanceof String str) {
            System.out.println(str.toUpperCase());
        }
    }
}
```
### 🔸 동작 방식
- 1. obj가 String 타입인지 검사
- 2. 맞다면 자동으로 String으로 캐스팅된 변수 str 생성
- 3. if 블록 안에서 바로 사용 가능

## 요약
> instanceof는 “이 객체가 이 타입인가?”를 검사하는 연산자
> Java 16 이후부터는 패턴 매칭 문법으로 훨씬 간결해졌음
