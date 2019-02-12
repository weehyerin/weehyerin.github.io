---
layout: post
title: "9. <참고> Data Structure - Collections"
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

## Collections

* List, Tuple, Dict에 대한 Python Built-in 확장 자료구조(모듈)
* 편의성, 실행 효율 등을 사용자에게 제공함
* 아래의 모듈이 존재함

```python
from collections import deque
from collections import Counter
from collections import OrderedDict
from collections import defaultdict
from collections import namedtuple
```

## deque

* Stack과 Queue를 지원하는 모듈
* List에 비해 효율적인 자료 저장 방식을 지원함
* rotate, reverse등 Linked list의 특성을 지원함
* 기존 list 형태의 함수를 모두 지원함

```python
>>> from collections import deque
>>> deque_list = deque()
>>> for i in range(5):
...     deque_list.append(i)
...
>>> print(deque_list)
deque([0, 1, 2, 3, 4])
>>> deque_list.appendleft(10)
>>> print(deque_list)
deque([10, 0, 1, 2, 3, 4])

>>> deque_list.rotate(2)
>>> print(deque_list)
deque([3, 4, 10, 0, 1, 2])

>>> deque_list.rotate(2)
>>> print(deque_list)
deque([1, 2, 3, 4, 10, 0])

>>> deque_list.extend([5,6,7])
>>> print(deque_list)
deque([1, 2, 3, 4, 10, 0, 5, 6, 7])

>>> deque_list.extendleft([5,6,7])
>>> print(deque_list)
deque([7, 6, 5, 1, 2, 3, 4, 10, 0, 5, 6, 7])

>>> print(deque(reversed(deque_list)))
deque([7, 6, 5, 0, 10, 4, 3, 2, 1, 5, 6, 7])
```

* deque는 기존 list보다 효율적인 자료구조 제공
* 효율적 메모리 구조로 처리 속도 향상

### deque를 쓸 때 시간

```python
from collections import deque
import time

start_time = time.clock()
deque_list = deque()
# Stack
for i in range(10000):
    for i in range(1000):
        deque_list.append(i)
        deque_list.pop()
print(time.clock() - start_time, "seconds")

# 1.5220373509999998 seconds
```

### general list를 쓸 때 시간

```python
import time
start_time = time.clock()
just_list = []
# Stack
for i in range(10000):
    for i in range(1000):
        just_list.append(i)
        just_list.pop()
print(time.clock() - start_time, "seconds")

# 3.242714918 seconds
```

## OrderedDict

* dict와 달리 데이터를 입력한 순서대로 dict를 반환함

### dict를 쓸 때

```python
>>> d = {}
>>> d['x'] = 100
>>> d['y'] = 200
>>> d['z'] = 300
>>> d['l'] = 500
>>> for k, v in d.items():
...     print(k,v)
...
('y', 200)
('x', 100)
('z', 300)
('l', 500)
```

### OrderedDict를 쓸 때

* Dict type의 값을 value 또는 key 값으로 정렬할 때 사용 가능

```python
>>> from collections import OrderedDict
>>> d = OrderedDict()
>>> d['x'] = 100
>>> d['y'] = 200
>>> d['z'] = 300
>>> d['l'] = 500
>>> for k, v in d.items():
...     print(k, v)
...
('x', 100)
('y', 200)
('z', 300)
('l', 500)
```

```python
>>> from collections import OrderedDict
>>>
>>> d = OrderedDict()
>>> d['x'] = 100
>>> d['y'] = 200
>>> d['z'] = 300
>>> d['l'] = 500

# 앞 알파벳 기준 정렬
>>> for k, v in OrderedDict(sorted(d.items(), key=lambda t: t[0])).items():
...     print(k, v)
...
('l', 500)
('x', 100)
('y', 200)
('z', 300)

# 뒤 값 기준 정렬
>>> for k, v in OrderedDict(sorted(d.items(), key = lambda t: t[1])).items():
...     print(k, v)
...
('x', 100)
('y', 200)
('z', 300)
('l', 500)
```

## defaultdict

* Dict type의 값에 기본 값을 지정하고 신규값 생성 시 사용하는 방법
* 많이 유용함

### 일반 dict의 경우

