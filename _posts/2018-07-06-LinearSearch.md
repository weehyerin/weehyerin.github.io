---
layout: post
title: "순차 탐색"
excerpt_separator:  <!--more-->
categories:
 - Data structure
tags:
  - 순차 탐색
  - 자료구조
---
# 출처
### 윤성우, 『윤성우의 열혈 자료구조』, 오렌지미디어(2013-03-26), 19-20p
---
<!--more-->
## LinearSerach.c
```cpp
#include <stdio.h>

int LSearch(int ar[], int len, int target) { // 순차 탐색 알고리즘 적용된 함수

  int i;
  for (i = 0; i < len; i++) {
    if (ar[i] == target)
      return i; // 찾은 대상의 인덱스 값 변환
  }
  return -1; // 찾지 못했음을 의미하는 값 반환
}

int main(void) {
  int arr[] = {3, 5, 2, 4, 9};

  int idx;

  idx = LSearch(arr, sizeof(arr)/sizeof(int), 4);
  if(idx == -1)
    printf("탐색 실패\n");
  else
    printf("타겟 저장 인덱스: %d \n", idx);
  
  idx = LSearch(arr, sizeof(arr)/sizeof(int), 7);
  if(idx == -1)
    printf("탐색 실패 \n");
  else
    printf("타겟 저장 인덱스: %d \n", idx);

  return 0;
}
```
---
## 실행결과
```bash
타겟 저장 인덱스: 3
탐색 실패
```
시간복잡도가 n인 순차탐색 알고리즘이다.