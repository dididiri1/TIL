# 섹션 11. Optional

## 옵셔널이 필요한 이유

### NullPointerException(NPE) 문제
- 자바에서 null 은 "값이 없음"을 표현하는 가장 기본적인 방법이다.
- 하지만 null 을 잘못 사용하거나 null 참조에 대해 메서드를 호출하면 NullPointerException이 발생하여 프로그램이 예기치 않게 종료될 수 있다.
- 특히 여러 메서드가 연쇄적으로 호출되어 내부에서 null 체크가 누락되면, 추적하기 어렵고 디버깅 비용이 증가 한다.

#### 가독성 저하
- null 을 반환하거나 사용하게 되면, 코드를 작성할 때마다 if (obj != null) { ... } else { ... } 같은 조건문으로 null 여부를 
  계속 확인해야 한다.
- 이러한 null 체크 로직이 누적되면 코드가 복잡해지고 가독성이 떨어진다.

#### 의도가 드러나지 않음
- 메서드 시그니처( String findNameById(Long id) )만 보고서는 이 메서드가 null 을 반환할 수도 있다는 사실을 명확히 알기 어렵다.
- 호출하는 입장에서는 "반드시 값이 존재할 것"이라고 가정했다가, 런타임에 null 이 나와서 문제가 생길 수 있다.

#### Optional의 등장
- 이러한 문제를 해결하고자 자바 8부터 Optional 클래스를 도입했다.
- Optional 은 "값이 있을 수도 있고 없을 수도 있음"을 명시적으로 표현해주어, 메서드의 계약(Contract)이나 호출 의도를 좀 더 분명하게 드러낸다.
- Optional 을 사용하면 "빈 값"을 표현할 때, 더 이상 null 자체를 넘겨주지 않고 Optional.empty() 처럼 의도를 드러내는 객체를 사용할 수 있다.
- 그 결과, Optional 을 사용하면 null 체크 로직을 간결하게 만들고, 특정 경우에 NPE가 발생할 수 있는 부분을 빌드 타입이나 IDE, 코드 리뷰에서
  더욱 쉽게 파악할 수 있게 해준다.

예제 코드를 통해 자세히 살펴보자.

### null을 직접 반환하는 경우
```
package optional;

import java.util.HashMap;
import java.util.Map;

public class OptionalStartMain1 {

    private static final Map<Long, String> map = new HashMap<>();

    static {
        map.put(1L, "Kim");
        map.put(2L, "Seo");
        // 3L은 넣지 않아서 찾을 수 없는 ID로 활용
    }

    public static void main(String[] args) {
        findAndPrint(1L); // 값이 있는 경우
        findAndPrint(3L); // 값이 없는 경우
    }

    // 이름이 있으면 이름을 대문자로 출력, 없으면 "UNKNOWN"을 출력해라.
    static void findAndPrint(Long id) {
        String name = findNameById(id);

        // 1. NullPointerException 유발
        // System.out.println("name = " + name.toUpperCase());

        // 2. if 문을 활용한 null 체크 필요
        if (name != null) {
            System.out.println(id + ": " + name.toUpperCase());
        } else {
            System.out.println(id + ": " + "UNKNOWN");
        }
    }

    static String findNameById(Long id) {
        return map.get(id);
    }
}
```

#### 실행 결과
```
1: KIM
3: UNKNOWN
```
- 이 예제에서는 map.get(id) 결과가 존재하지 않을 경우 null 을 반환한다.
- 따라서 findAndPrint() 메서드에서 String name = findNameById(id); 실행 후, name 이 null 인지 아닌지 매번 확인해야 한다.
- null 을 반환하는 방식을 택하면, 메서드를 호출하는 쪽에서 반드시 null 체크 로직을 작성해주어야 하고, 이 를 빠뜨리면 NullPointerException 
  이 발생한다.
- 반환 타입이 String 이므로 얼핏 보면 항상 문자열이 반환될 것처럼 보이지만, 실제로는 null 이 반환될 수도 있어 주의가 필요하다.

### Optional을 반환하는 경우

