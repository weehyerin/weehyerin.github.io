---
layout: post
title: "Case Study - News Categorization"
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

## 비슷한 뉴스기사 묶기

* 컴퓨터는 문자를 이해못해 숫자로 표현해야 함
* 숫자를 가깝게 배치하는 것을 유사하다고 정의하면 됨
* 문자를 Vector로 바꿔야 함

## 문자를 Vector로 - One-hot Encoding

* 하나의 단어를 Vector의 Index로 인식, 단어 존재 시 1 없으면 0

```python
Rome = [1, 0, 0, 0, 0, 0, 0]
Paris = [0, 1, 0, 0, 0, 0, 0]
Italy = [0, 0, 1, 0, 0, 0, 0]
France = [0, 0, 0, 1, 0, 0, 0]
```

## Bag of words

* 단어별로 인덱스를 부여해서 한 문장(또는 문서)의 단어의 개수를 Vector로 표현

![image](https://user-images.githubusercontent.com/28076542/45942462-6bcd4780-c01d-11e8-8dae-5e17a41dec64.png)

* Corpus: 문단에서 나온 모든 단어의 모음
* Dict: Corpus에 index를 가진 것

## 유사성 확인

* Euclidian Distance를 씀
* Cosine Similarity도 씀

## Euclidian Distance

![image](https://user-images.githubusercontent.com/28076542/45943725-78a06a00-c022-11e8-8767-2ada288164b5.png)

## Cosine Similarity

![image](https://user-images.githubusercontent.com/28076542/45943767-a08fcd80-c022-11e8-9e8d-c9c781dec22e.png)

* Distance보다 Direction이 더 결과가 좋음
* 따라서 Cosine Similarity가 더 좋음

## Data set

* [축구와 야구 선수들의 영문 기사를 분류해보기](https://github.com/TEAMLAB-Lecture/AI-python-connect)
* 야구 = 1, 2, 3, 4
* 축구 = 5, 6, 7, 8

## Process

* 파일 불러오기
* 파일 읽어서 단어사전(corpus) 만들기
* 단어별로 Index 만들기 --> Dict 사용
* 만들어진 Index로 문서별로 Bag of words vector 생성
* 비교하고자 하는 문서 비교하기 --> Cosine Similarity
* 얼마나 맞는지 측정하기

## 파일 불러오기

```python
import os

def get_file_list(dir_name):
    return os.listdir(dir_name)

if __name__ == "__main__":
    dir_name = "news_data"
    file_list = get_file_list(dir_name)
    file_list = [os.path.join(dir_name, file_name) for file_name in file_list]
```

* `get_file_list`는 디렉토리안 파일을 다 가져오기
* `join`으로 `dir_name`과 합쳐 경로까지 붙이기
* OS에 따라 경로 구분이 달라 `os.path.join`을 사용해야 함

## 파일 내용 가져오기

```python
def get_conetents(file_list):
    y_class = []
    X_text = []
    class_dict = {
        1: "0", 2: "0", 3:"0", 4:"0", 5:"1", 6:"1", 7:"1", 8:"1"}

    for file_name in file_list:
        try:
            f = open(file_name, "r",  encoding="cp949")
            category = int(file_name.split(os.sep)[1].split("_")[0])
            y_class.append(class_dict[category])
            X_text.append(f.read())
            f.close()
        except UnicodeDecodeError as e:
            print(e)
            print(file_name)
    return X_text, y_class
```

* 야구는 0
* 축구는 1
* `cp949`는 윈도우 인코딩
* `os.sep`은 경로 구분자, [1]은 파일명
* `1_name.txt`에서 1만 빼와 `category`에 넣으라는 코드가 중간에 있음
* 그럼 `category`에는 1~8의 값이 들어옴
* `y_class`에는 0~1의 값이 들어옴
* `X_text`는 기사의 내용

## Corpus 만들기 + 단어별 index 생성하기

```python
def get_cleaned_text(text):
    import re
    text = re.sub('\W+','', text.lower() )
    return text


def get_corpus_dict(text):
    text = [sentence.split() for sentence in text]
    clenad_words = [get_cleaned_text(word) for words in text for word in words]

    from collections import OrderedDict
    corpus_dict = OrderedDict()
    for i, v in enumerate(set(clenad_words)):
        corpus_dict[v] = i
    return corpus_dict
```

* `get_corpus_dict`에서 text 80개를 list로 split한 후 `text`에 다시 할당
* `text`는 결과적으로 이차원 배열
* `for words in text for word in words`는 이차원 배열 `text`에서 단어들만 뽑아오는 것
* 단어만 뽑아온 후 단어들마다 `get_cleaned_text` 호출
* `get_clenad_text`는 regular expression으로 문장 부호들을 없애주고 대문자를 소문자로 변환
* ex) I'm yours --> imyours
* `OrderedDict`를 이용한 이유는 `Dict`를 쓰면 순서가 지 맘대로 바뀌기 때문
* `set(clenad_words)`로 단어들은 겹치는 단어들이 사라짐
* `enumerate`을 사용하여 `i`에는 index, `v`에는 단어가 들어감
* `corpus_dict[v] = i`로 단어가 **key** 값이 됨

## 문서별로 Bag of words vector 생성

```python
def get_count_vector(text, corpus):
    text = [sentence.split() for sentence in text]
    word_number_list = [[corpus[get_cleaned_text(word)] for word in words] for words in text]
    X_vector = [[0 for _ in range(len(corpus))] for x in range(len(text))]

    for i, text in enumerate(word_number_list):
        for word_number in text:
            X_vector[i][word_number] += 1
    return X_vector
```

* `word_number_list`는 각 문서들마다 corpus에 해당되는 단어들이 어떻게 나열되어 있는지 보여주는 이차원 배열
* `X_vector`의 for문에서 `_`는 변수를 쓰지 않겠다는 뜻
* `X_vector`는 80 X 4302개의 0이 들어있는 2차원 배열
* 밑의 for문에서 X_vector의 해당 index를 하나씩 올려줌

## 비교하기

```python
import math
def get_cosine_similarity(v1,v2):
    "compute cosine similarity of v1 to v2: (v1 dot v2)/{||v1||*||v2||)"
    sumxx, sumxy, sumyy = 0, 0, 0
    for i in range(len(v1)):
        x = v1[i]; y = v2[i]
        sumxx += x*x
        sumyy += y*y
        sumxy += x*y
    return sumxy/math.sqrt(sumxx*sumyy)
```

* v1과 v2는 각각 하나의 문서에 대한 벡터 표현

## 비교 결과 정리하기

```python
def get_similarity_score(X_vector, source):
    source_vector = X_vector[source]
    similarity_list = []
    for target_vector in X_vector:
        similarity_list.append(
            get_cosine_similarity(source_vector, target_vector))
    return similarity_list


def get_top_n_similarity_news(similarity_score, n):
    import operator
    x = {i:v for i, v in enumerate(similarity_score)}
    sorted_x = sorted(x.items(), key=operator.itemgetter(1))

    return list(reversed(sorted_x))[1:n+1]
```

* `get_similarity_score`로 80개의 문서를 돌려줌
* `source`는 찾고자 하는 문서번호
* `similarity_list`의 마지막은 1이고 나머지는 유사도 결과가 저장됨
* 코드 예시로 `source`는 10으로 10번째 뉴스임
* `get_top_similarity_news`는 가장 유사한 n개의 뉴스 리스트를 리턴함

## 전체 코드

```python
import os


def get_file_list(dir_name):
    return os.listdir(dir_name)

def get_conetents(file_list):
    y_class = []
    X_text = []
    class_dict = {
        1: "0", 2: "0", 3:"0", 4:"0", 5:"1", 6:"1", 7:"1", 8:"1"}

    for file_name in file_list:
        try:
            f = open(file_name, "r",  encoding="cp949")
            category = int(file_name.split(os.sep)[1].split("_")[0])
            y_class.append(class_dict[category])
            X_text.append(f.read())
            f.close()
        except UnicodeDecodeError as e:
            print(e)
            print(file_name)
    return X_text, y_class



def get_cleaned_text(text):
    import re
    text = re.sub('\W+','', text.lower() )
    return text


def get_corpus_dict(text):
    text = [sentence.split() for sentence in text]
    clenad_words = [get_cleaned_text(word) for words in text for word in words]

    from collections import OrderedDict
    corpus_dict = OrderedDict()
    for i, v in enumerate(set(clenad_words)):
        corpus_dict[v] = i
    return corpus_dict


def get_count_vector(text, corpus):
    text = [sentence.split() for sentence in text]
    word_number_list = [[corpus[get_cleaned_text(word)] for word in words] for words in text]
    X_vector = [[0 for _ in range(len(corpus))] for x in range(len(text))]

    for i, text in enumerate(word_number_list):
        for word_number in text:
            X_vector[i][word_number] += 1
    return X_vector

import math
def get_cosine_similarity(v1,v2):
    "compute cosine similarity of v1 to v2: (v1 dot v2)/{||v1||*||v2||)"
    sumxx, sumxy, sumyy = 0, 0, 0
    for i in range(len(v1)):
        x = v1[i]; y = v2[i]
        sumxx += x*x
        sumyy += y*y
        sumxy += x*y
    return sumxy/math.sqrt(sumxx*sumyy)

def get_similarity_score(X_vector, source):
    source_vector = X_vector[source]
    similarity_list = []
    for target_vector in X_vector:
        similarity_list.append(
            get_cosine_similarity(source_vector, target_vector))
    return similarity_list


def get_top_n_similarity_news(similarity_score, n):
    import operator
    x = {i:v for i, v in enumerate(similarity_score)}
    sorted_x = sorted(x.items(), key=operator.itemgetter(1))

    return list(reversed(sorted_x))[1:n+1]

def get_accuracy(similarity_list, y_class, source_news):
    source_class = y_class[source_news]

    return sum([source_class == y_class[i[0]] for i in similarity_list]) / len(similarity_list)


if __name__ == "__main__":
    dir_name = "news_data"
    file_list = get_file_list(dir_name)
    file_list = [os.path.join(dir_name, file_name) for file_name in file_list]

    X_text, y_class = get_conetents(file_list)

    corpus = get_corpus_dict(X_text)
    print("Number of words : {0}".format(len(corpus)))
    X_vector = get_count_vector(X_text, corpus)
    source_number = 10

    result = []

    for i in range(80):
        source_number = i

        similarity_score = get_similarity_score(X_vector, source_number)
        similarity_news = get_top_n_similarity_news(similarity_score, 10)
        accuracy_score = get_accuracy(similarity_news, y_class, source_number)
        result.append(accuracy_score)
    print(sum(result) / 80)
```

* `accuracy_score`은 뽑아낸 similarity_news가 얼마나 정답을 맞춘건지 비율을 나타냄

---

# 그런데 이짓 실제로 안함

## CountVectorizer 모듈 씀

### 그냥 과정이 어떻게 되나 보여줄려고 한 것