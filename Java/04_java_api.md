# 4. Java API

> 4.1 문자열 클래스
> 4.2 Wrapper 클래스
> 4.3 예외처리
> 4.4 Java Collection Framework의 이해
> 4.5 제네릭의 이해
> 4.6 Collection 인터페이스
> 4.7 List 인터페이스
> 4.8 Set 인터페이스
> 4.9 Map 인터페이스
> 4.10 Iterator 인터페이스

## 4.1 문자열 클래스

### 문자열 클래스 - String 클래스
- 자바는 문자열 관리하기 위한 여러 클래스를 제공하고 있으며 대표적인 클래스가 바로 String 클래스 이다.
- String 클래스는 문자열 제어를 위한 다수의 메소드를 정의하고 있다.
- 프로그램내에 빈번하게 발생하는 문자열 처리를 위해서는 String 클래스의 주요 기능들에 대한 이해와 특성에 대한  
  이해가 필요하다.

```java
public class StringTest {

    public static void main(String[] args) {
        // Creating Strings
        String str1 = "Java Programming";
        String str2 = new String("Java Programming");
        char[] charAry = {'J', 'A', 'V', 'A'};
        String str3 = new String(charAry);

        // String's Methods
        System.out.println(str1.length());
        System.out.println(str1.toUpperCase());
        System.out.println(str2);
        System.out.println(str3);
    }
}
``` 

``` console
16
JAVA PROGRAMMING
Java Programming
JAVA
```

### 문자열 클래스 - String 클래스 문자 체계
- String 클래스는 내부적으로 byte[]을 이용해 문자열을 관리한다. (java 8까지 char[])
- 자바의 문자형 체계는 UTF-16 코드 체계를 채댁하고 있으며 이는 2byte 기반의 문자 체계이다.
- 1byte로 표현 가능한 문자의 경우 남은 1byte에 대한 메모리 낭비가 발상하게 되면 이를 개선하기 위해 java 9부터  
  Compact Strings 개념이 도입이 된다.

### 문자열 클래스 - String 클래스 특징
- String 클래스의 기능을 사용할 때 String 클래스의 특징을 이해하고 사용하는 것이 필요함.
- String 객체의 초기화 방식은 **리터럴**을 이용한 방식과 **객체 생성**을 통한 초기화 방식 2가지 이다.
- String은 불변(immutability)의 특성을 갖고 있으며 내부적으로 String Pool을 통해 문자열 상수를 관리한다.
- 문자열의 변경이 빈번한 로직에서 String의 사용은 메모리 누수(memory leak)이 발생할 수 있기 떄문에 주의해야한다.

```java
public class StringTest {

    public static void main(String[] args) {
        String str1 = "Java Programming";
        String str2 = new String("Java Programming");
        String str3 = "Java Programming";
    
        System.out.println(str1 == str2);
        System.out.println(str1 == str3);

        System.out.println();
  
        String str = "Java";
        str = str.concat(" Programming");
        System.out.println(str.toString());
        
    }
}
```
``` console
false
true

Java Programming
```

### 문자열 클래스 - StringBuilder, StringBuffer 클래스
- StringBuilder와 StringBuffer 클래스는 동기화(syschronized) 특성을 제외하고 모든 메소드의 기능이 동일하다.
- 이 두 클래스는 String 클래스와 달리 가변(mutable)의 특성을 갖고 있다.
- StringBuilder, StringBuffer 두 클래스는 AbstractStringBuilder 추상 클래스를 상속하고 있으며 이 클래스는 내부 적으로  
  문자열 관리하기 위한 byte[]과 배열의 길이를 계산히기 위한 count 속성을 갖고 있다.


### 문자열 클래스 - StringBuilder, StringBuffer 클래스의 차이
- StringBuilder 클래스와 StringBuffer 클래스의 유일한 차이는 동기화 처리 여부이다.
- StringBuffer 클래스는 멀티스레드(Multi Thread) 프로그램에서 데이터에 대한 동기화 문제가 발생하지 않도록 대두분의  
  메소드에서 동기화 처리를 하고 있다.
- 이런 동기화 처리 과정은 성능에 영향을 주기 때문에 단일스레드(Single Thread) 프로그램에서는 StringBuilder 클래스를 사용한다.

