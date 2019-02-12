---
layout: post
title: "18. Data Handling - Pandas #1"
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

# 이제 주피터로 하겠음

## Pandas

* 구조화된 데이터의 처리를 지원하는 파이썬 라이브러리
* 고성능Array 계산 라이브러리인 Numpy와 통합하여 강력한 "스프레드시트" 처리기능을 제공
* 인덱싱, 연산용 함수, 전처리 함수 등을 제공함
* 실습은 ipython으로 하셈

## 1_data_loading.ipynb

```python
import pandas as pd #라이브러리 호출

data_url = 'https://archive.ics.uci.edu/ml/machine-learning-databases/housing/housing.data' #Data URL
# data_url = './housing.data' #Data URL
df_data = pd.read_csv(data_url, sep='\s+', header = None) #csv 타입 데이터 로드, separate는 빈공간으로 지정하고, Column은 없음
```

* 데이터는 csv타입을 많이 씀
* `pd.read_csv`에서 `sep`은 데이터를 나누는 기준
* `\s`: 빈칸인 데이터를 가져오기
* `header`는 Columns에 뭔가 들어가 있느냐
* `df_data.head()`: 처음 다섯 줄 출력
* `df_data.as_matrix`로도 출력할 수 있음
* `type(df_data.values)`는 np 타입으로 나옴

## Pandas의 구성

![image](https://user-images.githubusercontent.com/28076542/46245356-53976700-c427-11e8-8ffb-c4121b653258.png)

* Series + DataFrame으로 구성
* shift+tab을 누르면 코드 상세정보 가능

## 3_pandas_series

```python
list_data = [1,2,3,4,5]
example_obj = Series(data = list_data)
example_obj
```

```python
list_data = [1,2,3,4,5]
list_name = ["a","b","c","d","e"]
example_obj = Series(data = list_data, index=list_name)
example_obj
```

* 이렇게 index에 값을 넣을 수 있는데 잘 안 씀
* 값을 넣으면 0, 1, 2 이런 식으로 접근 못함

## 4_pandas_dataframe

### DataFrame

* index와 columns로 일워진 matrix
* index = row
* 이 두 개로 데이터 접근 가능

```python
# Example from - https://chrisalbon.com/python/pandas_map_values_to_values.html
raw_data = {'first_name': ['Jason', 'Molly', 'Tina', 'Jake', 'Amy'],
        'last_name': ['Miller', 'Jacobson', 'Ali', 'Milner', 'Cooze'],
        'age': [42, 52, 36, 24, 73],
        'city': ['San Francisco', 'Baltimore', 'Miami', 'Douglas', 'Boston']}
df = pd.DataFrame(raw_data, columns = ['first_name', 'last_name', 'age', 'city'])
df
```

* 이렇게 dict로 선언하는건 잘 안 함

```python
DataFrame(raw_data, columns = ["age", "city"])
```

* 엑셀과 같이 column의 이름을 정해주면 column의 데이터를 뽑아 올 수 있음

### loc과 iloc의 차이

* loc: index location
* iloc: index position

```python
s = pd.Series(np.nan, index=[49,48,47,46,45, 1, 2, 3, 4, 5])
s
```

```bash
49   NaN
48   NaN
47   NaN
46   NaN
45   NaN
1    NaN
2    NaN
3    NaN
4    NaN
5    NaN
dtype: float64
```

#### loc

```python
s.loc[:3]
```

```bash
49   NaN
48   NaN
47   NaN
46   NaN
45   NaN
1    NaN
2    NaN
3    NaN
dtype: float64
```

* 인덱스 3까지 뽑아낸 모습

#### iloc

```python
s.iloc[:3]
```

```bash
49   NaN
48   NaN
47   NaN
dtype: float64
```

* 인덱스 세 번째까지 값을 뽑아낸 모습

## 데이터 할당

```python
df.debt = df.age > 40
df
```

* 새로운 feature을 만들어줄 때 사용하는 기법
* 이외에도 transpose, values, to_csv, del도 제공

## 5_data_selection

* selection: data 하나 가져오기

* pandas에서 데이터를 변경 확정 지으려면 inplace=True를 지정해야 함