```
package optional;

import java.util.HashMap;
import java.util.Map;
import java.util.Optional;

public class OptionalStartMain2 {

    private static final Map<Long, String> map = new HashMap<>();

    static {
        map.put(1L, "Kim");
        map.put(2L, "Seo");
    }

    public static void main(String[] args) {
        findAndPrint(1L); // 값이 있는 경우
        findAndPrint(3L); // 값이 없는 경우
    }

    // 이름이 있으면 이름을 대문자로 출력, 없으면 "UNKNOWN"을 출력해라.
    static void findAndPrint(Long id) {
        Optional<String> optName = findNameById(id);
        String name = optName.orElse("UNKNOWN");
        System.out.println(id + ": " + name.toUpperCase());
    }

    static Optional<String> findNameById(Long id) {
        String findName = map.get(id);
        Optional<String> optName = Optional.ofNullable(findName);
        return optName;
    }
}
```

#### 실행 결과
```
1: KIM
3: UNKNOWN
```

- 이번에는 Optional<String> 을 반환하도록 변경했다.
- Optional.ofNullable(findId)를 통해 null 이 될 수도 있는 값을 Optional 로 감싼다.
- 메서드 시그니처( Optional<String> findNameById(Long id) )만 보고도 "반환 결과가 있을 수도, 없을 수도 있겠구나"라고 
  명시적으로 인지할 수 있다.
- Optional.orElse("대체값") : 옵셔널에 값이 있으면 해당 값을 반환하고, 값이 없다면 대체값을 반환한다.
- findAndPrint() 메서드에서는 Optional<String> 을 받아서, orElse("UNKNOWN") 로 "빈 값"인 경우 대체 문자열을 지정할 수 있다.
- 이 방식은 "값이 없을 수도 있다"는 점을 호출하는 측에 명확히 전달하므로, 놓치기 쉬운 null 체크를 강제하고 코드의 안정성을 높인다.

### Optional 소개
**자바 Optional 클래스 코드**
```
package java.util;

public final class Optional<T> {
    private final T value;
    ...
}
```

#### 정의
- java.util.Optional<T> 는 "존재할 수도 있고 존재하지 않을 수도 있는" 값을 감싸는 일종의 컨테이너 클래스이다.
- 내부적으로 null 을 직접 다루는 대신, Optional 객체에 감싸서 Optional.empty() 또는 Optional.of(value) 형태로 다룬다.

#### 등장 배경
- 값이 없을 수 있다"는 상황을 프로그래머가 명시적으로 처리하도록 유도하고, 런타임 NullPointerException 을 사전에 예방하기 위해 도입되었다.
- 코드를 보는 사람이나 협업하는 팀원 모두가, 해당 메서드의 반환값이 비어있을 수도 있음을 알 수 있게 되어 오류를 줄일 수 있다.

#### 참고
- Optional 은 "값이 없을 수도 있다"는 상황을 반환할 때 주로 사용된다.
- "항상 값이 있어야 하는 상황"에서는 Optional 을 사용할 필요 없이 그냥 해당 타입을 바로 사용하거나 예외를 던지는 방식이 더 좋을 수 있다.

## Optional의 생성과 값 획득
### Optional 생성
Optional 을 생성하는 방법은 다음과 같다.

#### Optional을 생성하는 메서드
- Optional.of(T value)
  - 내부 값이 확실히 null 이 아닐 때 사용. null 을 전달하면 NullPointerException 발생
- Optional.ofNullable(T value)
  - 값이 null 일 수도 있고 아닐 수도 있을 때 사용. null 이면 Optional.empty() 를 반환한다.
- Optional.empty()
  - 명시적으로 "값이 없음"을 표현할 때 사용

