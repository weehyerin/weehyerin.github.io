---
layout: post
title: "연결 리스트"
excerpt_separator:  <!--more-->
categories:
 - Data structure
tags:
  - 연결 리스트
  - 자료구조
  - 리스트
---
# 출처
### 윤성우, 『윤성우의 열혈 자료구조』, 오렌지미디어(2013-03-26), 106-151p
---
<!--more-->
## 배열 리스트랑 다른 점
* 동적으로 메모리 길이 조절 가능

---
## LinkedRead.c
```cpp
#include <stdio.h>
#include <stdlib.h>

typedef struct _node {
  int data;
  struct _node *next;
} Node;

int main(void) {
  Node *head = NULL;
  Node *tail = NULL;
  Node *cur = NULL;

  Node *newNode = NULL;
  int readData;

  // 데이터를 입력받는 과정
  while (1) {
    printf("자연수 입력: ");
    scanf("%d", &readData);
    if (readData < 1)
      break;

    // 노드의 추가과정
    newNode = (Node *)malloc(sizeof(Node));
    newNode->data = readData;
    newNode->next = NULL;

    if (head == NULL)
      head = newNode;
    else
      tail->next = newNode;

    tail = newNode;
  }
  printf("\n");

  // 입력 받은 데이터의 출력과정
  printf("입력 받은 데이터의 전체 출력 \n");
  if (head == NULL)
    printf("저장된 자연수가 존재하지 않습니다. \n");
  else {
    cur = head;
    printf("%d ", cur->data); // 첫 번째 데이터 출력

    while (cur->next != NULL) { // 두 번째 이후의 데이터 출력
      cur = cur->next;
      printf("%d ", cur->data);
    }
  }
  printf("\n\n");

  // 메모리의 해제과정
  if (head == NULL)
    return 0; // 해제할 노드가 존재하지 않는다.
  else {
    Node *delNode = head;
    Node *delNextNode = head->next;

    printf("%d을(를) 삭제합니다. \n", head->data);
    free(delNode); // 첫 번째 노드 삭제

    while (delNextNode != NULL) { // 두 번째 이후 노드 삭제
      delNode = delNextNode;
      delNextNode = delNextNode->next;

      printf("%d을(를) 삭제합니다. \n", delNode->data);
      free(delNode);
    }
  }

  return 0;
}
```
## 실행결과
```bash
자연수 입력: 2
자연수 입력: 4
자연수 입력: 6
자연수 입력: 8
자연수 입력: 0

입력 받은 데이터의 전체 출력
2 4 6 8

2을(를) 삭제합니다.
4을(를) 삭제합니다.
6을(를) 삭제합니다.
8을(를) 삭제합니다.
```
---
**자료 추가는 head에 하는 것이 경제적**

**더미 노드(Dummy Node)가 있으면 코드가 간결**

## DLinkedRead.c
```cpp
#include <stdio.h>
#include <stdlib.h>

typedef struct _node {
  int data;
  struct _node *next;
} Node;

int main(void) {
  Node *head = NULL;
  Node *tail = NULL;
  Node *cur = NULL;

  Node *newNode = NULL;
  int readData;

  head = (Node *)malloc(sizeof(Node)); // 추가된 문장, 더미 노드 추가
  tail = head;                         // 추가된 문장

  // 데이터를 입력받는 과정
  while (1) {
    printf("자연수 입력: ");
    scanf("%d", &readData);
    if (readData < 1)
      break;

    // 노드의 추가과정
    newNode = (Node *)malloc(sizeof(Node));
    newNode->data = readData;
    newNode->next = NULL;

    /*
    if (head == NULL)
      head = newNode;
    else
      tail->next = newNode;
    */
    tail->next = newNode;

    tail = newNode;
  }
  printf("\n");

  // 입력 받은 데이터의 출력과정
  printf("입력 받은 데이터의 전체 출력 \n");
  // if (head == NULL)
  if (head == tail)
    printf("저장된 자연수가 존재하지 않습니다. \n");
  else {
    cur = head;
    // printf("%d ", cur->data); // 첫 번째 데이터 출력

    while (cur->next != NULL) {
      cur = cur->next;
      printf("%d ", cur->data);
    }
  }
  printf("\n\n");

  // 메모리의 해제과정
  // if (head == NULL)
  if (head == tail)
    return 0; // 해제할 노드가 존재하지 않는다.
  else {
    Node *delNode = head;
    Node *delNextNode = head->next;

    // printf("%d을(를) 삭제합니다. \n", head->data);
    // free(delNode); // 첫 번째 노드 삭제

    while (delNextNode != NULL) { // 두 번째 이후 노드 삭제
      delNode = delNextNode;
      delNextNode = delNextNode->next;

      printf("%d을(를) 삭제합니다. \n", delNode->data);
      free(delNode);
    }
  }

  return 0;
}
```
## 실행결과
```bash
자연수 입력: 2
자연수 입력: 4
자연수 입력: 6
자연수 입력: 8
자연수 입력: 0

입력 받은 데이터의 전체 출력
2 4 6 8

2을(를) 삭제합니다.
4을(를) 삭제합니다.
6을(를) 삭제합니다.
8을(를) 삭제합니다.
```
---
### 정렬 기능이 추가된 리스트 자료구조의 ADT
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
 
 void SetSortRule(List * plist, int (*comp)(LData d1, LData d2));
 - 리스트에 정렬의 기준이 되는 함수를 등록
 ```
## 더미노드와 정렬기능이 있는 연결 리스트

## DLinkedList.h
```cpp
#ifndef __D_LINKED_LIST_H__
#define __D_LINKED_LIST_H__

