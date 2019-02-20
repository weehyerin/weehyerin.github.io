---
layout: post
title: "Pythonic Code - Lambda & MapReduce"
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

## Lambda

* 함수 이름 없이 함수처럼 쓸 수 있는 익명함수
* 수학의 람다 대수에서 유래

![image](https://user-images.githubusercontent.com/28076542/45087522-249c1700-b141-11e8-9b1c-b2956e843f26.png)

* Python 3부터는 list comprehension이 있으니 쓰는 것을 권장하지 않으나 여전히 많이 쓰임

```python
>>> f = lambda x, y: x + y
>>> print(f(1,4))
5
>>> f = lambda x: x ** 2
>>> print(f(3))
9
>>> f = lambda x: x / 2
>>> print(f(3))
1.5
>>> print((lambda x: x +1)(5))
6
```

## Map function

* Sequence 자료형 각 element에 동일한 function을 적용함

![image](https://user-images.githubusercontent.com/28076542/45087739-e18e7380-b141-11e8-8391-3b50d0a2b026.png)

```python
>>> ex = [1, 2, 3, 4, 5]
>>> f = lambda x: x ** 2
>>> print(list(map(f, ex)))
# python 3에는 list를 꼭 붙여줘야 함
[1, 4, 9, 16, 25]
>>> f = lambda x, y: x + y
>>> print(list(map(f, ex, ex)))
[2, 4, 6, 8, 10]
>>> list(map(lambda x: x ** 2 if x % 2 == 0 else x, ex))
[1, 4, 3, 16, 5]
# python 3에서 람다의 필터는 else가 반드시 있어야 함

>>> [value ** 2 for value in ex]
[1, 4, 9, 16, 25]
# list comprehension으로도 같은 결과가 나옴

# list를 안 쓰고 출력하는 방법
>>> ex = [1, 2, 3, 4, 5]
>>> print(list(map(lambda x: x+x, ex)))
[2, 4, 6, 8, 10]

>>> print((map(lambda x: x+x, ex)))
<map object at 0x7f2305a8add8>

>>> f = lambda x: x ** 2
>>> print(map(f, ex))
<map object at 0x7f2305a8af28>

# for loop로 하나씩 받으면 출력할 수 있음
>>> for i in map(f, ex):
...     print(i)
...
1
4
9
16
25
```

## Reduce function

* map function과 달리 list에 똑같은 함수를 적용해서 통합

```python
# Reduce
>>> from functools import reduce
>>> print(reduce(lambda x, y: x+y, [1, 2, 3, 4, 5]))
# 1+2
# 3+3
# 6+4
# 10+5
15

>>> def factorial(n):
...     return reduce(
...             lambda x, y: x*y, range(1, n+1))
...
>>> factorial(5)
# 1*2
# 2*3
# 6*4
# 24*5
120
```

## Summary

* Lambda, map, reduce는 간단한 코드로 다양한 기능을 제공
* 그러나 코드의 직관성이 떨어져서 lambda나 reduce는 python3에서 사용을 권장하지 않음
* Legacy library나 다양한 머신러닝 코드에서 여전히 사용 중