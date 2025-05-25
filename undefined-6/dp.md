---
description: 불필요한 계산을 줄이고, 효율적으로 최적해를 찾아야만 풀리는 문제들입니다.
---

# DP(동적 계획법)

## 0. DP 문제 특징

* 최적화(최소, 최대, 최적) 문제
* 메모이제이션(이전 계산한 결과를 저장하고 재사용 가능한) 문제
* 작은 문제의 해답들이 큰 문제의 해답을 구성하는 문제
* 피보나치 수열과 같은 점화식 문제

## 1. 스티커 모으기(2)

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12971)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.2
* 두 가지 케이스를 나눠서 해를 찾는 문제

```java
class Solution {
    public int solution(int sticker[]) {
        int n = sticker.length;
        
        // 스티커가 1장인 경우
        if (n == 1) return sticker[0];
        // 스티커가 2장인 경우
        if (n == 2) return Math.max(sticker[0], sticker[1]);
        
        // 첫 번째 스티커를 선택하는 경우 (마지막 스티커는 선택 불가)
        int[] dp1 = new int[n];
        dp1[0] = sticker[0];
        dp1[1] = sticker[0];  // 두 번째는 선택 불가
        for (int i = 2; i < n-1; i++) {
            dp1[i] = Math.max(dp1[i-1], dp1[i-2] + sticker[i]);
        }
        
        // 첫 번째 스티커를 선택하지 않는 경우 (마지막 스티커 선택 가능)
        int[] dp2 = new int[n];
        dp2[0] = 0;  // 첫 번째는 선택하지 않음
        dp2[1] = sticker[1];
        for (int i = 2; i < n; i++) {
            dp2[i] = Math.max(dp2[i-1], dp2[i-2] + sticker[i]);
        }
        
        return Math.max(dp1[n-2], dp2[n-1]);
    }
}
```



## 2. N으로 표현

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/42895)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.3
* N을 1\~8번 사용했을 때 만들 수 있는 모든 숫자를 점진적으로 계산

```java
import java.util.*;

class Solution {
    public int solution(int N, int number) {
        if (N == number) {
            return 1;
        }
        // 각 횟수별로 만들 수 있는 수의 집합을 저장할 배열
        Set<Integer>[] dp = new Set[9];
        
        for (int i = 1; i <= 8; i++) {
            dp[i] = new HashSet<>();
        }
        
        // N을 i번 사용해서 만들 수 있는 값을 미리 저장
        int baseNumber = N;
        for (int i = 1; i <= 8; i++) {
            dp[i].add(baseNumber);
            baseNumber = baseNumber * 10 + N;
        }
        
        // 1부터 8까지 경우를 살펴보며 가능한 값을 만듦
        for (int i = 1; i <= 8; i++) {
            for (int j = 1; j < i; j++) {
                for (int x : dp[j]) {
                    for (int y : dp[i - j]) {
                        dp[i].add(x + y);
                        dp[i].add(x - y);
                        dp[i].add(x * y);
                        if (y != 0) {
                            dp[i].add(x / y);
                        }
                    }
                }
            }            
            
            // number를 찾으면 바로 return
            if (dp[i].contains(number)) {
                return i;
            }
        }
        
        return -1; // 8번 이내에 만들 수 없을 경우
    }
}
```



## 3. 정수 삼각형

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/43105)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.3

```java
import java.util.*;

class Solution {
    public int solution(int[][] triangle) {
        int n = triangle.length;
        
        // 삼각형의 마지막 층을 복사하여 dp 배열로 사용
        int[] dp = new int[n];
        for (int i = 0; i < n; i++) {
            dp[i] = triangle[n - 1][i];
        }
        
        // 아래에서부터 위로 올라가며 최대 경로 계산
        for (int i = n - 2; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                dp[j] = Math.max(dp[j], dp[j + 1]) + triangle[i][j];
            }
        }
        
        // dp[0]에 최종 결과가 저장됨
        return dp[0];
    }
}
```



## 4. 등굣길

* 문제 링크
* 프로그래머스 Lv.3 / 체감 난이도 Lv.4

```java
class Solution {
    public int solution(int m, int n, int[][] puddles) {
        int[][] street = new int[n][m];
        
        // 웅덩이는 -1
        for (int[] puddle : puddles) {
            street[puddle[1]-1][puddle[0]-1] = -1;
        }
        
        street[0][0] = 1;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if(street[i][j] == -1) { // 웅덩이면 0으로 바꾸고 continue
                  street[i][j] = 0;
                  continue;
                }
                
                if(i != 0) {
                    street[i][j] += street[i - 1][j] % 1000000007; 
                }

                if(j != 0) {
                    street[i][j] += street[i][j - 1] % 1000000007;   
                }
            }
        }
        
        return street[n - 1][m - 1] % 1000000007;
    }
}
```
