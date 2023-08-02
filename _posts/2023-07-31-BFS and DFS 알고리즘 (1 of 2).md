---
title: "BFS and DFS 알고리즘 (1 of 2)"
excerpt: "Algorithm; BFS; DFS;"
categories: 
- Algorithm
tags:
- [Algorithm, BFS, DFS]
published: true
toc: true
toc_sticky: true
---

### 📌 개요
---


<a style="color:red;">탐색이란 많은 양의 데이터 중에서 원하는 데이터를 찾는 과정을 의미</a> <br>
대표적인 탐색 알고리즘으로 DFS와 BFS를 꼽을 수 있음 <br>
기본 자료구조인 스택과 큐, 재귀 함수에 대한 이해가 전제된다. <br>
자료구조란 데이터를 표현하고 관리 및 처리하기 위한 구조를 의미한다. <br>
스택과 큐는 삽입과 삭제의 함수가 핵심적인 역할을 하며, <br>
오버플로우와 언더플로우를 고려하여 구현해야 한다.

<br><br>

### ✅ 스택
---


- 선입후출 / 후입선출 구조라고 한다.
- 리스트를 기반으로 하기 때문에 별도의 라이브러리를 사용할 필요가 없다.
- append() 메소드를 통해 PUSH를, pop() 메소드를 통해 POP을 구현한다.

<br><br><br>

### ✅ 큐
---


- 선입선출
- collections 모듈에서 제공하는 deque 자료구조를 활용한다.
```python
from collections import deque
```
- deque는 스택과 큐의 장점을 모두 채택한 것인데, <br>
데이터를 넣고 빼는 속도가 리스트 자료형에 비해 효율적이고 <br>
queue 라이브러리를 이용하는 것보다 더 간단하다.
- 대부분의 코딩 테스트에서는 collections 모듈과 같은 기본 라이브러리 사용을 허용한다.
- deque 객체를 리스트 자료형으로 변경하고자 한다면 list() 메소드를 이용한다.



<br><br><br>

### ✅ 재귀 함수
---


- 자기 자신을 다시 호출하는 함수를 의미한다.
- 재귀 함수가 언제 끝날지, 종료 조건을 꼭 명시해야 한다.
- 컴퓨터 내부에서 재귀 함수의 수행은 스택 자료구조를 이용한다.
- 재귀 함수를 이용하는 대표적 예제로는 팩토리얼 문제가 있다.
- 다만 재귀적으로 문제를 풀지, 반복적으로 풀지는 시간복잡도를 고려하여 고민해봐야 한다.
  
<br><br><br>

### ✅ 탐색 알고리즘 DFS / BFS
---


#### ✔ DFS
DFS는 Depth-First-Search, 깊이 우선 탐색이라고도 부르며, <br> 
<a style="color:red;">그래프에서 깊은 부분을 우선적으로 탐색하는 알고리즘이다.</a> <br>

    1️⃣ 탐색 시작 노드를 스택에 삽입하고 방문 처리를 한다.
    2️⃣ 스택의 최상단 노드에 방문하지 않은 인접 노드가 있으면 그 인접 노드를 스택에 넣고 방문 처리를 한다.
          방문하지 않은 인접 노드가 없으면 스택에서 최상단 노드를 꺼낸다.
    3️⃣ 2번의 과정을 더 이상 수행할 수 없을 때까지 반복한다.

DFS를 설명하기 전에 먼저 그래프의 기본 구조를 알아야 한다. <br>
그래프는 노드(정점)와 간선으로 표현된다. <br>
또, 그래프는 '인접 행렬 방식'과 '인접 리스트' 2가지 방식으로 표현할 수 있다. <br>

##### 인접 행렬 방식
2차원 배열로 그래프의 연결 관계를 표현하는 방식 <br>
연결이 되어 있지 않은 노드끼리는 무한의 비용이라고 작성한다. <br>
인접 행렬 방식은 모든 관계를 저장하므로 노드 개수가 많을수록 메모리가 불필요하게 낭비된다.
```python
INF = 999999999

graph = [
    [0, 7, 5],
    [7, 0, INF],
    [5, INF, 0]
]
```


##### 인접 리스트
리스트로 그래프의 연결관계를 표현하는 방식 <br>
인접 리스트는 '연결 리스트'라는 자료구조를 이용해 구현하는데, <br>
C++나 JAVA에서는 별도의 라이브러리가 존재하지만, <br>
파이썬에서는 기본 자료형인 리스트가 append()와 메소드를 제공하므로, <br>
인접 행렬 방식과 같이 단순히 2차원 리스트를 이용하면 된다는 점을 기억해야 한다. <br>
인접 리스트 방식은 인접 행렬 방식에 비해 <br>
특정한 두 노드가 연결되어 있는지에 대한 정보를 얻는 속도가 느리다. <br>
연결된 데이터를 하나씩 확인해야 하기 때문이다.
```python
graph = [[] for _ in range(3)]

graph[0].append((1, 7))
graph[0].append((2, 5))

graph[1].append((0, 7))

graph[2].append((0, 5))

print(graph)
```


##### DFS 예제
```python
def dfs(graph, v, visited):
    visited[v] = True
    print(v, end=' ')

    for i in graph[v]:
        if not visited[i]:
            dfs(graph, i, visited)

graph = [
    [],
    [2, 3, 8],
    [1, 7],
    [1, 4, 5],
    [3, 5],
    [3, 4],
    [7],
    [2, 6, 8],
    [1, 7]
]

visited = [False] * 9

dfs(graph, 1, visited)
```

<br><br><br>

#### ✔ BFS
BFS는 Breadth-First-Search, 너비 우선 탐색이라는 의미를 가진다. <br>
쉽게 말해 가까운 노드부터 탐색하는 알고리즘이다. <br>
스택 자료구조를 이용하는 DFS와는 다르게, <br>
<a style="color:red;">BFS는 큐 자료구조를 이용하는 것이 정석이다.</a> <br>
탐색을 수행함에 있어 O(N)의 시간이 소요된다. <br>
일반적인 경우 실제 수행 시간은 DFS보다 좋은 편이라는 점까지만 추가로 기억하자.

    1️⃣ 탐색 시작 노드를 큐에 삽입하고 방문 처리를 한다.
    2️⃣ 큐에서 노드를 꺼내 해당 노드의 인접 노드 중에서 
          방문하지 않은 노드를 모두 큐에 삽입하고 방문 처리를 한다.
    3️⃣ 2번의 과정을 더 이상 수행할 수 없을 때까지 반복한다.

##### BFS 예제
```python
from collections import deque

def bfs(graph, s, visited):
    queue = deque()
    queue.append(s)
    visited[s] = True

    while queue:
        v = queue.popleft()
        print(v, end=' ')

        for i in graph[v]:
            if not visited[i]:
                queue.append(i)
                visited[i] = True

graph = [
    [],
    [2, 3, 8],
    [1, 7],
    [1, 4, 5],
    [3, 5],
    [3, 4],
    [7],
    [2, 6, 8],
    [1, 7]
]

visited = [False] * 9

bfs(graph, 1, visited)
```
  
<br><br><br>

### 🤷‍♂️ 어디에 적용해야할까?

DFS나 BFS를 설명하는 데 전형적인 그래프 그림을 이용하게 되는데, <br>
1차원 배열이나 2차원 배열 또한 그래프 형태로 생각하면 수월하게 문제를 풀 수 있다. <br>
즉 코딩 테스트에서 탐색 문제를 보면 그래프 형태로 표현한 다음 풀이법을 고민하도록 하자.