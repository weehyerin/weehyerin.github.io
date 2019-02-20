---
layout: post
title: "Assignment - Insert Operation"
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

## 백준 문제

[문제 링크](https://www.acmicpc.net/problem/14888)

## 파이썬으로 푼 정답 코드

```python
import itertools
from functools import reduce


def insert_operation(length, input_num, input_oper):
    ops = {"0": (lambda x, y: x + y), "1": (lambda x, y: x - y), "2": (lambda x, y: x * y), "3": lambda x, y: x // y}
    oper_permutation = []
    result = []
    list(oper_permutation.extend([str(index)] * value) for index, value in enumerate(input_oper) if value > 0)
    permutation = [list(x) for x in set(itertools.permutations(oper_permutation))]
    for i in permutation:
        result.append(reduce(lambda x, y: ops[i.pop()](x, y), input_num))

    print(str(max(result)) + "\n" + str(min(result)))

n = 6
numbers = [1, 2, 3, 4, 5, 6]
arithmetics = [2, 1, 1, 1]
insert_operation(n, numbers, arithmetics)
```

* 파이썬으로 알고리즘 풀고 싶으면 [코드파이터](https://codesignal.com/)