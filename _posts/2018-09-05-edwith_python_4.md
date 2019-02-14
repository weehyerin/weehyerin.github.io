---
layout: post
title: "Pythonic Code - Split & Join"
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

## Split 함수

* String Type의 값을 나눠서 List 형태로 변환

```python
>>> items = 'zero one two three'.split() # 빈칸을 기준으로 문자열 나누기
>>> print(items)​ # 뉴스 데이터에서 많이 사용, BackOfWord, 한 문장에 단어가 얼마나 포함되어 있나
['zero', 'one', 'two', 'three']

>>> example = 'python,jquery,javascript' # ","을 기준으로 문자열 나누기
>>> print(example.split(","))
['python', 'jquery', 'javascript']

>>> a, b, c = example.split(",")​
# 리스트에 있는 각 값을 a, b, c 변수로 unpacking
# a는 python b는 jquery c는 javascript

>>> example = 'cs50.gachon.edu'
>>> subdomain, domain, tld = example.split(".")
# ","을 기준으로 문자열 나누기 --> Unpacking
# subdomain은 cs50, domain은 gachon, edu는 edu
```

## Join 함수

* String List를 합쳐 하나의 String으로 반환할 때 사용

```python
>>> colors = ['red', 'blue', 'green', 'yellow']
>>> result = ''.join(colors)
>>> result
'redbluegreenyellow'
>>> result = ' '.join(colors) # 연결 시 빈칸 1칸으로 연결
>>> result
'red blue green yellow'
>>> result = ', '.join(colors) # 연결 시 ", "으로 연결
>>> result
'red, blue, green, yellow'
>>> result = '-'.join(colors) # 연결 시 "-"으로 연결
>>> result
'red-blue-green-yellow'
```