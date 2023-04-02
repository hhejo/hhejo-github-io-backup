---
title:  "알고리즘 문제 풀이에 필요한 수학적 지식"
date: 2023-04-02
last_modified_at: 2023-04-02
excerpt: "나머지 연산, 최대공약수, 최소공배수, 소수, 에라토스테네스의 체, 소인수분해"
categories:
  - Algorithm
tags:
  - [Algorithm, math, Python]
---

---

<br>

# 알고리즘 문제에 필요한 수학

## 나머지 연산

- ```
  (A + B) mod M = ((A mod M) + (B mod M)) mod M
  ```
- ```
  (A x B) mod M = ((A mod M) × (B mod M)) mod M
  ```
- 나누기는 성립하지 않음 (modular inverse를 구해야 함)
- 뺄셈의 경우, 먼저 mod 연산을 한 결과가 음수가 나올 수 있기 때문에 해당 값을 더함
- ```
  (A - B) mod M = ((A mod M) - (B mod M) + M) mod M
  ```

## 최대공약수 (Greatest Common Divisor)

- A와 B의 공통된 약수 중 가장 큰 정수
- 서로소(coprime) : 최대공약수가 1인 두 수
- 최대공약수 찾는 방법
  1. `2`부터 `min(A, B)`까지 모든 정수로 나누기
     ```python
     g = 1
     for i in range(2, min(a, b) + 1):
       if a % i == 0 and b % i == 0:
         g = i
     ```
  2. 유클리드 호제법
     - `a % b = r` 일 때, `GCD(a, b) = GCD(b, r)`
     - `r = 0`일 때, `b`가 최대공약수
     - ```python
        def get_gcd(a, b):
            if b == 0:
                return a
            return gcd(b, a % b)
       ```
     - ```python
        def get_gcd(a, b):
            while b != 0:
                a, b = b, a % b
            return a
       ```

## 최소공배수 (Least Common Multiple)

- A와 B의 공통된 배수 중 가장 작은 정수
- ```
  LCM = (A * B) / GCM
  ```

## 소수

- 약수가 1과 자기 자신밖에 없는 수

### 에라토스테네스의 체

1. 2부터 N까지 모든 수 쓰기
2. 아직 지워지지 않은 수 중 가장 작은 수 찾기
3. 그 수는 소수가 됨
4. 그 수의 배수를 모두 지우고 반복

## 소인수분해

- N을 소인수분해 했을 때, 나올 수 있는 가장 큰 인수는 루트 N

---
