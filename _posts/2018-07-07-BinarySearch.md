---
layout: post
title: "이진 탐색"
excerpt_separator:  <!--more-->
categories:
 - Data structure
tags:
  - 이진 탐색
  - 자료구조
---

# 출처
### 윤성우, 『윤성우의 열혈 자료구조』, 오렌지미디어(2013-03-26), 28-29p

---

<!--more-->
## BinarySearch.c
```cpp
#include <stdio.h>

int BSearch(int ar[], int len, int target) {
  int first = 0;      // 탐색 대상의 시작 인덱스 값
  int last = len - 1; // 탐색 대상의 마지막 인덱스 값
  int mid;

  while (first <= last) {
    mid = (first + last) / 2; // 탐색 대상의 중앙을 찾는다.

    if (target == ar[mid]) // 중앙에 저장된 것이 타겟이라면
      return mid;          // 탐색 완료!
    else { // 타겟이 아니라면 탐색 대상을 반으로 줄인다.
      if (target < ar[mid])
        last = mid - 1; // 탐색범위를 좁히고 반복문 탈출용
      else
        first = mid + 1; // 탐색범위를 좁히고 반복문 탈출용
    }
  }
  return -1; // 찾지 못했을 때 반환되는 값 -1
}

int main(void) {
  int arr[] = {1, 3, 5, 7, 9};
  int idx;

  idx = BSearch(arr, sizeof(arr) / sizeof(int), 7);
  if (idx == -1)
    printf("탐색 실패\n");
  else
    printf("타겟 저장 인덱스: %d \n", idx);

  idx = BSearch(arr, sizeof(arr) / sizeof(int), 4);
  if (idx == -1)
    printf("탐식 실패 \n");
  else
    printf("타겟 저장 인덱스: %d \n", idx);

  return 0;
}
```

### 실행결과

```bash
타겟 저장 인덱스: 3
탐색 실패
```

시간복잡도가  $$\log_2 n$$인 이진탐색 알고리즘이다.