---
layout: post
title: "2. 기본적인 Machine Learning의 용어와 개념 설명"
excerpt_separator:  <!--more-->
categories:
 - edwith
tags:
  - 머신러닝과 딥러닝 BASIC
  - 기계학습
  - edwith
---

# 출처

## edwith, <https://www.edwith.org/>, 머신러닝과 딥러닝 BASIC

---

<!--more-->

## 오늘 배울 것

![1](https://user-images.githubusercontent.com/28076542/45295109-96f16a80-b538-11e8-99ab-1155abdd975d.PNG)

* 머신러닝의 기본적인 용어를 배울 것

---

## 기본 개념

![2](https://user-images.githubusercontent.com/28076542/45295110-96f16a80-b538-11e8-84d4-773fc9000303.PNG)

* ML = Machine Learning
* 이런거 얘기할 것

---

## 머신러닝은 뭐지?

![3](https://user-images.githubusercontent.com/28076542/45295111-978a0100-b538-11e8-91e7-be908745f139.PNG)

* 머신러닝은 소프트웨어
* 일반적인 소프트웨어는 입력을 하면 입력을 기반으로 데이터를 보여주는데 이러한 프로그램을 explit programming이라 함
* explit programming은 Smap filter로 스팸메일인지 아닌지 파악하기가 힘듦
* 1959 Arthur라는 사람이 자료에서 배우면 어떨까 하는 생각을 냄 --> 머신러닝
* 즉 머신러닝은 프로그램이 데이터로 학습하는 것

---

## 지도/비지도 학습

![4](https://user-images.githubusercontent.com/28076542/45295112-978a0100-b538-11e8-8c39-823374be5a90.PNG)

* 학습하는 방법에 따라서 지도/비지도로 나뉘어짐
* 지도는 정해져있는 labeled된 데이터로 학습하는 것

---

## 지도 학습의 예

![5](https://user-images.githubusercontent.com/28076542/45295113-978a0100-b538-11e8-8598-b942a0b22cbc.PNG)

* 이미지를 주어주고 개인지 고양이인지 파악하는 것도 지도 학습
* 개, 고양이로 labeled된 이미지를 주고 가르쳐준 것

---

## 비지도 학습은 뭐지?

![6](https://user-images.githubusercontent.com/28076542/45295114-978a0100-b538-11e8-845c-88dc25ea0671.PNG)

* 요즘엔 서비스 중단한 구글 뉴스는 유사한 것들끼리 기사를 모아놓았음
* 비슷한 단어들끼리 모아놓는 것
* 위 사례는 전부 labeled가 된 데이터가 아니고 데이터를 가지고 학습한 것으로 비지도 학습

---

## 강의에선 지도 학습을 주로 다룰 것

![7](https://user-images.githubusercontent.com/28076542/45295115-98229780-b538-11e8-91b5-52141482f8cc.PNG)

* 이미지 라벨링, 이메일 스팸 필터, 시험 성적 예상
* 위 사례는 전부 지도 학습의 예

---

## 지도 학습에서 사용하는 데이터

![8](https://user-images.githubusercontent.com/28076542/45295642-6b6f7f80-b53a-11e8-9cb2-1b2452b60dc6.PNG)

* 미리 트레이닝 시켜 놓고 $$X_test$$로 물어볼 때 답을 내는 것
* 이러한 트레이닝을 해주는 데이터를 트레이닝 데이터 셋이라 함

---

## 알파고도 마찬가지

![9](https://user-images.githubusercontent.com/28076542/45295787-e173e680-b53a-11e8-82bf-40bf9a6ca76d.PNG)

---

## 지도 학습의 유형

![10](https://user-images.githubusercontent.com/28076542/45295119-98229780-b538-11e8-9401-7c650d4f1672.PNG)

* 시간을 사용한 것에 기반해서 시험의 성적을 예측하는 시스템은 0점에서 100점까지 범위가 넓어 **regression**이라 함
* 시험 성적이 점수가 아니라 pass/non-pass라면 **binary classification**이라 함
* 시간을 사용한 것에 기반해서 A, B, C, D and F로 학점을 주려고 할 때 성적을 주려고 하면 레이블이 많아 **multi-label classification**이라 함

---

## regression의 예

![11](https://user-images.githubusercontent.com/28076542/45295121-98bb2e00-b538-11e8-899c-5a8d5cf25b01.PNG)

* 10시간 공부했더니 90점
* 9시간 공부했더니 80점와 같은 표를 이용해 학습을 함
* 어떤 사람이 7시간 공부하면 점수는 몇 점일까라고 물으면 대충 75점 정도 받겠다라고 예측함

---

## Pass/non-pass의 예

![12](https://user-images.githubusercontent.com/28076542/45295122-98bb2e00-b538-11e8-8d01-a9bfd87b3c0a.PNG)

* P/F는 classification --> binary classfication이라 할 수 있음

---

## Multi-label classfication의 예

![13](https://user-images.githubusercontent.com/28076542/45295124-98bb2e00-b538-11e8-9b50-b1a713d1f0cc.PNG)

* 제곧내

---

## 다음에 또 만나요

![14](https://user-images.githubusercontent.com/28076542/45295127-98bb2e00-b538-11e8-88c8-21ae8c6fda82.PNG)