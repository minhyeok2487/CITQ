# 해시(Hash)

## 1. 베스트 앨범

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/42579)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.2

```java
import java.util.*;

class Solution {
    public int[] solution(String[] genres, int[] plays) {
        
        // 장르별로 (인덱스, 재생 횟수) 맵을 저장할 albumMap과
        // 장르별 총 재생 횟수를 저장할 genrePlayCount 맵 생성
        Map<String, Map<Integer, Integer>> albumMap = new HashMap<>();
        Map<String, Integer> genrePlayCount = new HashMap<>();
        
        for (int i = 0; i < genres.length; i++) {
            albumMap.putIfAbsent(genres[i], new TreeMap<>());
            albumMap.get(genres[i]).put(i, plays[i]);
            genrePlayCount.put(genres[i], genrePlayCount.getOrDefault(genres[i], 0) + plays[i]);
        }
        
        // 장르별 총 재생 횟수를 기준으로 내림차순 정렬
        List<String> sortedGenres = new ArrayList<>(genrePlayCount.keySet());
        sortedGenres.sort((a, b) -> genrePlayCount.get(b) - genrePlayCount.get(a));
        
        // 각 장르별로 두 곡씩 가장 많이 재생된 곡을 선택
        List<Integer> answerList = new ArrayList<>();
        for (String genre : sortedGenres) {
            List<Map.Entry<Integer, Integer>> songList = new ArrayList<>(albumMap.get(genre).entrySet());
            songList.sort((a, b) -> b.getValue() - a.getValue());
            
            for (int i = 0; i < Math.min(2, songList.size()); i++) {
                answerList.add(songList.get(i).getKey());
            }
        }
        
        return answerList.stream().mapToInt(Integer::intValue).toArray();
    }
}
```
