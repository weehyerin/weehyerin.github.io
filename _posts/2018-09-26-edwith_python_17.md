---
layout: post
title: "Data handling section - Numerical Python - numpy"
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

## 코드로 방정식 표현하기

* 기존의 파이썬 코드로 수식을 표편하는 것은 너무 느림
* 그래서 Numpy를 씀

## Numpy

* Numerical Python
* 파이썬의 고성능 과학 계산용 패키지
* Matrix와 Vector와 같은 Array 연산의 사실상 표준
* 일반 List에 비해 빠르고 메모리 효율적
* **반복문 없이 데이터 배열에 대한 처리를 지원함**
* 선형대수와 관련된 다양한 기능을 제공함
* C, C++, 포트란 등의 언어와 통합 가능

## Numpy Install

* Jupyter가 있으면 추가 설치 필용 없음

---

## ndarray

### import

```python
import numpy as np
```

* numpy의 호출 방법
* 일반적으로 numpy는 np라는 alias(별칭) 이용해서 호출

### Array creation

```python
import numpy as np
test_array = np.array([1,4,5,8],float)
print(test_array)
type(test_array[3])
```

![image](https://user-images.githubusercontent.com/28076542/46060376-11a7c000-c19e-11e8-8e9d-457f5f715ceb.png)

* numpy는 np.array 함수를 활용하여 배열을 생성함 --> ndarray
* numpy는 하나의 데이터 type만 배열에 넣을 수 있음
* List와 가장 큰 차이점, **Dynamic typing not supported**
* C의 Array를 사용하여 배열을 생성함
* float64는 64비트라는 뜻

### Numpy와 Python List의 배열 저장방식 차이

![image](https://user-images.githubusercontent.com/28076542/46060576-ce018600-c19e-11e8-8019-59caff6c178c.png)

### shape

* numpy array의 object의 dimension을 반환함

```python
test_array1 = np.array([1, 4, 5, "8"], float)
test_array2 = np.array([[1, 4, 5, 8], [2, 4, 6, 8]], float)
test_array1.shape
test_array2.shape
```

![image](https://user-images.githubusercontent.com/28076542/46061014-00f84980-c1a0-11e8-8d62-e04dde9ef145.png)

* shape 결과로 나온 값은 dimension이 추가될 수록 하나씩 밀림
* (4, ) --> (3, 4) --> (4, 3, 4)...

### ndim(number of dimension) 와 size

![image](https://user-images.githubusercontent.com/28076542/46061088-62b8b380-c1a0-11e8-992b-3f942aab6a83.png)

* data type은 float32나 float64를 씀

## shape handling

### reshape

* Array의 shape의 크기를 변경함 (element의 개수는 동일)

```python
test_matrix = [[1, 2, 3, 4], [1, 2, 5, 8]]
np.array(test_matrix).shape
np.array(test_matrix).reshape(2,2,2)
```

![image](https://user-images.githubusercontent.com/28076542/46061434-9a742b00-c1a1-11e8-9c98-4333d0526add.png)

* reshape 인자에 -1을 넣으면 row나 column의 개수를 기반으로 자동으로 개수 설정

![image](https://user-images.githubusercontent.com/28076542/46061550-05bdfd00-c1a2-11e8-9311-52421a587e23.png)

### flatten

* 다차원 array를 1차원 array로 변환

```python
test_matrix = [[[1,2,3,4],[1,2,5,8]], [[1, 2, 3, 4], [1, 2, 5, 8]]]
np.array(test_matrix).flatten()
```

![image](https://user-images.githubusercontent.com/28076542/46061694-79600a00-c1a2-11e8-8f46-0228b8c8ff43.png)

---

## Indexing and Slicing

### Indexing

```python
a = np.array([[1, 2, 3], [4.5, 5, 6]], int)
print(a)
print(a[0,0]) # Two dimensional array representation #1
print(a[0][0]) # Two dimensional array representation #2

a[0,0] = 12 # Matrix 0,0에 12 할당
print(a)
a[0][0] =5 # Matrix 0,0에 5 할당
print(a)
```

* List와 달리 이차원 배열에서 [0, 0]과 같은 표기법을 제공함
* Matrix일 경우 앞은 row 뒤는 column을 의미함

### slicing

```python
a = np.array([[1, 2, 3, 4, 5], [6, 7, 8, 9, 10]], int)
a[:,2:] # 전체 Row의 2열 이상
a[1, 1:3] # 1 Row의 1열 ~ 2열
a[1:3] # 1 Row ~ 2 Row의 전체
```

* List와 달리 행과 열 부분을 나눠서 slicing이 가능함
* Matrix의 부분 집합을 추출할 때 유용함

![image](https://user-images.githubusercontent.com/28076542/46062321-6817fd00-c1a4-11e8-9c17-14e8c77105c6.png)

## create function

### arange

```python
np.arange(30) # range: List의 range와 같은 효과, integer로 0부터 29까지 배열추출
np.arange(0, 5, 0.5) # 시작, 끝, step, floating point도 표시가능함
np.arange(30).reshape(5, 6)
```

![image](https://user-images.githubusercontent.com/28076542/46062705-90ecc200-c1a5-11e8-94ef-609103e44a07.png)

### zeros

* 0으로 가득찬 ndarray 생성

```python
np.zeros(shape=(10,), dtype=np.int8) # 10 - zero vector 생성
np.zero((2.5)) # 2 by 5 - zero matrix 생성
```

![image](https://user-images.githubusercontent.com/28076542/46062998-76671880-c1a6-11e8-9278-4c9489a2c059.png)

### ones

* 1로 가득찬 ndarray 생성

```python
np.ones(shape=(10, ), dtype=np.int8)
np.ones((2, 5))
```

![image](https://user-images.githubusercontent.com/28076542/46064080-60a72280-c1a9-11e8-9a78-a050ad726ef0.png)

### empty

* shape만 주어지고 비어있는 ndarray 생성인데 memory initialization이 되지 않음

```python
np.empty(shape=(10, ), dtype=np.int8)
np.empty((3, 5))
```

![image](https://user-images.githubusercontent.com/28076542/46064156-9ba95600-c1a9-11e8-8d89-29e75f21af56.png)

* 강의 결과랑 다름
* 어차피 empty는 안 씀

### something_like

* 기존 ndarray의 shape 크기 만큼 1, 0 또는 empty array를 반환

```python
test_matrix = np.arange(30).reshape(5,6)
np.ones_like(test_matrix) # 1로 채워라
```

![image](https://user-images.githubusercontent.com/28076542/46064281-fb076600-c1a9-11e8-8687-1dd39e7bb285.png)

* 딥러닝에서 은근 쓰임
* 딥러닝은 numpy에서 미분 가능하게 한 것
* numpy의 기본적인 옵션이 그래서 tensorflow에 다 있음

### identity

* 단위 행렬(i 행렬)을 생성함

```python
np.identity(n=3, dtype=np.int8) # n = number of rows
np.identity(5)
```

![image](https://user-images.githubusercontent.com/28076542/46064530-94cf1300-c1aa-11e8-98b7-b9b42f8438b4.png)

### eye

* 대각선이 1인 행렬, k값의 시작 index의 변경이 가능

```python
np.eye(N=3, M=5, dtype=np.int8)
np.eye(3)
np.eye(3, 5, k=2) # k는 start index
```

![image](https://user-images.githubusercontent.com/28076542/46064678-0909b680-c1ab-11e8-825d-eeba78e656f7.png)

### diag

* 대각 행렬의 값을 추출함

```python
matrix = np.arange(9).reshape(3, 3)
np.diag(matrix)
np.diag(matrix, k=1)
```

![image](https://user-images.githubusercontent.com/28076542/46064828-74ec1f00-c1ab-11e8-8335-7720e911fe51.png)

![image](https://user-images.githubusercontent.com/28076542/46064850-80d7e100-c1ab-11e8-8ae4-aaa1ad3fca5d.png)

### random sampling

* 데이터 분포에 따른 sampling으로 array를 생성

```python
np.random.uniform(0, 1, 10).reshape(2, 5) # 균등분포
np.random.normal(0, 1, 10).reshape(2, 5) # 정규분포
```

![image](https://user-images.githubusercontent.com/28076542/46064988-e035f100-c1ab-11e8-9d5b-7083a96e996c.png)

* 분포에 따른 값을 array로 만들 수 있다정도로 기억

---

## operation functions

### sum

* ndarray의 element들 간의 합을 구함, list의 sum 기능과 동일

```python
test_array = np.arange(1, 11)
test_array
test_array.sum(dtype=np.float)
```

![image](https://user-images.githubusercontent.com/28076542/46065190-6baf8200-c1ac-11e8-8e8b-cfe6d44adea7.png)

### axis

* 모든 operation function을 실행할 때 기준이 되는 dimension 축

![image](https://user-images.githubusercontent.com/28076542/46065308-b5986800-c1ac-11e8-916f-632d6e6102d3.png)

```python
test_array = np.arange(1, 13).reshape(3, 4)
test_array
test_array.sum(axis=1), test_array.sum(axis=0)
```

![image](https://user-images.githubusercontent.com/28076542/46065402-e8426080-c1ac-11e8-8fbf-a32ef30dd6e6.png)

* 새로 생기는 부분이 axis 0이 되는 것을 기억하면 됨

![image](https://user-images.githubusercontent.com/28076542/46065636-8df5cf80-c1ad-11e8-890f-e9299cfaca52.png)

```python
third_order_tensor = np.array([test_array, test_array, test_array])
third_order_tensor
```

![image](https://user-images.githubusercontent.com/28076542/46065704-c4cbe580-c1ad-11e8-9c2a-2b9d69ed2c3e.png)

### mean & std

* ndarray의 element들 간의 평균 또는 표준편차를 반환

```python
test_array = np.arange(1, 13).reshape(3,4)
test_array
test_array.mean(), test_array.mean(axis=0)
test_array.std(), test_array.std(axis=0)
```

![image](https://user-images.githubusercontent.com/28076542/46066624-ef1ea280-c1af-11e8-8679-62eb111eb957.png)

### Mathematical functions

* 다양한 수학 연산자를 제공

#### exponential

* exp, expm1, exp2, log, log10, log1p, log2, power. sqrt

#### trigonometric

* sin, cos, tan, acsin, arccos, atctan

#### hyperbolic

* sinh, cosh, tanh, acsinh, arccosh, atctanh

```python
np.exp(test_array), np.sqrt(test_array)
```

![image](https://user-images.githubusercontent.com/28076542/46066921-93a0e480-c1b0-11e8-9630-e81b8f44d9d3.png)

### concatenate

* Numpy array를 합치는 함수

![image](https://user-images.githubusercontent.com/28076542/46066980-ad422c00-c1b0-11e8-840f-4a3286784fcc.png)

```python
a = np.array([1, 2, 3])
b = np.array([2, 3, 4])
np.vstack((a, b))
a = np.array([[1], [2], [3]])
b = np.array([ [2], [3], [4]])
np.hstack((a, b))
```

![image](https://user-images.githubusercontent.com/28076542/46067481-e16a1c80-c1b1-11e8-9e79-c3cb586b2839.png)

* 축 값을 사용해서 쓰는 방법도 있음

![image](https://user-images.githubusercontent.com/28076542/46067568-1d9d7d00-c1b2-11e8-9b1f-60c828456988.png)

```python
a = np.array([[1, 2, 3]])
b = np.array([[2, 3, 4]])
np.concatenate((a, b), axis = 0)
a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6]])
np.concatenate((a, b.T), axis = 1)
```

![image](https://user-images.githubusercontent.com/28076542/46067733-84bb3180-c1b2-11e8-867b-381d9ebb13c4.png)

## array operation

### Operations b/t arrays

* Numpy는 array간의 기본적인 사칙연산을 지원함

```python
test_a = np.array([[1,2,3],[4,5,6]], float)
test_a + test_a # Matrix + Matrix 연산
test_a - test_a # Matrix - Matrix 연산
test_a * test_a # Matrix내 element들 간 같은 위치에 있는 값들끼리 연산
```

![image](https://user-images.githubusercontent.com/28076542/46071767-b97fb680-c1bb-11e8-8adf-a429410cca3f.png)

### Element-wise operations

* Array간 shape이 같을 때 일어나는 연산

![image](https://user-images.githubusercontent.com/28076542/46071910-0ebbc800-c1bc-11e8-8831-9a63fc95e8f9.png)

```python
matrix_a = np.arange(1, 13).reshape(3, 4)
matrix_a * matrix_a
```

![image](https://user-images.githubusercontent.com/28076542/46071954-2abf6980-c1bc-11e8-8c46-dbc7da00abcc.png)

### Dot product

* Matrix의 기본 연산
* dot 함수 사용

![image](https://user-images.githubusercontent.com/28076542/46072060-5e01f880-c1bc-11e8-95f3-cc03db7ba3d9.png)

```python
test_a = np.arange(1, 7).reshape(2,3)
test_b = np.arange(7, 13).reshape(3,2)
test_a.dot(test_b)
```

![image](https://user-images.githubusercontent.com/28076542/46072104-78d46d00-c1bc-11e8-86fb-61c23ec164e2.png)

### Transpose

* transpose 또는 T attribute 사용

```python
test_a = np.arange(1, 7).reshape(2, 3)
test_a
test_a.transpose()
test_a.T.dot(test_a) # Matrix 간 곱셈
test_a.T
```

![image](https://user-images.githubusercontent.com/28076542/46072224-cb158e00-c1bc-11e8-9be8-627921880e3c.png)

### broadcasting

* Shape이 다른 배열 간 연산을 지원하는 기능

![image](https://user-images.githubusercontent.com/28076542/46072278-e84a5c80-c1bc-11e8-9bb8-308fab262284.png)

```python
test_matrix = np.array([[1, 2, 3], [4, 5, 6]], float)
scalar = 3
test_matrix + scalar # Matrix - Scalar 덧셈
```

![image](https://user-images.githubusercontent.com/28076542/46072325-0c0da280-c1bd-11e8-8092-4c44c1988140.png)

```python
test_matrix - scalar # Matrix - Scalar 뺄셈
test_matrix * 5 # Matrix - Scalar 곱셈
test_matrix / 5 # Matrix - Scalar 나눗셈
test_matrix // 5 # Matrix - Scalar 몫
test_matrix ** 5 # Matrix - Scalar 제곱
```

![image](https://user-images.githubusercontent.com/28076542/46072479-6eff3980-c1bd-11e8-813a-35be622d85ff.png)

![image](https://user-images.githubusercontent.com/28076542/46072632-d1f0d080-c1bd-11e8-8bc1-d29c5b837c94.png)

## Numpy performance #1

```python
def sclar_vector_product(scalar, vector):
    result = []
    for value in vector:
        result.append(scalar * value)
    return result


iternation_max = 100000000
vector = list(range(iternation_max))
scalar = 2
%timeit sclar_vector_product(scalar,vector)  # for loop을 이용한 성능 
%timeit [scalar * value for value in range(iternation_max)] # list comprehension을 이용한 성능 
%timeit np.arange(iternation_max) * scalar # numpy를 이용한 성능
```

* timeeit: jupyter 환경에서 코드의 퍼포먼스를 체크하는 함수
* numpy > list comprehension > for loop 순으로 빠름
* concat 시 numpy는 느림

![image](https://user-images.githubusercontent.com/28076542/46074066-87715300-c1c1-11e8-9b50-50a029bbc4ff.png)

## comparisons

### All & Any

* Array의 데이터 전부(and) 또는 일부(or)가 조건에 만족 여부 반환

```python
a = np.arange(10)
a
a>5
np.any(a>5), np.any(a<0) # any --> 하나라도 조건에 만족한다면 true
np.all(a>5), np.all(a<10) # all --> 모두가 조건에 만족한다면 true
```

![image](https://user-images.githubusercontent.com/28076542/46074857-ba1c4b00-c1c3-11e8-8513-72897d64bcf9.png)

### Comparison operation #1

* Numpy는 배열의 크기가 동일할 때 element간 비교의 결과를 Boolean type으로 반환하여 돌려줌

```python
test_a = np.array([1, 3, 0], float)
test_b = np.array([5, 2, 1], float)
test_a > test_b
test_a == test_b
(test_a > test_b).any() # any --> 하나라도 true면 true
```

![image](https://user-images.githubusercontent.com/28076542/46074426-8856b480-c1c2-11e8-9353-d1f60ac1d9e3.png)

### Comparison operation #2

```python
a = np.array([1, 3, 0], float)
np.logical_and(a > 0, a < 3) # and 조건의 condition
b = np.array([True, False, True], bool)
np.logical_not(b) # NOT 조건의 condition
c = np.array([False, True, False], bool)
np.logical_or(b, c) # OR 조건의 condition
```

![image](https://user-images.githubusercontent.com/28076542/46074732-6578d000-c1c3-11e8-916c-fbcc5a08c7f7.png)

### np.where

```python
np.where(a > 0, 3, 2) # whrer(condition, TRUE, FALSE)
a = np.arange(10)
np.where(a>5) # Index 값 반환
a = np.array([1, np.NaN, np.Inf], float) # NaN == Not a Number
np.isnan(a)
np.isfinite(a)
```

![image](https://user-images.githubusercontent.com/28076542/46075012-28610d80-c1c4-11e8-9c59-6d6e3c0fd803.png)

### argmax & argmin

* array내 최댓값 또는 최솟값의 index를 반환함

```python
a = np.array([1, 2, 4, 5, 8, 78, 23, 3])
np.argmax(a), np.argmin(a)
```

![image](https://user-images.githubusercontent.com/28076542/46075353-36fbf480-c1c5-11e8-86ab-49bc8683fbbb.png)

* axis 기반의 반환

```python
a = np.array([[1, 2, 4, 7], [9, 88, 6, 45], [9, 76, 3, 4]])
np.argmax(a, axis=1), np.argmin(a, axis=0)
```

![image](https://user-images.githubusercontent.com/28076542/46075292-06b45600-c1c5-11e8-87b2-52796aa38827.png)

![image](https://user-images.githubusercontent.com/28076542/46075363-42e7b680-c1c5-11e8-8f78-d6600f54e58a.png)

### boolean index

* numpy의 배열은 특정 조건에 따른 값을 배열 형태로 추출할 수 있음
* Comparison operation 함수들도 모두 사용 가능

```python
test_array = np.array([1, 4, 0, 2, 3, 8, 9, 7], float)
test_array > 3
test_array[test_array > 3] # 조건이 True인 index의 element만 추출
condition = test_array < 3
test_array[condition]
```

![image](https://user-images.githubusercontent.com/28076542/46076064-6a3f8300-c1c7-11e8-8e35-5c45725f855b.png)

* index가 아니라 값을 뽑아오는 것을 잊지 마십쇼

### fancy index

* numpy에서 array를 index value로 사용해서 값을 추출하는 방법

```python
a = np.array([2, 4, 6, 8], float)
b = np.array([0, 0, 1, 3, 2, 1], int) # 반드시 integer로 선언
a[b] # bracket index, b 배열의 값을 index로 하여 a의 값들을 추출함
a.take(b) # take 함수: bracket index와 같은 효과
```

![image](https://user-images.githubusercontent.com/28076542/46076345-3f096380-c1c8-11e8-9a91-1aa8195f2f46.png)

* 추천 관련 공부를 할 때 자주 사용하는 기법
* take 함수 쓰는 것을 권장

## numpy data i/o

### loadtxt & savetxt

* Text type의 데이터를 읽고 저장하는 기능

```txt
# year	hare	lynx	carrot
1900	30e3	4e3	48300
1901	47.2e3	6.1e3	48200
1902	70.2e3	9.8e3	41500
1903	77.4e3	35.2e3	38200
1904	36.3e3	59.4e3	40600
1905	20.6e3	41.7e3	39800
1906	18.1e3	19e3	38600
1907	21.4e3	13e3	42300
1908	22e3	8.3e3	44500
1909	25.4e3	9.1e3	42100
1910	27.1e3	7.4e3	46000
1911	40.3e3	8e3	46800
1912	57e3	12.3e3	43800
1913	76.6e3	19.5e3	40900
1914	52.3e3	45.7e3	39400
1915	19.5e3	51.1e3	39000
1916	11.2e3	29.7e3	36700
1917	7.6e3	15.8e3	41800
1918	14.6e3	9.7e3	43300
1919	16.2e3	10.1e3	41300
1920	24.7e3	8.6e3	47300
```

```python
a = np.loadtxt("./populations.txt") # 파일 호출
a[:10]
a_int = a.astype(int) # int type 변환
a_int[:3]
np.savetxt('int_data.csv', a_int, delimiter=",") # int_data.csv로 저장
```

![image](https://user-images.githubusercontent.com/28076542/46085337-c236b380-c1e0-11e8-9f88-dd463d1f2502.png)

```csv
1.90E+03	3.00E+04	4.00E+03	4.83E+04
1.90E+03	4.72E+04	6.10E+03	4.82E+04
1.90E+03	7.02E+04	9.80E+03	4.15E+04
1.90E+03	7.74E+04	3.52E+04	3.82E+04
1.90E+03	3.63E+04	5.94E+04	4.06E+04
1.91E+03	2.06E+04	4.17E+04	3.98E+04
1.91E+03	1.81E+04	1.90E+04	3.86E+04
1.91E+03	2.14E+04	1.30E+04	4.23E+04
1.91E+03	2.20E+04	8.30E+03	4.45E+04
1.91E+03	2.54E+04	9.10E+03	4.21E+04
1.91E+03	2.71E+04	7.40E+03	4.60E+04
1.91E+03	4.03E+04	8.00E+03	4.68E+04
1.91E+03	5.70E+04	1.23E+04	4.38E+04
1.91E+03	7.66E+04	1.95E+04	4.09E+04
1.91E+03	5.23E+04	4.57E+04	3.94E+04
1.92E+03	1.95E+04	5.11E+04	3.90E+04
1.92E+03	1.12E+04	2.97E+04	3.67E+04
1.92E+03	7.60E+03	1.58E+04	4.18E+04
1.92E+03	1.46E+04	9.70E+03	4.33E+04
1.92E+03	1.62E+04	1.01E+04	4.13E+04
1.92E+03	2.47E+04	8.60E+03	4.73E+04
```

### numpy object - npy

* Numpy object (pickle) 형태로 데이터를 저장하고 불러옴
* Binary 파일 형태로 저장함

```python
np.save("npy_test", arr=a_int)
npy_array = np.load(file="npy_test.npy")
npy_array[:3]
```

![image](https://user-images.githubusercontent.com/28076542/46085564-47ba6380-c1e1-11e8-9167-b3b15ea255ed.png)