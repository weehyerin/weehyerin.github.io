---
layout: post
title: "피보나치"
excerpt_separator:  <!--more-->
categories:
 - Data structure
tags:
  - 피보나치
  - 자료구조
---

# 출처
### 윤성우, 『윤성우의 열혈 자료구조』, 오렌지미디어(2013-03-26), 57p

---

<!--more-->
## FibonacciFunc.c

```cpp
#include <stdio.h>

int Fibo(int n) {
  if (n == 1)
    return 0;
  else if (n == 2)
    return 1;
  else
    return Fibo(n - 1) + Fibo(n - 2);
}

int main(void) {
  int i;
  for (i = 1; i < 15; i++)
    printf("%d ", Fibo(i));

  return 0;
}
```
### 실행결과

```bash
0 1 1 2 3 5 8 13 21 34 55 89 144 233
```

재귀를 이용한 피보나치 수열이다.