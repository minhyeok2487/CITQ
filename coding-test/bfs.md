# BFS(너비 우선 탐색)

## 0. 문제 유형

* 최단 경로 탐색



## 1. 게임 맵 최단거리

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/1844)
* 프로그래머스 Lv.2 / 체감 난이도 Lv.2
* 대표적인 최단경로 탐색 문제

```java
import java.util.*;

class Solution {
    int[] dx = {1, 0, -1, 0}; // 행 이동
    int[] dy = {0, 1, 0, -1}; // 열 이동
    
    class Point {
        int x, y, distance;
        Point(int x, int y, int distance) {
            this.x = x;
            this.y = y;
            this.distance = distance;
        }
    }
    
    public int solution(int[][] maps) {
        int n = maps.length;
        int m = maps[0].length;
        boolean[][] visited = new boolean[n][m];
        
        Deque<Point> deq = new ArrayDeque<>();
        deq.offerLast(new Point(0, 0, 1));
        visited[0][0] = true;
        
        while (!deq.isEmpty()) {
            Point current = deq.pollFirst();
            
            // 도착지점에 도달한 경우
            if (current.x == n-1 && current.y == m-1) {
                return current.distance;
            }
            
            // 4방향 탐색
            for (int i = 0; i < 4; i++) {
                int nx = current.x + dx[i];
                int ny = current.y + dy[i];
                
                // 맵 범위 내이고, 벽이 아니고, 방문하지 않은 경우
                if (nx >= 0 && nx < n && ny >= 0 && ny < m
                   && maps[nx][ny] == 1 && !visited[nx][ny]) {
                    visited[nx][ny] = true;
                    deq.offerLast(new Point(nx, ny, current.distance + 1));
                }
            }
        }
        
        return -1;
    }
}
```



## 2. 아이템 줍기

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/87694)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.4
* 최단거리 문제인데, 경로를 새로 만들어줘야함
* 경로가 아닌 부분도 볼 수 있어서 크기를 2배로 늘리기

```java
import java.util.*;

class Solution {
    int[] dx = {1, 0, -1, 0}; // 행 이동
    int[] dy = {0, 1, 0, -1}; // 열 이동
    
    class Node {
        int x;
        int y;
        int count;
        
        public Node(int x, int y, int count) {
            this.x = x;
            this.y = y;
            this.count = count;
        }
    }
    
    public int solution(int[][] rectangle, int characterX, int characterY, int itemX, int itemY) {
        // 건너뛰는 경우를 포함할 수 있어 전체적인 크기를 두배로 늘림
        Boolean[][] road = new Boolean[101][101];
        characterX *= 2;
        characterY *= 2;
        itemX *= 2;
        itemY *= 2;
        
        // 전체 경로 구하기
        for (int i = 0; i< rectangle.length; i++) {
            int[] now = rectangle[i];
            for (int j = 0; j < 4; j++) now[j] *= 2;
            
            // Boolean 배열 기본 값 = null
            // 직사각형 내부 = false, 빗변 = true
            // road[x][y] != false : 다른 직사각형의 내부도 아닐때
            // (null 이면 기본값으로 체크되지 않음, true면 같은 빗변임) 
            for (int x = now[0]; x <= now[2]; x++) {
                for (int y = now[1]; y <= now[3]; y++) {
                    road[x][y] = (x == now[0] || x == now[2] 
                                  || y == now[1] || y == now[3]) 
                        && road[x][y] != Boolean.FALSE;
                }
            }
        }
        
        // 출발
        Deque<Node> deq = new ArrayDeque<>();
        road[characterX][characterY] = false; //지나간 곳 false로 변경
        deq.offer(new Node(characterX, characterY, 0));
        
        while(!deq.isEmpty()) {
            Node node = deq.poll();
            
            if(node.x == itemX && node.y == itemY) {
                return node.count / 2;
            }
            
            for(int i = 0; i < 4; i++) {
                int x = node.x + dx[i];
                int y = node.y + dy[i];
                if(x < 2 || y < 2 || x > 100 || y > 100) continue;
                if(road[x][y] != Boolean.TRUE) continue;
                
                road[x][y] = false;
                deq.offer(new Node(x, y, node.count + 1));
            }
        }
        return -1;
    }
}
```



