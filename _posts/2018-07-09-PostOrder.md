---
layout: post
title: "후위순회 수식표현"
excerpt_separator:  <!--more-->
categories:
 - Algorithm
tags:
  - part 02
  - 삼성 알고리즘
  - 후위순회
  - 알고리즘
  - 삼성 소프트웨어 역량테스트
---
# 출처

## (사)한국소프트웨어기술인협회 소프트웨어역량연구소, 『삼성 소프트웨어 역량테스트』, 와우패스(2018-01-01), 130-143p

---

<!--more-->

## 1. 문제

호엽이가 수식에 대한 후위 순회동작 방식을 공부하고 해당 알고리즘을 응용한 수식 트리를 작성하여 확인하고자 한다.

### (1) 문제 제시

이진트리 후위 순회 알고리즘을 이용하여 수식 트리를 작성하라. [예를 들어 (3 + 4) * 8은 중위 순회 방식이고, 앞 수식을 후위 순회 방식으로 바꾸면 3 4 + 8 *이 된다]

### (2) 제약사항

수식은 사칙연산과 괄호만으로 구성한다. 테스트케이스는 10 이하로 한다.

---

## 2. 입력 및 출력 형태

### (1) 입력

1. 첫째 줄은 수식의 개수
2. 중위 순회로 표현된 수식

### (2) 입력 예

```bash
input.txt
6
21+(3-4)*(8-3)*(3+1)/2
(3+4)*8-(3+7)/2
(11-8)/3+7*2
11+(14-3+2)*2-3+(1+6-5)
(1+2+3+4+5+6)/3+4+5
1*2+3/2+4-4+5+6+6+5-(5*7-5)
```

### (3) 출력

* #x 후위수식

### (4) 출력 예

```bash
output.txt
#1 21 3 4 - 8 3 - * 3 1 + * 2 / +
#2 3 4 + 8 * 3 7 + 2 / -
#3 11 8 - 3 / 7 2 * +
#4 11 14 3 - 2 + 2 * + 3 - 1 6 + 5 - +
#5 1 2 + 3 + 4 + 5 + 6 + 3 / 4 + 5 +
#6 1 2 * 3 2 / + 4 + 4 - 5 + 6 + 6 + 5 + 5 7 * 5 - -
```

---

## 3. 문제 해결 방법

1. 좌측 하단부터 트리를 읽기
2. 하단 좌측의 노드를 읽은 후 상위 레벨의 노드 읽기
3. 같은 레벨의 하위에 노드가 존재할 시 위와 같이 읽기
4. 1~3을 반복 수행하여 Root 노드까지 올라오기

---

## 4. 해결 프로그램

**input.txt는 미리 만들어 놓았다.**

```cpp
#include <stdio.h>
#include <stdlib.h>

int stack[50]; // 수식을 저장할 변수
int stackTop;  // 마지막에 입력한 값을 저장할 변수

void initStack() { // 스택 초기화
  stackTop = -1;
}

int popStack(void) {  // 스택에서 제거하는 함수
  if (stackTop < 0) { // 스택 언더플로우 발생
    printf("\n 스택 언더플로우 ");
    return 1;
  }
  return stack[stackTop--];
}

int pushStack(int item) {   // 스택에 입력하는 함수
  if (stackTop >= 50 - 1) { // 스택 오버플로우 발생
    printf("\n 스택 오버플로우 ");
    return 1;
  }
  stack[++stackTop] = item;
  return item;
}

int getStackTop(void) { // 마지막 입력 값 변환
  if (stackTop < 0)
    return -1;
  else
    return stack[stackTop];
}

int isStackEmpty(void) { // 스택이 비어있는지 확인하는 함수
  return (stackTop < 0);
}

int isOperator(int op) { // 연산자인지를 확인하는 함수
  return op == '+' || op == '-' || op == '*' || op == '/';
}

int precedence(int op) { // 연산자의 우선순위를 판단하는 함수
  switch (op) {
  case '(':
    return 0;
    break;
  case '+':
  case '-':
    return 1;
    break;
  case '*':
  case '/':
    return 2;
    break;
  default:
    return 3;
    break;
  }
}

void postChange(char *dst, char *src) { // 중위순회를 후위순회로 변환
  initStack();
  while (*src) {
    if (*src == '(') { // 괄호가 시작될 때 실행
      pushStack(*src);
      src++;
    } else if (*src == ')') { // 괄호가 끝날 때 실행
      while (getStackTop() != '(') {
        *dst++ = popStack();
        *dst++ = ' ';
      }
      popStack();
      src++;
    } else if (isOperator(*src)) { // 연산자일 때 실행
      while (!isStackEmpty() && precedence(getStackTop()) >= precedence(*src)) {
        *dst++ = popStack();
        *dst++ = ' ';
      }
      pushStack(*src);
      src++;
    } else if (*src >= '0' && *src <= '9') { // 숫자일 때 실행
      do {
        *dst = *src;
        dst++;
        src++;
      } while (*src >= '0' && *src <= '9'); // 숫자일 때 계속 반복 실행
      *dst++ = ' ';
    } else
      src++;
  }
  while (!isStackEmpty()) {
    *dst++ = popStack();
    *dst++ = ' ';
  }
  dst--;
  *dst = 0;
}

int main(void) {
  char postfix[256], infix[256]; // 후위순회 수식, 중위순회 수식
  FILE *in, *out;
  in = fopen("input.txt", "r");
  out = fopen("output.txt", "w");
  char testStr[10]; // 파일입출력
  int testNum;      // 테스트케이스
  int i;            // 반복문 인덱스
  printf("중위순회로 표시된 수식을 후위순회로 변환하는 프로그램\n");
  printf("입력된 중위표기법 수식\n\n");
  testNum = atoi(fgets(testStr, 10, in)); // 파일 입력(수식의 개수)
  for (i = 0; i < testNum; i++) {
    printf("#%d\n", i + 1);
    fprintf(out, "#%d ", i + 1);
    fgets(infix, 256, in); // 파일 입력(수식)
    postChange(postfix, infix); // 중위순회를 후위순회로 변환하는 함수 호출
    printf("중위순회: %s", infix);
    printf("후위순회: %s\n\n", postfix);
    fprintf(out, "%s\n", postfix);
  }
  fclose(in);
  fclose(out);
  return 0;
}
```

---

## 5. 실행 결과

```bash
중위순회로 표시된 수식을 후위순회로 변환하는 프로그램
입력된 중위표기법 수식

#1
중위순회: 21+(3-4)*(8-3)*(3+1)/2
후위순회: 21 3 4 - 8 3 - * 3 1 + * 2 / +

#2
중위순회: (3+4)*8-(3+7)/2
후위순회: 3 4 + 8 * 3 7 + 2 / -

#3
중위순회: (11-8)/3+7*2
후위순회: 11 8 - 3 / 7 2 * +

#4
중위순회: 11+(14-3+2)*2-3+(1+6-5)
후위순회: 11 14 3 - 2 + 2 * + 3 - 1 6 + 5 - +

#5
중위순회: (1+2+3+4+5+6)/3+4+5
후위순회: 1 2 + 3 + 4 + 5 + 6 + 3 / 4 + 5 +

#6
중위순회: 1*2+3/2+4-4+5+6+6+5-(5*7-5)후위순회: 1 2 * 3 2 / + 4 + 4 - 5 + 6 + 6 + 5 + 5 7 * 5 - -
```