# 클래스의 관계

## 상속(Inheritance)
### 이해(1)
- 상속은 연관 있는 클래스들에 대해 공통적인 구성요소를 정의하고, 이를 대표하는 클래스를 정의하는 것을 의미한다.
- 상속 관계는 "is a" 관계를 의미하며 **extends** 키워드를 이용해 상속 관계를 정의한다.
- 상속 관계에서 상속을 받는 클래스를 sub class, derived class, extended class, child class라 한다.
- 상속 관계에서 상속을 제공하는 클래스를 super class, base class, parent class라 한다.

``` java
public class C extends P {
    
    // 필드 및 메소드 정의
}
``` 
### 이해(2)
- 자식 클래스는 부모클래스를 상속받아서 부모클래스의 모든 자원 속성, 메소드을 사용할 수 있다.
- 자식 클래스는 부모클래스에 없는 필드와 메소드를 정의하여 기능을 추가할 수 있다.
- 또한, 상위클래스에 정의된 메소드를 재정의하여 다르게 동작시킬 수 있다. (오버라이딩)

```java
public class Employee {

    private String name;
    private double salary;

    public Employee(String name) {
        this.name = name;
    }

    public double getSalary() {
        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }
}
```
```java
public class Manager extends exam01.Employee {

    private double bonus;
    
    public Manager(String name) { // 추가적인 필드 정의
        super(name);
    }
    
    public void setBonus(double bonus) { // 추가적인 메소드 정의
        this.bonus = bonus;
    }
    
    public double getSalary() { // 메소드 재정의 (Override)
        return super.getSalary() + bonus;
    }
}
``` 

### protected
- 상속 대상이 되는 부모 클래스에 **protected** 접근 지정자로 정의된 구성요소는 자식 클래스 구성요소가 된다.
- 자식 클래스는 부모 클래스의 protected, public 구성요소에 대해 **this** 접근이 가능하다.
- 즉, 상속 관계에서 자식 클래스는 부모 클래스에서 제공하는 구성요소들을 자신의 구성요소로 포함한다.
- 부모 클래스의 private 구성요소는 자식 클래스에서 직접 접근할 수 없다.

```java
import java.awt.*;

class Shape {

    protected int x;
    protected int y;
    protected Color color;
    
    public void draw() {
        System.out.println("Drawing exam01.Shape~");
    }
}

public class Rectangle extends exam01.Shape {

    private int width;
    private int height;
    
    public void resize(int width, int height) {
        this.width = width;
        this.height = height;
    }
    
    public void move(int x, int y) {
        this.x = x;
        this.y = y;
    }

}
```

### 생성자 호출
- 상속 관계에서 자식 클래스를 인스턴스화 하면 부모 클래스의 객체도 인스턴스화가 진행된다.
- 자식 클래스의 객체가 인스턴스화 되기 위해서는 먼저 부모 클래스의 객체가 인스턴스화 되어야 한다.
- 따라서, 상속 구조에서 가장 상위의 부모 클래스부터 차례로 인스턴스화가 진행된다.

```java
class Art {
    public Art() {
        System.out.println("Art 호출");
    }
}

class Drawing extends Art {
    public Drawing() {
        System.out.println("Drawing 호출");
    }
}

class Cartoon extends Drawing {
    public Cartoon() {
        System.out.println("Cartoon 호출");
    }
}

public class InheritanceAssist {

    public static void main(String[] args) {
        Cartoon cartoon = new Cartoon();
    }
}
```

``` console
Art 호출
Drawing 호출
Cartoon 호출
```

### super()
- super() 생성자는 자식 클래스에서 명시적으로 부모클래스의 생성자를 호출할 수 있도록 하는 방법이다.
- 상속 관계에서 부모 클래스의 생성자 호출을 외부에서 명시적으로 지정할 수 없다.
- 자바는 자식 클래스의 객체가 인스턴스화 될 때 기본적으로 부모 클래스의 디폴트 생성자를 호출한다.
- 부모 클래스에 디폴트 생성자가 정의되어 있지 않으며 자식 클래스는 명시적으로 부모 클래스의 생성자를 호출해야 한다.

```java
package exam02;

class Employee {
    
    private String name;

    public Employee(String name) {
        this.name = name;
    }

    // public Employee() {} (1)
}
public class Developer extends Employee{
    
    private double salary;

    // (1) 디폴트 생성자가 정의되어 있지 않으며 명시적으로 부모 클래스 생성 호출해야 함.
    public Developer(String name) {
        super(name);
    }

    public double getSalary() {
        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }
}
```

## 메소드 재정의(overriding)