코드로 알아보자.
```
package optional;

import java.util.Optional;

public class OptionalCreationMain {

    public static void main(String[] args) {
        // 1. of() : 값이 null이 아님이 확실할 때 사용, null이면 NullPointerException 발생
        String nonNullValue = "Hello Optional!";
        Optional<String> opt1 = Optional.of(nonNullValue);
        System.out.println("opt1 = " + opt1); // Optional[Hello Optional!]

        // 2. ofNullable() : 값이 null일 수도 아닐 수도 있을 때
        Optional<String> opt2 = Optional.ofNullable("Hello!");
        Optional<String> opt3 = Optional.ofNullable(null);
        System.out.println("opt2 = " + opt2); // Optional[Hello!]
        System.out.println("opt3 = " + opt3); // Optional.empty

        // 3. empty() : 비어있는 Optional을 명시적으로 생성
        Optional<String> opt4 = Optional.empty();
        System.out.println("opt4 = " + opt4); // Optional.empty
    }
}
```
#### 실행 결과
```
opt1 = Optional[Hello Optional!]
opt2 = Optional[Hello!]
opt3 = Optional.empty
opt4 = Optional.empty
```

### Optional 값 획득
Optional 에 들어있는 값을 꺼내는 여러 가지 방법을 알아보자.

#### Optional의 값을 확인하거나, 획득하는 메서드
1. isPresent() , isEmpty()
- 값이 있으면 true
- 값이 없으면 false 를 반환. 간단 확인용.
- isEmpty() : 자바 11 이상에서 사용 가능, 값이 비어있으면 true , 값이 있으면 false 를 반환
2. get()
- 값이 있는 경우 그 값을 반환
- 값이 없으면 NoSuchElementException 발생.
- 직접 사용 시 주의해야 하며, 가급적이면 orElse , orElseXxx 계열 메서드를 사용하는 것이 안전.
3. orElse(T other)
- 값이 있으면 그 값을 반환
- 값이 없으면 other 를 반환.
4. orElseGet(Supplier<? extends T> supplier)
- 값이 있으면 그 값을 반환
- 값이 없으면 supplier 호출하여 생성된 값을 반환.
5. orElseThrow(...)
- 값이 있으면 그 값을 반환
- 값이 없으면 지정한 예외를 던짐.
6. or(Supplier<? extends Optional<? extends T>> supplier)
- 값이 있으면 해당 값의 Optional 을 그대로 반환
- 값이 없으면 supplier 가 제공하는 다른 Optional 반환
- 값 대신 Optional 을 반환한다는 특징

