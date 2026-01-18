# BFS (너비 우선 탐색)

## 개념
시작 노드에서 가장 가까운 노드를 우선적으로 방문하면서 탐색하는 그래프 완전 탐색 기법

## 목적
* 최단 경로 탐색 (가중치 없는 그래프)
* 레벨별 탐색 (같은 거리의 노드들)
* 최소 이동 횟수 계산
* 그래프의 모든 노드 방문

## 구현 방법

```java
Queue<Integer> queue = new LinkedList<>();
boolean[] visited = new boolean[n + 1];

queue.offer(start);
visited[start] = true;

while (!queue.isEmpty()) {
    int node = queue.poll();
    // 노드 처리
    
    for (int next : graph[node]) {
        if (!visited[next]) {
            visited[next] = true;
            queue.offer(next);
        }
    }
}
```

## 필수 요소

| 요소 | 설명 | 이유 |
|------|------|------|
| Queue | FIFO 구조 | 가까운 노드 우선 방문 보장 |
| visited 배열 | 방문 여부 체크 | 중복 방문 방지 |
| 그래프 표현 | 인접 리스트 | 메모리 효율적, 간선 탐색 용이 |

## DFS vs BFS 비교

| 구분 | DFS | BFS |
|------|-----|-----|
| 탐색 방식 | 깊이 우선 | 너비 우선 (레벨 순) |
| 자료구조 | Stack / 재귀 | **Queue** |
| 탐색 순서 | 한 분기 끝까지 | 가까운 노드부터 |
| 최단 경로 | **보장 안 됨** | **보장됨** |
| 메모리 사용 | 적음 (깊이만큼) | 많음 (너비만큼) |
| 사용 사례 | 경로 존재, 사이클 검출 | 최단 거리, 최소 이동 |

## 시간/공간 복잡도

| 복잡도 | 값 | 설명 |
|--------|-----|------|
| 시간 복잡도 | O(V + E) | V: 노드 수, E: 간선 수 |
| 공간 복잡도 | O(V) | visited 배열 + Queue |

## 장점
* **최단 경로 보장** (가중치 없는 그래프)
* 레벨별 탐색 가능
* 목표 노드 발견 시 즉시 종료 가능

## 단점 및 주의사항
* DFS보다 메모리 사용량 많음 (Queue에 많은 노드 저장)
* 가중치 있는 그래프에서는 최단 경로 보장 안 됨
* visited 체크 필수 (무한 루프 방지)

## 사용 가이드

### 언제 사용하면 좋은가?
* **최단 거리/최소 이동 횟수** 문제
* "가장 빠른", "최소 시간" 키워드
* 레벨별 탐색이 필요할 때
* 미로 찾기, 최단 경로

### 언제 사용하지 말아야 하는가?
* 가중치 있는 최단 경로 → 다익스트라 사용
* 모든 경로 탐색 → DFS 사용
* 메모리가 매우 제한적일 때

## 구현 패턴

### 기본 BFS
```java
// 초기화
Queue<Integer> queue = new LinkedList<>();
boolean[] visited = new boolean[n + 1];

// 시작
queue.offer(start);
visited[start] = true;

// 탐색
while (!queue.isEmpty()) {
    int curr = queue.poll();
    
    for (int next : graph[curr]) {
        if (!visited[next]) {
            visited[next] = true;
            queue.offer(next);
        }
    }
}
```

### 거리 측정 BFS
```java
int[] dist = new int[n + 1];
Arrays.fill(dist, -1);

queue.offer(start);
dist[start] = 0;

while (!queue.isEmpty()) {
    int curr = queue.poll();
    
    for (int next : graph[curr]) {
        if (dist[next] == -1) {
            dist[next] = dist[curr] + 1;
            queue.offer(next);
        }
    }
}
```

## 참고 자료
* 백준 BFS 기본 문제: 1260 , 2178, 7569


```java
// 백준 1260 DFS와 BFS
import java.util.*;
import java.io.*;

public class Main {
    static ArrayList<Integer>[] graph;
    static boolean[] visited;
    static StringBuilder dfsResult = new StringBuilder();
    static StringBuilder bfsResult = new StringBuilder();

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());

        int n = Integer.parseInt(st.nextToken()); // 정점 개수
        int m = Integer.parseInt(st.nextToken()); // 간선 개수
        int v = Integer.parseInt(st.nextToken()); // 시작 정점

        // 그래프 초기화
        graph = new ArrayList[n + 1];
        for (int i = 1; i <= n; i++) {
            graph[i] = new ArrayList<>();
        }

        // 간선 정보 입력(문제조건)
        // "양방향"
        for (int i = 0; i < m; i++) {
            st = new StringTokenizer(br.readLine());
            int a = Integer.parseInt(st.nextToken());
            int b = Integer.parseInt(st.nextToken());

            graph[a].add(b);
            graph[b].add(a);
        }
        // 간선 정렬(문제조건)
        // "단, 방문할 수 있는 정점이 여러 개인 경우에는 정점 번호가 작은 것을 먼저 방문하고"
        for (int i = 1; i <= n; i++) {
            Collections.sort(graph[i]);
        }

        visited = new boolean[n + 1];
        dfs(v);

        visited = new boolean[n + 1];
        bfs(v);

        System.out.println(dfsResult);
        System.out.println(bfsResult);
    }

    static void dfs(int node) {
        visited[node] = true;
        dfsResult.append(node).append(" ");
        for (int next : graph[node]) {
            if (!visited[next]) {
                dfs(next);
            }
        }
    }

    static void bfs(int start) {
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(start);
        visited[start] = true;

        while (!queue.isEmpty()) {
            int node = queue.poll();
            bfsResult.append(node).append(" ");
            for (int next : graph[node]) {
                if (!visited[next]) {
                    visited[next] = true;
                    queue.offer(next);
                }
            }
        }
    }
}
```