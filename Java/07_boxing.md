# AutoBoxing(오토 박싱) & UnBoxing(언박싱)

## Wrapper 클래스란?
> Wrapper 클래스는 자바의 기본 데이터 타입을 객체로 다루기 위해 제공되는 클래스  
> 기본 데이터 타입은 int, char, boolean등이 있고,  이러한 데이터 타입을 객체로  
> 다루어야 할 때 Wrapper 클래스를 사용한다.

## 기본 타입과 Wrapper 클래스
원시 타입 : int, long, float, double, boolean
참조 타입 : Integer, Long, Float, Double, Boolean

| Primitive type | Wrapper class |
|:---------------|:-------------:|
| boolean        |    Boolean    |
| byte           |     Byte      |
| char           |   Character   |
| float          |     Float     |
| int            |    Integer    |
| long           |     Long      |
| short          |     Short     |
| double         |    Double     |

![](https://github.com/dididiri1/TIL/blob/main/Java/images/07_01.png?raw=true)

## 오토 언박싱

> 오토박싱은 기본 데이터 타입의 값을 해당하는 Wrapper 클래스 객체로 자동 변환하는 것을 말한다.   
> 이는 컴파일러가 자동으로 처리해주므로 개발자가 명시적인 변환을 직접 작성하지 않아도 된다.


### 예시
```
int i = 10;
Integer num = new Integer(i);
```

## 오토 언박싱
> 언박싱은 Wrapper 클래스 객체를 해당하는 기본 데이터 타입의 값으로 자동 변환하는 것을 말한다.  
> 마찬가지로 컴파일러가 자동으로 처리해주므로 명시적인 변환을 작성하지 않아도 됩니다.


### 예시
```
Intger num = new Integer(10);
int i = num.intValue();
```