```python
d = dict()
print(d["first"])

raceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'first'
```

### defaultdict의 경우

```python
>>> from collections import defaultdict
>>> d = defaultdict(object) # Default dictionary를 생성
>>> d = defaultdict(lambda: 0) # Default 값을 0으로 설정함
>>> print(d["first"])
0
```

### 비교 1. for와 if로 배열 초기화

```python
from collections import defaultdict
from collections import OrderedDict

text = """A press release is the quickest and easiest way to get free publicity. If well written, a press release can result in multiple published articles about your firm and its products. And that can mean new prospects contacting you asking you to sell to them. Talk about low-hanging fruit!
What's more, press releases are cost effective. If the release results in an article that (for instance) appears to recommend your firm or your product, that article is more likely to drive prospects to contact you than a comparable paid advertisement.
However, most press releases never accomplish that. Most press releases are just spray and pray. Nobody reads them, least of all the reporters and editors for whom they're intended. Worst case, a badly-written press release simply makes your firm look clueless and stupid.
For example, a while back I received a press release containing the following sentence: "Release 6.0 doubles the level of functionality available, providing organizations of all sizes with a fast-to-deploy, highly robust, and easy-to-use solution to better acquire, retain, and serve customers."
Translation: "The new release does more stuff." Why the extra verbiage? As I explained in the post "Why Marketers Speak Biz Blab", the BS words are simply a way to try to make something unimportant seem important. And, let's face it, a 6.0 release of a product probably isn't all that important.
As a reporter, my immediate response to that press release was that it's not important because it expended an entire sentence saying absolutely nothing. And I assumed (probably rightly) that the company's marketing team was a bunch of idiots.""".lower().split()

print(text)

word_count = {}

# 초기화 
for word in text:
    if word in word_count.keys():
        word_count[word] += 1
    else:
        word_count[word] = 1
print(word_count)

for word in text:
    word_count[word] += 1
for i, v in OrderedDict(sorted(
        word_count.items(), key=lambda t: t[1], reverse=True)).items():
    print(i, v)
```

### 비교 2. defaultdict로 초기화

```python
from collections import defaultdict
from collections import OrderedDict

text = """A press release is the quickest and easiest way to get free publicity. If well written, a press release can result in multiple published articles about your firm and its products. And that can mean new prospects contacting you asking you to sell to them. Talk about low-hanging fruit!
What's more, press releases are cost effective. If the release results in an article that (for instance) appears to recommend your firm or your product, that article is more likely to drive prospects to contact you than a comparable paid advertisement.
However, most press releases never accomplish that. Most press releases are just spray and pray. Nobody reads them, least of all the reporters and editors for whom they're intended. Worst case, a badly-written press release simply makes your firm look clueless and stupid.
For example, a while back I received a press release containing the following sentence: "Release 6.0 doubles the level of functionality available, providing organizations of all sizes with a fast-to-deploy, highly robust, and easy-to-use solution to better acquire, retain, and serve customers."
Translation: "The new release does more stuff." Why the extra verbiage? As I explained in the post "Why Marketers Speak Biz Blab", the BS words are simply a way to try to make something unimportant seem important. And, let's face it, a 6.0 release of a product probably isn't all that important.
As a reporter, my immediate response to that press release was that it's not important because it expended an entire sentence saying absolutely nothing. And I assumed (probably rightly) that the company's marketing team was a bunch of idiots.""".lower().split()

print(text)

word_count = {}

word_count = defaultdict(object)     # Default dictionary를 생성
word_count = defaultdict(lambda: 1)  # Default 값을 1로 설정함
for word in text:
    word_count[word] += 1
for i, v in OrderedDict(sorted(
        word_count.items(), key=lambda t: t[1], reverse=True)).items():
    print(i, v)
```

### 결과는 다음과 같이 같음

