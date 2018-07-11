---
layout: post
title: "원형 연결 리스트"
excerpt_separator:  <!--more-->
categories:
 - Data structure
tags:
  - 연결 리스트
  - 원형 연결 리스트
  - 자료구조
  - 리스트
---
# 출처
### 윤성우, 『윤성우의 열혈 자료구조』, 오렌지미디어(2013-03-26), 158-177p
---
<!--more-->
## 원형 연결 리스트
* 마지막 노드가 첫 번째 노드를 가리킴

---
## CLinkedList.h
```cpp
#ifndef __C_LINKED_LIST_H__
#define __C_LINKED_LIST_H__

#define TRUE 1
#define FALSE 0

typedef int Data;

typedef struct _node {
  Data data;
  struct _node *next;
} Node;

typedef struct _CLL {
  Node *tail;
  Node *cur;
  Node *before;
  int numOfData;
} CList;

typedef CList List;

void ListInit(List *plist);
void LInsert(List *plist, Data data);      // 꼬리에 노드를 추가
void LInsertFront(List *plist, Data data); // 머리에 노드를 추가

int LFirst(List *plist, Data *pdata);
int LNext(List *plist, Data *pdata);
Data LRemove(List *plist);
int LCount(List *plist);

#endif
```
## CLinkedList.c
```cpp
#include <stdio.h>
#include <stdlib.h>
#include "CLinkedList.h"

void ListInit(List *plist) {
  plist->tail = NULL;
  plist->cur = NULL;
  plist->before = NULL;
  plist->numOfData = 0;
}

void LInsert(List *plist, Data data) {
  Node *newNode = (Node *)malloc(sizeof(Node)); // 새 노드 생성
  newNode->data = data; // 새 노드에 데이터 저장

  if (plist->tail == NULL) { // 첫 번째 노드라면
    plist->tail = newNode;   // tail이 새 노드를 가리키게 한다.
    newNode->next = newNode; // 새 노드 자신을 가리키게 한다.
  } else {                   // 두 번째 이후의 노드라면
    newNode->next = plist->tail->next;
    plist->tail->next = newNode;
    plist->tail = newNode; // LInsertFront 함수와의 유일한 차이점
  }

  (plist->numOfData)++;
}

void LInsertFront(List *plist, Data data) {
  Node *newNode = (Node *)malloc(sizeof(Node)); // 새 노드 생성
  newNode->data = data; // 새 노드에 데이터 저장

  if (plist->tail == NULL) { // 첫 번째 노드라면
    plist->tail = newNode;   // tail이 새 노드를 가리키게 한다.
    newNode->next = newNode; // 새 노드 자신을 가리키게 한다.
  } else {                   // 두 번째 이후의 노드라면
    newNode->next = plist->tail->next;
    plist->tail->next = newNode;
  }

  (plist->numOfData)++;
}

int LFirst(List *plist, Data *pdata) {
  if (plist->tail == NULL) // 저장된 노드가 없다면
    return FALSE;

  plist->before = plist->tail;    // before가 꼬리를 가리키게 한다.
  plist->cur = plist->tail->next; // cur이 머리를 가리키게 한다.

  *pdata = plist->cur->data; // cur이 가리키는 노드의 데이터 반환
  return TRUE;
}

int LNext(List *plist, Data *pdata) {
  if (plist->tail == NULL) // 저장된 노드가 없다면
    return FALSE;

  plist->before = plist->cur;    // before 다음 노드를 가리키게 한다.
  plist->cur = plist->cur->next; // cur이 다음 노드를 가리키게 한다.

  *pdata = plist->cur->data; // cur이 가리키는 노드의 데이터 반환
  return TRUE;
}

Data LRemove(List *plist) {
  Node *rpos = plist->cur;
  Data rdata = rpos->data;

  if (rpos == plist->tail) { // 삭제 대상을 tail이 가리킨다면
    if (plist->tail == plist->tail->next) // 그리고 마지막 남은 노드라면
      plist->tail = NULL;
    else
      plist->tail = plist->before;
  }

  plist->before->next = plist->cur->next; // 핵심연산 1
  plist->cur = plist->before;             // 핵심연산 2

  free(rpos);
  (plist->numOfData)--;
  return rdata;
}

int LCount(List *plist) { return plist->numOfData; }
```
## CLinkedListMain.c
```cpp
#include <stdio.h>
#include "CLinkedList.h"

int main(void) {

  // 원형 연결 리스트의 생성 및 초기화
  List list;
  int data, i, nodeNum;
  ListInit(&list);

  // 5개의 데이터 저장
  LInsert(&list, 3);
  LInsert(&list, 4);
  LInsert(&list, 5);
  LInsertFront(&list, 2);
  LInsertFront(&list, 1);

  // 리스트에 저장된 데이터를 연속 3회 출력
  if (LFirst(&list, &data)) {
    printf("%d ", data);

    for (i = 0; i < LCount(&list) * 3 - 1; i++)
      if (LNext(&list, &data))
        printf("%d ", data);
  }
  printf("\n");

  // 2의 배수를 찾아서 모두 삭제
  nodeNum = LCount(&list);

  if (nodeNum != 0) {
    LFirst(&list, &data);
    if (data % 2 == 0)
      LRemove(&list);

    for (i = 0; i < nodeNum - 1; i++) {
      LNext(&list, &data);
      if (data % 2 == 0)
        LRemove(&list);
    }
  }

  // 전체 데이터 1회 출력
  if (LFirst(&list, &data)) {
    printf("%d ", data);

    for (i = 0; i < LCount(&list) - 1; i++)
      if (LNext(&list, &data))
        printf("%d ", data);
  }
  return 0;
}
```
---
`gcc -c CLinkedList.c`  
`gcc -c CLinkedListMain.c`   
`gcc CLinkedListMain.o CLinkedList.o`  
를 해야 실행파일이 나옴
## 실행결과
```bash
1 2 3 4 5 1 2 3 4 5 1 2 3 4 5
1 3 5
```
원형 연결 리스트이다.