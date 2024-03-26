# [자료구조 알고리즘] 빅오(Big-O)표기법

### 알고리즘 성능 표기법
알고리즘 성능을 표기하는 방법은 크게 세 가지가 있다. 이 중 Big-O(빅 오) 표기법을 가장 많이 쓴다.

### Big-O(빅 오) 표기법이란?
빅오 표기법은 알고리즘의 성능을 수학적으로 표현해주는 표기법이다. 이걸로 시간과 공간 복잡도를 표현할 수 있다.   
그리고 빅오표기법은 알고리즘의 실제 러닝타임을 표시하는거라기 보다. 데이터나 사용자의 증가율에 따른 알고리즘의 성능을 예측하는 것이다.

빅오 표기법에서는 주로 최악의 경우 시간 복잡도를 나타낸다.  
즉, 입력 크기에 따라 알고리즘의 실행 시간이 어떻게 증가하는지, 그 중 '아무리 많이 걸려도 최대 이 시간 안에는 끝난다는 것’을 나타낸다.
### 시간 복잡도(Time Complexity)

### 1. O(1) : 상수 시간 복잡도를 의미한다. 
- 입력 크기와 관계없이 실행 시간이 일정하다.
``` java
public boolean solution(int[] n) {
    return (n[0] == 0) ? true : false;
}
```

### 2. O(logn) 로그 시간 복잡도를 의미한다. 
- 실행 시간이 입력 크기의 로그에 비례한다. 
- 주로 이진 검색과 같이 입력을 반씩 나누는 알고리즘에서 나타난다.
#### 이진탐색(Binary Search)
``` java
public void solution(int n, int m, int[] arr) {
    int lt = 0, rt = n-1;
    
    while (lt <= rt) {
        int mid = (lt + rt) / 2;
        if (arr[mid] == m) {
            answer = mid+1;
            break;
        } else if(arr[mid] > m) {
            rt = mid-1;
        } else if (arr[mid] < m){
            lt = mid+1;
        }
    }
}
```

### 3. O(n) 선형 시간 복잡도를 의미한다.
- 입력 크기에 비례해 선형적으로 증가한다.
``` java
public void solution(int[] n) {
    for(int i = 0; i < n.length; i++) {
        System.out.println(i);
    }
}
```
### 4. O(nlogn) 선형 로그 시간 복잡도를 의미한다.
- 입력 크기에 비례해 실행 시간이 증가하지만, 로그 요소도 추가된다.
- 주로 효율적인 정렬 알고리즘인 병합 정렬과 퀵 정렬에서 나타난다.

### 5. O(n2) 이차 시간 복잡도를 의미한다.
- 입력 크기의 제곱에 비례해 증가한다.
- 이는 주로 이중 반복문이 있는 알고리즘에서 나타난다.
``` java
public void solution(int[] n) {
    for(int i = 0; i < n.length; i++) {
       for(int j = 0; j < n.length; j++) {
          System.out.println(i+j);
       }
    }
}
```


### 6. O(2n) 지수 시간 복잡도를 의미한다.
- 입력 크기에 대해 2의 거듭제곱에 비례해 증가한다.
- 이는 주로 완전 탐색과 같이 가능한 모든 경우의 수를 확인해야 하는 알고리즘에서 나타난다.
#### Fibonacci Numbers
``` java
public int DFS(int n) {
    if (n == 1) {
        return 1;
    } else if (n == 2) {
        return 1;
    } else {
        return DFS(n-2) + DFS(n-1);
    }
}
```

### 시간 복잡도 크기 순서
> O(1) < O(logn) < O(n) < O(nlogn) < O(n2) < O(2n) < O(n!)

![](https://github.com/dididiri1/TIL/blob/main/Algorithm/images/01.png?raw=true)

### Reference
- [엔지니어 대한민국 - 빅오(Big-O)표기법 완전정복](https://www.youtube.com/watch?v=6Iq5iMCVsXA&t=182s)
- [Understanding Big-O Notation With JavaScript](https://dev.to/b0nbon1/understanding-big-o-notation-with-javascript-25mc)