#define TRUE 1
#define FALSE 0

typedef int LData;

typedef struct _node {
  LData data;
  struct _node *next;
} Node;

typedef struct _linkedList {
  Node *head;
  Node *cur;
  Node *before;
  int numOfData;
  int (*comp)(LData d1, LData d2);
} LinkedList;

typedef LinkedList List;

void ListInit(List *plist);
void LInsert(List *plist, LData data);

int LFirst(List *plist, LData *pdata);
int LNext(List *plist, LData *pdata);

LData LRemove(List *plist);
int LCount(List *plist);

void SetSortRule(List *plist, int (*comp)(LData d1, LData d2));

#endif
```
## DLinkedList.c
```cpp
#include <stdio.h>
#include <stdlib.h>
#include "DLinkedList.h"

void ListInit(List *plist) {
  plist->head = (Node *)malloc(sizeof(Node));
  plist->head->next = NULL;
  plist->comp = NULL;
  plist->numOfData = 0;
}

void FInsert(List *plist, LData data) {
  Node *newNode = (Node *)malloc(sizeof(Node));
  newNode->data = data;

  newNode->next = plist->head->next;
  plist->head->next = newNode;

  (plist->numOfData)++;
}

void SInsert(List *plist, LData data) {
  Node *newNode = (Node *)malloc(sizeof(Node)); // 새 노드의 생성
  Node *pred = plist->head; // pred는 더미 노드를 가리킴
  newNode->data = data;     // 새 노드에 데이터 저장

  // 새 노드가 들어갈 위치를 찾기 위한 반복문
  while (pred->next != NULL && plist->comp(data, pred->next->data) != 0)
    pred = pred->next;

  newNode->next = pred->next; // 새 노드의 오른쪽을 연결
  pred->next = newNode;       // 새 노드의 왼쪽을 연결

  (plist->numOfData)++; // 저장된 데이터의 수 하나 증가
}

void LInsert(List *plist, LData data) {
  if (plist->comp == NULL)
    FInsert(plist, data);
  else
    SInsert(plist, data);
}

int LFirst(List *plist, LData *pdata) {
  if (plist->head->next == NULL)
    return FALSE;

  plist->before = plist->head;
  plist->cur = plist->head->next;

  *pdata = plist->cur->data;
  return TRUE;
}

int LNext(List *plist, LData *pdata) {
  if (plist->cur->next == NULL)
    return FALSE;

  plist->before = plist->cur;
  plist->cur = plist->cur->next;

  *pdata = plist->cur->data;
  return TRUE;
}

LData LRemove(List *plist) {
  Node *rpos = plist->cur;
  LData rdata = rpos->data;

  plist->before->next = plist->cur->next;
  plist->cur = plist->before;

  free(rpos);
  (plist->numOfData)--;
  return rdata;
}

int LCount(List *plist) { return plist->numOfData; }

void SetSortRule(List *plist, int (*comp)(LData d1, LData d2)) {
  plist->comp = comp;
}
```
## DLinkedListSortMain.c
```cpp
#include <stdio.h>
#include "DlinkedList.h"

int WhoIsPrecede(int d1, int d2) {
  if (d1 < d2)
    return 0; // d1이 정렬 순서상 앞선다.
  else
    return 1; // d2가 정렬 순서상 앞서거나 같다.
}

int main(void) {
  // 리스트의 생성 및 초기화
  List list;
  int data;
  ListInit(&list);

  SetSortRule(&list, WhoIsPrecede); // 정렬의 기준을 등록한다.
  // 5개의 데이터 저장
  LInsert(&list, 11);
  LInsert(&list, 11);
  LInsert(&list, 22);
  LInsert(&list, 22);
  LInsert(&list, 33);

  // 저장된 데이터 전체 출력
  printf("현재 데이터의 수: %d \n", LCount(&list));

  if (LFirst(&list, &data)) {
    printf("%d ", data);

    while (LNext(&list, &data))
      printf("%d ", data);
  }
  printf("\n\n");

  if (LFirst(&list, &data)) {
    if (data == 22)
      LRemove(&list);

    while (LNext(&list, &data)) {
      if (data == 22)
        LRemove(&list);
    }
  }

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
---
`gcc -c DLinkedList.c`  
`gcc -c DLinkedListSortMain.c`   
`gcc DLinkedListSortMain.o DLinkedList.o`  
를 해야 실행파일이 나옴
## 실행결과
```bash
현재 데이터의 수: 5
11 11 22 22 33

현재 데이터의 수: 3
11 11 33
```
더미노드와 정렬기능이 있는 연결 리스트이다.