### 개요
- 메소드 재정의(overriding)는 부모 클래스의 메소드를 자식 클래스가 확장하거나 다시 정의하는 것을 의미한다.
- 메소드 재정의를 구현하는 방법은 부모 클래스로부터 상속받은 **메소드의 반환타입, 메소드명, 파라미터를 동일하게** 하여  
  자식 클래스에서 정의한다.
- 자식 클래스가 부모 클래스의 메소드를 재정의 할 때 접근 지정자의 범위는 **넓거나 같아야** 한다.


| 부모 클래스           |           자식 클래스           |
|:-----------------|:--------------------------:|
| private          |           재정의 불가           |
| protected        |     protected, public      |
| package(default) | package, protected, public |
| public           |           public          |

```java
class Shape {
    
    public void draw(){
        System.out.println("Drawing Shape~");
    }
}

public class Rectangle extends Shape{

    @Override
    public void draw() {
        System.out.println("Drawing Rectangle~");
    }
}
```

### super
- 메소드 재정의는 부모 클래스로부터 상속 받은 기능을 새롭게 변경하거나 확장하기 위해서 입니다.
- 자식 클래스에서 상속 받은 메소드의 기능을 확장하기 위해서는 부모 클래스의 메소드에 대한 호출이 필요하다.
- 자식 클래스가 부모 클래스의 구성 요소에 접근하기 위해서는 super 키워드를 이용한다.

```java
class Shape {
    
    public void draw(){
        System.out.println("Drawing Shape~");
    }
}

public class Rectangle extends Shape{

    @Override
    public void draw() {
        super.draw();
        System.out.println("Drawing Rectangle~");
    }

    public static void main(String[] args) {
        Shape shape = new Shape();
        shape.draw();

        System.out.println();

        Shape rect = new Rectangle();
      rect.draw();
    }
}
```

``` console
Drawing Shape~

Drawing Shape~
Drawing Rectangle~
```

## 객체간 타입 형변화
### Strongly typed language
- Strongly typed language 개념은 데이터의 타입을 미리 정의하고 사용 하고, 한번 정의된 데이터 타입은 프로그램 종료 까지  
  변하지 않는 것을 의미한다.
- 이 개념은 프로그램 개발에 있어 명확함과 구체성을 제공하며 많은 프로그램 언어들이 적용하고 있다.
- 다만, 프로그램의 유연성 제약이라는 단점을 갖고 있으며 반대의 개념이 Loosely typed language 이다.

``` java
int x = 10; // assign
int y = 20; // assign

public void sum(int x, int y) {
    ...
}
```

- 자바에서 예외적으로 Strongly typed language가 적용되지 않는 경우가 있다.
- 상속 관계에서 자식 클래스가 부모 클래스 타입으로 참조되는 것이 허용되며 이를 uo-casting이라고 한다.
- 한번 부모 클래 타입의 클래스로 참조가 이러우진 이후 다시 자식 클래스로 참조하는 것을 down-casting이라고 한다.

```java
public class Shape {

    public void draw(){
        System.out.println("Drawing Shape~");
    }
}
``` 

```java
public class Rectangle extends Shape{

    @Override
    public void draw() {
        super.draw();
        System.out.println("Drawing Rectangle~");
    }
}
```
```java
public class Ellipse extends Shape{

    @Override
    public void draw() {
        System.out.println("Drawing Ellipse~");
    }
}
```
```java
public class Line extends Shape{

    @Override
    public void draw() {
        System.out.println("Drawing Line~");
    }
}
```
```java
public class InheritanceAssist {

    public static void main(String[] args) {
        Shape shape = new Rectangle(); // up-casting
        Rectangle rect = (Rectangle) shape; // down-casting
    }
}
```

## 3.7 Object 클래스

### Object 클래스 - 개요
- Object 클래스는 모든 자바 클래스가 상속하는 최상위 클래스 이다.
- Object 클래스는 모두 11개의 메소드를 정의하고 있으며 이 메소드들은 자바의 모든 클래스가 갖는 기능이다.
- 자바의 모든 클래스가 상속 받아 갖는 Object 클래스 메소드에 대한 목적과 기능을 이해하는 것이 중요하다.
- Object 클래스를 통해 상속 받는 메소드의 의미를 이해해야 그 의미에 맞게 재정의 할 수 있다.

``` java
public class EmptyClass {
    
}
```
``` java
public vlass ObjectExam {
    
    public static void main(String[] args) {
        EmptyClass empty = new EmptyClass();
        empty.toString();
        empty.hashCode();
    }
    
}
```

### Object 클래스 - toString 메소드
- toString 메소드는 해당 클래스에 대한 설명을 문자열 타입으로 반환하는 메소드 이다.
- 자바의 모든 클래스는 스스로에 대한 정보를 읽기 쉬운 형태로 제공 할 수 있으며 이 기능이 toString() 메소드 이다.
- 클래스를 정의할 때 그 클래스를 나타내는 주요 정보를 이용해 toString() 메소드를 재정의 한다.

