# BABBA
[문제](https://www.acmicpc.net/problem/9625)

### 문제 이해
- 주어진 조건 
  - A는 다음 순서에서 B가 됨  
  - B는 다음 순서에서 BA가 됨  
  - K(1 ≤ K ≤ 45)  
- 필요한 결과 : 버튼을 K번 눌렀을 때 화면에 A와 B의 개수

### 재정의, 추상화
처음에 감을 못잡아서 찾아보았을때 규칙이 피보나치 수라고 해서 직접 개수를 구해보니 피보나치 수가 맞았음  
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55 .. 이런 배열이 있을 때  
k번 눌렀을 때 A의 개수는 배열의 k - 1번째, B의 개수는 k번째 수  

### 사용할 알고리즘과 자료구조 선택
자료구조는 배열, 알고리즘은 DP?  

### 계획 검증(걸리는 시간, 메모리 확인)
배열에 45개의 피보나치 수를 전부 담아서 찾아가면 오래걸리진 않을 것으로 예상  

### 계획 수행(코드로 구현)
```java

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class BJ9625 {

    private static int[] fibo = {0, 1, 1, 2, 3,
                                5, 8, 13, 21, 34,
                                55, 89, 144, 233, 377,
                                610, 987, 1597, 2584, 4181,
                                6765, 10946, 17711, 28657, 46368,
                                75025, 121393, 196418, 317811, 514229,
                                832040, 1346269, 2178309, 3524578, 5702887,
                                9227465, 14930352, 24157817, 39088169, 63245986,
                                102334155, 165580141, 267914296, 443494437, 701408733,
                                1134903170};

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int k = Integer.parseInt(br.readLine());
        System.out.println(fibo[k - 1] + " " + fibo[k]);

        br.close();
    }
}

```

### 개선, 회고
두번째 시도때 성공함  
첫번째 시도때 `fibo[45]`의 값을 1144903170으로 적어서..
`fibo[43]`의 값을 443494437으로 했을 때 맞는거 보니 테스트케이스에 k가 43인 경우가 없었나..  
원래 433494437이 맞음  

노가다로 풀면 안되는 문제인건 알고있지만 왠지 한번 그렇게 풀어보고싶은 날이었음  
다음엔 DP로 풀어봐야지..

```java

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class BJ9625_2 {

    private static int[] aArr;
    private static int[] bArr;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int k = Integer.parseInt(br.readLine());
        aArr = new int[k + 1];
        bArr = new int[k + 1];

        aArr[0] = 1;
        bArr[0] = 0;
        aArr[1] = 0;
        bArr[1] = 1;

        System.out.println(fibo(k, aArr) + " " + fibo(k, bArr));

        br.close();
    }

    private static int fibo(int n, int[] arr) {
        if (n == 0) {
            return arr[0];
        }

        if (n == 1) {
            return arr[1];
        }

        if (arr[n] != 0) {
            return arr[n];
        }

        return arr[n] = fibo(n - 1, arr) + fibo(n - 2, arr);
    }
}

```
정답보고 코딩  
피보나치 수 문제는 이렇게 풀면 될 것이라 생각  
난이도에 따라 다르겠지만..