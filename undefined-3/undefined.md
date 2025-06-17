# 투 포인터 알고리즘

## 0. 들어가기전

> 배열이나 리스트와 같은 순타적인 자료 구조에서 두 개의 포인터를 이용하여&#x20;
>
> 특정 조건을 만족하는 구간이나 값을 효율적으로 찾는 알고리즘
>
> 보통 배열의 양 끝이나 특정 위치에서 시작하여 포인터를 이동시키면서 문제 해결



## 1. 보석 쇼핑

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/67258)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.3

```java
import java.util.*;

class Solution {
    public int[] solution(String[] gems) {
        int[] answer = new int[2];
        Set<String> gemKinds = new HashSet<>(Arrays.asList(gems));
        int kindCount = gemKinds.size();
        
        // 현재 구간에 포함된 보석들의 개수를 저장하는 Map
        Map<String, Integer> gemCount = new HashMap<>();
        
        int start = 0;
        int end = 0;
        int minLength = Integer.MAX_VALUE;
        int minStart = 0;
        
        while(true) {
            // 모든 종류의 보석을 포함할 때까지 end 포인터 이동
            if(gemCount.size() < kindCount && end < gems.length) {
                gemCount.put(gems[end], gemCount.getOrDefault(gems[end], 0) + 1);
                end++;
            }
            // 모든 종류의 보석을 포함하면 start 포인터 이동
            else if(gemCount.size() == kindCount) {
                if(end - start < minLength) {
                    minLength = end - start;
                    minStart = start;
                }
                
                gemCount.put(gems[start], gemCount.get(gems[start]) - 1);
                if(gemCount.get(gems[start]) == 0) {
                    gemCount.remove(gems[start]);
                }
                start++;
            }
            // 더 이상 진행할 수 없는 경우
            else {
                break;
            }
        }
        
        answer[0] = minStart + 1;
        answer[1] = minStart + minLength;
        
        return answer;
    }
}
```
