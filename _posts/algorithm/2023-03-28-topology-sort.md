---
title:  "위상 정렬(Topology Sort) 알고리즘 Python 구현"
date: 2023-03-28
last_modified_at: 2023-03-28
excerpt: "위상 정렬이란?"
categories:
  - Algorithm
tags:
  - [Algorithm, Topology Sort, Python]
---

---

<br>

# 위상 정렬

- 사이클이 없는 방향 그래프의 모든 노드를 방향성에 거스르지 않도록 순서대로 나열하는 것

진입차수 (Indegree)

- 특정한 노드로 들어오는 간선의 개수

진출차수 (Outdegree)

- 특정한 노드에서 나가는 간선의 개수

위상 정렬 알고리즘

1. 진입차수가 0인 모든 노드를 큐에 삽입
2. 큐가 빌 때까지 다음의 과정을 반복
   1. 큐에서 원소를 꺼내 해당 노드에서 나가는 간선을 그래프에서 제거
   2. 새롭게 진입차수가 0이 된 노드를 큐에 삽입

- 결과적으로 각 노드가 큐에 들어온 순서가 위상 정렬을 수행한 결과가 됨
- 위상 정렬을 수행할 그래프는 사이클이 없는 방향 그래프(DAG, Directed Acyclic Graph)여야 함

위상 정렬의 특징

- DAG(순환하지 않는 방향 그래프)에 대해서만 수행 가능
- 여러 답이 존재할 수 있음
  - 한 단계에서 큐에 새롭게 들어가는 원소가 2개 이상인 경우가 있다면, 여러 가지 답이 존재
- 모든 원소를 방문하기 전에 큐가 빈다면, 사이클이 존재한다고 판단할 수 있음
  - 사이클에 포함된 원소 중에서 어떠한 원소도 큐에 들어갈 수 없음
- 스택을 활용한 DFS를 이용해 위상 정렬을 수행할 수도 있음

```python
from collections import deque

V, E = map(int, input().split())  # 노드 개수, 간선 개수
indegree = [0] * (V + 1)
g = [[] for _ in range(V + 1)]

# 간선 정보 입력
for _ in range(E):
    A, B = map(int, input().split())
    g[A].append(B)  # 정점 A -> B 이동 가능
    indegree[B] += 1  # 진입 차수 1 증가


# 위상 정렬 함수
def topology_sort():
    result = []  # 알고리즘 수행 결과
    que = deque()  # 큐
    # 처음 시작할 때는 진입차수가 0인 노드를 인큐
    for i in range(1, V + 1):
        if indegree[i] == 0:
            que.append(i)
    # 큐가 빌 때까지
    while que:
        cur = que.popleft()
        result.append(cur)
        # 해당 원소와 연결된 노드들의 진입차수에서 1 빼기
        for i in g[cur]:
            indegree[i] -= 1
            # 새롭게 진입차수가 0이 되는 노드를 인큐
            if indegree[i] == 0:
                que.append(i)
    # 위상 정렬 결과 출력
    for n in result:
        print(n, end=' ')
    return


topology_sort()

```

- 위상 정렬을 위해 차례대로 모든 노드를 확인하며 각 노드에서 나가는 간선을 차례대로 제거해야 함
- 위상 정렬 알고리즘의 시간 복잡도는 `O(V + E)`

---
