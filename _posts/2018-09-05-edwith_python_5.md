---
layout: post
title: "5. Pythonic Code - List Comprehension"
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

## List comprehension

* 기존 List 사용하여 간단히 다른 List를 만드는 기법
* 포괄적인 List, 포함되는 리스트라는 의미로 사용됨
* 파이썬에서 가장 많이 사용되는 기법 중 하나
* 일반적으로 for + append보다 빠름

## List comprehension (1/4)

### General style

```python
>>> result = []
>>> for i in range(10):
... result.append(i)
...
>>> result
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### List comprehension Style

```python
>>> result = [i for i in range(10)]
>>> result
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> result = [i for i in range(10) if i % 2 == 0] # 필터를 만들어 조건을 만듦
>>> result
[0, 2, 4, 6, 8]
```

## List comprehension (2/4)

```python
>>> word_1 = "Hello"
>>> word_2 = "World"
>>> result = [i+j for i in word_1 for j in word_2]
  # Nested For loop
>>> result
['HW', 'Ho', 'Hr', 'Hl', 'Hd', 'eW', 'eo', 'er', 'el', 'ed', 'lW', 'lo', 'lr', 'll', 'ld', 'lW', 'lo', 'lr', 'll', 'ld', 'oW', 'oo', 'or', 'ol', 'od']
```

`for i in word_1 for j in word_2`는 실제로

```python
for i in word+1:
  for j in word_2:
```

위와 같다.

`[i+j for i in word_1 for j in word_2]`와

`[[i+j for i in word_1] for j in word_2]`와 다르다.

```python
>>> word_1 = "Hello"
>>> word_2 = "World"
>>> result = [i+j for i in word_1 for j in word_2]
>>> result
['HW', 'Ho', 'Hr', 'Hl', 'Hd', 'eW', 'eo', 'er', 'el', 'ed', 'lW', 'lo', 'lr', 'll', 'ld', 'lW', 'lo', 'lr', 'll', 'ld', 'oW', 'oo', 'or', 'ol', 'od']
>>> result2 = [[i+j for i in word_1] for j in word_2]
>>> result2
[['HW', 'eW', 'lW', 'lW', 'oW'], ['Ho', 'eo', 'lo', 'lo', 'oo'], ['Hr', 'er', 'lr', 'lr', 'or'], ['Hl', 'el', 'll', 'll', 'ol'], ['Hd', 'ed', 'ld', 'ld', 'od']]
```

## List comprehension (3/4)

```python
>>> case_1 = ["A", "B", "C"]
>>> case_2 = ["D", "E", "A"]
>>> result = [i+j for i in case_1 for j in case_2]
>>> result
['AD', 'AE', 'AA', 'BD', 'BE', 'BA', 'CD', 'CE', 'CA']
>>> result = [i+j for i in case_1 for j in case_2 if not(i==j)]
# Filter: i랑 j가 같다면 list에 추가하지 않음
>>> result
['AD', 'AE', 'BD', 'BE', 'BA', 'CD', 'CE', 'CA']
>>> result.sort()
>>> result
['AD', 'AE', 'BA', 'BD', 'BE', 'CA', 'CD', 'CE']
```

> 카르테지안 프로덕트에서 합쳐서 경우의 수를 찾을 때 for loop 두 번을 사용하면 됨

## List comprehension(4/4)

```python
>>> words = 'The quick brown fox jumps over the lazy dog'.split()
# 문장을 빈 칸 기준으로 나눠 list로 변환

>>> print(words)
['The', 'quick', 'brown', 'fox', 'jumps', 'over', 'the', 'lazy', 'dog']
>>>
>>> stuff = [[w.upper(), w.lower(), len(w)] for w in words]
# list의 각 elements들을 대문자, 소문자, 길이로 변환하여 two dimensional list로 변환

>>> for i in stuff:
...     print(i)
...
['THE', 'the', 3]
['QUICK', 'quick', 5]
['BROWN', 'brown', 5]
['FOX', 'fox', 3]
['JUMPS', 'jumps', 5]
['OVER', 'over', 4]
['THE', 'the', 3]
['LAZY', 'lazy', 4]
['DOG', 'dog', 3]
```

### Two dimensional vs One dimensional

```python
>>> case_1 = ["A", "B", "C"]
>>> case_2 = ["D", "E", "A"]

# 안이 고정되고 대괄호 한 쌍
>>> result = [a+b for a in case_1 for b in case_2]
>>> print(result)
['AD', 'AE', 'AA', 'BD', 'BE', 'BA', 'CD', 'CE', 'CA']

# 바깥이 고정되고 대괄호 세 쌍
>>> result = [[a+b for a in case_1] for b in case_2]
>>> print(result)
[['AD', 'BD', 'CD'], ['AE', 'BE', 'CE'], ['AA', 'BA', 'CA']]
```