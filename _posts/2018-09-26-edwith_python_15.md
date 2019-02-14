---
layout: post
title: "Matchine Learning Overview & An understanding of data"
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

## Machine Learning Process

![image](https://user-images.githubusercontent.com/28076542/46059202-42392b00-c199-11e8-91f4-6497376508b4.png)

* 기존에 있는 데이터를 알고리즘을 사용해서 모델을 만들고 새로운 데이터에 모델을 적용하여 예측하는 방법
* 핵심은 머신러닝 알고리즘과 모델

## Key Concepts

### Model

* 예측을 위한 수학 공식, 함수 1차 방정식, 확률분포, condition rule

### Algorithms

* 어떠한 문제를 플기 위한 과정, Model을 생성하기 위한 (훈련) 과정

## 모델을 학습할 때 영향을 주는 값들

![image](https://user-images.githubusercontent.com/28076542/46059302-96dca600-c199-11e8-821c-ac150ccfa541.png)

* y값에 영향을 주는 것은 x값 하나가 아님

## Feature

* 머신러닝에서 데이터의 특징을 나타내는 변수
* feature, 독립변수, input 변수 등은 동일의미로 사용
* 일반적으로 Table 상에 Data를 표현할 때 Column을 의미
* 하나의 Data instance(실제 데이터)는 feature vector로 표현

![image](https://user-images.githubusercontent.com/28076542/46059385-fd61c400-c199-11e8-9479-af514659cbd0.png)

## Feature Vector

![image](https://user-images.githubusercontent.com/28076542/46059426-26825480-c19a-11e8-87b3-9eec64afb895.png)

![image](https://user-images.githubusercontent.com/28076542/46059445-39952480-c19a-11e8-8e6a-529db1f40364.png)

## 기본 용어

![image](https://user-images.githubusercontent.com/28076542/46059677-3cdce000-c19b-11e8-8dcc-fab8f9400518.png)

## Pandas

* 엑셀처럼 데이터 사용