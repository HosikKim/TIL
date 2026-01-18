# DFS (깊이 우선 탐색)

## 개념
시작 노드에서 한 분기를 최대 깊이까지 탐색한 후 다른 분기로 이동하는 그래프 완전 탐색 기법

## 목적
* 그래프의 모든 노드를 체계적으로 방문
* 경로 탐색 (미로 찾기, 퍼즐 해결)
* 연결 요소 확인
* 위상 정렬, 사이클 탐지

## 구현 방법

### 1. 재귀 함수 방식
```java
boolean[] visited;

void dfs(int node) {
    visited[node] = true;
    // 노드 처리
    
    for (int next : graph[node]) {
        if (!visited[next]) {
            dfs(next);
        }
    }
}
```

### 2. 스택 자료구조 방식
```java
Stack<Integer> stack = new Stack<>();
stack.push(start);
visited[start] = true;

while (!stack.isEmpty()) {
    int node = stack.pop();
    // 노드 처리
    
    for (int next : graph[node]) {
        if (!visited[next]) {
            visited[next] = true;
            stack.push(next);
        }
    }
}
```

## 필수 요소

| 요소 | 설명 | 이유 |
|------|------|------|
| visited 배열 | 방문 여부 체크 | 중복 방문 방지, 무한 루프 방지 |
| 그래프 표현 | 인접 리스트 | 메모리 효율적, 간선 탐색 용이 |
| 재귀/스택 | 탐색 순서 관리 | 깊이 우선 순서 보장 |

## 시간/공간 복잡도

| 복잡도 | 값 | 설명 |
|--------|-----|------|
| 시간 복잡도 | O(V + E) | V: 노드 수, E: 간선 수 |
| 공간 복잡도 | O(V) | visited 배열 + 재귀 스택 |

## 장점
* 구현이 간단함 (재귀 사용 시)
* 메모리 사용이 BFS보다 적을 수 있음
* 경로 추적이 용이함

## 단점 및 주의사항
* **스택 오버플로 위험**: 깊이가 깊은 그래프에서 재귀 호출 시 주의
* 최단 경로 보장 안 됨
* 무한 루프 가능성: visited 체크 필수

## 사용 가이드

### 언제 사용하면 좋은가?
* 모든 노드를 방문해야 할 때
* 경로의 존재 여부만 확인
* 백트래킹 문제 (순열, 조합)
* 연결 요소 개수 파악
* 사이클 탐지

### 언제 사용하지 말아야 하는가?
* 최단 경로를 찾아야 할 때 → BFS 사용
* 그래프 깊이가 매우 깊을 때 (스택 오버플로)
* 레벨별 탐색이 필요할 때

## 인접 리스트 표현 예시
```java
// 그래프 초기화
List<Integer>[] graph = new ArrayList[n + 1];
for (int i = 1; i <= n; i++) {
    graph[i] = new ArrayList<>();
}

// 간선 추가 (양방향)
graph[1].add(2);
graph[2].add(1);
```

## 참고 자료
* 백준 DFS 기본 문제: 1260, 2606, 2667, 11724