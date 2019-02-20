---
layout: post
title: "Pythonic Code - Linear algebra codes"
excerpt_separator:  <!--more-->
categories:
 - edwith
tags:
  - Python
  - 머신러닝을 위한 Python
  - 기계학습
  - edwith
---

<!--more-->

# 출처

## edwith, <https://www.edwith.org/>, 머신러닝을 위한 Python

---

## Vector representation of python

* 다양한 방법이 존재

```python
vector_a = [1, 2, 10] # List로 표현했을 경우
vector_b = (1, 2, 10) # Tuple로 표현했을 경우
vector_c = {'x' : 1, 'y' : 1, 'z' : 10} # dict로 표현했을 경우

print(vector_a, vector_b, vector_c)
```

* 이 수업에서는 vector 연산으로 할 것

## Vector의 계산

```python
u = [2, 2]
v = [2, 3]
z = [3, 5]
result = []
for i in range(len(u)):
    result.append(u[i] + v[i] + z[i])
print(result)
```

* 이런 코드는 파이썬답지 않아서 쓰지말 것
* 이렇게 쓸 것

```python
u = [2, 2]
v = [2, 3]
z = [3, 5]

result = [sum(t) for t in zip(u, v, z)]
print(result)
```

* 뺄셈은 다음과 같이 하면 됨

```python
u = [2, 2, 3]
v = [2, 4, 6]
z = [2, 5, 7]

result = [t[0] - sum(t[1:]) for t in zip(u, v, z)]
print(result)
```

## Vector의 계산: Scalar-Vector product

```python
u = [1, 2, 3] # 2([1, 2, 3] + [4, 4, 4]) = 2[5,6,7] = [10, 12, 14]
v = [4, 4, 4]
alpha = 2

result = [alpha*sum(t) for t in zip(u, v)]
print(result)
```

* 어차피 numpy로 간단하게 구현됨

## Matrix representatoin of python

* Matrix 역시 Python으로 표시하는 다양한 방법이 존재

```python
matrix_a = [[3, 6], [4, 5]] # List로 표현했을 경우
matrix_b = [(3, 6), (4, 5)] # Tuple로 표현했을 경우
matrix_c = {(0, 0): 3, (0, 1): 6, (1, 0): 4, (1, 1): 5} # dict 표현했을 경우
```

* 특히 dict로 표현할 때는 무궁무진한 방법이 있음
* 본 수업에서는 기본적으로 two-dimensional list 형태로 표현함
* [[1번째 row], [2번째 row], [3번째 row]]

## Matrix의 계산: Matrix addtion

$$C = A + B = \begin{pmatrix} 3 & 6 \\ 4 & 5 \end{pmatrix} + \begin{pmatrix} 5 & 8 \\ 6 & 7 \end{pmatrix} = \begin{pmatrix} 8 & 14 \\ 10 & 12 \end{pmatrix}$$

```python
matrix_a = [[3, 6], [4, 5]]
matrix_b = [[5, 8], [6, 7]]
result = [[sum(row) for row in zip(*t)] for t in zip(matrix_a, matrix_b)]
# zip으로 묶이면 tuple 형태임을 잊지 마십쇼
# Asterisk(*)은 괄호를 벗긴다는 것을 잊지 마십쇼
print(result)
```

## Matrix의 계산: Scalar-Matrix Product

$$\alpha\times A = 4\times \begin{pmatrix} 3 & 6 \\ 4 & 5 \end{pmatrix} = \begin{pmatrix} 12 & 24 \\ 16 & 20 \end{pmatrix}$$

```python
matrix_a = [[3, 6], [4, 5]]
alpha = 4
result = [[alpha * element for element in t] for t in matrix_a]

print(result)
```

## Matrix의 계산: Matrix Transpose

$$A = \begin{pmatrix} 1 & 2 & 3 \\ 4 & 5 & 6 \end{pmatrix}, A^T = \begin{pmatrix} 1 & 4 \\ 2 & 5 \\ 3 & 6 \end{pmatrix}$$

```python
matrix_a = [[1, 2, 3], [4, 5, 6]]
result = [[element for element in t] for t in zip(*matrix_a)]
print(result)
```

## Matrix의 계산: Matrix Product

$$A = \begin{pmatrix} 1 & 1 & 2 \\ 2 & 1 & 1 \end{pmatrix}, B = \begin{pmatrix} 1 & 1 \\ 2 & 1 \\ 1 & 3 \end{pmatrix}이면 C = A\times B = \begin{pmatrix} 1 & 1 & 2 \\ 2 & 1 & 1 \end{pmatrix}\times \begin{pmatrix} 1 & 1 \\ 2 & 1 \\ 1 & 3 \end{pmatrix} = \begin{pmatrix} 5 & 8 \\ 5 & 6 \end{pmatrix}$$

```python
matrix_a = [[1, 1, 2], [2, 1, 1]]
matrix_b = [[1, 1], [2, 1], [1, 3]]
result = [[sum(a * b for a, b, in zip(row_a, column_b)) \ 
           for column_b in zip(*matrix_b)] for row_a in matrix_a]

print(result)
```

* 첫 번째 매트릭스는 row를 가져오고 두 번째 매트릭스는 column을 가져옴
* zip(*matrix_b) 부분을 잘 이해하면 됨