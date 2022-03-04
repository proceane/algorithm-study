# Party Invitation
[문제](https://www.acmicpc.net/problem/10104)

### 문제 이해
- 주어진 조건  
  - 첫번째 줄에 정수 k(1 ≤ K ≤ 100)  
  - 두번째 줄에 제거 라운드 정수 m  
  - m개 줄에 하나의 정수를 포함  
- 필요한 결과 : 제거되지 않은 숫자를 한줄에 하나씩 출력

### 재정의, 추상화
제거 위치는 0부터 시작하지 않고 1부터 시작함  

제거위치의 배수만큼 제거  
만약 2면 2번째, 4번째, 6번째.. 제거  

### 사용할 알고리즘과 자료구조 선택
링크드 리스트로 구현  

### 계획 검증(걸리는 시간, 메모리 확인)
k의 최대값이 100이라서 제한시간 1초를 넘기지 않을 것으로 예상  

### 계획 수행(코드로 구현)
```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class BJ10104 {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int k = Integer.parseInt(br.readLine());
        Party party = new Party();
        for (int i = 1; i <= k; i++) {
            party.addLast(i);
        }

        int m = Integer.parseInt(br.readLine());
        for (int i = 0; i < m; i++) {
            int removeIndex = Integer.parseInt(br.readLine());
            party.removeMultipleIndex(removeIndex);

        }
        System.out.println(party);

        br.close();
    }

    private static class Party {
        private Person head;
        private Person tail;
        private int size;

        private class Person {
            private int number;
            private Person next;

            public Person(int number) {
                this.number = number;
                this.next = null;
            }
        }

        private Person getPerson(int index) {
            Person p = head;
            for (int i = 0; i < index; i++) {
                p = p.next;
            }
            return p;
        }

        public void addFirst(int number) {
            Person newPerson = new Person(number);
            newPerson.next = head;  // 기존 head는 뒤로 밀림
            head = newPerson;       // head 교체
            size++;
            if (head.next == null) {    // 다음 노드가 없으면
                tail = newPerson;       // 신규 노드를 tail로 지정
            }
        }

        public void addLast(int number) {
            if (size == 0) {
                addFirst(number);
            } else {
                Person newPerson = new Person(number);
                tail.next = newPerson;
                tail = newPerson;
                size++;
            }
        }

        public void removeFirst() {
            Person temp = head;
            head = temp.next;
            temp = null;
            size--;
        }

        public Person removePerson(int index) {
            if (index == 0) {
                removeFirst();
            }
            Person prevPerson = getPerson(index - 1);
            Person removePerson = prevPerson.next;
            prevPerson.next = prevPerson.next.next;
            if (removePerson == tail) {
                tail = prevPerson;
            }
//            removePerson = null;
            size--;
            return removePerson;
        }

        public void removeMultipleIndex(int index) {
            for (int i = 0; i <= size; i++) {
                if (index * i <= size) {
                    removePerson(index + (index - 1) * i - 1);
                }
            }
        }

        @Override
        public String toString() {
            StringBuilder sb = new StringBuilder();
            Person p = head;
            while (p.next != null) {
                sb.append(p.number).append("\n");
                p = p.next;
            }
            sb.append(p.number);
            return sb.toString();
        }
    }
}

```
### 개선, 회고
1차 시도 실패  

마지막 노드를 제거 안하는 케이스 찾음  

removeMultipleIndex 수정  
```java
public void removeMultipleIndex(int index, int max) {
    for (int i = 0; i < max; i++) {
        if (index + (index - 1) * i - 1 < size) {
            removePerson(index + (index - 1) * i - 1);
        }
    }
}
```  

2차례의 NullPointerException을 겪고 4차시도 성공  
14120kb, 120ms  

이 문제에서 어려웠던 점은 배수만큼 삭제하는 로직이었음  
삭제를 하면 인덱스가 앞으로 당겨져서 그 규칙을 찾는게 어려웠음  
그렇게 찾은 규칙이  
`index + (index - 1) * i - 1`  

이 문제 덕분에 size의 중요성을 깨달음  
size가 생각보다 많이 쓰임..  

그리고 노드를 삭제할 때 삭제 자체가 다음 로직에 영향을 준다면 문제 체감 난이도가 높아지지 않을까 예상..  
이 문제의 경우에도 단순하게 삭제하고 끝나는 것이 아니라 삭제를 했을 때 인덱스가 앞으로 당겨져서 문제가 되었음  
다양한 케이스의 문제를 많이 풀어봐야될것 같음  