```
package optional;

import java.util.Optional;

public class OptionalRetrievalMain {
    public static void main(String[] args) {
        // 예제: 문자열 "Hello"가 있는 Optional과 비어있는 Optional 준비
        Optional<String> optValue = Optional.of("Hello");
        Optional<String> optEmpty = Optional.empty();

        // isPresent() : 값이 있으면 true
        System.out.println("=== 1. isPresent() / isEmpty() ===");
        System.out.println("optValue.isPresent() = " + optValue.isPresent()); // true
        System.out.println("optEmpty.isPresent() = " + optEmpty.isPresent()); // false

        // isEmpty() : 값이 없으면 true
        System.out.println("optEmpty.isEmpty() = " + optEmpty.isEmpty());

        // get() : 직접 내부 값을 꺼냄, 값이 없으면 예외 (NoSuchElementException)
        System.out.println("=== 2. get() ===");
        String getValue = optValue.get();
        System.out.println("getValue = " + getValue);
        // String getValue2 = optEmpty.get(); // 예외 발생 -> 주석 처리

        // 값이 있으면 그 값, 없으면 지정된 기본값 사용
        System.out.println("=== 3. orElse() ===");
        String value1 = optValue.orElse("기본값");
        String empty1 = optEmpty.orElse("기본값");
        System.out.println("value1 = " + value1);
        System.out.println("empty1 = " + empty1);

        // 값이 없을 때만 람다(Supplier)가 실행되어 기본값 생성
        System.out.println("=== 4. orElseGet() ===");
        String value2 = optValue.orElseGet(() -> {
            System.out.println("람다 호출 - optValue");
            return "New Value";
        });
        String empty2 = optEmpty.orElseGet(() -> {
            System.out.println("람다 호출 - optEmpty");
            return "New Value";
        });
        System.out.println("value2 = " + value2);
        System.out.println("empty2 = " + empty2);

        // 값이 있으면 반환, 없으면 예외 발생
        System.out.println("=== 5. orElseThrow() ===");
        String value3 = optValue.orElseThrow(() -> new RuntimeException("값이 없습니다!"));
        System.out.println("value3 = " + value3);

        try {
            // optEmpty는 값이 없으므로 예외 발생
            String empty3 = optEmpty.orElseThrow(() -> new RuntimeException("값이 없습니다!"));
            System.out.println("empty3 = " + empty3); // 실행되지 않음
        } catch (Exception e) {
            System.out.println("예외 발생: " + e.getMessage());
        }

        // Optional을 반환
        System.out.println("=== 6. or() ===");
        Optional<String> result1 = optValue.or(() -> Optional.of("Fallback"));
        System.out.println(result1); // Optional[Hello], 값이 이미 존재 -> 원본 그대로

        Optional<String> result2 = optEmpty.or(() -> Optional.of("Fallback"));
        System.out.println(result2); // Optional[Fallback], 비어있으므로 대체 Optional 반환
    }
}
```
#### 실행 결과
```
=== 1. isPresent() / isEmpty() ===
optValue.isPresent() = true
optEmpty.isPresent() = false
optEmpty.isEmpty() = true
=== 2. get() ===
getValue = Hello
=== 3. orElse() ===
value1 = Hello
empty1 = 기본값
=== 4. orElseGet() ===
람다 호출 - optEmpty
value2 = Hello
empty2 = New Value
=== 5. orElseThrow() ===
value3 = Hello
예외 발생: 값이 없습니다!
=== 6. or() ===
Optional[Hello]
Optional[Fallback]
```
- optValue.isPresent() 가 true 인 이유는 Optional.of("Hello") 로 생성했기 때문이다.
- optEmpty.isPresent() 가 false 인 이유는 Optional.empty() 로 생성했기 때문이다.
- isEmpty() 는 자바 11부터 사용할 수 있는 메서드로, 값이 없으면 true 를 반환한다.
- get() 메서드는 Optional 사용 시 가능하면 피해야 한다.
  - 값이 없는 상태( Optional.empty() )에서 get() 을 호출하면 바로 예외가 터지므로, 안전하게 사용하려면 isPresent() 같은 사전 체크가 필요하다.
  - get() 보다는 orElse() , orElseGet() , orElseThrow() 등의 메서드를 활용하면 좀 더 세련되고 안전하게 값을 처리할 수 있다.

여기서 orElse() , orElseGet() 의 차이가 잘 느껴지지 않을 수 있다. 지금은 값이 없는 경우 기본값을 제공한다정도로 이해하자.   
이 부분은 뒤에서 자세히 설명하겠다

### Optional 값 처리
Optional 에서는 값이 존재할 때와 존재하지 않을 때를 처리하기 위한 다양한 메서드들을 제공한다.
이를 활용하면, null 체크 로직 없이도 안전하고 간결하게 값을 다룰 수 있다.

#### Optional 값 처리 메서드
- ifPresent(Consumer<? super T> action)
  - 값이 존재하면 action 실행
  - 값이 없으면 아무것도 안 함
- ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction)
  - 값이 존재하면 action 실행
  - 값이 없으면 emptyAction 실행
- map(Function<? super T, ? extends U> mapper)
  - 값이 있으면 mapper 를 적용한 결과 (Optional<U>) 반환
  - 값이 없으면 Optional.empty() 반환
- flatMap(Function<? super T, ? extends Optional<? extends U>> mapper)
  - map과 유사하지만, Optional 을 반환할 때 중첩되지 않고 평탄화(flat)해서 반환
- filter(Predicate<? super T> predicate)
  - 값이 있고 조건을 만족하면 그대로 반환,
  - 조건 불만족이거나 비어있으면 Optional.empty() 반환
- stream()
  - 값이 있으면 단일 요소를 담은 Stream<T> 반환
  - 값이 없으면 빈 스트림 반환