## 3. 퍼즐 조각 채우기

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/84021)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.5
* 어려움

```java
import java.util.*;

class Solution {
    // 상하좌우 이동을 위한 방향 배열
    int[] dx = {-1, 1, 0, 0};
    int[] dy = {0, 0, -1, 1};
    
    class Point {
        int x, y;
        Point(int x, int y) {
            this.x = x;
            this.y = y;
        }
    }
    
    // 퍼즐 조각 정보를 담는 클래스
    class Puzzle {
        List<Point> points;
        int minX, minY, maxX, maxY;
        
        Puzzle() {
            points = new ArrayList<>();
            minX = minY = Integer.MAX_VALUE;
            maxX = maxY = Integer.MIN_VALUE;
        }
        
        // 조각의 좌표를 정규화(왼쪽 상단이 0,0이 되도록)
        void normalize() {
            for (Point p : points) {
                minX = Math.min(minX, p.x);
                minY = Math.min(minY, p.y);
                maxX = Math.max(maxX, p.x);
                maxY = Math.max(maxY, p.y);
            }
            
            List<Point> normalized = new ArrayList<>();
            for (Point p : points) {
                normalized.add(new Point(p.x - minX, p.y - minY));
            }
            points = normalized;
            maxX -= minX;
            maxY -= minY;
            minX = minY = 0;
        }
        
        // 90도 회전
        Puzzle rotate() {
            Puzzle rotated = new Puzzle();
            for (Point p : points) {
                rotated.points.add(new Point(p.y, maxX - p.x));
            }
            rotated.normalize();
            return rotated;
        }
    }
    
    public int solution(int[][] game_board, int[][] table) {
        int n = game_board.length;
        List<Puzzle> emptySpaces = findPieces(game_board, 0); // 빈 공간 찾기
        List<Puzzle> puzzlePieces = findPieces(table, 1); // 퍼즐 조각 찾기
        
        boolean[] usedPuzzle = new boolean[puzzlePieces.size()];
        int answer = 0;
        
        // 각 빈 공간에 대해
        for (Puzzle space : emptySpaces) {
            // 각 퍼즐 조각에 대해
            for (int i = 0; i < puzzlePieces.size(); i++) {
                if (usedPuzzle[i]) continue;
                
                Puzzle piece = puzzlePieces.get(i);
                boolean matched = false;
                
                // 4번 회전시키면서 확인
                for (int rot = 0; rot < 4 && !matched; rot++) {
                    if (isMatch(space, piece)) {
                        matched = true;
                        usedPuzzle[i] = true;
                        answer += piece.points.size();
                        break;
                    }
                    piece = piece.rotate();
                }
                if (matched) break;
            }
        }
        
        return answer;
    }
    
    // BFS로 연결된 영역(조각) 찾기
    private List<Puzzle> findPieces(int[][] board, int target) {
        int n = board.length;
        boolean[][] visited = new boolean[n][n];
        List<Puzzle> pieces = new ArrayList<>();
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == target && !visited[i][j]) {
                    Puzzle piece = new Puzzle();
                    Queue<Point> queue = new LinkedList<>();
                    Point start = new Point(i, j);
                    
                    queue.offer(start);
                    visited[i][j] = true;
                    
                    while (!queue.isEmpty()) {
                        Point curr = queue.poll();
                        piece.points.add(curr);
                        
                        for (int d = 0; d < 4; d++) {
                            int nx = curr.x + dx[d];
                            int ny = curr.y + dy[d];
                            
                            if (nx >= 0 && nx < n && ny >= 0 && ny < n 
                                && board[nx][ny] == target && !visited[nx][ny]) {
                                visited[nx][ny] = true;
                                queue.offer(new Point(nx, ny));
                            }
                        }
                    }
                    
                    piece.normalize();
                    pieces.add(piece);
                }
            }
        }
        
        return pieces;
    }
    
    // 두 조각이 일치하는지 확인
    private boolean isMatch(Puzzle space, Puzzle piece) {
        if (space.points.size() != piece.points.size()) return false;
        if (space.maxX != piece.maxX || space.maxY != piece.maxY) return false;
        
        // 각 점의 상대적 위치가 모두 일치하는지 확인
        Set<String> spacePoints = new HashSet<>();
        for (Point p : space.points) {
            spacePoints.add(p.x + "," + p.y);
        }
        
        for (Point p : piece.points) {
            if (!spacePoints.contains(p.x + "," + p.y)) {
                return false;
            }
        }
        
        return true;
    }
}
```