```python
# print(text)
['a', 'press', 'release', 'is', 'the', 'quickest', 'and', 'easiest', 'way', 'to', 'get', 'free', 'publicity.', 'if', 'well', 'written,', 'a', 'press', 'release', 'can', 'result', 'in', 'multiple', 'published', 'articles', 'about', 'your', 'firm', 'and', 'its', 'products.', 'and', 'that', 'can', 'mean', 'new', 'prospects', 'contacting', 'you', 'asking', 'you', 'to', 'sell', 'to', 'them.', 'talk', 'about', 'low-hanging', 'fruit!', "what's", 'more,', 'press', 'releases', 'are', 'cost', 'effective.', 'if', 'the', 'release', 'results', 'in', 'an', 'article', 'that', '(for', 'instance)', 'appears', 'to', 'recommend', 'your', 'firm', 'or', 'your', 'product,', 'that', 'article', 'is', 'more', 'likely', 'to', 'drive', 'prospects', 'to', 'contact', 'you', 'than', 'a', 'comparable', 'paid', 'advertisement.', 'however,', 'most', 'press', 'releases', 'never', 'accomplish', 'that.', 'most', 'press', 'releases', 'are', 'just', 'spray', 'and', 'pray.', 'nobody', 'reads', 'them,', 'least', 'of', 'all', 'the', 'reporters', 'and', 'editors', 'for', 'whom', "they're", 'intended.', 'worst', 'case,', 'a', 'badly-written', 'press', 'release', 'simply', 'makes', 'your', 'firm', 'look', 'clueless', 'and', 'stupid.', 'for', 'example,', 'a', 'while', 'back', 'i', 'received', 'a', 'press', 'release', 'containing', 'the', 'following', 'sentence:', '"release', '6.0', 'doubles', 'the', 'level', 'of', 'functionality', 'available,', 'providing', 'organizations', 'of', 'all', 'sizes', 'with', 'a', 'fast-to-deploy,', 'highly', 'robust,', 'and', 'easy-to-use', 'solution', 'to', 'better', 'acquire,', 'retain,', 'and', 'serve', 'customers."', 'translation:', '"the', 'new', 'release', 'does', 'more', 'stuff."', 'why', 'the', 'extra', 'verbiage?', 'as', 'i', 'explained', 'in', 'the', 'post', '"why', 'marketers', 'speak', 'biz', 'blab",', 'the', 'bs', 'words', 'are', 'simply', 'a', 'way', 'to', 'try', 'to', 'make', 'something', 'unimportant', 'seem', 'important.', 'and,', "let's", 'face', 'it,', 'a', '6.0', 'release', 'of', 'a', 'product', 'probably', "isn't", 'all', 'that', 'important.', 'as', 'a', 'reporter,', 'my', 'immediate', 'response', 'to', 'that', 'press', 'release', 'was', 'that', "it's", 'not', 'important', 'because', 'it', 'expended', 'an', 'entire', 'sentence', 'saying', 'absolutely', 'nothing.', 'and', 'i', 'assumed', '(probably', 'rightly)', 'that', 'the', "company's", 'marketing', 'team', 'was', 'a', 'bunch', 'of', 'idiots.']

# print(word_count)
{'a': 12, 'press': 8, 'release': 8, 'is': 2, 'the': 9, 'quickest': 1, 'and': 9, 'easiest': 1, 'way': 2, 'to': 10, 'get': 1, 'free': 1, 'publicity.': 1, 'if': 2, 'well': 1, 'written,': 1, 'can': 2, 'result': 1, 'in': 3, 'multiple': 1, 'published': 1, 'articles': 1, 'about': 2, 'your': 4, 'firm': 3, 'its': 1, 'products.': 1, 'that': 7, 'mean': 1, 'new': 2, 'prospects': 2, 'contacting': 1, 'you': 3, 'asking': 1, 'sell': 1, 'them.': 1, 'talk': 1, 'low-hanging': 1, 'fruit!': 1, "what's": 1, 'more,': 1, 'releases': 3, 'are': 3, 'cost': 1, 'effective.': 1, 'results': 1, 'an': 2, 'article': 2, '(for': 1, 'instance)': 1, 'appears': 1, 'recommend': 1, 'or': 1, 'product,': 1, 'more': 2, 'likely': 1, 'drive': 1, 'contact': 1, 'than': 1, 'comparable': 1, 'paid': 1, 'advertisement.': 1, 'however,': 1, 'most': 2, 'never': 1, 'accomplish': 1, 'that.': 1, 'just': 1, 'spray': 1, 'pray.': 1, 'nobody': 1, 'reads': 1, 'them,': 1, 'least': 1, 'of': 5, 'all': 3, 'reporters': 1, 'editors': 1, 'for': 2, 'whom': 1, "they're": 1, 'intended.': 1, 'worst': 1, 'case,': 1, 'badly-written': 1, 'simply': 2, 'makes': 1, 'look': 1, 'clueless': 1, 'stupid.': 1, 'example,': 1, 'while': 1, 'back': 1, 'i': 3, 'received': 1, 'containing': 1, 'following': 1, 'sentence:': 1, '"release': 1, '6.0': 2, 'doubles': 1, 'level': 1, 'functionality': 1, 'available,': 1, 'providing': 1, 'organizations': 1, 'sizes': 1, 'with': 1, 'fast-to-deploy,': 1, 'highly': 1, 'robust,': 1, 'easy-to-use': 1, 'solution': 1, 'better': 1, 'acquire,': 1, 'retain,': 1, 'serve': 1, 'customers."': 1, 'translation:': 1, '"the': 1, 'does': 1, 'stuff."': 1, 'why': 1, 'extra': 1, 'verbiage?': 1, 'as': 2, 'explained': 1, 'post': 1, '"why': 1, 'marketers': 1, 'speak': 1, 'biz': 1, 'blab",': 1, 'bs': 1, 'words': 1, 'try': 1, 'make': 1, 'something': 1, 'unimportant': 1, 'seem': 1, 'important.': 2, 'and,': 1, "let's": 1, 'face': 1, 'it,': 1, 'product': 1, 'probably': 1, "isn't": 1, 'reporter,': 1, 'my': 1, 'immediate': 1, 'response': 1, 'was': 2, "it's": 1, 'not': 1, 'important': 1, 'because': 1, 'it': 1, 'expended': 1, 'entire': 1, 'sentence': 1, 'saying': 1, 'absolutely': 1, 'nothing.': 1, 'assumed': 1, '(probably': 1, 'rightly)': 1, "company's": 1, 'marketing': 1, 'team': 1, 'bunch': 1, 'idiots.': 1}

# print(i, v), value값 기준 정렬
a 24
to 20
the 18
and 18
press 16
release 16
that 14
of 10
your 8
in 6
firm 6
you 6
releases 6
are 6
all 6
i 6
is 4
way 4
if 4
can 4
about 4
new 4
prospects 4
an 4
article 4
more 4
most 4
for 4
simply 4
6.0 4
as 4
important. 4
was 4
quickest 2
easiest 2
get 2
free 2
publicity. 2
well 2
written, 2
result 2
multiple 2
published 2
articles 2
its 2
products. 2
mean 2
contacting 2
asking 2
sell 2
them. 2
talk 2
low-hanging 2
fruit! 2
what's 2
more, 2
cost 2
effective. 2
results 2
(for 2
instance) 2
appears 2
recommend 2
or 2
product, 2
likely 2
drive 2
contact 2
than 2
comparable 2
paid 2
advertisement. 2
however, 2
never 2
accomplish 2
that. 2
just 2
spray 2
pray. 2
nobody 2
reads 2
them, 2
least 2
reporters 2
editors 2
whom 2
they're 2
intended. 2
worst 2
case, 2
badly-written 2
makes 2
look 2
clueless 2
stupid. 2
example, 2
while 2
back 2
received 2
containing 2
following 2
sentence: 2
"release 2
doubles 2
level 2
functionality 2
available, 2
providing 2
organizations 2
sizes 2
with 2
fast-to-deploy, 2
highly 2
robust, 2
easy-to-use 2
solution 2
better 2
acquire, 2
retain, 2
serve 2
customers." 2
translation: 2
"the 2
does 2
stuff." 2
why 2
extra 2
verbiage? 2
explained 2
post 2
"why 2
marketers 2
speak 2
biz 2
blab", 2
bs 2
words 2
try 2
make 2
something 2
unimportant 2
seem 2
and, 2
let's 2
face 2
it, 2
product 2
probably 2
isn't 2
reporter, 2
my 2
immediate 2
response 2
it's 2
not 2
important 2
because 2
it 2
expended 2
entire 2
sentence 2
saying 2
absolutely 2
nothing. 2
assumed 2
(probably 2
rightly) 2
company's 2
marketing 2
team 2
bunch 2
idiots. 2
```

