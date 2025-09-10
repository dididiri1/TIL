# 7. 자바 메모리 구조와 static

## 자바 메모리 구조

![](https://github.com/dididiri1/TIL/blob/main/Java/basicimages/08_01.png?raw=true)

자바의 메모리 구조는 크게 메서드 영역, 스택 영역, 힙 영역 3개로 나눌 수 있다.
- 메서드 영역: 클래스 정보를 보관한다. 이 클래스 정보가 붕어빵 틀이다.
- 스택 영역: 실제 프로그램이 실행되는 영역이다. 메서드를 실행할 때 마다 하나씩 쌓인다.
- 힙 영역: 객체(인스턴스)가 생성되는 영역이다. new 명령어를 사용하면 이 영역을 사용한다. 쉽게 이야기해서 붕어빵 틀로부터 생성된 붕어빵이 
  존재하는 공간이다. 참고로 배열도 이 영역에 생성된다.

방금 설명한 내용은 쉽게 비유로 한 것이고 실제는 다음과 같다.

![](https://github.com/dididiri1/TIL/blob/main/Java/basicimages/08_02.png?raw=true)
- 메서드 영역(Method Area): 메서드 영역은 프로그램을 실행하는데 필요한 공통 데이터를 관리한다. 이 영역은 프로그램의 모든 영역에서 공유한다.
  - 클래스 정보: 클래스의 실행 코드(바이트 코드), 필드, 메서드와 생성자 코드등 모든 실행 코드가 존재한다.
  - static 영역: static 변수들을 보관한다. 뒤에서 자세히 설명한다.
  - 런타임 상수 풀: 프로그램을 실행하는데 필요한 공통 리터럴 상수를 보관한다. 예를 들어서 프로그램에 "hello" 라는 리터럴 문자가 있으면 
    이런 문자를 공통으로 묶어서 관리한다. 이 외에도 프로그램을 효율 적으로 관리하기 위한 상수들을 관리한다. 
    (참고로 문자열을 다루는 문자열 풀은 자바 7부터 힙 영역으로 이 동했다.)
- 스택 영역(Stack Area): 자바 실행 시, 하나의 실행 스택이 생성된다. 각 스택 프레임은 지역 변수, 중간 연산 결과, 메서드 호출 정보 등을 포함한다.
  - 스택 프레임: 스택 영역에 쌓이는 네모 박스가 하나의 스택 프레임이다. 메서드를 호출할 때 마다 하나의 스택 프레임이 쌓이고, 메서드가 종료되면 
    해당 스택 프레임이 제거된다.
- 힙 영역(Heap Area): 객체(인스턴스)와 배열이 생성되는 영역이다. 가비지 컬렉션(GC)이 이루어지는 주요 영역이며, 더 이상 참조되지 않는 객체는
  GC에 의해 제거된다.

> 참고: 스택 영역은 더 정확히는 각 쓰레드별로 하나의 실행 스택이 생성된다. 따라서 쓰레드 수 만큼 스택 영역이 생성된다. 
> 지금은 쓰레드를 1개만 사용하므로 스택 영역도 하나이다. 쓰레드에 대한 부분은 멀티 쓰레드를 학습해야 이해할 수 있다. 

#### 메서드 코드는 메서드 영역에
![](https://github.com/dididiri1/TIL/blob/main/Java/basicimages/08_03.png?raw=true)

자바에서 특정 클래스로 100개의 인스턴스를 생성하면, 힙 메모리에 100개의 인스턴스가 생긴다. 각각의 인스턴스는 내부에 변수와 메서드를 가진다. 
같은 클래스로 부터 생성된 객체라도, 인스턴스 내부의 변수 값은 서로 다를 수 있지만, 메서드는 공통된 코드를 공유한다. 따라서 객체가 생성될 때, 
인스턴스 변수에는 메모리가 할당되지만, 메서드에 대한 새로운 메모리 할당은 없다. 메서드는 메서드 영역에서 공통으로 관리되고 실행된다. 
정리하면 인스턴스의 메서드를 호출하면 실제로는 메서드 영역에 있는 코드를 불러서 수행한다.

## 스택과 큐 자료 구조
자바 메모리 구조 중 스택 영역에 대해 알아보기 전에 먼저 스택(Stack)이라는 자료 구조에 대해서 알아보자.

### 스택 구조
![](https://github.com/dididiri1/TIL/blob/main/Java/basicimages/08_04.png?raw=true)

이 블럭을 다음과 같이 생긴 통에 넣는다고 생각해보자. 위쪽만 열려있기 때문에 위쪽으로 블럭을 넣고, 위쪽으로 블럭을 빼야 한다. 
쉽게 이야기해서 넣는 곳과 빼는 곳이 같다.

![](https://github.com/dididiri1/TIL/blob/main/Java/basicimages/08_05.png?raw=true)
블럭은 1 2 3 순서대로 넣을 수 있다

![](https://github.com/dididiri1/TIL/blob/main/Java/basicimages/08_06.png?raw=true)
블럭을 빼려면 위에서 부터 순서대로 빼야한다.
블럭은 3 2 1 순서로 뺄 수 있다.

정리하면 다음과 같다.
1(넣기) 2(넣기) 3(넣기) 3(빼기) 2(빼기) 1(빼기)

#### 후입 선출(LIFO, Last In First Out)
여기서 가장 마지막에 넣은 3번이 가장 먼저 나온다. 이렇게 나중에 넣은 것이 가장 먼저 나오는 것을 후입 선출이라 하
고, 이런 자료 구조를 스택이라 한다.

#### 선입 선출(FIFO, First In First Out)
후입 선출과 반대로 가장 먼저 넣은 것이 가장 먼저 나오는 것을 선입 선출이라 한다. 이런 자료 구조를 큐(Queue)라 한다.

![](https://github.com/dididiri1/TIL/blob/main/Java/basicimages/08_07.png?raw=true)

정리하면 다음과 같다.
1(넣기) 2(넣기) 3(넣기) 1(빼기) 2(빼기) 3(빼기)

이런 자료 구조는 각자 필요한 영역이 있다. 예를 들어서 선착순 이벤트를 하는데 고객이 대기해야 한다면 큐 자료 구조 를 사용해야 한다.

이번시간에 중요한 것은 스택이다. 프로그램 실행과 메서드 호출에는 스택 구조가 적합하다. 스택 구조를 학습했으니,
자바에서 스택 영역이 어떤 방식으로 작동하는지 알아보자.

## 스택 영역
다음 코드를 실행하면 스택 영역에서 어떤 변화가 있는지 확인해보자.

#### JavaMemoryMain1
``` 
package memory;

public class JavaMemoryMain1 {
    public static void main(String[] args) {
        System.out.println("main start");
        method1(10);
        System.out.println("main end");
    }

    static void method1(int m1) {
        System.out.println("method1 start");
        int cal = m1 * 2;
        method2(cal);
        System.out.println("method1 end");
    }

    static void method2(int m2) {
        System.out.println("method2 start");
        System.out.println("method2 end");
    }
}
``` 

#### 실행 결과
``` 
main start
method1 start
method2 start
method2 end
method1 end
main end
``` 

#### 호출 그림

![](https://github.com/dididiri1/TIL/blob/main/Java/basicimages/08_08.png?raw=true)

- 처음 자바 프로그램을 실행하면 main() 을 실행한다. 이때 main() 을 위한 스택 프레임이 하나 생성된다.
  - main() 스택 프레임은 내부에 args 라는 매개변수를 가진다. args 는 뒤에서 다룬다.
- main() 은 method1() 을 호출한다. method1() 스택 프레임이 생성된다.
  - method1() 는 m1 , cal 지역 변수(매개변수 포함)를 가지므로 해당 지역 변수들이 스택 프레임에 포함 된다.
- method1() 은 method2() 를 호출한다. method2() 스택 프레임이 생성된다. method2() 는 m2 지역 변수(매개변수 포함)를 가지므로 해당 
  지역 변수가 스택 프레임에 포함된다.

#### 종료 그림
![](https://github.com/dididiri1/TIL/blob/main/Java/basicimages/08_09.png?raw=true)
- method2() 가 종료된다. 이때 method2() 스택 프레임이 제거되고, 매개변수 m2 도 제거된다. method2() 스택 프레임이 제거 되었으므로 
  프로그램은 method1() 로 돌아간다. 물론 method1() 을 처음부터 시작하는 것이 아니라 method1() 에서 method2() 를 호출한 지점으로 돌아간다.
- method1()이 종료된다. 이때 method1() 스택 프레임이 제거되고, 지역 변수(매개변수 포함) m1 ,cal 도 제거된다. 프로그램은 main() 으로 돌아간다.
- main() 이 종료된다. 더 이상 호출할 메서드가 없고, 스택 프레임도 완전히 비워졌다. 자바는 프로그램을 정리하고 종료한다.

#### 정리
- 자바는 스택 영역을 사용해서 메서드 호출과 지역 변수(매개변수 포함)를 관리한다.
- 메서드를 계속 호출하면 스택 프레임이 계속 쌓인다.
- 지역 변수(매개변수 포함)는 스택 영역에서 관리한다.
- 스택 프레임이 종료되면 지역 변수도 함께 제거된다.
- 스택 프레임이 모두 제거되면 프로그램도 종료된다.

