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



## 2. 가장 긴 팰린드롬

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12904)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.3
* 큰 수부터 작은 수까지 좁히면서 생각해보는 문제

```java
import java.util.*;

class Solution
{
    static String s;
    
    public int solution(String s)     {
        this.s = s;
 
        for(int i = s.length() ; i > 0 ; i--) {    
            for(int j = 0 ; j+i <= s.length() ; j++) {
                if(isPalindrome(j, j + i - 1)) {
                    return i;
                }
            }
        }
 
        return 0;
    }
    
    private static boolean isPalindrome(int start, int end) {
        while(start <= end) {
            if(s.charAt(start++) != s.charAt(end--))
                return false;
        }
        
        return true;        
    }
}
```