### 문자열 클래스 - + 연산자
- 자바의 + 연산자는 피연산자 문자열일 경우 두 피연산자을 문자열로 연결해 주는 기능을 갖고 있다.
- 문자열의 연결은 String 클래스의 concat(), StringBuilder 클래스의 append()와 동일한 기능이다.
- java 8까지 문자열에 대한 + 연산은 StringBuilder 클스의 append() 기능을 이용해 수행한다.
- java 9부터 StringConcatFactory라는 새로운 클래스의 기능을 이용해 문자열에 대한 + 연산을 진행한다.

``` java
String str = new String("Java");
str = str.concat("Programming");
str = str + "Programming";
```

### Wrapper 클래스(1/2) - Wrapper 클래스의 이해
- 자바프로그램에서 관리하는 데이터의 기본 단위는 객체이다.
- 자바에서는 int, double 등과 같은 기본 데이터 타입(Primitive Data Type)들은 객체로 관리 할 수 있도록 하는 클래스들을  
  제공하며 이 클래스들을 Wrapper 클래스라고 한다.
- Wrapper 클래스들은 기본 데이터 타입에 대한 객체화와 함께 다양한 기능들을 정의하고 있다.



| Primitive Type           |           Wrapper Class           |
|:-----------------|:--------------------------:|
| boolean          |           Boolean           |
| byte          |           Byte           |
| short          |           Short           |
| int          |           Integer           |
| char          |           Character           |
| long          |           Long           |
| float          |           Float           |
| double          |           Double           |


### Wrapper 클래스(2/2) - Auto Boxing, Unboxing
- 기본 데이터 타입을 Wrapper 클래스로 감싸거나 Wrapper 클래스가 갖고 있는 기본 데이터 타입을 다시 꺼내는 과정은  
  Auto Boxing, Unboxing 기능을 통해 손쉽게 구현할 수 있다.
``` java
Integer intWrap = Integer.valueOf(10);
int number = intWrap.intValue();

Integer intWrap = 10; // Auto Boxing
int number = intWrap; // Unboxing
``` 

- Wrapper 이외에도 실수 계산과 같은 오차가 발생할 수 있는 연산에는 BigInterger, BigDecimal과 같은 클래스의 기능을 사용한다.
``` java
double da = 3.14;
System.out.println(da + 1); // 4.140000000000001

System.out.println(BigDecimal.valueOf(3.14).add(BigDecimal.valueOf(1))); // 4.14
```

### Java Collection Framework의 이해(1/3)
- java Collection Framework는 객체들을 관리하기 위해 사용하는 컨터에너 클래스들의 집합이다.
- 컨테이너 클래스들의 종류는 크게 List, Set, Queue, Map 계열로 구분한다.
- 각 계열에 따라 객체들을 저장하고 관리하는 방식에 차이가 있다.
- 컬렉션 프레임워크 클래스들을 잘 활용하기 위해서는 각 클래스들의 특징과 저장 방식에 대한 이해가 필요힘.

### Java Collection Framework의 이해(2/3)
#### 컬렉션 관련 인터페이시들과 클래스들은 java.utll.* 패키지(pacckage)에 포함되어 있다.
- Collection interface : 순서 없는 객체들의 집합이다.
- List interface : 순차적 나열로 순서 지정이 가능한 객체들의 집합이다.
- Set interface : 중복을 허용하지 않는 객체들의 집합이다.
- Queue interface : FIFO(First In First Out) 처음 저장하는 객체가 가장 먼저 나오는 구조의 집합이다.
- Map : 키와 그 키에 대응하는 객체들로 이루어진 집합이다.

