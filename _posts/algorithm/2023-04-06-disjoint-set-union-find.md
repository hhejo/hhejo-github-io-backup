---
title:  "상호 배타 집합과 유니온 파인드"
date: 2023-04-06
last_modified_at: 2023-04-06
excerpt: "상호 배타 집합(분리 집합, 서로소 집합)이란? 그리고 유니온 파인드?"
categories:
  - Algorithm
tags:
  - [Algorithm, Union-Find, Disjoint-Set, Python]
---

---

<br>

# 상호 배타 집합 (Disjoint-Set)

- 분리 집합, 서로소 집합이라고도 부르며, 공통 원소가 없는 두 집합을 의미한다.
- 서로소 집합 자료구조 : 서로소 부분 집합들로 나누어진 원소들의 데이터를 처리하기 위한 자료구조
- 서로소 집합 자료구조의 연산
  - 합집합(Union) : 두 개의 원소가 포함된 집합을 하나의 집합으로 합치는 연산
  - 찾기(Find) : 특정한 원소가 속한 집합이 어떤 집합인지 알려주는 연산
- 여러 개의 합치기 연산이 주어졌을 때,
  1. 합집합(Union) 연산을 확인하여, 서로 연결된 두 노드 A, B를 확인
     1. A와 B의 루트 노드 A', B'을 각각 찾음
     2. A'을 B'의 부모 노드로 설정
  2. 모든 합집합(Union) 연산을 처리할 때까지 1번의 과정을 반복
- 서로소 집합 자료구조에서는 연결성을 통해 손쉽게 집합의 형태를 확인할 수 있음
- 기본적인 형태의 서로소 집합 자료구조에서는 루트 노드에 즉시 접근할 수 없음 -> 루트 노드를 찾기 위해 부모 테이블을 계속해서 확인하며 거슬러 올라가야 함

## Union-Find

```python
# x가 속한 집합 탐색
def find(x):
    global p
    if p[x] != x:
        p[x] = find(p[x])
    return p[x]


# x와 y가 속한 집합 병합 (x <- y)
def union(x, y):
    x, y = find(x), find(y)
    p[y] = x
    return


V, E = map(int, input().split())  # 노드 개수, 간선 개수

p = [i for i in range(V + 1)]  # 부모 배열, p[i] : i의 부모 인덱스

# Union
for i in range(E):
    A, B = map(int, input().split())
    union(A, B)

print(*p[1:])

```


### 경로 압축

- 합집합(Union) 연산이 편향되게 이루어지는 경우, 찾기(Find) 함수가 비효율적으로 동작
- 최악의 경우, 찾기(Find) 함수가 모든 노드를 다 확인하게 되어 시간 복잡도가 `O(V)`
  - `{1, 2, 3, 4, 5}` : `Union(4, 5)`, `Union(3, 4)`, `Union(2, 3)`, `Union(1, 2)`
- 경로 압축(Path Compression) -> Find 연산 최적화
- Find 함수를 재귀적으로 호출하며 부모 테이블 값을 바로 갱신
- ```python
  # 변경 전
  def find(x):
      if p[x] != x:
          return find(p[x])  # 그냥 부모 찾기
      return x  # 부모(루트) 리턴
  ```
- ```python
  # 변경 후
  def find(x):
      if p[x] != x:
          p[x] = find(p[x])  # 부모 테이블 계속 갱신
      return p[x]  # 갱신된 부모 (루트) 리턴
  ```
- 경로 압축 기법을 적용하면 각 노드에 대하여 찾기(Find) 함수를 호출한 이후, 해당 노드의 루트 노드가 바로 부모 노드가 됨
- 모든 Union 연산 후, 각 원소에 대해 Find 연산 수행 -> 경로 압축 (부모 테이블 갱신)
- 시간 복잡도 개선

## 무방향 그래프 사이클 확인

- 무방향 그래프의 사이클 여부 -> 상호 배타 집합
  - 각 간선에 대해, 두 원소 각각 Find 연산을 수행하고, 그 결과가 같다면 (루트 노드가 같음 => 같은 집합에 속한다면) 사이클이 발생한 것
- 방향 그래프의 사이클 여부 -> DFS

```python
for a, b in edges:
    if find(a) == find(b):
        print('사이클 발생')
        break
```

---
