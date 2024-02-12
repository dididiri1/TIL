# 클래스의 이해
- 2.1 객체지향 프로그래밍
- 2.2 클래스 그리고 객체
- 2.3 클래스의 구성요소
- 2.4 필드(Field)의 정의
- 2.5 연산자(Operatiors)의 이해
- 2.6 메소드(Method)의 정의
- 2.7 생성자(Constructor)의 정의


## 객체지향 프로그래밍
- 프로그래밍 언어의 패러다임을 이해하는 과정은 해당 프로그램 언어를 익히고 사용하는데 매우 중요한 절차이다.
- 프로그래밍은 현실 세계의 특정 문제를 컴퓨터 세계를 통해 풀어가는 방법이라고 할 수 있다.
- 현실 세계의 문제를 기능 또는 구조위주의 관점으로 보고, 기능을 세분화하여 풀어가는 것을 절차지향이라고 한다.
- 객체지향은 문제를 데이터의 관점으로 보며, 데이터들의 상화 관계를 정의함으로써 해결함책을 찾아간다.

## 클래스(Class) 그리고 객체(Object)
- 자바 언어로 구현하는 프로그램은 다수의 클래스들로 이루어지며 이 클래스를 이용해 객체를 만들고 사용한다.
- 클래스를 정의한다는 것은 객체를 만들기 위한 과정이며 클래스는 객체에 대한 청사진 또는 템플릿이라 할 수 있다.
- 클래스로부터 만들어지는 객체를 인스턴스(instance) 혹은 인스턴스 객체(instance object)라고 한다.


## 클래스의 구성요소 
- 자바 프로그램의 기본인 클래스를 잘 정의하기 위해서는 클래스를 구성요소에 대한 이해가 필요하다.
- 클래스는 상태와 행위를 가지며 상태를 필드(Field), 행위를 메소드(Method)라고 한다.
- 클래스에는 필드와 메소드 외에 생성자(Constructor)라는 특수한 메소드도 반드시 하나 이상 갖는다.


