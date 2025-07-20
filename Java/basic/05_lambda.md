# 자바 기초 강의 - 5-5강 람다 표현식의 이해(1)

## 개요[2/1]
- java 8에서 가장 중요한 변화라 할 수 있는 람다 표현식의 등장은 불필요한 코드를 줄이고, 코드의 이해를 돕는다.
- 람다 표현식은 메소드로 전달할 수 있는 익명 함수를 단순화한 코드의 블록이다.
- 람다 표현식은 특정 클래스에 종속되지 않으며 함수라는 이름으로 명명 한다.
- 람다 표현식은 함수 자체를 전달 인자로 보내거나 변수에 저장하는 것이 가능하다.

``` java
import java.util.ArrayList;
import java.util.List;

public class Ex01 {

    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("Java");
        list.add("C");
        list.add("React");


        list.stream().forEach((String str) -> {System.out.println(str);} );
        list.stream().forEach(str -> System.out.println(str) );  // () 파라미터 생략 가능 , {} 실행문 생략 가능
        list.stream().forEach(System.out::println);
    }
}
```

# 자바 기초 강의 - 5-6강 람다 표현식의 이해(2)

## 개요[2/2]
- 람다 표현식은 익명 구현 클래스를 생성하고 객체화 한다.
- 익명 구현 클래스로 생성된 람다 표현식은 인터페이스로 대입 가능하며 이 인터페이스를 함수형 인터페이스라고 한다.
- 하나의 추상 메소드를 갖는 인터페이스는 모두 함수형 인터페이스가 될 수 있습니다.
- 다수의 디폴트 메소드를 갖는 인터페이스라도 추상 메소드가 하나라면 함수형 인터페이스 이다.
- 함수형 인터페이스를 정의 할 때 @FunctionalUnterface 어노테이션을 이용해 컴파일 검사를 진행할 수 있다.
- 함수형 인터페이스의 추상메소드 시그니처를 함수 디스크립터(Function descriptor)라고 한다.

### Reference

- [자바 기초 - 나무소리](https://www.youtube.com/@namoosori/playlists)