![](https://github.com/dididiri1/TIL/blob/main/java/images/04_01.png?raw=true)

| 인터페이스 |                구현클래스                | 특징                                                                                               |
|:------|:-----------------------------------:|:-------------------------------------------------------------------------------------------------|
| Set   |         HashSet<br/>TreeSet         | 순서를 유지하지 않는 데이터의 집합으로 데이터의 중복을 허용하지 않는다.                                                         |
| List  | LinkedList<br/>Vector<br/>ArrayList | 순서가 있는 데이터의 집합으로 데이터의 중복을 허용한다.                                                                  |
| Queue |    LinkedList<br/>PriorityQueue     | List와 유사                                                                                         |
| Map   |  Hashtable<br/>HashMap<br/>TreeMap  | 키(Key), 값(Value)의 쌍으로 이루어진 데이터으 집합으로,<br/> 순서는 유지되지 않으며 키(Key)의 중복을 허용하지 않으나 값(Value)의 중복은 허용한다. |


### Java Collection Framework의 이해(3/3)
#### 저장 관리 하고자 하는 객체들의 특성에 따라 사용할 컬렉션 클래스를 선택한다.
#### 컬렉션 클래스를 선택할 때 필요한 대표적인 고려사항은 다음과 같다,
- 저장 객체의 추가, 삭제와 같은 데이터의 변경이 자주 발생 하는지
- 저장 객체의 빠른 탐색이 최우선인지 필요한지
- 저장 메모리를 최대한 효율적으로 사용하고자 하는지


### 제네릭의 이해(1/2) - 개요제네릭의 이해(1/2) - 개요
- 제네릭(Generic)은 java 5에서 추가된 기능으로 특히 객체를 수집, 관리하는 컬렉션을 이용할 떄 반드시 사용한다.
- 제네릭을 사용하면 데이터를 저장하는 시점에 어떤 데이터를 저장할 것인지 명시할 수 있다.
- 이를 통해 사용하고자 하는 데이터의 타입을 명확히 선언할 수 있고, 정확한 데이터의 사용 여부를 컴파일 시점에 확인할 수 있다,

``` java
public class Box<T> {

    private T item;
    
    public Box(T item){
        this.item = item;
    }
    
    public T getItem(){
        return item;
    }
}
```
``` java
Box<Apple> box = new Box(new Apple(10));
Apple apple = box.getItem();
System.out.println(apple.getSugarContent());
``` 

### 제네릭의 이해(2/2) - 제네릭과 컬렉션
- Object 클래스는 최상위 클래스로서 java의 모든 클래스를 참조 할 수 있다.
- 모든 클래스를 참조 할 수 있다는 것은 분명 편리할 수 있지만 오류를 발생시킬 수 있는 여지 또한 크다.
- 예를 들어, Object 배열에 다양한 객체의 참조를 넣었을 때를 생각해 볼 수 있다.
- 객체의 구분없이 배열을 담을 수 있다는 것은 담을 떄의 편리성은 있지만 다시 꺼낼 때는 문제가 발생한다.

### Collection 인터페이스(1/2)
#### java.util.Collection 인터페이스
- 컬렉션 프레임워크의 최상위 인터페이스 이다.
- 요소(객체)에 대한 삽입, 삭제, 탐색의 기능을 정의한다.

#### 주요 메소드
- add() : 새로운 요소를 삽입한다. 중복 요소를 허용하지 않는 경우 false를 반환함.
- clear() : 모든 요소를 제거한다.
- contains() : 파라미터로 전달되는 객체가 요소로 존재하는지 반환함.
- isEmpty() : 해당 컬렉션이 포함하고 있는 요소가 0인지를 반환함.
- remove() : 파라미터로 전달되는 객체를 제거한다. 전달되는 객체가 요소로 존재하지 않다면 false를 반환함.
- size() : 현재 포함하고 있는 요소의 개수를 반환한다.
- iterator() : 해당 컬렉션이 포함하고 있는 요소들을 순회하기 위한 iterator 객체를 반환한다.
#### Java 8 버전 이후 Stream 관련 디폴트 메소드들이 추가 되었다.

### Collection 인터페이스(2/2)
#### java.util.Collection 추가 기능과 같은 주요 메소드는 다음과 같다.
- addAll() : 파라미터로 전달되는 컬렉션의 모든 요소를 추가한다.
- containsAll() : 파라미터로 전달되는 컬렉션의 모든 요소를 현재 포함하고 있다면 true를 반환한다.
- removeAll() : 파라미터로 전달되는 컬렉션의 요소들과 일치하는 요소들을 모두 제거한다.
- retainAll() : 파라미터로 전달되는 컬렉션의 요소들과 일치하지 않는 요소들을 모두 제거한다.

![](https://github.com/dididiri1/TIL/blob/main/java/images/04_02.png?raw=true)

### List 인터페이스(1/5) - 개요
- List 계열의 컬렉션은 저장 요소를 순차적으로 관리하며 중복된 값과 null 값을 요소로 가질 수 있다.
- 요소에 대한 접근은 배열과 마찬가지로 인덱스(index)를 통해 접근한다.
- 배열 자료구조 형태로 데이터를 저장할 때 저장 데이터의 특성에 따라 적절한 List 계열의 클래스들을 활용한다.
- List 인터페이스를 구현한 대표클래스는 ArrayList, LinkedList, Vector 클래스가 있다.

### List 인터페이스(2/5) - ArrayList 클래스(1/2)
- ArrayList 클래스는 내부에 배열을 갖고 있다. 따라서 고정 길이 저장 공간으로 요소들을 관리한다.
- ArrayList 클래스의 내부 배열이 요소를 담을 수 있는 용량을 capacity라고 한다.
- 내부 배열의 용량(capacity)을 넘어 요소를 저장할 경우 내부적으로 용량을 늘린 새로운 배열을 만들어 요소를 담는다.
- 요소에 대한 접근은 배열과 마찬가지로 인덱스(index)를 통해 접근한다.

### List 인터페이스(3/5) - ArrayList 클래스(2/2)
- ArrayList는 순차적으로 요소를 저장할 수 있는 메소드와 인덱스를 통해 저장할 수 있는 add() 메소드를 제공한다.
- 특정 인덱스에 요소를 저장할 경우 기존에 저장된 요소들의 이동이 내부적으로 이루어진다.
- 특정 요소를 삭제하기 위해서는 해당 요소의 인데스가 필요하며 이 경우에도 저장된 요소들의 이동이 발생한다.
- ArrayList가 갖는 이와 같은 특징(용량, 요소의 이동)은 추가, 삭제가 빈번한 데이터의 관리에는 적합하지 않는다.

### ArrayList 클래스의 주요 메소드
| 메서드           |                  설명                  |
|:-----------------|:------------------------------------:|
| boolean add(E e)          |  요소 하나를 배열에 추가한다(E는 요소의 자료형을 의미한다.)  | 
| int size()          |       배열에 추가된 요소 전체 개수를 반환한다.        |   
| E get(int index)          |     배열의 index위치에 있는 요소 값을 반환한다.      | 
| E remove(int index)          | 배열의 index위치에 있는 요소 값을 제거하고 그 값을 반환한다 | 
| boolean isEmpty()          |           배열이 비어 있는지 확인한다.           | 

### List 인터페이스(4/5) - LinkedList 클래스(1/2)
- LinkedList 클래스는 집합하는 각 요소들을 노드(node)로 표현하고, 각 노드들을 서로 연결하여 리스트를 구성한다.
- 요소를 갖는 각 노드들은 다음 노드에 대한 참조 정보를 통해 접근한다.
- 노드는 필요한 경우 만들어서 연결하여 사용하기 때문에, 미리 정의된 용량(capacity)의 개념이 없다.
- List 인터페이스를 구현한 다른 클래스들과 마찬가지로 인덱스를 통해 요소에 접근한다.


### List 인터페이스(5/5) - LinkedList 클래스(2/2)
- 요소의 추가는 곧 노드의 추가를 의미하며, 일반적인 추가(add)는 순차적으로 링크를 연결하며 추가한다.
- 인덱스를 지정해 특정 지점에 요소를 추가할 경우에는 기존 노드의 연결을 끊고 새로운 노드를 연결하여 추가한다.
- 요소의 삭제는 삭제할 노드가 갖고 있는 다음 노드에 대한 포인트를 이전 노드가 포인트를 할 수 있도록 하여 삭제한다.
- ArrayList와 달리 요소의 추가, 삭제에 대한 부하가 적은 반면 메모리의 사용은 더 많다는 특징을 갖는다.

### Set 인터페이스(1/2) - 개요
- Set 인터페이스를 구현한 컬렉션 클래스들의 가장 큰 특징은 저장하는 요소의 중복을 허용하지 않는다는 것이다,
- Set 인터페이스의 구현 클래스들은 equals() 매소드를 이용해 저장 요소의 중복을 검사한다.
- Set 인터페이스가 정의하고 있는 추상 메소드 중에는 단일 요소를 꺼내기 위한 get() 메소드가 존재하지 않는다.
- Set 인터페이스를 구현한 주요 클래스는 HashSet, LinkedHashSet, TreeSet 등이 있다.

![](https://github.com/dididiri1/TIL/blob/main/java/images/04_03.png?raw=true)

### Set 인터페이스(2/2) - 주요 클래스
- Set 인터페이스의 구현체 HashSet은 집합 하는 요소의 중복을 허용하지 않고 입력 순서를 유지하지 않는다.
- HashSet 클래스는 순서에 상관없이 어떤 데이터가 존재하는지의 여부를 확인하는 용도로 많이 사용된다.
- 집합의 요소의 정렬이 필요할 경우 TreeSet이나 LinkedHashSet 클래스를 사용한다.
- Set 계열의 클래스에서 전체 집합 요소를 순회하고자 할 경우 Iterator를 이용한다.

``` java
Random rand = new Random(47);
Set<Integer> intSet = new HashSet<Integer>();

for (int i = 0; i < 10000; i++) {
    intSet.add(rand.nextInt(30));
}

System.out.println(intSet);
``` 
``` console
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 17, 16, 19, 18, 21, 20, 23, 22, 25, 24, 27, 26, 29, 28]
``` 

### Map 인터페이스(1/2) - 개요
- Map 인터페이스가 갖는 대표적인 특성은 요소를 저장하기 위해서는 유일한 키(key)와 함께 저장해야 한다는 것이다.
- List, Set, Queue와 달리 Map 인터페이스는 Collection 인터페이스를 상속하지 않는다.
- Map 인터페이스는 내부에 Entry 인터페이스를 가지고 있으며 Entry는 키와 값을 가진 객체의 순서 쌍이다.
- Map 인터페이스의 주요 구현 클래스는 HashMap, LinkedHashMap, TreeMap 등이 있다.

![](https://github.com/dididiri1/TIL/blob/main/java/images/04_04.png?raw=true)

### Map 인터페이스(2/2) - 주요 클래스
- HashMap에 요소를 저장하기 위해서는 키(key)와 값(value)이 필요하며 값의 중복이 가능하다.
- HashMap 클래스는 저장되는 요소의 순서를 보장하지 않는다.
- LinkedHashMap 클래스는 요소를 추가한 순서를 보장한다.
- TreeMap 클래스는 요소의 키에 대한 정렬된 순서를 보장하며 이를 위해서는 키 객체가 Comparable 인터페이스를 구현하고 있어야 한다.

``` java
import java.util.HashMap;
import java.util.Map;

class Customer {

    private String email;

    private String name;

    public Customer(String email, String name) {
        this.email = email;
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "Customer{" +
                "email='" + email + '\'' +
                ", name='" + name + '\'' +
                '}';
    }
}

public class MapTest {
    
    public static void main(String[] args) {
        Map<String, Customer> map = new HashMap<>();
        Customer kim = new Customer("kim@namoosori.io", "Kim");

        map.put(kim.getEmail(), kim);

        System.out.println(map);
    }
}
```
``` java
{kim@namoosori.io=Customer{email='kim@namoosori.io', name='Kim'}}
```

### Iterator 인터페이스
- Iterator 인터페이스는 컬렉션 객체가 가지고 있는 요소들을 순회할 수 있는 기능들을 명세한 인터페이스 이다.
- Collection 인터페이스를 구현한 클래스들은 iterator() 메소드를 통해 iterator 구현 객체를 반환 한다.
- Map 구현 클래스의 경우 Map.values() 메소드를 통해 값(요소) 들을 Collection 타입으로 겍체를 반환 받은 이후에  
  다시 iterator 구현 객체를 이용하여 순회할 수 있다.

``` java
import java.util.*;

public class IteratorTest {

    public static void main(String[] args) {

        Iterator<Customer> iterator = null;
        
        List<Customer> list = new ArrayList<>();
        list.add(new Customer("kim@namoosori.io", "Kim"));
        list.add(new Customer("lee@namoosori.io", "Lee"));
        list.add(new Customer("park@namoosori.io", "Park"));

        iterator = list.iterator();
        while (iterator.hasNext()) {
            Customer customer = iterator.next();
            System.out.println("customer = " + customer);
        }

        System.out.println();

        Map<String, Customer> map = new HashMap();
        map.put("kim@namoosori.io", new Customer("kim@namoosori.io", "Kim"));
        map.put("lee@namoosori.io", new Customer("lee@namoosori.io", "Lee"));
        map.put("park@namoosori.io", new Customer("park@namoosori.io", "Park"));
        
        Collection<Customer> coll = map.values();
        iterator = coll.iterator();
        while(iterator.hasNext()){
            Customer customer = iterator.next();
            System.out.println("customer = " + customer);
        }
    }
}
``` 
``` java
customer = Customer{email='kim@namoosori.io', name='Kim'}
customer = Customer{email='lee@namoosori.io', name='Lee'}
customer = Customer{email='park@namoosori.io', name='Park'}

customer = Customer{email='lee@namoosori.io', name='Lee'}
customer = Customer{email='park@namoosori.io', name='Park'}
customer = Customer{email='kim@namoosori.io', name='Kim'}
``` 