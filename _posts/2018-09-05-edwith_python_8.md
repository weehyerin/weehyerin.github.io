---
layout: post
title: "Pythonic Code - Asterisk"
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

## Asterisk

* 흔히 알고 있는 *를 의미함
* 단순 곱셈, 제곱연산, 가변 인자 활용 등 다양하게 사용됨

### *args

```python
>>> def asterisk_test(a, *args):
...     print(a, args) # a에 1이 할당, 나머지는 args에 할당
...     print(type(args))
...
>>> asterisk_test(1,2,3,4,5,6)
1 (2, 3, 4, 5, 6)
<class 'tuple'>
```

* 몇 개의 값이 들어올지 모를 때 *를 사용함 --> 가변인자

### **kargs

```python
>>> asterisk_test(1,2,3,4,5,6)
1 (2, 3, 4, 5, 6)
<class 'tuple'>
>>> def asterisk_test(a, **kargs):
...     print(a, kargs)
...     print(type(kargs))
...
>>> asterisk_test(1, b=2, c=3, d=4, e=5, f=6)
1 {'b': 2, 'c': 3, 'd': 4, 'e': 5, 'f': 6}
<class 'dict'>
```

* 키워드 인자 ex) def(a=1, b=2)와 같은 것에서 한 번에 넘겨줄 때 사용

## Asterisk - unpacking a container

* tuple, dict 등 자료형에 들어가 있는 값을 unpacking
* 함수의 입력값, zip 등에 유용하게 사용 가능

```python
>>> def asterisk_test(a, *args):
...     print(a, args[0])
...     print(type(args))
...
>>> asterisk_test(1, (2,3,4,5,6))
1 (2, 3, 4, 5, 6)
<class 'tuple'>

# args[0]을 출력하게 해야 함
# (2,3,4,5,6)은 변수 1개임
# 만약 args를 출력하게하면
```

```python
>>> def asterisk_test(a, *args):
...     print(a, args)
...     print(type(args))
...
>>> asterisk_test(1, (2,3,4,5,6))
1 ((2, 3, 4, 5, 6),)
<class 'tuple'>

# 1 ((2, 3, 4, 5, 6),)와 같이 나오는데 맨 뒤에 ,가 들어왔다는 것은 tuple로 왔다는 뜻
```

```python
>>> def asterisk_test(a, args):
...     print(a, *args)
...     print(type(args))
...
>>> asterisk_test(1, (2,3,4,5,6))
1 2 3 4 5 6
<class 'tuple'>

# (2, 3, 4, 5, 6)이 print(a, *args)에서 unpacking 됨
```

```python
>>> def asterisk_test(a, *args):
...     print(a, args)
...     print(type(args))
...
>>> asterisk_test(1, *(2,3,4,5,6))
1 (2, 3, 4, 5, 6)
<class 'tuple'>

# 이건 unpacking 상태로 들어가서 이것도 됨
```

```python
>>> a, b, c = ([1,2], [3,4], [5,6])
>>> print(a,b,c)
[1, 2] [3, 4] [5, 6]

>>> data = ([1,2],[3,4],[5,6])
>>> print(*data)
[1, 2] [3, 4] [5, 6]
```

* *울 붙이면 알아서 unpacking 해줌

```python
>>> def asterisk_test(a, b, c, d):
...     print(a, b, c, d)
...
>>> data = {"b":1, "c":2, "d":3}
>>> asterisk_test(10, **data)
10 1 2 3
```

* **는 키워드 인자 --> dict 타입에서 내용을 보냄

```python
>>> for data in zip(*([1,2], [3,4], [5,6])):
...     print(data)
...
(1, 3, 5)
(2, 4, 6)
```

* [1,2], [3,4], [5,6]을 zip으로 묶은 것

```python
>>> for data in zip(*([1, 2], [3, 4], [5, 6])):
...     print(sum(data))
...
9
12
```

```python
>>> def asterisk_test(a,b,c,d, e=0):
...     print(a,b,c,d,e)
...
>>> data = {"d":1, "c":2, "b":3, "e":56}
>>> asterisk_test(10, **data)
10 3 2 1 56
```