```
package optional;

import java.util.Optional;

public class OptionalProcessingMain {
    public static void main(String[] args) {
        Optional<String> optValue = Optional.of("Hello");
        Optional<String> optEmpty = Optional.empty();

        // 값이 존재하면 Consumer 실행, 없으면 아무 일도 하지 않음
        System.out.println("=== 1. ifPresent() ===");
        optValue.ifPresent(v -> System.out.println("optValue 값: " + v));
        optEmpty.ifPresent(v -> System.out.println("optEmpty 값: " + v)); // 실행 X

        // 값이 있으면 Consumer 실행, 없으면 Runnable 실행
        System.out.println("=== 2. ifPresentOrElse() ===");
        optValue.ifPresentOrElse(
            v -> System.out.println("optValue 값: " + v),
            () -> System.out.println("optValue는 비어있음")
        );
        optEmpty.ifPresentOrElse(
            v -> System.out.println("optEmpty 값: " + v),
            () -> System.out.println("optEmpty는 비어있음")
        );

        // 값이 있으면 Function 적용 후 Optional로 반환, 없으면 Optional.empty()
        System.out.println("=== 3. map() ===");
        Optional<Integer> lengthOpt1 = optValue.map(String::length);
        System.out.println("optValue.map(String::length) = " + lengthOpt1);
        Optional<Integer> lengthOpt2 = optEmpty.map(String::length);
        System.out.println("optEmpty.map(String::length) = " + lengthOpt2);

        // map()과 유사하나, 이미 Optional을 반환하는 경우 중첩을 제거
        System.out.println("=== 4. flatMap() ===");
        System.out.println("[map]");
        // Optional[Hello] -> Optional[Optional[HELLO]]
        Optional<Optional<String>> nestedOpt = optValue.map(s -> Optional.of(s));
        System.out.println("optValue = " + optValue);
        System.out.println("nestedOpt = " + nestedOpt);

        System.out.println("[flatMap]");
        // flatMap을 사용하면 한 번에 평탄화
        // Optional[Hello] -> Optional[HELLO]
        Optional<String> flattenedOpt = optValue.flatMap(s -> Optional.of(s));
        System.out.println("optValue = " + optValue);
        System.out.println("flattenedOpt = " + flattenedOpt);

        System.out.println("=== 5. filter() ===");
        // 값이 있고 조건을 만족하면 그 값을 그대로, 불만족시 Optional.empty()
        Optional<String> filtered1 = optValue.filter(s -> s.startsWith("H"));
        Optional<String> filtered2 = optValue.filter(s -> s.startsWith("X"));
        System.out.println("filter(H) = " + filtered1); // Optional[Hello]
        System.out.println("filter(X) = " + filtered2); // Optional.empty

        System.out.println("=== 6. stream() ===");
        // 값이 있으면 단일 요소 스트림, 없으면 빈 스트림
        optValue.stream()
                .forEach(s -> System.out.println("optValue.stream() -> " + s));
        // 값이 없으므로 실행 안 됨
        optEmpty.stream()
                .forEach(s -> System.out.println("optEmpty.stream() -> " + s));
    }
}
```

### 실행 결과
```
=== 1. ifPresent() ===
optValue 값: Hello
=== 2. ifPresentOfElse() ===
optValue 값: Hello
optEmpty는 비어있음
=== 3. map() ===
optValue.map(String::length) = Optional[5]
optEmpty.map(String::length) = Optional.empty
=== 4. flatMap() ===
[map]
optValue = Optional[Hello]
nestedOpt = Optional[Optional[Hello]]
[flatMap]
optValue = Optional[Hello]
flattenedOpt = Optional[Hello]
=== 5. filter() ===
filter(H) = Optional[Hello]
filter(X) = Optional.empty
=== 6. stream() ===
optValue.stream() -> Hello
```
Optional 의 다양한 메서드를 활용하면, 값이 존재할 때와 존재하지 않을 때의 로직을 명확하고 간결하게 구현할 수 있다.
이러한 기능 덕분에 null 체크로 인한 복잡한 코드와 예외 처리를 줄이고, 더 읽기 쉽고 안전한 코드를 작성할 수 있다.