## Counter

* Sequence type의 data element의 개수를 dict 형태로 반환

```python
>>from collections import Counter
>>> c = Counter() # a new, empty counter
>>> c = Counter('gallahad') # a new counter from an iterable
>>> print(c)
Counter({'a': 3, 'l': 2, 'h': 1, 'g': 1, 'd': 1})
```

* Dict type, keyword parameter 등도 모두 처리 가능

```python
>>> c = Counter({'red': 4, 'blue': 2}) # a new counter from a mapping
>>> print(c)
Counter({'red': 4, 'blue': 2})
>>> print(list(c.elements()))
['blue', 'blue', 'red', 'red', 'red', 'red']

>>> c = Counter(cats=4, dogs=8) # a new counter from keyword args
>>> print(c)
Counter({'dogs': 8, 'cats': 4})
>>> print(list(c.elements()))
['cats', 'cats', 'cats', 'cats', 'dogs', 'dogs', 'dogs', 'dogs', 'dogs', 'dogs', 'dogs', 'dogs']
```

## namedtuple

* Tuple 형태로 Data 구조체를 저장하는 방법
* 저장되는 data의 variable을 사전에 지정해서 저장함

```python
>>> from collections import namedtuple
>>> Point = namedtuple('Point', ['x', 'y'])
>>> p = Point(11, y=22)
>>> print(p[0] + p[1])
33
>>> x, y = p
>>> print(x, y)
(11, 22)
>>> print(p.x + p.y)
33
>>> print(Point(x=11, y=22))
Point(x=11, y=22)
```

