---
title: "[1260] Baekjoon - DFS와 BFS"
excerpt: "Algorithm; DFS; BFS"
categories: 
- Algorithm
tags:
- [Algorithm, DFS, BFS]
published: true
toc: true
toc_sticky: true
---

<br><br>
##### **문제**

```
그래프를 DFS로 탐색한 결과와 BFS로 탐색한 결과를 출력하는 프로그램을 작성하시오. 단, 방문할 수 있는 정점이 여러 개인 경우에는 정점 번호가 작은 것을 먼저 방문하고, 더 이상 방문할 수 있는 점이 없는 경우 종료한다. 정점 번호는 1번부터 N번까지이다.
```

<br><br>
##### **입력**
```
첫째 줄에 정점의 개수 N(1 ≤ N ≤ 1,000), 간선의 개수 M(1 ≤ M ≤ 10,000), 탐색을 시작할 정점의 번호 V가 주어진다. 다음 M개의 줄에는 간선이 연결하는 두 정점의 번호가 주어진다. 어떤 두 정점 사이에 여러 개의 간선이 있을 수 있다. 입력으로 주어지는 간선은 양방향이다.
```

<br><br>
##### **출력**
```
첫째 줄에 DFS를 수행한 결과를, 그 다음 줄에는 BFS를 수행한 결과를 출력한다. V부터 방문된 점을 순서대로 출력하면 된다.
```

<br><br>
##### **코드**
```python
import sys
from collections import deque

# BFS : 함수 선언
def bfs(V) :
    q = deque()
    q.append(V)
    visited_1[V] = 1
    while q :
        V = q.popleft()
        print(V, end=' ')
        for i in range(1, N+1) :
            if visited_1[i] == 0 and graph[V][i] == 1 :
                q.append(i)
                visited_1[i] = 1

# DFS : 함수 선언
def dfs(V) :
    visited_2[V] = 1
    print(V, end=' ')
    for i in range(1, N+1) :
        if visited_2[i] == 0 and graph[V][i] == 1 :
            dfs(i)

# 입력 : 변수 초기화
input = sys.stdin.readline
N, M, V = map(int, input().split())
visited_1 = [0] * (N+1)
visited_2 = [0] * (N+1)
graph = [[0] * (N+1) for _ in range(N+1)]

# 입력 : 그래프 초기화
for _ in range(M) :
    a, b = map(int, input().split())
    graph[a][b] = graph[b][a] = 1

# 함수 실행
dfs(V)
print()
bfs(V)
```

<br>
---
<br>

##### **후기**<br><br>
DFS와 BFS에 대한 기초적인 문제였으나, <br>
어떻게 시작해야할지 감이 안 잡혀 다른 분의 코드를 참고하였다.<br>
계속 보고, 분석하여 스스로 생각하여 풀 수 있게 해야겠다.


<br><br>
```참고사이트```
- [https://velog.io/@hamfan524/%EB%B0%B1%EC%A4%80-1260%EB%B2%88-Python-%ED%8C%8C%EC%9D%B4%EC%8D%AC](https://velog.io/@hamfan524/%EB%B0%B1%EC%A4%80-1260%EB%B2%88-Python-%ED%8C%8C%EC%9D%B4%EC%8D%AC)