## 4. \[카카오 인턴] 경주로 건설

* [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/67259)
* 프로그래머스 Lv.3 / 체감 난이도 Lv.5
* 단순히 최단 거리가 아니라 최소 비용을 찾아야 한다.
* 하지만, 직진과 코너가 비용이 다르다
  * 직진 : 100원
  * 코너 : 600원(직진 비용 추가)
* 따라서 한 지점에 도착했을 때, 그게 직진으로 온 값인지, 코너에서 온 값인지 알아야한다.(방향)

```java
import java.util.*;

class Solution {
    static boolean[][] visit;
    static int answer; // 건설 비용
    static int n; // 배열 크기
    static int[][][] arr; //[dir][x][y]

    static class Point {
        int x, y, dir, cost;

        Point(int x, int y, int dir, int cost) {
            this.x = x;
            this.y = y;
            this.dir = dir;
            this.cost = cost;
        }
    }

    public int solution(int[][] board) {
        // 직진 - 100, 코너 - 600
        // 코너를 돈다 = 방향을 바꾼다 => 현재 방향을 기억해야 한다
        answer = Integer.MAX_VALUE;
        n = board.length;
        arr = new int[4][n][n];
        for(int i=0;i<4;i++) {
            for(int x=0;x<n;x++) {
                for(int y=0;y<n;y++) {
                    arr[i][x][y] = Integer.MAX_VALUE;
                }
            }
        }
        visit = new boolean[n][n];

        bfs(0, 0, -1, 0, board); //(0,0)에서 출발, 초기 방향은 -1(출발)

        return answer;
    }

    private static void bfs(int x, int y, int dir, int cost, int[][] board) {
        int[] dx = {-1, 1, 0, 0}; // 상, 하, 좌, 우
        int[] dy = {0, 0, -1, 1};

        Queue<Point> q = new LinkedList<>();
        q.add(new Point(x, y, dir, cost));
        visit[x][y] = true;

        while (!q.isEmpty()) {
            Point point = q.poll();
            int px = point.x;
            int py = point.y;
            int pdir = point.dir;
            int pcost = point.cost;

            if (px == n - 1 && py == n - 1) {
                answer = Math.min(answer, pcost);
            }

            for (int i = 0; i < 4; i++) {
                int nx = px + dx[i];
                int ny = py + dy[i];
                int ndir = i;
                int ncost = pcost;

                if(nx<0 || nx>=n || ny<0 || ny>=n || board[nx][ny]==1) {
                    continue;
                }

                if(pdir == -1) { // 출발하는 경우는 무조건 100
                    ncost += 100;
                } else if(pdir==ndir) { // 방향이 같다 - 직진
                    ncost += 100;
                } else { // 코너 돌기
                    ncost += 600;
                }

                if(!visit[nx][ny] || arr[ndir][nx][ny] >= ncost) {
                    visit[nx][ny] = true;
                    arr[ndir][nx][ny] = ncost;
                    q.add(new Point(nx, ny, ndir, ncost));
                }
            }
        }
    }
}
```
