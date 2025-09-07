# Java Stream에서 toList() vs collect(Collectors.toList()) 차이

### 1. Collectors.toList() vs Collectors.toUnmodifiableList()
- Java 8부터 사용 가능
- Collectors.toList()는 mutable(수정 가능한) 리스트를 반환합니다.
- 구현체는 보통 ArrayList이지만, 명세상 어떤 List가 올지는 보장하지 않음.

``` 
public class StreamEx1 {

    public static void main(String[] args) {

        List<String> data = List.of("a", "b", "c");

        List<String> result1 = data.stream()
                .map((String s) -> s.toUpperCase())
                .collect(Collectors.toUnmodifiableList());

        //result1.add("d"); // ❌ UnsupportedOperationException

        System.out.println("result1 = " + result1);

        List<String> result2 = data.stream()
                .map(String::toUpperCase)
                .collect(Collectors.toList());

        result2.add("d"); // ✅ 가능

        System.out.println("result2 = " + result2);
    }
}
``` 

#### 👉 결과 리스트를 이후에 수정할 필요가 있다면 이 방식을 사용합니다.

### 2. toList()
- Java 16부터 추가
- Stream의 기본 메서드.
- 내부적으로 Collectors.toUnmodifiableList()와 유사하게 동작하여 immutable(수정 불가능) 리스트를 반환.
``` 
public class StreamEx2 {

    public static void main(String[] args) {

        List<String> data = List.of("a", "b", "c");

        List<String> result1 = data.stream()
                .map((String s) -> s.toUpperCase())
                .toList();

        //result1.add("d"); // ❌ UnsupportedOperationException

        System.out.println("result1 = " + result1);
    }
}
```

### 3. 차이 정리
| 구분           | collect(Collectors.toList()) | collect(Collectors.toUnmodifiableList()) | toList()            |
|--------------|------------------------------|------------------------------------------|---------------------|
| **도입 버전**    | Java 8+                      | Java 10+ | Java 16+            |
| **반환 리스트**   | 구현체 미보장 (보통 ArrayList)       | 불변 리스트 | 불변 리스트              |
| **수정 가능 여부** | ✅ 가능                         | ❌ 불가능 | ❌ 불가능               |
| **권장 상황**    | 리스트를 조작해야 할 때                | 읽기 전용 필요 (Java 10~15) | 읽기 전용 필요 (Java 16+) |

✅ 마무리

> collect(Collectors.toList()) → 수정 가능, JDK 8부터  
> collect(Collectors.toUnmodifiableList()) → 불변, JDK 10부터  
> toList() → 불변, JDK 16부터, 가장 간결  

👉 따라서 최신 자바에서는 toList()를 기본으로 쓰고, 꼭 수정이 필요한 경우에만 collect(Collectors.toList())를 쓰는 게 베스트입니다.