### 나중에 설명한댔지만 궁금해서...

#### users.csv

```csv
user_id,integration_id,login_id,password,first_name,last_name,full_name,sortable_name,short_name,email,status
1555551,,lrice,,Lonnie,Rice,Lonnie Rice,"Rice, Lonnie",Lonnie Rice,lrice@institution-name.edu,active
1555552,,jthompson,,Jan,Thompson,Jan Thompson,"Thompson, Jan",Jan Thompson,jthompson@institution-name.edu,active
1555553,,rreeves,,Roger,Reeves,Roger Reeves,"Reeves, Roger",Roger Reeves,rreeves@institution-name.edu,active
1555554,,wgriffin,,Warren,Griffin,Warren Griffin,"Griffin, Warren",Warren Griffin,wgriffin@institution-name.edu,active
1555555,,tsanders,,Tammy,Sanders,Tammy Sanders,"Sanders, Tammy",Tammy Sanders,tsanders@institution-name.edu,active
1555556,,cprice,,Carrie,Price,Carrie Price,"Price, Carrie",Carrie Price,cprice@institution-name.edu,active
1555557,,abowers,,Alicia,Bowers,Alicia Bowers,"Bowers, Alicia",Alicia Bowers,abowers@institution-name.edu,active
1555558,,eramsey,,Essie,Ramsey,Essie Ramsey,"Ramsey, Essie",Essie Ramsey,eramsey@institution-name.edu,active
1555559,,lwillis,,Lila,Willis,Lila Willis,"Willis, Lila",Lila Willis,lwillis@institution-name.edu,active
1555560,,csoto,,Corey,Soto,Corey Soto,"Soto, Corey",Corey Soto,csoto@institution-name.edu,active
```

```python
from collections import namedtuple
import csv
f = open("users.csv", "r")
next(f)
reader = csv.reader(f)
student_list = []
for row in reader:
    student_list.append(row)
    print(row)
print(student_list)

columns = ["user_id", "integration_id", "login_id", "password", "first_name",
            "last_name", "full_name", "sortable_name", "short_name",
            "email", "status"]
Student = namedtuple('Student', columns)
student_namedtupe_list = []
for row in student_list:
    student = Student(*row)
    student_namedtupe_list.append(student)
print(student_namedtupe_list[0])
print(student_namedtupe_list[0].full_name)
```

### 궁금한 코드 결과

