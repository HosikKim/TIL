# 최소 신장 트리 (MST) - 크루스칼 알고리즘

## 개념
그래프의 모든 노드를 연결하면서 사용된 에지 가중치의 합을 최소로 하는 트리

## 목적
* 최소 비용으로 모든 노드 연결
* 네트워크 구축 비용 최소화
* 도로망, 통신망 설계
* 클러스터링

## 핵심 특징

| 특징 | 설명 |
|------|------|
| 사이클 | **포함 불가** (사이클 있으면 최소 아님) |
| 에지 개수 | **N-1개** (N: 노드 개수) |
| 알고리즘 | 크루스칼, 프림 |
| 시간 복잡도 | O(E log E) - 정렬 때문 |

## MST 구현 알고리즘

| 알고리즘 | 방식 | 자료구조 | 시간 복잡도 |
|---------|------|---------|-----------|
| 크루스칼 | 에지 중심 | 에지 리스트 + 유니온 파인드 | O(E log E) |
| 프림 | 노드 중심 | 인접 리스트 + 우선순위 큐 | O(E log V) |

## 크루스칼 알고리즘 5단계

### 1. 에지 리스트 + 유니온 파인드 초기화

```java
class Edge implements Comparable<Edge> {
    int start, end, weight;
    
    Edge(int s, int e, int w) {
        start = s; end = e; weight = w;
    }
    
    @Override
    public int compareTo(Edge o) {
        return this.weight - o.weight;
    }
}

List<Edge> edges = new ArrayList<>();
int[] parent = new int[n + 1];

// 유니온 파인드 초기화
for (int i = 1; i <= n; i++) {
    parent[i] = i;
}
```

### 2. 가중치 기준 오름차순 정렬

```java
Collections.sort(edges);  // Comparable 구현으로 자동 정렬
```

| 인덱스 | 출발 | 도착 | 가중치 |
|--------|------|------|--------|
| 1 | 4 | 5 | 2 |
| 2 | 1 | 3 | 3 |
| 3 | 2 | 4 | 4 |
| 4 | 2 | 5 | 5 |
| 5 | 1 | 2 | 8 |
| 6 | 3 | 4 | 13 |

### 3. 가중치 낮은 에지부터 연결 시도

**사이클 확인 후 연결**

```java
for (Edge edge : edges) {
    // find 연산으로 사이클 확인
    if (find(edge.start) != find(edge.end)) {
        // 사이클 형성 안 되면 union 연결
        union(edge.start, edge.end);
        totalCost += edge.weight;
        edgeCount++;
    }
}
```

### 4. N-1개까지 반복

```java
if (edgeCount == n - 1) {
    break;  // MST 완성
}
```

### 5. 총 에지 비용 출력

```java
System.out.println(totalCost);
```

## 완전한 구현 코드

```java
class Kruskal {
    static class Edge implements Comparable<Edge> {
        int start, end, weight;
        Edge(int s, int e, int w) {
            start = s; end = e; weight = w;
        }
        public int compareTo(Edge o) {
            return this.weight - o.weight;
        }
    }
    
    static int[] parent;
    static List<Edge> edges;
    static int n, m;
    
    // Find 연산 (경로 압축)
    static int find(int x) {
        if (parent[x] == x) return x;
        return parent[x] = find(parent[x]);
    }
    
    // Union 연산
    static void union(int a, int b) {
        a = find(a);
        b = find(b);
        if (a != b) parent[b] = a;
    }
    
    static int kruskal() {
        // 1. 유니온 파인드 초기화
        parent = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            parent[i] = i;
        }
        
        // 2. 가중치 오름차순 정렬
        Collections.sort(edges);
        
        int totalCost = 0;
        int edgeCount = 0;
        
        // 3. 낮은 가중치부터 연결
        for (Edge edge : edges) {
            if (find(edge.start) != find(edge.end)) {
                union(edge.start, edge.end);
                totalCost += edge.weight;
                edgeCount++;
                
                // 4. N-1개면 종료
                if (edgeCount == n - 1) break;
            }
        }
        
        return totalCost;
    }
}
```

## 동작 과정 예시

**노드 5개, 에지: 4-5(2), 1-3(3), 2-4(4), 2-5(5), 1-2(8), 3-4(13)**

