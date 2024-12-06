# 수학적 사고

## 1. 최고의 집합

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12938)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.2
* 케이스를 나눠서 풀어보는 문제

```java
import java.util.*;

class Solution {
    public int[] solution(int n, int s) {
        // 불가능한 경우 체크 (n개의 자연수로 s를 만들 수 없는 경우)
        if (n > s) {
            return new int[]{-1};
        }
        
        // 결과 배열 초기화
        int[] answer = new int[n];
        
        // 기본값 = s를 n으로 나눈 몫
        int baseValue = s / n;
        for (int i = 0; i < n; i++) {
            answer[i] = baseValue;
        }
        
        // 나머지를 1씩 분배
        // 뒤에서부터 분배하면 자동으로 오름차순 정렬됨
        int remainder = s % n;
        for (int i = n - remainder; i < n; i++) {
            answer[i]++;
        }
        
        return answer;
    }
}
```
