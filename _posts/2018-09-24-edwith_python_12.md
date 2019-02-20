---
layout: post
title: "Assignment - Basic Linear Algebra"
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

## 숙제파일 다운로드

* [다운로드 링크](https://github.com/TEAMLAB-Lecture/AI-python-connect/raw/master/lab_assignments/lab_1/lab_bla.zip)

## 구현할 함수

함수명 | 함수내용
--------------------|--------------------------
vector_size_check | vector 간 덧셈 또는 뺄셈 연산을 할 때, 연산이 가능한 사이즈인지를 확인하여 가능 여부를 True 또는 False로 반환함
vector_addition | vector간 덧셈을 실행하여 결과를 반환함, 단 입력되는 vector의 갯수와 크기는 일정하지 않음
vector_subtraction | vector간 뺄셈을 실행하여 결과를 반환함, 단 입력되는 vector의 갯수와 크기는 일정하지 않음
scalar_vector_product | 하나의 scalar 값을 vector에 곱함, 단 입력되는 vector의 크기는 일정하지 않음
matrix_size_check | matrix 간 덧셈 또는 뺄셈 연산을 할 때, 연산이 가능한 사이즈인지를 확인하여 가능 여부를 True 또는 False로 반환함
is_matrix_equal | 비교가 되는 n개의 matrix가 서로 동치인지 확인하여 True 또는 False를 반환함
matrix_addition | matrix간 덧셈을 실행하여 결과를 반환함, 단 입력되는 matrix의 갯수와 크기는 일정하지 않음
matrix_subtraction | matrix간 뺄셈을 실행하여 결과를 반환함, 단 입력되는 matrix의 갯수와 크기는 일정하지 않음
matrix_transpose | matrix의 역행렬을 구하여 결과를 반환함, 단 입력되는 matrix의 크기는 일정하지 않음
scalar_matrix_product | 하나의 scalar 값을 matrix에 곱함, 단 입력되는 matrix의  크기는 일정하지 않음
is_product_availability_matrix | 두 개의 matrix가 입력 되었을 경우, 두 matrix의 곱셈 연산의 가능 여부를 True 또는 False로 반환함
matrix_product | 곱셈 연산이 가능한 두 개의 matrix의 곱셈을 실행하여 반환함

## 숙제 코드

```python
def vector_size_check(*vector_variables):
    return None

def vector_addition(*vector_variables):
    return None


def vector_subtraction(*vector_variables):
    if vector_size_check(*vector_variables) == False:
        raise ArithmeticError
    return None


def scalar_vector_product(alpha, vector_variable):
    return None


def matrix_size_check(*matrix_variables):
    return None


def is_matrix_equal(*matrix_variables):
    return None


def matrix_addition(*matrix_variables):
    if matrix_size_check(*matrix_variables) == False:
        raise ArithmeticError
    return None


def matrix_subtraction(*matrix_variables):
    if matrix_size_check(*matrix_variables) == False:
        raise ArithmeticError
    return None


def matrix_transpose(matrix_variable):
    return None


def scalar_matrix_product(alpha, matrix_variable):
    return None


def is_product_availability_matrix(matrix_a, matrix_b):
    return None


def matrix_product(matrix_a, matrix_b):
    if is_product_availability_matrix(matrix_a, matrix_b) == False:
        raise ArithmeticError
    return None
```

---

## vector_size_check

```python
def vector_size_check(*vector_variables):
    return True and (len(vector_variables) == len([0 for i in vector_variables if len(i) == len(vector_variables[0])]))

# 실행결과
print(vector_size_check([1,2,3], [2,3,4], [5,6,7])) # Expected value: True
print(vector_size_check([1, 3], [2,4], [6,7])) # Expected value: True
print(vector_size_check([1, 3, 4], [4], [6,7])) # Expected value: False
```

### vector_size_check 모범답안

```python
def vector_size_check(*vector_variables):
    return all(len(vector_variables[0]) == x for x in [len(vector) for vector in vector_variables[1:]])

# all이라는 값은 리스트 안의 모든 값이 True일 때 True
# ex) all([True, True, True]) --> True
# any는 하나라도 리스트 안의 값이 True면 True
```

## vector_addition

```python
def vector_addition(*vector_variables):
    if vector_size_check(*vector_variables) is not True:
        raise ArithmeticError
    return [sum(i) for i in zip(*vector_variables)]

# 실행결과
print(vector_addition([1, 3], [2, 4], [6, 7])) # Expected value: [9, 14]
print(vector_addition([1, 5], [10, 4], [4, 7])) # Expected value: [15, 16]
print(vector_addition([1, 3, 4], [4], [6,7])) # Expected value: ArithmeticError
```

### vector_addition 모범답안

```python
def vector_addition(*vector_variables):
    if vector_size_check(*vector_variables) == False:
        raise ArithmeticError
    return [sum(elements) for elements in zip(*vector_variables)]
```

## vector_subtraction

```python
def vector_subtraction(*vector_variables):
    if vector_size_check(*vector_variables) is not True:
        raise ArithmeticError
    return [i[0] - sum(i[1:]) for i in zip(*vector_variables)]

# 실행결과
print(vector_subtraction([1, 3], [2, 4])) # Expected value: [-1, -1]
print(vector_subtraction([1, 5], [10, 4], [4, 7])) # Expected value: [-13, -6]
```

### vector_subtraction 모범답안

```python
def vector_subtraction(*vector_variables):
    if vector_size_check(*vector_variables) == False:
        raise ArithmeticError
    return [elements[0]*2 - sum(elements) for elements in zip(*vector_variables)]
```

## scalar_vector_product

```python
def scalar_vector_product(alpha, vector_variable):
    return [alpha*i for i in vector_variable]

# 실행결과
print (scalar_vector_product(5,[1,2,3])) # Expected value: [5, 10, 15]
print (scalar_vector_product(3,[2,2])) # Expected value: [6, 6]
print (scalar_vector_product(4,[1])) # Expected value: [4]
```

### scalar_vector_product 모범답안

```python
def scalar_vector_product(alpha, vector_variable):
    return [alpha*element for element in vector_variable]
```

## matrix_size_check

```python
def matrix_size_check(*matrix_variables):
    return True and (len(matrix_variables[0]) == len([0 for i in zip(*matrix_variables) if vector_size_check(i) is True]))

#실행결과
matrix_x = [[2, 2], [2, 2], [2, 2]]
matrix_y = [[2, 5], [2, 1]]
matrix_z = [[2, 4], [5, 3]]
matrix_w = [[2, 5], [1, 1], [2, 2]]

print (matrix_size_check(matrix_x, matrix_y, matrix_z)) # Expected value: False
print (matrix_size_check(matrix_y, matrix_z)) # Expected value: True
print (matrix_size_check(matrix_x, matrix_w)) # Expected value: True
```

### matrix_size_check 모범답안

```python
def matrix_size_check(*matrix_variables):
    return all([len(set(len(matrix[0]) for matrix in matrix_variables)) == 1]) and all([len(matrix_variables[0]) == len(matrix) for matrix in matrix_variables])

# set을 쓰는 이유는 set에 들어가는 list의 값이 같으면 길이는 1이 나오기 때문
# row의 길이를 먼저 비교하고 column을 비교하는 방식
```

## is_matrix_equal

```python
def is_matrix_equal(*matrix_variables):
    return True and (len(matrix_variables[0])*len(matrix_variables) == len([0 for i in zip(*matrix_variables) for j in i if matrix_size_check(j, i[0]) and j == i[0]]))

# 실행결과
matrix_x = [[2, 2], [2, 2]]
matrix_y = [[2, 5], [2, 1]]
matrix_z = [[2, 4], [5, 3]]

print (matrix_addition(matrix_x, matrix_y)) # Expected value: [[4, 7], [4, 3]]
print (matrix_addition(matrix_x, matrix_y, matrix_z)) # Expected value: [[6, 11], [9, 6]]
```

### is_matrix_equal 모범답안

```python
def is_matrix_equal(*matrix_variables):
    return all([all([len(set(row)) == 1 for row in zip(*matrix)]) for matrix in zip(*matrix_variables)])
```

## matrix_addition

```python
def matrix_addition(*matrix_variables):
    if matrix_size_check(*matrix_variables) is not True:
        raise ArithmeticError
    return [vector_addition(*i) for i in zip(*matrix_variables)]

# 실행결과
matrix_x = [[2, 2], [2, 2]]
matrix_y = [[2, 5], [2, 1]]
matrix_z = [[2, 4], [5, 3]]

print (matrix_addition(matrix_x, matrix_y)) # Expected value: [[4, 7], [4, 3]]
print (matrix_addition(matrix_x, matrix_y, matrix_z)) # Expected value: [[6, 11], [9, 6]]
```

### matrix_addition 모범답안

```python
def matrix_addition(*matrix_variables):
    if matrix_size_check(*matrix_variables) is not True:
        raise ArithmeticError
    return [[sum(row) for row in zip(*matrix)] for matrix in zip(*matrix_variables)]

# 2차원 배열에서 zip의 zip은 각각의 element를 가져올 수 있는 기술
```

## matrix_subtraction

```python
def matrix_subtraction(*matrix_variables):
    if matrix_size_check(*matrix_variables) == False:
        raise ArithmeticError
    return [vector_subtraction(*i) for i in zip(*matrix_variables)]

# 실행결과
matrix_x = [[2, 2], [2, 2]]
matrix_y = [[2, 5], [2, 1]]
matrix_z = [[2, 4], [5, 3]]

print (matrix_subtraction(matrix_x, matrix_y)) # Expected value: [[0, -3], [0, 1]]
print (matrix_subtraction(matrix_x, matrix_y, matrix_z)) # Expected value: [[-2, -7], [-5, -2]]
```

### matrix_subtraction 모범답안

```python
def matrix_subtraction(*matrix_variables):
    if matrix_size_check(*matrix_variables) == False:
        raise ArithmeticError
    return [[row[0]*2 - sum(row) for row in zip(*matrix)] for matrix in zip(*matrix_variables)]
```

## matrix_transpose

```python
def matrix_transpose(matrix_variable):
    return [list(i) for i in zip(*matrix_variable)]

# 실행결과
matrix_w = [[2, 5], [1, 1], [2, 2]]
print (matrix_transpose(matrix_w)) # Expected value: [[2, 1, 2], [5, 1, 2]]
```

### matrix_transpose 모범답안

```python
def matrix_transpose(matrix_variable):
    return[[element for element in row] for row in zip(*matrix_variable)]
```

## scalar_matrix_product

```python
def scalar_matrix_product(alpha, matrix_variable):
    return [scalar_vector_product(alpha, i) for i in matrix_variable]

# 실행결과
matrix_x = [[2, 2], [2, 2], [2, 2]]
matrix_y = [[2, 5], [2, 1]]
matrix_z = [[2, 4], [5, 3]]
matrix_w = [[2, 5], [1, 1], [2, 2]]

print(scalar_matrix_product(3, matrix_x)) #Expected value: [[6, 6], [6, 6], [6, 6]]
print(scalar_matrix_product(2, matrix_y)) #Expected value: [[4, 10], [4, 2]]
print(scalar_matrix_product(4, matrix_z)) #Expected value: [[8, 16], [20, 12]]
print(scalar_matrix_product(3, matrix_w)) #Expected value: [[6, 15], [3, 3], [6, 6]]    
```

### scalar_matrix_product 모범답안

```python
def scalar_matrix_product(alpha, matrix_variable):
    return [scalar_vector_product(alpha, row) for row in matrix_variable]
```

## is_product_availability_matrix

```python
def is_product_availability_matrix(matrix_a, matrix_b):
    return True and len(matrix_a[0]) == len(matrix_b)

# 실행결과
matrix_x= [[2, 5], [1, 1]]
matrix_y = [[1, 1, 2], [2, 1, 1]]
matrix_z = [[2, 4], [5, 3], [1, 3]]

print(is_product_availability_matrix(matrix_y, matrix_z)) # Expected value: True
print(is_product_availability_matrix(matrix_z, matrix_x)) # Expected value: True
print(is_product_availability_matrix(matrix_z, matrix_w)) # Expected value: False //matrix_w가없습니다
print(is_product_availability_matrix(matrix_x, matrix_x)) # Expected value: True
```

### is_product_availability_matrix 모범답안

```python
def is_product_availability_matrix(matrix_a, matrix_b):
    return len([column_vector for column_vector in zip(*matrix_a)] == len(matrix_b))
```

## matrix_product

```python
def matrix_product(matrix_a, matrix_b):
    if is_product_availability_matrix(matrix_a, matrix_b) is not True:
        raise ArithmeticError
    return [[sum(i*j for i, j in zip(row_a, column_b)) for column_b in matrix_transpose(matrix_b)] for row_a in matrix_a]

# 실행결과
matrix_x= [[2, 5], [1, 1]]
matrix_y = [[1, 1, 2], [2, 1, 1]]
matrix_z = [[2, 4], [5, 3], [1, 3]]

print(matrix_product(matrix_y, matrix_z)) # Expected value: [[9, 13], [10, 14]]
print(matrix_product(matrix_z, matrix_x)) # Expected value: [[8, 14], [13, 28], [5, 8]]
print(matrix_product(matrix_x, matrix_x)) # Expected value: [[9, 15], [3, 6]]
print(matrix_product(matrix_z, matrix_w)) # Expected value: False
```

### matrix_product 모범답안

```python
def matrix_product(matrix_a, matrix_b):
    if is_product_availability_matrix(matrix_a, matrix_b) is not True:
        raise ArithmeticError
    return [[sum(a*b for a, b in zip(row_a, column_b)) for column_b in zip(*matrix_b)] for row_a in matrix_a]
```