---
layout: post
title: "Pythonic Code Overview"
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

## 파이썬 스타일 코드(Pythonic Code)

### 예시: 여러 단어들을 하나로 붙일 때

```python
>>> colors = ['red', 'blue', 'green', 'yellow']
>>> result = ''
>>> for s in colors:
      result += s
```

### 예시: 여러 단어들을 하나로 붙일 때(join 사용)

```python
>>> colors = ['red', 'blue', 'green', 'yellow']
>>> result = ''.join(colors)
>>> result
'redbluegreenyellow'
```

### Pythonic Code

* 파이썬 스타일의 코딩 기법
* **파이썬 특유의 문법**을 활용하여 효율적으로 코드를 표현함
* 고급 코드를 작성할 수록 더 많이 필요해짐

### 앞으로 배울 것

* Split & Join
* List Comprehension
* Enumerate & Zip

### Why Pythonic Code?

#### 남 코드에 대한 이해도

많은 개발자들이 python 스타일로 코딩한다.

#### 효율

단순 for loop append보다 list가 조금 더 빠르다.
익숙해지면 코드도 짧아진다.

#### 간지

쓰면 왠지 코드 잘 짜는 것처럼 보인다.