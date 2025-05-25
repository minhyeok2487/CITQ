# Shortest path

## 0. 들어가기 전

최단경로 알고리즘은 그래프에서 정점간에 가장 짧은 경로를 찾는 알고리즘을 말합니다.

* 다익스트라 알고리즘(Dijkstra's algorithm)
* 플로이드-워셜 알고리즘(Floyd-Warshall algorithm)
* 벨만-포드 알고리즘(Bellman-Ford algorithm)



## 1. 순위

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/49191)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.4
* 플로이드 워셜 알고리즘을 안다면 쉽게 풀 수 있고 모르면 어려운 문제

```java
import java.util.*;

class Solution {
    public int solution(int n, int[][] results) {
        // 승패 관계를 저장할 그래프 초기화
        // graph[i][j] = 1: i가 j를 이겼다
        // graph[i][j] = -1: i가 j에게 졌다
        // graph[i][j] = 0: i와 j의 관계가 아직 정해지지 않음
        int[][] graph = new int[n + 1][n + 1];
        for (int[] edge : results) {
            graph[edge[0]][edge[1]] = 1;   // edge[0]이 edge[1]을 이김
            graph[edge[1]][edge[0]] = -1;  // edge[1]이 edge[0]에게 짐
        }
        
        // 플로이드-와샬 알고리즘을 사용하여 간접 승패 관계를 계산
        // i: 출발 노드
        for (int i = 1; i <= n; i++) {
            // j: 도착 노드
            for (int j = 1; j <= n; j++) {
                // k: 중간에 거쳐가는 노드
                for (int k = 1; k <= n; k++) {
                    // i -> k -> j로 가는 경로가 있는 경우 (i가 k를 이기고, k가 j를 이김)
                    if (graph[i][k] == 1 && graph[k][j] == 1) {
                        graph[i][j] = 1;   // i가 j를 이긴 것으로 표시
                        graph[j][i] = -1;  // j가 i에게 진 것으로 표시
                    }
                    
                    // i <- k <- j로 가는 경로가 있는 경우 (i가 k에게 지고, k가 j에게 짐)
                    if (graph[i][k] == -1 && graph[k][j] == -1) {
                        graph[i][j] = -1;  // i가 j에게 진 것으로 표시
                        graph[j][i] = 1;   // j가 i를 이긴 것으로 표시
                    }
                }
            }
        }
        
        int answer = 0; // 순위를 알 수 있는 선수의 수
        
        // 각 선수에 대해 자신과 다른 모든 선수와의 승패 관계를 확인
        for (int i = 1; i <= n; i++) {
            int count = 0; // i와 다른 선수들과의 관계를 알 수 있는 수
            for (int j = 1; j <= n; j++) {
                if (graph[i][j] != 0) count++; // 승패 관계가 있으면 카운트 증가
            }
            // 자신을 제외한 모든 선수와의 관계가 명확한 경우
            if (count == n - 1) answer++;
        }
        
        return answer;
    }
}
```