![](https://github.com/dididiri1/TIL/blob/main/java/images/02_01.png?raw=true)

# 자바 기초 강의 - 2-2강 클래스의 이해 - 필드의 정의

## 개요

- 클래스에 정의하는 속성은 특정한 값을 가지며, 객체의 속성 값은 해당 객체의 상태를 표현한다.
- 현실 세계의 객체는 다양하고 광범위한 속성들을 갖고 있기 때문에 정의하고자 하는 클래스의 특성을 잘 이해하고 해당  
  클래스의 핵심 속성들들 정의한다.
- 필드를 정의할 떄는 반드시 접근지정자(Access Modifier), 타입, 필드명을 명시한다.

![](https://github.com/dididiri1/TIL/blob/main/java/images/02_02.png?raw=true)

## 변수의 유형
### 변수는 정의된 위체 따리 4가지의 유형으로 구분하며 각 유형에 따라 갖는 특성에 차이가 있다.
- 지역변수(Local variables)
- 매개변수(Parameter variables)
- 인스턴스 변수(Instance variables)
- 정적 변수(Class variables)

``` java
public class VariableTypes {
    
    /** 정적 변수 */
    public static int cassVar = 1;
    
    /** 인스턴스 변수 */
    private int instanceVar;
    
    /**
     * @param paramVar 매개변수
     */
    public static void main(String[] paramVar)  {
        // 지역변수
        int localVal = 10;
        
    }
    
}

```

## 자료형(Data Type)의 이해

### 정수형은 소수부가 없는 숫자를 나태나는 자료형이다.
- 자바는 데이터의 표현범위에 따라 4가지의 정수형을 제공한다.

|  자료형  |  메모리 크기  | 표현범위                              |  표현 범위(지수표현)  |
|:-----:|:--------:|----------------------------------------------|:-------------:|
| byte  |  1 바이트   | -128 ~ 127                                   |  -27 ~ 27-1   |
| short |  2 바이트   | -32,768 ~ 32,767                             | -215 ~ 215-1  |
|  int  |  4 바이트   | -2,147,483,648 ~ 2,147,483,647               | -231 ~ 231-1  |
| long  |  8 바이트   | -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807 | -263 ~ 263-1  |

## 논리 자료형 (Boolean Type)
### 논리 자료형은 **참과 거짓**을 표현하는 자료형이다.
- true : 참을 표현하는 값
- false : 거짓을 표현하는 값
``` java
public class BooleanType {

    public static void main(String[] args) {
        boolean isTrue = true;
        boolean isFalse = false;
        System.out.println(isTrue);
        System.out.println(isFalse);
    }

}
```
``` console
true
false
```

## 상수 (Constants)
- 사용자 정의 상수는 변수를 선언하고 여게에 final 키워드를 붙이면 한번 초기화 후 그 값을 변경 할 수 없다.
``` java
final double PI = 3.14;
System.out.println("PI is " + PI);
```

## 참조 자료형 (Reference Type)
- 참조 자료형은 갖는 변수는 특정 객체의 참조 정보를 저장한다.
- 기본 데이터 타입(byte, short, int, long, float, double 등) 이외의 타입을 의미한다.
- 참조 자료형 변수는 4byte의 크기를 갖으며 인스턴스 객체에 접근할 수 있는 정보를 갖는다.
- 객체의 삭제는 객체가 더 이상 사용되지 않을 때 자바의 Garbage Collector에 의해 자동적으로 제거된다.

![](https://github.com/dididiri1/TIL/blob/main/java/images/02_03.png?raw=true)


# 2.5 연산자(Operators)의 이해

## 대입연산자

| 연산자 | 예         | 의미             |
|:---:|:-----------:|:----------------:|
| +=  | num += 10 | num = num + 10 |
| -=  | num -= 10 | num = num - 10 |
| *=  | num *= 10 | num = num * 10 |
| /=  | num /= 10 | num = num / 10 |
| %=  | num %= 10 | num = num % 10 |

## 산술연산자
| 연산자 |  연산식   |
|:---:|:------:|
|  +  | 10 + 5 |
|  -  | 10 - 5 |
|  *  | 10 * 5 |
|  %  | 10 % 5 |

## 관계연산자
| 연산자 |   예    |      의미       |
|:---:|:------:|:-------------:|
| ==  | a == b |   a와 b가 같다    |
| !=  | a != b |  a와 b가 같지 않다  |
|  >  | a > b  |   a가 b보다 크다   |
| >=  | a >= b | a가 b보다 크거나 같다 |
|  <  | a < b  |   a가 b보다 작다   |
| <=  | a <= b | a가 b보다 작거나 같다 |

## 증감 연산자
- 변수에 저장된 값을 증가(++)시키거나 또는 감소(--) 시키는 단항 연산자이다.
- 연산자를 정의하는 위체에 따라 연산이 수행되는 시점이 다르게 적용된다.

|    연산자    |   예    |
|:---------:|:------:|
|  전위 연산자   |  ++a   |
|  후위 연산자   |  a++   |

## 논리 연산자
- 논리 연산을 수행하는 연산자로 이항 연산자인 AND (논리곱, &&), OR(논리합, ||) 연산자와  
  단항 연산자인 NOT(논리부정, !) 연산자가 있다. 
- 우선 순위는 NOT > AND > OR

## 비트 연산자(Shift)
- 정수형 피연산자에 대해 비트를 왼쪽, 혹은 오른쪽으로 이동시키는 연산자.

| 연산자 |   의미    |                의미                |
|:---:|:-------:|:--------------------------------:|
| >>  | 5 >> 2  |     5의 이진수표현에서 오른쪽으로 2칸 쉬프트      |
| <<  | 5 << 2  |      5의 이진수표현에서 왼쪽으로 2칸 쉬프트      |
| >>> | 5 >>> 2 | 5의 이진수표현에서 오른쪽으로 2칸 쉬프트(0으로 채워짐) |

## 삼항 연산자
- 삼항 연산자는 피연산자를 3개 갖는 연산자 이다.
- 조건의 참, 거짓 여부에 따라 서로 다른 수식을 실행함.

``` java
int x, y, z;
x = 10; y = 20;

z = ( x > y ? x : y);
System.out.println(z); //z : 20

z = ( y > x ? x : y);
System.out.println(z); // z : 10
``` 

## 형변환(casting) 연산자
### 암묵적 형 변환(Implicit Conversion)
- Java는 데이터가 손실되지 않거나, 손실이 제한적인 범위 내에서 암묵적으로 형을 변환한다.
- 자료형이 다른 변수에 값을 할당하는 경우
``` java
float f = 10;
```
- 자료형이 다른 값을 연산하는 경우
``` java
double d = 10.0 + 20;
``` 

### 명시적 형 변환(Explicit Conversion)
- 명시적으로 형 변환하는 것을 캐스팅(Casting)이라고 한다.
- 자동 형 변환 규칙에 위배되는 상황임에도 강제로 형 변환할 경우 데이터 손실의 위험이 있음.
``` java
int i1 = 20.5; // Error

int i2 = (int) 20.5; // 20
```

# 2.6 메소드(Method) 정의

## 메소드 구성 및 정의
- 클래스를 구성하는 구성요소에서 해당 클래스의 행위를 의미하는 것이 메소드(Method)이다.
- 필드와 마찬가지로 메소드르에도 접근 지정자를 지정하여 메소드를 호출에 대한 범위를 정할 수 있다.
- 메소드는 static 키워드를 이용해 클래스 메소드와 인스턴스 메소드로 정의할 수 있다.
-
![](https://github.com/dididiri1/TIL/blob/main/java/images/02_04.png?raw=true)


## 오버로딩(Overloading)
- 메소드의 이름을 같게 하고, 파라미터를 달리 하여 여러 메소드를 정의하는 방법을 오버로딩이라고 한다.
- 오버로딩은 하나의 클래스내에서의 기능이며 파라미터는 타입, 순서, 개수를 달리하여 구분할 수 있어야 한다.

```java
class Calculator {

    public int sum(int x, int y){
        return x + y;
    }
    
    public int sum(int x, int y, int z){
        return x + y + z;
    }
    
    public double sum(double x, double y){
        return x + y;
    }
}

public class OverloadingExam {

    public static void main(String[] args) {
        Calculator calc = new Calculator();
        System.out.println(calc.sum(10, 20)); 
        System.out.println(calc.sum(10, 20, 30)); 
        System.out.println(calc.sum(10.0, 20.0));
    }
}
``` 
``` console
30
60
30.0
``` 

##  생성자(Constructor)
- 생성자(Constructor)는 클래스 구성요소 중 하나로, 객체를 인스턴스화 할 때 가장 먼저 호출되는 메소드이다.
- 생성자의 역할은 객체가 갖는 필드의 초기화이며 따라서 모든 클래스는 하나 이상의 생성자를 갖는다.
- 생성자의 이름은 해당 클래스의 이름과 같고 반환 타입은 갖지 않는다.
- 생성자도 모든 접근제어자를 적용할 수 있으며 일반적으로는 public 접근제어자가 이다.

``` java
class exam01.Employee {
    
    private String name;
    private String department;

    public exam01.Employee(){
        System.out.println("exam01.Employee() 호출");
    }

    public exam01.Employee(String name){
        this.name = name;
    }
}

public class ConstructorExam {

    public static void main(String[] args) {
        exam01.Employee employee = new exam01.Employee();
    }
}

```

## 디폴트 생성자 (Default Constructor)
- 매개 변수가 없고 구현 내용 없이 정의하는 생성자를 기본 생성자 혹은 디폴트 생성자(Default Constructor)라고 한다.
- 클래스는 반드시 하나 이상의 생성자를 가지며 사용자가 생성자를 정의하지 않으면 디폴트 생성자가 자동으로 생성된다.
- 생성자에서 명시적으로 필ㄷ의 값을 설정하지 않으면 디폴트 값으로 초기화 된다.

``` java
class Student {

    private String name;
}

class exam01.Employee {

    private String name;

    public exam01.Employee(String name){
        this.name = name;
    }
}

public class ConstructorExam {

    public static void main(String[] args) {
        Student student = new Student();
        exam01.Employee employee = new exam01.Employee(); // compile Error
    }
}
```
> 참고: exam01.Employee 클래스에 디폴트 생성자가 없어 컴파일 에러가 발생한다.

## 사용자 정의 생성자(User Defined Constructor)
- 클래스 필드의 초기화를 위해서 정의하는 생성자를 사용자 정의 생성자(User Defined Constructor)라 한다.
- 사용자 정의 생성자는 해당 클래스가 갖는 필드의 초기화를 위해 정의하며 여러 형태로 정의가 가능한다.
- 만일, 사용자가 하나 이상의 사용자 정의 생성자를 만들었다면 **디폴트 생성자**는 자동으로 생성되지 않음!

```java
public class Student {
    
    private int id;
    private String name;
    
    public Student(int id) { // User Defined Constructor
        this.id = id;
    }
    
    public Student(int id, String name) {
        this.id = id;
        this.name = name;
    }
}
```

## 생성자 오버로딩(Constructor Overloading)
- 클래스 필드의 초기화하는 다양한 방식으로 이루어질 수 있으며 이를 위해 다양한 형태의 생성자를 정의할 수 있따.
- 생성자의 이름은 반드시 클래스의 이름과 동일해야 하기 떄문에, 다수의 생성자 정의는 생성자 오버로딩으로 정의한다.
- 생성자 오버로딩은 해당 클래스를 구성하는 생성자들의 매개변수 개수와 타입, 순서를 달리하여 정의한다.
- 객체의 인스턴스화 과정에서 생성자를 호출하여 이때 전달인자를 다르게 하여 필요한 생성자를 호출한다.

``` java
class Customer {

    private String name;
    
    private int age;

    public Customer() {
        System.out.println("Customer() 생성자 호출");
    }
    public Customer(String name) {
        System.out.println("Customer(String name) 생성자 호출");
    }
    public Customer(String name, int age){
        System.out.println("Customer(String name, int age) 생성자 호출");
    }
}

public class Test {
    
  public static void main(String[] args) {
      Customer customer1 = new Customer();
      Customer customer2 = new Customer("춘식이");
      Customer customer3 = new Customer("라이엇", 23);
  }
  
}
```

``` console
Customer() 생성자 호출
Customer(String name) 생성자 호출
Customer(String name, int age) 생성자 호출
```

## this() constructor
- 하나의 클래스에 정의된 다수의 생성자 간에 this() 생성자를 통해 호출이 가능하다.
- this() 생성자는 중복되는 코드를 제거하고 생성자를 재사용하기 위해 사용한다.
- this() 생성자의 호출은 반드시 생성자 이름의 바로 아래의 위치해야 한다.
``` java
class exam01.Employee {

    private String id;
    private String name;
  
    public exam01.Employee() {}
  
    public exam01.Employee(String id){
        this.id = id;
        System.out.println("exam01.Employee(id) 호출");
    }
  
    public exam01.Employee(String id, String name){
        this(id);
        this.name = name;
        System.out.println("exam01.Employee(id, name) 호출");
    }
}

public class Test {
    
    public static void main(String[] args) {
        exam01.Employee employee = new exam01.Employee("001", "춘식이");
    }
}

```

``` console
exam01.Employee(id) 호출
exam01.Employee(id, name) 호출
```

## Java 메모리 구조
### Java의 JVM이 관리하는 메모리 공강은 크게 3가지 영역으로 나눌 수 있다.
- 스태틱 영역(Static Area) 또는 메소드 영역 : 메소드의 바이트 코드, static 변수가 할당 된다.(Stack Frame에 저장됨.)
- 스택 영역(Stack Area) : 지역 변수(Local Variable), 매개 변수(Parameter)가 할당되는 영역으로 초기화가 진행되지 않는다.
- 힙 영역(Heap Area) : 배열과 모든 인스턴스 객체가 할당되는 영역으로 자동 초기화가 진행된다.
- Static Data 세그먼트, Code 세그먼트

## static과 final
- static과 final 키워드는 클래스, 필드, 메소드에 적용할 수 있는 키워드이며 각 위치에 따라 다른 의미를 갖는다.
- static 키워드는 정적 키워드로 정적 필드, 정적 메소드를 선언할 떄 사용한다.
- final 키워드를 필드에 정의할 경우 초기 한번의 초기화만 가능하여 이후에는 다른 값을 대입할 수 없다.
- static, final 키워드가 어느 위치에 있느냐에 따라 그 기능이 다른 만큼 정확히 이해하고 사용해야 한다.

```java
public class StaticExam {
    private static String message;

    static {
        message = "Public Message~~";
    }

    public static void showMessage() {
        System.out.println(message);
    }
}
```

```java
public class FinalExam {
    
    private final String message;
  
    public FinalExam() {
        this.message = "Final Message";
    }
  
    public final void showMessage() {
        System.out.println(message);
    }
  
    public void showMessage(final String message) {
        //message = "New Message!";
        //Cannot assign a value to final variable 'message'
        System.out.println(message);
    }
}
```

### final
- final 키워드는 클래스, 필드, 메소드, 지역변수, 파라미터에 적용할 수 있다.
- 클래스에 final은 상속을 허용하지 않으며 메소드의 final은 오버라이딩(overriding) 금지를 의미함.
- 필드, 지역변수, 파리미터에 final을 적용하면 한번 초기화 한 이후에는 다른 값으로 변경할 수 없다.
- final 필드의 초기화 방식은 필드 선언 시점의 초기화, 초기화 블록, 생성자를 통한 초기화 방법 3가지가 있다.

```java
public class FinalExam {
    
    private final String message = "Final Message"; // 필드 선언 시점의 초기화(1)
    {
        message = "Final Message"; // 초기화 블록(2)
    }  
  
    public FinalExam() {
        this.message = "Final Message"; // 생성자를 통한 초기화(3)
    }

    public final void showMessage() {
        System.out.println(message);
    }

    public void showMessage(final String message) {
        //message = "New Message!";
        //Cannot assign a value to final variable 'message'
        System.out.println(message);
    }
}
```
> 참고: 초기화는 한번만 가능하며 초기화 방법 3가지 있다.    
> 초기화 한번 했다면 다시 초기화하면 에러가 발생.

### static field
- static 키워드가 적용된 필드를 정적 필드 혹은 클래스 변수라고 한다.
- 전적 필드는 해당 클래스의 모든 인스턴스 객체들이 공유하는 변수이며 이런 의미가 바로 클래스 변수이다.
- 정적 필드는 객체의 인스턴스화(생성) 없이 클래스 이름으로 정적 필드에 접근할 수 있다.
  - 단, 해당 정적 필드의 접근지정자가 무엇인지에 따라 접근 방식에 차이가 있다.

### 사용자 정의 상수
- 전적 필드에 final 키워드를 적용하여 값을 변경할 수 없도록 하는 것으로 사용자 정의 상수를 정의할 수 있다.
- 사용자 정의 상수는 정적 필드나 메소드의 접근과 마찬가지로 클래스 이름을 통해 접근하여 사용한다.
- 사용자 정의 상수는 접근 지정자의 범위에 따라 공유하는 범위가 결정된다.

#### Math.PI
``` java
public final class Math {
    . . .
    public static final double PI = 3.141592653589793;
    . . .
}
```

### static method 
#### 정적 메소드는 static으로 선언된 메소드로써 인스턴스 없이도 호출할 수 있다.
- 정적 메소드는 인스턴스 필드에는 접근할 수 없고, 정적 필드에만 접근할 수 있다.
- 정적 메소드는 객체를 통해 사용될 수 있지만, 반드시 클래스명과 함께 사용해야 한다.
#### 정적 메소드 예시
- exam01.Employee 클래스의 정적필드

``` java
public class exam01.Employee {

    private static int nextId = 1;
    private int id;

    public static int getNextId() {
        return nextId;
    }
}
``` 

``` java
public class Test {

    public static void main(String[] args) {
        int nextId = exam01.Employee.getNextId();
        System.out.println(nextId);
    }
}
``` 

#### 정적 메소드는 언제 사용할까?
- 객체의 상태에 접근하지 않고, 필요한 파리미터가 모두 명시적 파리미터인 경우 (예: Math.pow)
- 클래스의 **정적 필드**에만 접근하는 경우 (예: exam01.Employee.getNextId())

```java
public class StaticExam {
    
    private static String staticMessage = "Static Message";

    private String instanceMessage = "Instance Message";
    
    
    public void showInstanceMessage() {
        System.out.println(instanceMessage);
        System.out.println(staticMessage); // O
        StaticExam.showStaticMessage(); // O
    }

    public static void showStaticMessage() {
        System.out.println(staticMessage);
        //System.out.println(instanceMessage);  X
        //this.showStaticMessage();  X
        new StaticExam().showInstanceMessage(); // O
    }

}
```

### Reference

- [자바 기초 - 나무소리](https://www.youtube.com/@namoosori/playlists)

