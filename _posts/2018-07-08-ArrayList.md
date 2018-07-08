---
layout: post
title: "배열 리스트"
excerpt_separator:  <!--more-->
categories:
 - Data structure
tags:
  - 배열 리스트
  - 자료구조
  - 리스트
---

# 출처
### 윤성우, 『윤성우의 열혈 자료구조』, 오렌지미디어(2013-03-26), 74-98p

---

<!--more-->
## ADT
* Abstract Data type
* 순수한 기능을 나열

---

## 정수 기반 배열 리스트

### 리스트 자료구조의 ADT
```
void ListInit(List * plist);
 - 초기화할 리스트의 주소값을 인자로 전달
 - 리스트 생성 후 먼저 호출되는 함수  

void LInsert(List * plist, LData data);
 - 리스트에 데이터를 저장

int LFirst(List * plist, LData * pdata);
 - 첫 번째 데이터를 pdata가 가리키는 메모리에 저장
 - 데이터 참조를 위한 초기화 진행
 - 성공 시 1, 실패 시 0 반환

int LNext(List * plist, LData * pdata);
 - 참조된 데이터의 다음 데이터가 pdata가 가리키는 메모리에 저장
 - 반복 호출 가능
 - 참조를 시작하려면 LFirst 함수부터 호출해야 함
 - 성공 시 1, 실패 시 0 반환

LData LRemove(List * plist);
 - LFirst 또는 LNext 함수의 마지막 반환 데이터 삭제
 - 삭제된 데이터는 반환
 - 반복 호출 불가능

int LCount(List * plist);
 - 리스트에 저장되어 있는 데이터 수 반환
 ```

## ArrayList.h

```cpp
#ifndef __ARRAY_LIST_H__
#define __ARRAY_LIST_H__

#define TRUE 1  // '참'을 표현하기 위한 매크로 정의
#define FALSE 0 // '거짓'을 표현하기 위한 매크로 정의

#define LIST_LEN 100
typedef int LData; // LData에 대한 typedef 선언

typedef struct __ArrayList // 배열기반 리스트를 정의한 구조체
    {
  LData arr[LIST_LEN]; // 리스트의 저장소인 배열
  int numOfData;       // 저장된 데이터의 수
  int curPosition;     // 데이터 참조위치 기록
} ArrayList;

typedef ArrayList List;

void ListInit(List *plist);            // 초기화
void LInsert(List *plist, LData data); // 데이터 저장

int LFirst(List *plist, LData *pdata); // 첫 데이터 참조
int LNext(List *plist, LData *pdata);  // 두 번째 이후 데이터 참조

LData LRemove(List *plist); // 참조한 데이터 삭제
int LCount(List *plist);    // 저장된 데이터의 수 반환

#endif
```

## ArrayList.c
```cpp
#include <stdio.h>
#include "ArrayList.h"

void ListInit(List *plist) {
  (plist->numOfData) = 0;
  (plist->curPosition) = -1;
}

void LInsert(List *plist, LData data) {
  if (plist->numOfData >= LIST_LEN) {
    puts("저장이 불가능합니다.");
    return;
  }

  plist->arr[plist->numOfData] = data;
  (plist->numOfData)++;
}

int LFirst(List *plist, LData *pdata) {
  if (plist->numOfData == 0)
    return FALSE;

  (plist->curPosition) = 0;
  *pdata = plist->arr[0];
  return TRUE;
}

int LNext(List *plist, LData *pdata) {
  if (plist->curPosition >= (plist->numOfData) - 1)
    return FALSE;

  (plist->curPosition)++;
  *pdata = plist->arr[plist->curPosition];
  return TRUE;
}

LData LRemove(List *plist) {
  int rpos = plist->curPosition;
  int num = plist->numOfData;
  int i;
  LData rdata = plist->arr[rpos];

  for (i = rpos; i < num - 1; i++)
    plist->arr[i] = plist->arr[i + 1];

  (plist->numOfData)--;
  (plist->curPosition)--;
  return rdata;
}

int LCount(List *plist) { return plist->numOfData; }
```

## ListMain.c
```cpp
#include <stdio.h>
#include "ArrayList.h"

int main(void) {
  // ArrayList의 생성 및 초기화
  List list;
  int data;
  ListInit(&list);

  // 5개의 데이터 저장
  LInsert(&list, 11);
  LInsert(&list, 11);
  LInsert(&list, 22);
  LInsert(&list, 22);
  LInsert(&list, 33);

  // 저장된 데이터의 전체 출력
  printf("현재 데이터의 수: %d \n", LCount(&list));

  if (LFirst(&list, &data)) { // 첫 번째 데이터 조회
    printf("%d ", data);

    while (LNext(&list, &data)) // 두 번째 이후의 데이터 조회
      printf("%d ", data);
  }
  printf("\n\n");

  // 숫자 22를 탐색하여 모두 삭제
  if (LFirst(&list, &data)) {
    if (data == 22)
      LRemove(&list);

    while (LNext(&list, &data)) {
      if (data == 22)
        LRemove(&list);
    }
  }

  // 삭제 후 남은 데이터 전체 출력
  printf("현재 데이터의 수: %d \n", LCount(&list));

  if (LFirst(&list, &data)) {
    printf("%d ", data);

    while (LNext(&list, &data))
      printf("%d ", data);
  }
  printf("\n\n");
  return 0;
}
```

