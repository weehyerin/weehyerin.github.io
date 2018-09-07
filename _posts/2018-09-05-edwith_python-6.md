---
layout: post
title: "6. Pythonic Code - Enumerate & Zip"
excerpt_separator:  <!--more-->
categories:
 - edwith
tags:
  - Python
  - 머신러닝을 위한 Python
  - 기계학습
  - edwith
---

# 출처

## edwith, <https://www.edwith.org/>, 머신러닝을 위한 Python

---

<!--more-->

## Enumerate

* List의 element를 추출할 때 번호를 붙여서 추출

```python
>>> for i, v in enumerate(['tic', 'tac', 'toe']):
# list에 있는 index와 값을 unpacking
...     print(i, v)
...
0 tic
1 tac
2 toe


>>> mylist = ["a", "b", "c", "d"]
>>> list(enumerate(mylist)) # list에 있는 index와 값을 unpacking하여 list로 저장
[(0, 'a'), (1, 'b'), (2, 'c'), (3, 'd')]
>>> {i:j for i,j in enumerate('Gachon University is an academic institute located in South Korea.'.split())}
# 문장을 list로 만들고 list의 index와 값을 unpacking하여 dict로 저장
{0: 'Gachon', 1: 'University', 2: 'is', 3: 'an', 4: 'academic', 5: 'institute', 6: 'located', 7: 'in', 8: 'South', 9: 'Korea.'}
# 텍스트 마이닝할 때 쓰임
```

## Zip

* 두 개의 list의 값을 병렬적으로 추출함

```python
>>> alist = ['a1', 'a2', 'a3']
>>> blist = ['b1', 'b2', 'b3']
>>> for a, b in zip(alist, blist): # 병렬적으로 값을 추출
...     print (a,b)
...
a1 b1
a2 b2
a3 b3

>>> a,b,c = zip((1, 2, 3), (10, 20, 30), (100, 200, 300)) # 각 tuple의 같은 index끼리 묶음
>>> print(a, b, c)
(1, 10, 100) (2, 20, 200) (3, 30, 300)

>>> [sum(x) for x in zip((1,2,3), (10,20,30), (100,200,300))]
# 각 tuple 같은 index를 묶어 합을 list로 변환
# x에 tuple 형태로 들어감

[111, 222, 333]
```

* 벡터 계산에 유용
* [1, 1] + [2, 2] = [3, 3]을 만들 땐

```python
>>> a = [1, 1]
>>> b = [2, 2]
>>> c = [sum(x) for x in zip(a, b)]
>>> c
[3, 3]
```

## Enumerate & Zip

```python
>>> alist = ['a1', 'a2', 'a3']
>>> blist = ['b1', 'b2', 'b3']
>>>
>>> for i, (a,b) in enumerate(zip(alist, blist)):
...     print(i, a, b) # index alist[index] blist[index] 표시
...
0 a1 b1
1 a2 b2
2 a3 b3

>>> for i, j in enumerate(zip(alist, blist)):
...     print(i, j)
...
0 ('a1', 'b1')
1 ('a2', 'b2')
2 ('a3', 'b3')
```