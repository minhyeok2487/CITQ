---
description: 우선순위 큐를 기반으로 푸는 문제
---

# Heap(우선 순위 큐)

## 0. 알아야 하는 코드

### 1) Heap 구현

```java
import java.util.PriorityQueue;

// 기본 최소 힙 (오름차순)
PriorityQueue<Integer> minHeap = new PriorityQueue<>();

// 최대 힙 (내림차순)
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

// 복잡한 객체로 힙 만들기 (오름차순)
PriorityQueue<Student> studentHeap = new PriorityQueue<>(
    (s1, s2) -> Integer.compare(s1.getScore(), s2.getScore())
);
```



### 2) Heap 기본 연산

```java
PriorityQueue<Integer> heap = new PriorityQueue<>();

// 원소 추가
heap.offer(10);
heap.add(20);

// 최상위 원소 확인 (제거하지 않음)
int top = heap.peek();

// 최상위 원소 뽑고 제거
int removedTop = heap.poll();

heap.remove(Element);   // 해당 Element 탐색해서 첫번째 제거 
heap.removeEq(Element); // 해당 Element 탐색해서 전부 제거
heap.clear();           // 초기화

// 힙 크기 확인
int size = heap.size();

// 힙이 비어있는지 확인
boolean isEmpty = heap.isEmpty();
```



## 1. 더 맵게

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/42626)
* 프로그래머스 Lv.2 / 체감난이도 Lv.1
* 효율성을 위한 우선순위 큐 사용 문제

```java
import java.util.*;

class Solution {
    public int solution(int[] scoville, int K) {
        int answer = 0;
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        for(int i : scoville) {
            pq.add(i);
        }
        
        while(!pq.isEmpty()) {
            int start = pq.poll();
            if (start >= K) {
                return answer;
            } else {
                if (pq.isEmpty()) {
                    return -1;
                } else {
                    int next = pq.poll();
                    pq.add(start + (next) * 2);
                    answer++;
                }
            }
        }
        return -1;
    }
}
```



## 2. 야근지수

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12927)
* 프로그래머스 Lv.3 / 체감난이도 Lv.2
* 기본적인 문제로 하나씩 뽑아서 게산하는 데 우선순위가 있음

```java
import java.util.*;

class Solution {
    public long solution(int n, int[] works) {
        // 최대 힙 우선순위 큐
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        
        // 모든 작업을 최대 힙에 추가
        for (int work : works) {
            maxHeap.offer(work);
        }
        
        
        // n시간 동안 가장 큰 작업량을 1씩 감소
        while ( n > 0 && !maxHeap.isEmpty()) {
            int max = maxHeap.poll();
            if (max > 0) {
                maxHeap.offer(max - 1);
            }
            n--;
        }
        
        // 남은 작업량의 제곱의 합 계산
        long answer = 0;
        while(!maxHeap.isEmpty()) {
            int work = maxHeap.poll();
            answer += work * work;
        }
        
        return answer;
    }
}
```



## 3. 이중우선순위 큐

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/42628)
* 프로그래머스 Lv.3 / 체감난이도 Lv.3
* 문제 자체가 이중우선순위 큐 문제

```java
import java.util.*;

class Solution {
    public int[] solution(String[] operations) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

        for (String operation : operations) {
            String[] parts = operation.split(" ");
            String command = parts[0];
            int number = Integer.parseInt(parts[1]);

            if (command.equals("I")) {
                minHeap.add(number);
                maxHeap.add(number);
            } else if (command.equals("D")) {
                if (number == 1) {
                    if (!maxHeap.isEmpty()) {
                        int max = maxHeap.poll();
                        minHeap.remove(max);
                    }
                } else {
                    if (!minHeap.isEmpty()) {
                        int min = minHeap.poll();
                        maxHeap.remove(min);
                    }
                }
            }
        }

        int[] answer = new int[2];
        if (!maxHeap.isEmpty() && !minHeap.isEmpty()) {
            answer[0] = maxHeap.peek();
            answer[1] = minHeap.peek();
        }

        return answer;
    }
}
```



## 4. 디스크 컨트롤러 문제

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/42627)
* 프로그래머스 Lv.3 / 체감난이도 Lv.3
* 문제에 "우선순위 디스크 컨트롤러라는 가상의 장치를 이용한다고 가정"

```java
import java.util.*;

class Solution {
    public int solution(int[][] jobs) {
        int answer = 0;

        // 작업이 요청되는 시점 기준으로 오름차순 정렬
        Arrays.sort(jobs, (o1, o2) -> o1[0] - o2[0]);

        // 작업의 소요시간 기준으로 오름차순 정렬
        PriorityQueue<int[]> pq = new PriorityQueue<>((o1, o2) -> o1[1] - o2[1]);

        int jobs_index = 0; // 작업 배열 인덱스
        int finish_job = 0; // 처리 완료된 작업 개수
        int end_time = 0; // 작업 처리 완료 시간

        while(true) {
            if(finish_job == jobs.length) break; // 모든 작업을 처리했다면 종료

            // 이전 작업 처리 중 요청된 작업 add
            while(jobs_index < jobs.length && jobs[jobs_index][0] <= end_time) {
                pq.add(jobs[jobs_index++]);
            }

            if(!pq.isEmpty()) { // 이전 작업 처리 중 요청된 작업이 있는 경우
                int[] job = pq.poll();
                answer += end_time - job[0] + job[1]; // 작업 요청부터 종료까지 걸린 시간 추가
                end_time += job[1]; // 작업 처리 완료 시간 갱신
                finish_job++; // 처리 완료된 작업 개수 1 증가
            } else { // 이전 작업 처리 중 요청된 작업이 없는 경우
                end_time = jobs[jobs_index][0]; // 다음 작업 요청 시점으로 갱신
            }
        }

        return answer / jobs.length;
    }
}
```

&#x20;





