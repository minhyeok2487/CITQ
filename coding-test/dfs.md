# DFS(깊이 우선 탐색)

## 0. 문제 유형

* 모든 경로 탐색



## 1. 네트워크

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/43162)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.2
* 끝까지 가는 모든 경로를 탐색하는 문제

```java
import java.util.*;

class Solution {
    public int solution(int n, int[][] computers) {
        int answer = 0;
        boolean[] check = new boolean[n];
        for (int i = 0; i < n; i++) {
            if(!check[i]) {
                dfs(computers, i, check);
                answer++;
            }
        }
        
        return answer;
    }
    
    public void dfs(int[][] computers, int i, boolean[] check) {
        check[i] = true;
        
        for(int j = 0; j < computers.length; j++) {
            if (i != j && computers[i][j] == 1 && !check[j]) {
                dfs(computers, j, check);
            }
        }
    }
}
```



## 2. 단어 변환

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/43163)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.3
* 하나씩 끝까지 만들어보면서 단어를 찾음 + 이미 사용한 단어 체크하는 배열

```java
import java.util.*;

class Solution {
    static boolean[] visited;
    static int answer;
    
    public int solution(String begin, String target, String[] words) {
        visited = new boolean[words.length];
        dfs(begin, target, words, 0);
    
        return answer;
    }
    
    public static void dfs(String begin, String target, String[] words, int cnt) {
        if (begin.equals(target)) {
            answer = cnt;
            return;
        }
        
        for (int i =0; i < words.length; i++) {
            if (visited[i]) {
                continue;
            }
            
            int k = 0;
            for (int j = 0; j < begin.length(); j++) {
                if (begin.charAt(j) == words[i].charAt(j)) {
                    k++;
                }
            }
            
            if (k == begin.length() - 1) {
                visited[i] = true;
                dfs(words[i], target, words, cnt+1);
                visited[i] = false;
            }
        }
    }
}
```



## 3. 여행 경로

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/43164)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.3
* 끝까지 가는 모든 경로를 탐색하는 문제 + 정렬

```java
import java.util.*;

class Solution {
    static List<String> answer;
    static boolean[] visited;
    
    public String[] solution(String[][] tickets) {
        answer = null;
        visited = new boolean[tickets.length];
        
        // 출발지가 같은 경우 도착지를 기준으로 정렬
        Arrays.sort(tickets, (a, b) -> {
            if(a[0].equals(b[0])) {
                return a[1].compareTo(b[1]);
            }
            return a[0].compareTo(b[0]);
        });
        
        List<String> route = new ArrayList<>();
        route.add("ICN");
        
        dfs(tickets, "ICN", route, 0);
        
        return answer.toArray(new String[0]);
    }
    
    public void dfs(String[][] tickets, String current, List<String> route, int count) {
        if(count == tickets.length) {
            // 처음 찾은 경로이거나 더 알파벳 순서가 앞선 경로를 찾은 경우
            if(answer == null) {
                answer = new ArrayList<>(route);
            }
            return;
        }
        
        for(int i = 0; i < tickets.length; i++) {
            if(!visited[i] && tickets[i][0].equals(current)) {
                visited[i] = true;
                route.add(tickets[i][1]);
                
                dfs(tickets, tickets[i][1], route, count + 1);
                
                // 백트래킹
                route.remove(route.size() - 1);
                visited[i] = false;
            }
        }
    }
}
```



## 4. 불량사용자

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/64064)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.3
* DFS + 백 트래킹

```java
import java.util.*;

class Solution {
    private HashSet<HashSet<String>> result; // 최종 결과를 저장할 Set
    
    public int solution(String[] user_id, String[] banned_id) {
        result = new HashSet<>();
        
        // 각 불량 사용자 패턴별로 매칭되는 사용자 ID를 저장
        List<List<String>> matchList = new ArrayList<>();
        
        // 각 불량 사용자 패턴에 매칭되는 사용자 ID들을 찾음
        for(String banned: banned_id) {
            List<String> matches = new ArrayList<>();
            for(String user: user_id) {
                if(match(user, banned)) {
                    matches.add(user);
                }
            }
            matchList.add(matches);
        }
        
        // DFS로 가능한 모든 조합을 찾음
        dfs(new HashSet<>(), 0, matchList);
        
        return result.size();
    }
    
    private void dfs(HashSet<String> set, int depth, List<List<String>> matchList) {
        // 모든 불량 사용자를 처리했으면 결과에 추가
        if(depth == matchList.size()) {
            result.add(new HashSet<>(set));
            return;
        }
        
        // 현재 불량 사용자에 매칭되는 사용자 ID들에 대해 반복
        for(String user: matchList.get(depth)) {
            // 이미 선택된 사용자 ID는 건너뜀
            if(!set.contains(user)) {
                set.add(user);
                dfs(set, depth + 1, matchList);
                set.remove(user);
            }
        }
    }
    
    private boolean match(String user, String banned) {
        if(user.length() != banned.length()) {
            return false;
        }
        
        for(int i = 0; i < user.length(); i++) {
            if(banned.charAt(i) != '*' && banned.charAt(i) != user.charAt(i)) {
                return false;
            }
        }
        return true;
    }
}
```



## 5. 다단계 칫솔 판매

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/77486)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.3
* 트리구조지만 Map과 DFS로 풀 수 있는 문제

```java
import java.util.*;

class Solution {
    public int[] solution(String[] enroll, String[] referral, String[] seller, int[] amount) {
        // map
        Map<String, Integer> profitMap = new HashMap<>();
        Map<String, String> referralMap = new HashMap<>();
        for(int i = 0; i < enroll.length; i++) {
            profitMap.put(enroll[i], 0); // 0원 으로 초기화
            referralMap.put(enroll[i], referral[i]);
        }
        
        for(int i = 0; i < seller.length; i++) {
            dfs(profitMap, referralMap, seller[i], amount[i]*100);
        }
        int[] answer = new int[enroll.length];
        for(int i = 0; i < enroll.length; i++) {
            answer[i] = profitMap.get(enroll[i]);
        }
        return answer;
    }
    
    public void dfs(Map<String, Integer> profitMap, Map<String, String> referralMap, String name, int profit) {
        if (profit < 10) {
            profitMap.put(name, profitMap.get(name) + profit);
        } else {
            int ownProfit = (profit * 9 + 9) / 10; // 올림 처리된 자신의 몫
            int referralProfit = profit / 10; // 추천인의 몫

            profitMap.put(name, profitMap.get(name) + ownProfit);

            if (!referralMap.get(name).equals("-")) {
                dfs(profitMap, referralMap, referralMap.get(name), referralProfit);
            }
        }
    }
}
```