``` java
public class Person {

    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Name:" + name + ", " + "Age:" + age;
    }
}
```
``` java
public class ObjectExam {

    public static void main(String[] args) {
        Person person = new Person("Kim", 25);
        System.out.println(person.toString());
    }
}
``` 

``` console
Name:Kim, Age:25
```

### Object 클래스 - euqals, hashCode 메소드
- 자바의 모든 클래스는 비교가 가능해야 하며, 비교는 해당 클래스의 속성을 기준으로 한다.
- 자바에서 비교는 동일(identity) 비교와 동등(equality) 비교로 구분 된다.
- equals() 메소드는 인스턴스 객체와 파라미터로 전달되는 객체를 같은지 비교하여 결과를 반환한다.
- hashCode() 메소드는 객체를 식별하는 정수값을 의미하며 정수값을 반환하는 메소드이다.

``` java
import java.util.Objects;

public class Person {

    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Person)) return false;
        Person person = (Person) o;
        return Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name);
    }
}
``` 

### Object 클래스 - clone 메소드 [1/2]
- clone() 메소드는 인스턴스 객체의 복제를 위한 메소드로 해당 인스턴스 객체를 복사하여 그 참조값을 반환한다.
- 클래스의 복제를 가능하게 하기 위해서는 Cloneable 인터페이스를 재정의 해야 한다.
- Cloneable 인터페이스를 구현(implements) 하지 않은 클래스의 인스턴스의 clone() 메소드를 호출하면  
  CloneNotSupportedException 예외가 발생한다.

``` java
public class User implements Cloneable {

    private  String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public Object clone() throws CloneNotSupportedException{
        return super.clone();
    }
}
```
### Object 클래스 - clone 메소드 [2/2]
- 객체의 복사는 단순히 같은 참조 정보를 복사하는 형태와 동일한 객체의 인스턴스를 복사하는 것의 차이를 이해해야 한다.
- Object 클래스의 clone() 메소드는 protected 접근 권한을 갖고 잇으며 이를 재정의하는 클래스를 이를 public 접근  
  권한으로 재정의하여 어디서나 복제가 가능하도록 한다.
- 객체의 복제는 얕은 복제(Shallow Copy), 깊은 복제(Deep Copy)가 있으며 이를 유의한다.