```python
# print(row)
['1555551', '', 'lrice', '', 'Lonnie', 'Rice', 'Lonnie Rice', 'Rice, Lonnie', 'Lonnie Rice', 'lrice@institution-name.edu', 'active']
['1555552', '', 'jthompson', '', 'Jan', 'Thompson', 'Jan Thompson', 'Thompson, Jan', 'Jan Thompson', 'jthompson@institution-name.edu', 'active']
['1555553', '', 'rreeves', '', 'Roger', 'Reeves', 'Roger Reeves', 'Reeves, Roger', 'Roger Reeves', 'rreeves@institution-name.edu', 'active']
['1555554', '', 'wgriffin', '', 'Warren', 'Griffin', 'Warren Griffin', 'Griffin, Warren', 'Warren Griffin', 'wgriffin@institution-name.edu', 'active']
['1555555', '', 'tsanders', '', 'Tammy', 'Sanders', 'Tammy Sanders', 'Sanders, Tammy', 'Tammy Sanders', 'tsanders@institution-name.edu', 'active']
['1555556', '', 'cprice', '', 'Carrie', 'Price', 'Carrie Price', 'Price, Carrie', 'Carrie Price', 'cprice@institution-name.edu', 'active']
['1555557', '', 'abowers', '', 'Alicia', 'Bowers', 'Alicia Bowers', 'Bowers, Alicia', 'Alicia Bowers', 'abowers@institution-name.edu', 'active']
['1555558', '', 'eramsey', '', 'Essie', 'Ramsey', 'Essie Ramsey', 'Ramsey, Essie', 'Essie Ramsey', 'eramsey@institution-name.edu', 'active']
['1555559', '', 'lwillis', '', 'Lila', 'Willis', 'Lila Willis', 'Willis, Lila', 'Lila Willis', 'lwillis@institution-name.edu', 'active']
['1555560', '', 'csoto', '', 'Corey', 'Soto', 'Corey Soto', 'Soto, Corey', 'Corey Soto', 'csoto@institution-name.edu', 'active']

# print(student_list)
[['1555551', '', 'lrice', '', 'Lonnie', 'Rice', 'Lonnie Rice', 'Rice, Lonnie', 'Lonnie Rice', 'lrice@institution-name.edu', 'active'], ['1555552', '', 'jthompson', '', 'Jan', 
'Thompson', 'Jan Thompson', 'Thompson, Jan', 'Jan Thompson', 'jthompson@institution-name.edu', 'active'], ['1555553', '', 'rreeves', '', 'Roger', 'Reeves', 'Roger Reeves', 'Reeves, Roger', 'Roger Reeves', 'rreeves@institution-name.edu', 'active'], ['1555554', '', 'wgriffin', '', 'Warren', 'Griffin', 'Warren Griffin', 'Griffin, Warren', 'Warren Griffin', 'wgriffin@institution-name.edu', 'active'], ['1555555', '', 'tsanders', '', 'Tammy', 'Sanders', 'Tammy Sanders', 'Sanders, Tammy', 'Tammy Sanders', 'tsanders@institution-name.edu', 'active'], ['1555556', '', 'cprice', '', 'Carrie', 'Price', 'Carrie Price', 'Price, Carrie', 'Carrie Price', 'cprice@institution-name.edu', 'active'], ['1555557', '', 'abowers', '', 'Alicia', 'Bowers', 'Alicia Bowers', 'Bowers, Alicia', 'Alicia Bowers', 'abowers@institution-name.edu', 'active'], ['1555558', '', 'eramsey', '', 'Essie', 'Ramsey', 'Essie Ramsey', 'Ramsey, Essie', 'Essie Ramsey', 'eramsey@institution-name.edu', 'active'], ['1555559', '', 'lwillis', '', 'Lila', 'Willis', 'Lila Willis', 'Willis, Lila', 'Lila Willis', 'lwillis@institution-name.edu', 'active'], ['1555560', '', 'csoto', '', 'Corey', 'Soto', 'Corey Soto', 'Soto, Corey', 'Corey Soto', 'csoto@institution-name.edu', 'active']]

# print(student_namedtupe_list[0])
Student(user_id='1555551', integration_id='', login_id='lrice', password='', first_name='Lonnie', last_name='Rice', full_name='Lonnie Rice', sortable_name='Rice, Lonnie', short_name='Lonnie Rice', email='lrice@institution-name.edu', status='active')

# print(student_namedtupe_list[0].full_name)
Lonnie Rice
```