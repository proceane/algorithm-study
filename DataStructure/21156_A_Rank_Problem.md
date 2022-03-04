# A Rank Problem
[문제](https://www.acmicpc.net/problem/21156)

### 문제 이해
- 주어진 조건  
  - 첫째줄에 팀의 수 n과 경기 수 m이 주어짐(n과 m은 100 이하)  
  - m개 줄에 이긴팀, 진팀이 입력됨  
- 필요한 결과 : 한줄에 팀 순위대로 나열하여 출력   

### 재정의, 추상화
처음에 T1 ~ T5 순으로 순위가 매겨짐  
만약 이기게 되면 이긴 팀은 한칸 왼쪽으로 이동, 진 팀은 이긴팀의 기존 순위 위치로 이동  

이겼는데 원래 순위가 진팀보다 높으면 가만히 있기  

### 사용할 알고리즘과 자료구조 선택
자료구조는 링크드 리스트 사용

### 계획 검증(걸리는 시간, 메모리 확인)
n,m의 최대값이 100이기때문에 제한시간 1초를 넘기지 않을 것으로 예상  

### 계획 수행(코드로 구현)
```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class BJ21156 {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());
        Rank rank = new Rank();
        for (int i = 1; i <= n; i++) {
            rank.addLast("T" + i);
        }

        int m = Integer.parseInt(st.nextToken());
        for (int i = 0; i < m; i++) {
            st = new StringTokenizer(br.readLine());
            String winTeam = st.nextToken();
            String loseTeam = st.nextToken();
            int winIndex = rank.indexOf(winTeam);    // 이긴팀의 원래 위치
            int loseIndex = rank.indexOf(loseTeam);   // 진팀의 원래 위치

            // 이긴 팀이 진팀보다 뒤에있으면
            if (winIndex > loseIndex) {
                // 진팀은 이긴팀의 원래 위치로 이동 
                // 이긴팀은 한칸 앞으로 이동 -> 이긴팀의 원래 위치로 이동하면 이긴팀도 한칸씩 앞으로 밀림
                String loseTeamName = rank.remove(rank.indexOf(loseTeam));
                rank.add(winIndex, loseTeamName);
            }
        }

        System.out.println(rank);

        br.close();
    }

    private static class Rank {
        private Team head;
        private Team tail;
        private int size;

        private class Team {
            private String teamName;
            private Team next;

            public Team(String teamName) {
                this.teamName = teamName;
                this.next = null;
            }
        }

        public void addFirst(String teamName) {
            Team newTeam = new Team(teamName);
            newTeam.next = head;
            head = newTeam;
            size++;
            if (head.next == null) {
                tail = newTeam;
            }
        }

        public void addLast(String teamName) {
            if (size == 0) {
                addFirst(teamName);
            } else {
                Team newTeam = new Team(teamName);
                tail.next = newTeam;
                tail = newTeam;
                size++;
            }
        }

        public void add(int index, String teamName) {
            if (index == 0) {
                addFirst(teamName);
            } else {
                Team prevTeam = getTeam(index - 1);
                Team nextTeam = prevTeam.next;
                Team newTeam = new Team(teamName);
                prevTeam.next = newTeam;
                newTeam.next = nextTeam;
                size++;
                if (newTeam.next == null) {
                    tail = newTeam;
                }
            }
        }

        public int indexOf(String teamName) {
            Team t = head;
            int index = 0;
            while (!t.teamName.equals(teamName)) {
                t = t.next;
                index++;
                if (t == null) {
                    return -1;
                }
            }
            return index;
        }

        private Team getTeam(int index) {
            Team t = head;
            for (int i = 0; i < index; i++) {
                t = t.next;
            }
            return t;
        }

        public Team removeFirst() {
            Team removeTeam = head;
            head = removeTeam.next;
            size--;
            return removeTeam;
        }

        public String remove(int index) {
            if (index == 0) {
                return removeFirst().teamName;
            } else {
                Team prevTeam = getTeam(index - 1);
                Team removeTeam = prevTeam.next;
                prevTeam.next = prevTeam.next.next;

                if (removeTeam == tail) {
                    tail = prevTeam;
                }
                size--;
                return removeTeam.teamName;
            }
        }

        @Override
        public String toString() {
            if (size == 0) {
                return "";
            }
            StringBuilder sb = new StringBuilder();
            Team t = head;
            while (t.next != null) {
                sb.append(t.teamName).append(" ");
                t = t.next;
            }
            sb.append(tail.teamName);
            return sb.toString();
        }
    }
}

```
### 개선, 회고
1차 시도 성공  
16008kb, 148ms  
자바로 이문제 푼사람 나밖에 없음..

링크드 리스트 노드 교체하는게 조금 난이도 있을수도?  
이 문제같은 경우에는 중간에 노드 추가하면 자동으로 원래 위치 노드는 한칸씩 앞으로 밀리게되서 괜찮았음  

연결리스트 브론즈 문제 다풀었다..