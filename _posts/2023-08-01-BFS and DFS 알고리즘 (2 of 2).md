---
title: "BFS and DFS 알고리즘 (2 of 2)"
excerpt: "Algorithm; BFS; DFS;"
categories: 
- Algorithm
tags:
- [Algorithm, BFS, DFS]
published: true
toc: true
toc_sticky: true
---

#### ✅ 음료수 얼려 먹기
```
N × M 크기의 얼음 틀이 있다. 구멍이 뚫려 있는 부분은 0, 칸막이가 존재하는 부분은 1로 표시된다.
구멍이 뚫려 있는 부분끼리 상, 하, 좌, 우로 붙어 있는 경우 서로 연결되어 있는 것으로 간주한다.
이때 얼음 틀의 모양이 주어졌을 때 생성되는 총 아이스크림의 개수를 구하는 프로그램을 작성하라.
다음의 4 × 5 얼음 틀 예시에서는 아이스크림이 총 3개가 생성된다
```
<img src="/assets/images/2023-08-01-BFS and DFS 알고리즘 (2 of 2)/1.png">

##### 1. 입력 조건
```
첫 번째 줄에 얼음 틀의 세로 길이 N과 가로 길이 M이 주어진다. (1 <= N, M <= 1,000)
두 번째 줄부터 N + 1 번째 줄까지 얼음 틀의 형태가 주어진다.
이때 구멍이 뚫려있는 부분은 0, 그렇지 않은 부분은 1이다.
```

##### 2. 출력 조건
```
한 번에 만들 수 있는 아이스크림의 개수를 출력한다.
```

##### 3. 입력 예시
```
4 5
00110
00011
11111
00000
```

##### 4. 출력 예시
```
3
```

##### 5. 구현 코드
```python
n, m = map(int, input().split())
graph = []
cnt = 0

for i in range(n):
    graph.append(list(map(int, input())))

def dfs(x, y):
    if x <= -1 or x >= n or y <= -1 or y >= m:
        return False
    if graph[x][y] == 0:
        graph[x][y] = 1
        dfs(x - 1, y)
        dfs(x + 1, y)
        dfs(x, y + 1)
        dfs(x, y - 1)
        return True
    return False

for i in range(n):
    for j in range(m):
        if dfs(i, j) == True:
            cnt += 1

print(cnt)
```

##### 6. 문제 해설
    1️⃣ 특정한 지점의 주변 상, 하, 좌, 우를 살펴본 뒤에 주변 지점 중에서 
          값이 '0'이면서 아직 방문하지 않은 지점이 있다면 해당 지점을 방문한다.
    2️⃣ 방문한 지점에서 다시 상, 하, 좌, 우를 살펴보면서 다시 진행하면, 연결된 모든 지점을 방문할 수 있다.
    3️⃣ 1 ~ 2번의 과정을 모든 노드에 반복하며 방문하지 않은 지점의 수를 센다.

<br><br><br>

#### ✅ 미로 탈출
```
N x M 크기의 직사각형 형태의 미로에 여러 마리의 괴물이 있어 이를 피해 탈출해야 한다. 
현재 위치는 (1, 1)이고 미로의 출구는 (N,M)의 위치에 존재하며 한 번에 한 칸씩 이동할 수 있다. 
괴물이 있는 부분은 0으로, 괴물이 없는 부분은 1로 표시되어 있다. 
미로는 반드시 탈출할 수 있는 형태로 제시된다. 

탈출하기 위해 움직여야 하는 최소 칸의 개수를 구하라. 
칸을 셀 때는 시작 칸과 마지막 칸을 모두 포함해서 계산한다.
```

##### 1. 입력 조건
```
첫째 줄에 두 정수 N, M(4 <= N, M <= 200)이 주어진다. 
다음 N개의 줄에는 각각 M개의 정수(0혹은 1)로 미로의 정보가 주어진다. 
각각의 수들은 공백 없이붙어서 입력으로 제시된다. 
또한 시작 칸과 마지막 칸은 항상 1이다.
```

##### 2. 출력 조건
```
첫째 줄에 최소 이동 칸의 개수를 출력한다.
```

##### 3. 입력 예시
```
5 6
101010
111111
000001
111111
111111
```

##### 4. 출력 예시
```
10
```

##### 5. 구현 코드
```python
from collections import deque

n, m = map(int, input().split())
graph = []
cnt = 0

for i in range(n):
    graph.append(list(map(int, input())))

dx = [-1, 1, 0, 0]
dy = [0, 0, -1, 1]

def bfs(x, y):
    q = deque()
    q.append((x, y))
    while q:
        x, y = q.popleft()
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx <= -1 or nx >= n or ny <= -1 or ny >= m:
                continue
            if graph[nx][ny] == 0:
                continue
            if graph[nx][ny] == 1:
                graph[nx][ny] = graph[x][y] + 1
                q.append((nx, ny))
    return graph[n - 1][m - 1]

print(bfs(0, 0))
```

##### 6. 문제 해설
    1️⃣ 맨 처음에 (1, 1) 위치에서 시작하며, (1, 1)의 값은 항상 1이라고 문제에서 언급되어있다.
    2️⃣ (1, 1) 좌표에서 상, 하, 좌, 우로 탐색을 진행하면 바로 옆 노드인 (1, 2) 위치의 노드를 방문하게 되고
          새롭게 방문하는 (1, 2)노드의 값을 2로 바꾸게 된다.
    3️⃣ 마찬가지로 BFS를 계속 수행하면 결과적으로 다음과 같이 최단 경로의 값들이 1씩 증가하는 형태로 변경된다.