`gcc -c ArrayList.c`  
`gcc -c ListMain.c`  
`gcc ListMain.o ArrayList.o`를 해야 실행파일이 나옴

### 실행결과

```bash
현재 데이터의 수: 5
11 11 22 22 33

현재 데이터의 수: 3
11 11 33
```

정수 기반 배열 리스트이다.

---
## 구조체(Point) 기반 배열 리스트

* 좌표 저장
* 좌표 출력
* 좌표 값 비교

**ArrayList.h**에서  
`#include "Point.h"` 추가  
`typedef int LData;`를 `typedef Point * LData;`로 변경

## ArrayList.h
```cpp
#ifndef __ARRAY_LIST_H__
#define __ARRAY_LIST_H__

#include "Point.h"
#define TRUE 1  // '참'을 표현하기 위한 매크로 정의
#define FALSE 0 // '거짓'을 표현하기 위한 매크로 정의

#define LIST_LEN 100
//typedef int LData; // LData에 대한 typedef 선언
typedef Point * LData;

typedef struct __ArrayList // 배열기반 리스트를 정의한 구조체
    {
  LData arr[LIST_LEN]; // 리스트의 저장소인 배열
  int numOfData;       // 저장된 데이터의 수
  int curPosition;     // 데이터 참조위치 기록
} ArrayList;

typedef ArrayList List;

void ListInit(List *plist);            // 초기화
void LInsert(List *plist, LData data); // 데이터 저장

int LFirst(List *plist, LData *pdata); // 첫 데이터 참조
int LNext(List *plist, LData *pdata);  // 두 번째 이후 데이터 참조

LData LRemove(List *plist); // 참조한 데이터 삭제
int LCount(List *plist);    // 저장된 데이터의 수 반환

#endif
```

## Point.h
```cpp
#ifndef __POINT_H__
#define __POINT_H__

typedef struct _point {
  int xpos;
  int ypos;
} Point;

// Point 변수의 xpos, ypos 값 설정
void SetPointPos(Point *ppos, int xpos, int ypos);

// Point 변수의 xpos, ypos 정보 출력
void ShowPointPos(Point *ppos);

// 두 Point 변수의 비교
int PointComp(Point *pos1, Point *pos2);

#endif
```

## Point.c
```cpp
#include <stdio.h>
#include "Point.h"

void SetPointPos(Point *ppos, int xpos, int ypos) {
  ppos->xpos = xpos;
  ppos->ypos = ypos;
}

void ShowPointPos(Point *ppos) {
  printf("[%d, %d] \n", ppos->xpos, ppos->ypos);
}

int PointComp(Point *pos1, Point *pos2) {
  if (pos1->xpos == pos2->xpos && pos1->ypos == pos2->ypos)
    return 0;
  else if (pos1->xpos == pos2->xpos)
    return 1;
  else if (pos1->ypos == pos2->ypos)
    return 2;
  else
    return -1;
}
```

## PointListMain.c
```cpp
#include <stdio.h>
#include <stdlib.h>
#include "ArrayList.h"
#include "Point.h"

int main(void) {
  List list;
  Point compPos;
  Point *ppos;

  ListInit(&list);

  // 4개의 데이터 저장
  ppos = (Point *)malloc(sizeof(Point));
  SetPointPos(ppos, 2, 1);
  LInsert(&list, ppos);

  ppos = (Point *)malloc(sizeof(Point));
  SetPointPos(ppos, 2, 2);
  LInsert(&list, ppos);

  ppos = (Point *)malloc(sizeof(Point));
  SetPointPos(ppos, 3, 1);
  LInsert(&list, ppos);

  ppos = (Point *)malloc(sizeof(Point));
  SetPointPos(ppos, 3, 2);
  LInsert(&list, ppos);

  // 저장된 데이터의 출력
  printf("현재 데이터의 수: %d \n", LCount(&list));

  if (LFirst(&list, &ppos)) {
    ShowPointPos(ppos);

    while (LNext(&list, &ppos))
      ShowPointPos(ppos);
  }
  printf("\n");

  // xpos가 2인 모든 데이터 삭제
  compPos.xpos = 2;
  compPos.ypos = 0;

  if (LFirst(&list, &ppos)) {
    if (PointComp(ppos, &compPos) == 1) {
      ppos = LRemove(&list);
      free(ppos);
    }

    while (LNext(&list, &ppos)) {
      if (PointComp(ppos, &compPos) == 1) {
        ppos = LRemove(&list);
        free(ppos);
      }
    }
  }

  // 삭제 후 남은 데이터 전체 출력
  printf("현재 데이터의 수: %d \n", LCount(&list));

  if (LFirst(&list, &ppos)) {
    ShowPointPos(ppos);

    while (LNext(&list, &ppos))
      ShowPointPos(ppos);
  }
  printf("\n");

  return 0;
}
```

`gcc -c ArrayList.c`  
`gcc -c Point.c`  
`gcc -c PointListMain.c`  
`gcc PointListMain.o Point.o ArrayList.o`  
를 해야 실행파일이 나옴

### 실행결과

```bash
현재 데이터의 수: 4
[2, 1]
[2, 2]
[3, 1]
[3, 2]

현재 데이터의 수: 2
[3, 1]
[3, 2]
```

구조체 기반 배열 리스트이다.