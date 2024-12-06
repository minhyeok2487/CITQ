---
description: 우선순위 큐를 기반으로 푸는 문제
---

# Heap(우선 순위 큐)

## 1. 야근지수

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12927)
* 프로그래머스 Lv.3
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