| 단계 | 에지 | 가중치 | 사이클? | 연결 | 누적 비용 |
|-----|------|--------|---------|------|----------|
| 1 | 4-5 | 2 | X | ✓ | 2 |
| 2 | 1-3 | 3 | X | ✓ | 5 |
| 3 | 2-4 | 4 | X | ✓ | 9 |
| 4 | 2-5 | 5 | X | ✓ | 14 |
| 5 | 1-2 | 8 | ✗ | - | 14 |
| 6 | 3-4 | 13 | ✗ | - | 14 |

→ N-1 = 4개 에지 연결 완료, 총 비용: 14

## 시간/공간 복잡도

| 복잡도 | 값 | 설명 |
|--------|-----|------|
| 시간 복잡도 | O(E log E) | 에지 정렬이 병목 |
| 공간 복잡도 | O(E + V) | 에지 리스트 + 유니온 파인드 |

## 핵심 포인트

| 포인트 | 설명 |
|--------|------|
| 유니온 파인드 필수 | 사이클 판별용 |
| 에지 정렬 | 가중치 오름차순 |
| find 연산 | 대표 노드 비교로 사이클 확인 |
| 종료 조건 | 에지 개수 N-1개 |

## 왜 N-1개 에지인가?

트리의 정의:
- N개 노드를 연결하는 최소 에지 = N-1개
- N-1개보다 적으면 → 연결 안 된 노드 존재
- N개 이상이면 → 사이클 존재

## 장점 및 단점

### 장점
* 간단하고 직관적인 구현
* 희소 그래프(E가 작음)에서 효율적
* 모든 노드 연결 보장

### 단점
* 에지 정렬 비용 O(E log E)
* 밀집 그래프(E가 많음)에서 비효율적
* 동적 그래프에 부적합

## 그래프 초기화 예시

```java
List<Edge> edges = new ArrayList<>();

// 에지 추가 (양방향은 한 번만)
edges.add(new Edge(1, 2, 8));
edges.add(new Edge(1, 3, 3));
edges.add(new Edge(2, 4, 4));
// ...

// 유니온 파인드 초기화
int[] parent = new int[n + 1];
for (int i = 1; i <= n; i++) {
    parent[i] = i;
}
```

## 참고 자료
* 백준 MST 문제: 1197
```java
import java.io.*;
import java.util.*;

class Edge implements Comparable<Edge> {
    int start, end, weight;

    Edge(int s, int e, int w) {
        start = s;
        end = e;
        weight = w;
    }

    @Override
    public int compareTo(Edge o) {
        return Integer.compare(this.weight, o.weight);
    }
}

public class Main {
    static int[] parent; // 유니온 파인드 배열(부모노드를 찾기 위해)

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());

        int V = Integer.parseInt(st.nextToken());
        int E = Integer.parseInt(st.nextToken());

        ArrayList<Edge> edges = new ArrayList<>();

        for (int i = 0; i < E; i++) {
            st = new StringTokenizer(br.readLine());
            int start = Integer.parseInt(st.nextToken());
            int end = Integer.parseInt(st.nextToken());
            int weight = Integer.parseInt(st.nextToken());
            edges.add(new Edge(start, end, weight));
        }

        parent = new int[V + 1];
        for (int i = 1; i <= V; i++) {
            parent[i] = i;
        }

        Collections.sort(edges);

        int selectedEdges = 0;
        long sumWeight = 0;

        for (Edge edge : edges) {
            if (union(edge.start, edge.end)) {
                selectedEdges++;
                sumWeight += edge.weight;
            }

            if (selectedEdges == V - 1) {
                break;
            }
        }
        System.out.println(sumWeight);
    }

    // x의 부모 찾기
    static int find(int x) {
        if (parent[x] == x)
            return x;
        return parent[x] = find(parent[x]);
    }

    // a와 b를 같은 집합으로
    static boolean union(int a, int b) {
        // 두 원소의 부모를 찾아서 연결
        a = find(a);
        b = find(b);
        // 부모가 다르면 연결 가능(사이클이 없음)
        if (a != b) {
            parent[b] = a;
            return true;
        }
        return false;
    }
}
```