![](https://github.com/dididiri1/java_basic/blob/main/study/images/03_02.png?raw=true)


## 추상 클래스와 인터페이스

### 추상 클래스(abstract class)의 이해
- 추상 클래스는 하나 이상의 추상 메소드(absract method)를 갖는 클래스 이다.
- 상속 관계에서 부모 클래스의 역활을 갖기 위한 클래스이며 추상 메소드와 일반 메소드를 가질 수 있다.
- 추상 메소드는 메소드의 몸체(body)가 없는 메소드이며 자식 클래스에서 재정의 하도록 하기 위한 메소드 이다.
- 추상 클래스는 new 동적 할당자를 통해 인스턴스 객체를 만들 수 없다.

``` java
abstract class Shape {

    private String type;

    public Shape(){
        System.out.println("부모 초기화");
    }

    public Shape(String type) {
        this.type = type;
    }

    public abstract  void draw();
}

public class AbstractAssist extends Shape {

    @Override
    public void draw() {
        System.out.println("재정의!");
    }
    
}
```

### 인터페이스(Interface)의 이해 - 개요
- 인터페이스는 일반적으로 **추상 메소드**만 가지며 interface 키워드를 이용해 정의한다.
- 특정 클래스가 인터페이스를 구현하기 위해서는 implements 키워드를 통해 구현한다.
- 상속과 달리 인터페이스는 하나의 클래스가 둘 이상의 인터페이스를 동시에 구현할 수 있다.
- 인터페이스를 통해 설계와 구현을 완전히 분리할 수 있다.

``` java
public interface IBehavior {
    
    // public abstract 생략 가능
    public abstract void play();
}
```
``` java
public class Soccer extends Sport implements IBehavior {

    @Override
    public void play() {
        System.out.println("Playing Soccer~~");
    }
}
``` 

### 인터페이스(Interface)의 이해 - 특징
- 인터페이스에는 필드, 추상메소드, static 메소드, default 메소드를 정의할 수 있다.
- 인터페이스에 정의하는 모든 필드는 public static fianl(사용자 정의 상수)이 자동으로 적용된다.
- java 8부터 인터페이스에 static 메소드를 추가할 수 있으며 static 메소드의 사용은 일반 클래스와 동일하다.
- java 8부터 default 메소드가 추가되었으며 이 메소드는 그 자체로 완전한 메소드이며 구현 클래스는 선택적으로 재정의할 수 있다.

``` java
public interface MyInterface {

    // public static final 생략 가능
    public static final String MESSAGE = "User define constant";
    
    default void defaultMethod(){
        System.out.println("Default Method~~");
    }
    
    public static void staticMethod(){
        System.out.println("Static Method~~");
    }
    
    // 추상메소드는 반드시 재정의됨.
    public abstract void play();
}
```

### 인터페이스의 활용 (1/2) - 다형성
- 자바의 상속 구조는 단일 상속의 원칙을 갖기 때문에 하나의 클래스가 여러 부모 클래스를 상속할 수 없다.
- 서로 다른 부무 클래스를 갖는 클래스간에도 같은 인터페이스를 구현할 수 있다.
- 같은 인터페이스를 구현하고 있는 클래스간에는 그 인터페이스로 하여금 대표성을 갖게 할 수 있다.

### 인터페이스의 활용 (2/2) - 인터페이스 역할
- 하나의 프로그램의 다수으 ㅣ클래스들이 서로 관계를 형성하게 되고 각 클래스들은 역할에 따라 구분한다.
- 클래스간에 관계를 밀접하게 구성하게 되면 특징 클래스에서 변경이 일어날 경우 많은 클래스들이 영향을 받는다.
- 따라서, 클래스 간에 관계를 구성할 때 그 관계를 느슨하게 관리하는 것이 중요하다.
- 클래스와 클래스 사이에 인터페이스를 구성하면 직접적인 접근이 없는 느슨한 관계를 구성할 수 있다.

![](https://github.com/dididiri1/java_basic/blob/main/study/images/03_03.png?raw=true)

> final 클래스 상속 금지!
> final 메소드 재정의 금지!
> 

## 3.11 배열

### 배열[1/4]

#### 배열은 동일한 타입의 값들을 저장할 수 있는 자료구조이다.
#### 배열에 담기는 각각의 값들은 인섹스 번호를 통해 접근할 수 있다.
#### 배열의 선언
- 문법: 배열 요소의 타입[] 배열명;
- 선언 예 : int[] a; 또는 int a[];


#### 배열의 생성
- 문법 : 변수명 = new 배열 요소의 타입[요소 수];
- 배열 나타내는 변수에 새로운 배열을 생성하여 할당한다.
  - int[] a;
  - a = new int[100];
#### 배열 선언과 동시에 새로운 배열을 생성할 수도 있다.
  - int[] a = new int[100];


### 배열[2/4]

#### 배열 초기화
- 자바는 배열 생성과 동시에 값을 초기활 수 있는 문법을 제공한다. (배열 선언 시에만 사용)
``` java
int[] months = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12};

int[] months;
months = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12}; (x)
``` 

#### 배열 복사
- 배열 변수를 복사하는 경우, 참조변수만 복사되어 실제로는 두 변수가 동일한 배열을 참조한다.
``` java
int[] a = new int[5];
int[] b = a;
b[0] = 5; // a[0]의 값도 변경된다.
``` 
- Arrays.copyOf() 메소드는 배열의 내용까지 복사한다.
``` java
int[] copy = Arrays.copyOf(a, a.length);
``` 
- 배열 복사와 동시에 배열의 크기를 늘릴 수 있다.
``` java
int[] copyDoubleSize = Arrays.copyOf(a, 2 * a.length);
``` 

### 배열[3/4]
#### 배열 정렬(Sorting)
- Arrays.sort() 메소드는 배열 내부의 요소(element)를 정렬 한다.  
``` java
import java.util.Arrays;

public class ArraySortTest {

    public static void main(String[] args) {
        int[] a = {2, 8, 6, 3, 1, 10};
        Arrays.sort(a);

        System.out.println(Arrays.toString(a));
    }
}

``` 
``` console
[1, 2, 3, 6, 8, 10]
``` 

#### 다차원 배열
``` java
double[][] balances;
balances = new double[12][10];
int[][] matrix = {
              {1, 5, 2, 8},
              {2, 7, 1, 9},
              {7, 2, 6, 2},
              {9, 1, 4, 8}
          };

``` 

### 배열[4/4]
- 배열과 같이 집합을 나타내는 자료형은 요소를 반복하기 위하여 반복문을 사용한다.
- 배열 요소의 인덱스 값은 0부터 시작한다.
- for each 문은 집합의 요소를 처음부터 끝까지 반복 한다.

``` java
int arr[] = {1, 5, 2, 8};

// 확장 for문
for (int num : arr) {
    System.out.println(num);
}

for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}
``` 