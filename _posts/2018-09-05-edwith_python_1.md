---
layout: post
title: "Intro"
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

## 공부 순서

1. Python
2. 데이터 전처리
3. 약간의 머신러닝
4. 딥러닝은 안 함 --> 김성훈 교수 강의 들으셈

### 사이킷런

* 머신러닝 오픈소스 코드
* 이런 것이 있음

### 이 코드를 쓸 수 있는 가는 여정

```python
>>> from sklearn import linear_model
>>> reg = linear_model.LinearRegression()
>>> reg.fit ([[0, 0], [1, 1], [2, 2]], [0, 1, 2])
LinearRegression(copy_X=True, fit_intercept=True, n_jobs=1, normalize=False)
>>> reg.coef_
array([0.5 0.5])
```

### 배우면 좋을 사람

1. 파이썬 배운지 얼마 안 된 사람
2. 딥러닝 막 시작한 사람

### 쉬울 것