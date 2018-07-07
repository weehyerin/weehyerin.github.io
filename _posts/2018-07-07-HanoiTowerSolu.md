---
layout: post
title: "하노이 타워"
excerpt_separator:  <!--more-->
categories:
 - Data structure
tags:
  - 하노이 타워
  - 자료구조
---

# 출처
### 윤성우, 『윤성우의 열혈 자료구조』, 오렌지미디어(2013-03-26), 71p

---

<!--more-->
## HanoiTowerSolu.c

```cpp
#include <stdio.h>

void HanoiTowerMove(int num, char from, char by, char to) {
  if(num == 1) // 이동할 원반의 수가 1개라면
    printf("원반 1을 %c에서 %c로 이동 \n", from, to);
  else {
    HanoiTowerMove(num-1, from, to, by);
    printf("원반 %d을(를) %c에서 %c로 이동 \n", num, from, to);
    HanoiTowerMove(num-1, by, from, to);
  }
}

int main(void) {
  // 막대 A의 원반 3개를 막대 B를 경유하여 막대 C로 옮기기
  HanoiTowerMove(3, 'A', 'B', 'C');
  return 0;
}
```
### 실행결과

```bash
원반 1을 A에서 C로 이동
원반 2을(를) A에서 B로 이동
원반 1을 C에서 B로 이동
원반 3을(를) A에서 C로 이동
원반 1을 B에서 A로 이동
원반 2을(를) B에서 C로 이동
원반 1을 A에서 C로 이동
```

재귀를 이용한 하노이 타워다.