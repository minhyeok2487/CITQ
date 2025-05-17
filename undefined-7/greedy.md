# Greedy(그리디 알고리즘)

## 0. 들어가기전

> 문제를 해결할 때 매 단 게에서 현재 상황의 가장 최선의 선택을 하는 방식으로 최적의 해답을 찾는 알고리즘. 즉, 각 단게에서 당장 이득이 되는 선택을 반복적으로 하여 최종적인 해답을 얻으려는 방법
>
> * 국소 최적해(현재 최선의 선택)가 전체 최적해로 이어진다고 가정



## 1. 단속카메라

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/42884)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.2

```java
import java.util.*;

class Solution {
    public int solution(int[][] routes) {
        // 진입 지점을 기준으로 오름차순 정렬
        Arrays.sort(routes, (a, b) -> a[0] - b[0]);
        
        int count = 1;  // 필요한 카메라 수
        int lastCamera = routes[0][1];  // 마지막 카메라 위치
        
        // 모든 차량의 경로를 확인
        for (int i = 1; i < routes.length; i++) {
            // 현재 차량의 진입 지점이 마지막 카메라 위치보다 뒤에 있으면
            if (routes[i][0] > lastCamera) {
                count++;  // 새로운 카메라 필요
                lastCamera = routes[i][1];  // 카메라 위치 업데이트
            } else {
                // 현재 차량의 진출 지점이 더 앞에 있다면, 카메라 위치를 앞으로 당김
                lastCamera = Math.min(lastCamera, routes[i][1]);
            }
        }
        
        return count;
    }
}
```



## 2. 기지국 설치

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12979)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.3

```java
class Solution {
    public int solution(int n, int[] stations, int w) {
        int answer = 0;
        int position = 1;  // 현재 위치
        int idx = 0;      // stations 배열의 인덱스
        
        while (position <= n) {
            // 현재 위치가 기존 기지국의 범위 안에 있는 경우
            if (idx < stations.length && position >= stations[idx] - w) {
                position = stations[idx] + w + 1;
                idx++;
            }
            // 현재 위치가 기존 기지국의 범위를 벗어난 경우
            else {
                answer++;
                position += 2 * w + 1;
            }
        }
        
        return answer;
    }
}
```



## 3. 섬 연결하기

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/42861)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.4

```java
import java.util.*;

class Solution {
    // 부모 노드를 찾는 함수
    private int findParent(int[] parent, int x) {
        // 자기 자신이 부모라면 그대로 반환
        if (parent[x] == x) {
            return x;
        }
        
        // 경로 압축을 위해 재귀적으로 부모를 찾아 갱신
        return parent[x] = findParent(parent, parent[x]);
    }
    
    // 두 노드를 연결하는 함수
    private void unionParent(int[] parent, int a, int b) {
        a = findParent(parent, a); // a의 최상위 부모 찾기
        b = findParent(parent, b); // b의 최상위 부모 찾기
        
        // 더 작은 번호를 가진 노드가 부모가 되도록 설정
        if (a < b) {
            parent[b] = a;
        } else {
            parent[a] = b;
        }
    }
    
    public int solution(int n, int[][] costs) {
        // 1. 비용을 기준으로 오름차순 정렬
        Arrays.sort(costs, (a, b) -> Integer.compare(a[2], b[2]));
        
        // 2. 부모 배열 초기화
        int[] parent = new int[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
        
        int totalCost = 0; // 총 비용
        int connectedEdges = 0; // 연결된 간선 수
        
        // 3. 비용이 적은 간선부터 선택하면서 MST 구성
        for (int[] cost : costs) {
            int from = cost[0]; // 시작
            int to = cost[1]; // 도착
            int weight = cost[2]; // 건설 비용
            
            // 사이클이 생기지 않는 경우에만 연결
            if (findParent(parent, from) != findParent(parent, to)) {
                unionParent(parent, from, to);
                totalCost += weight;
                connectedEdges++;
                
                // 모든 섬이 연결되었다면 종료
                if (connectedEdges == n - 1) {
                    break;
                }
            }
        }
        
        return totalCost;
    }
}
```
