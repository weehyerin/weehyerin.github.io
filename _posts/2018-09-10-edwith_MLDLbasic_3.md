---
layout: post
title: "기본적인 Machine Learning의 용어와 개념 설명"
excerpt_separator:  <!--more-->
categories:
 - edwith
tags:
  - 머신러닝과 딥러닝 BASIC
  - 기계학습
  - edwith
---

# 출처

## edwith, <https://www.edwith.org/>, 머신러닝과 딥러닝 BASIC>

## <https://steemit.com/it/@sukjunko/38ndhz-sj>

---

<!--more-->

## 텐서플로 기본을 배워볼게여

![6](https://user-images.githubusercontent.com/28076542/45296404-eb96e480-b53c-11e8-81b2-623db8a57e8b.PNG)

---

## 텐서플로

![7](https://user-images.githubusercontent.com/28076542/45296405-eb96e480-b53c-11e8-88e0-0f086aec44aa.PNG)

* 구글에서 만들었어여
* 머신 인텔리전스를 위한 오픈소스 라이브러리에여

---

## 많은 라이브러리 중 왜 텐서플로냐

![8](https://user-images.githubusercontent.com/28076542/45296406-ec2f7b00-b53c-11e8-814f-af2763e4a8ae.PNG)

* 사람들이 많이 사용함

---

## 텐서플로 사람들이 많이 사용함

![9](https://user-images.githubusercontent.com/28076542/45296407-ec2f7b00-b53c-11e8-9144-1ab8c2355581.PNG)

* 공부할 때 많은 자료가 있음

---

## 텐서플로는 그래서 뭐냐

![10](https://user-images.githubusercontent.com/28076542/45296408-ec2f7b00-b53c-11e8-82a5-441c41f755c3.PNG)

* 데이터 플로 그래프를 사용해서 어떤 numerical한 계산을 할 수 있는 라이브러리
* 파이썬을 쓰니 개꿀

---

## 데이터 플로가 뭐지

![11](https://user-images.githubusercontent.com/28076542/45296409-ec2f7b00-b53c-11e8-9134-2edf58ecb268.PNG)

* 노드와 노드를 구성하는 엣지를 그래프라고 함
* 데이터 플로 그래프에서 노드는 하나의 operation(더하기, 곱하기)
* 엣지는 데이터, 데이터 배열(tensor)
* 어떤 연산이 일어나 결과물을 얻거나 작업을 할 수 있는 것을 데이터 플로 그래프라 함
* 이런 것들을 할 수 있는게 텐서플로
* 텐서 = 데이터, 텐서 플로 = 데이터가 돌아다닌다

---

## 텐서플로 설치 방법

![12](https://user-images.githubusercontent.com/28076542/45296410-ecc81180-b53c-11e8-82e3-390d2337a903.PNG)

* 리눅스, 맥, 윈도우 전부 pip를 써야함
* bazel이라는 빌드 프로그램을 사용해서 설치 가능
* 검색해서 설치해라
* 난 아나콘다의 주피터 노트북 쓰겠음
* [이거 참고](https://steemit.com/it/@sukjunko/38ndhz-sj)

---

## 텐서플로 설치 확인

![13](https://user-images.githubusercontent.com/28076542/45296411-ecc81180-b53c-11e8-962e-afeaa6ea1ac2.PNG)

```python
>>> import tensorflow as tf
>>> tf.__version__
'1.10.1'
```

---

## 많은 소스코드들...

![14](https://user-images.githubusercontent.com/28076542/45296412-ecc81180-b53c-11e8-8474-fc0f06305aee.PNG)

<https://github.com/hunkim/DeepLearningZeroToAll/>

---

## Hello World!

![15](https://user-images.githubusercontent.com/28076542/45296414-ed60a800-b53c-11e8-81c1-22915ab08526.PNG)

![image](https://user-images.githubusercontent.com/28076542/45298707-4e3fae80-b544-11e8-8039-b4ce6f71804b.png)

* tensorflow를 tf로 불러옴
* `tf.constant()`를 사용해서 constant를 만듦
* 실제로 그래프 속에 노드 하나가 있고 그 노드에 "Hello, TensorFlow"가 만들어진 것
* 실행하려면 `Session()`이 필요
* 실행하는 명령어는 `run()`
* b라고 나오는 것은 *Bytes literals*라는 것으로 어쨋든 정상임

---

## 좀 더 복잡한 Computational Graph

![16](https://user-images.githubusercontent.com/28076542/45296416-ed60a800-b53c-11e8-948b-39fac14480da.PNG)

* float32는 데이터 타입을 준 것
* 3.0, 4.0, (3+4) 노드 세 개를 만듦

![image](https://user-images.githubusercontent.com/28076542/45298966-0f5e2880-b545-11e8-85a8-094a648693b5.png)

* 출력 결과는 그래프의 한 요소를 보여주는 Tensor를 보여줌, 즉 더하기 결과가 나오지 않음 --> 세션을 만들어야 함

![image](https://user-images.githubusercontent.com/28076542/45299100-78de3700-b545-11e8-8acd-692251245e1e.png)

* 세션을 만들고 `sees.run([node1, node2])`를 실행
* `sess.run(node3)`로 더하기도 실행

---

## 텐서플로의 구조

![17](https://user-images.githubusercontent.com/28076542/45296417-ed60a800-b53c-11e8-9754-f817f5874b7a.PNG)

* 텐서플로는 기본 프로그램과 다름

1. 첫 번째로 그래프를 빌드함
2. `see.run(op)`로 그래프를 실행함
3. 그래프 속의 값이 나옴

---

## Computational Graph를 다시 보면

![18](https://user-images.githubusercontent.com/28076542/45296420-ed60a800-b53c-11e8-8338-331f7aa82f0b.PNG)

1. 그래프를 빌드하는 과정
2. `sess.run(op)`로 세션이 오퍼레이션을 넣음
3. 리턴 값 확인 가능

---

## Placeholder 노드

![19](https://user-images.githubusercontent.com/28076542/45296421-edf93e80-b53c-11e8-940b-756f712b19c6.PNG)

* 값을 상수로 정해서 넣는게 아니라 식을 만들고 값을 넣고 싶을 때 사용
* a와 b를 placeholder로 만듦
* `feed_dict`로 값을 넘겨줄 수 있음
* `feed dict`에 값을 여러 개 넘겨주는데 보면 각각 실행해서 덧셈 결과 반환함

---

## 정리하면

![20](https://user-images.githubusercontent.com/28076542/45296422-edf93e80-b53c-11e8-872b-0cf042a5ac48.PNG)

* 텐서플로는 그래프를 정의하고 이 때 placeholder로 만들 수 있음
* placeholdoer는 세션을 실행할 때 `feed_dict`로 값을 넘겨줌
* 그 후 결과를 출력

---

## Tensor의 의미

![21](https://user-images.githubusercontent.com/28076542/45296423-edf93e80-b53c-11e8-8cc9-1da10d4a7a72.PNG)

* Tensor는 기본적으로 배열들을 말함

---

## Tensor를 말할 때(Ranks)

![22](https://user-images.githubusercontent.com/28076542/45296424-edf93e80-b53c-11e8-9faa-8badd930b00e.PNG)

* Ranks는 몇 차원 배열이냐를 의미

---

## Tensor를 말할 때(Shapes)

![23](https://user-images.githubusercontent.com/28076542/45296425-ee91d500-b53c-11e8-9369-d4e405ff0728.PNG)

* 각각의 elements에 몇 개 들어있는가가 Shape
* t = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]의 Shape은

--> [3 3] 혹은 [3, 3]으로 나타냄

---

## Tensor를 말할 때(Types)

![24](https://user-images.githubusercontent.com/28076542/45296426-ee91d500-b53c-11e8-9175-753e67a64d23.PNG)

* 32타입을 많이 사용함

---

## 마지막으로 정리

![25](https://user-images.githubusercontent.com/28076542/45296427-ee91d500-b53c-11e8-9f2d-c652e24e343e.PNG)

* TensorFlow는 기존 프로그램과 달리 그래프를 먼저 만들고 그래프를 실행할 때 placeholder 노드는 feed_dict로 넘겨줄 수 있음

---

## 다음시간엔

![26](https://user-images.githubusercontent.com/28076542/45296428-ef2a6b80-b53c-11e8-8b91-4778cd8f18cc.PNG)

* 이거 하겠음