---
layout: post
title: "삼성 알고리즘 - 벽돌 깨기"
excerpt_separator:  <!--more-->
categories:
 - Algorithm
tags:
  - 삼성 알고리즘
  - 알고리즘
  - 삼성 소프트웨어 역량테스트
---

# 출처

## SW Expert Academy, <https://www.swexpertacademy.com/main/main.do>

---

<!--more-->

## 1. 문제

[벽돌 깨기](https://www.swexpertacademy.com/main/code/problem/problemDetail.do)

---

## 2. 문제 해결 방법

1. 중복순열 경우의 수를 만드는 함수 생성 --> `void permutation(int k, int depth)`
2. 하단 좌측의 노드를 읽은 후 상위 레벨의 노드 읽기
3. 같은 레벨의 하위에 노드가 존재할 시 위와 같이 읽기
4. 1~3을 반복 수행하여 Root 노드까지 올라오기

---

## 3. 코드

```cpp
// https://www.swexpertacademy.com/main/code/problem/problemDetail.do
// 벽돌 깨기
#include <iostream>
#include <queue>
#include <deque>

using namespace std;

int T, N, W, H;
int **map = NULL;
int **cpMap = NULL;
int blockMin;

deque<deque<int>> perList;
deque<int> hi;

int dir[4][2] = { {-1, 0}, {1, 0}, {0, -1}, {0, 1} }; // 상하좌우

void permutation(int k, int depth) {
  if (depth == N) {
    perList.push_back(hi);
    return;
  }
  for (int i = 0; i < k; i++) {
    hi.at(depth) = i;
    permutation(k, depth + 1);
  }
}

bool IsInside(int xPos, int yPos) {
  return (xPos >= 0 && xPos < H && yPos >= 0 && yPos < W);
}

void Dfs(int column) {

  // 복사, 체크 배열 생성
  int **copy = new int *[H];
  bool **check = new bool *[H];
  for (int i = 0; i < H; i++) {
    copy[i] = new int[W];
    check[i] = new bool[W];
    for (int j = 0; j < W; j++) {
      copy[i][j] = map[i][j];
      check[i][j] = false;
    }
  }

  queue<pair<int, int>> q;
  int row = 0;
  int curXPos, curYPos;
  int nextXPos, nextYPos;
  while (map[row][column] == 0 && row < (H - 1)) // 행 찾기
    row++;

  // bfs 시작
  q.push(pair<int, int>(row, column));
  check[row][column] = true;
  while (!q.empty()) {
    curXPos = q.front().first;
    curYPos = q.front().second;
    q.pop();
    for (int i = 0; i < 4; i++) {
      nextXPos = curXPos;
      nextYPos = curYPos;
      for (int j = 1; j < copy[curXPos][curYPos]; j++) {
        nextXPos += dir[i][0];
        nextYPos += dir[i][1];
        if (IsInside(nextXPos, nextYPos) == true) {
          if (check[nextXPos][nextYPos] == false &&
              copy[nextXPos][nextYPos] != 0) {
            map[nextXPos][nextYPos] = 0;
            check[nextXPos][nextYPos] = true;
            q.push(pair<int, int>(nextXPos, nextYPos));
          }
        } else
          break;
      }
    }
    map[curXPos][curYPos] = 0;
  }
  // 기나긴 bfs를 지나

  //  블록 정리작업
  for (int n = 0; n < W; n++) {
    for (int m = H - 1; m > 0; m--) {
      for (int o = m - 1; o >= 0; o--) {
        if (map[m][n] == 0 && map[o][n] != 0) {
          map[m][n] = map[o][n];
          map[o][n] = 0;
        }
      }
    }
  }
  for (int k = 0; k < H; k++) {
    delete[] check[k];
    delete[] copy[k];
  }
  delete[] copy;
  delete[] check;
}
int main(void) {
  cin >> T;
  int count = 0;

  for (int i = 1; i <= T; i++) {
    cin >> N >> W >> H;
    hi = deque<int>(N);
    map = new int *[H];
    cpMap = new int *[H];
    blockMin = 999999;
    count = 0;

    for (int j = 0; j < H; j++) {
      map[j] = new int[W];
      cpMap[j] = new int[W];
      for (int k = 0; k < W; k++) {
        cin >> map[j][k];
        cpMap[j][k] = map[j][k];
      }
    }

    // 배열 할당 완료

    permutation(W, 0);

    for (int a = 0; a < perList.size(); a++) {
      count = 0;
      for (int b = 0; b < perList.at(a).size(); b++)
        Dfs(perList.at(a).at(b));
      for (int c = 0; c < H; c++) {
        for (int d = 0; d < W; d++) {
          if (map[c][d] != 0)
            count++;
        }
      }
      if (count == 0) {
        blockMin = count;
        break;
      }
      blockMin = blockMin < count ? blockMin : count;
      for (int q = 0; q < H; q++)
        for (int w = 0; w < W; w++)
          map[q][w] = cpMap[q][w];
    }

    cout << "#" << i << " " << blockMin << endl;
    for (int j = 0; j < H; j++) {
      delete[] map[j];
      delete[] cpMap[j];
    }
    delete[] cpMap;
    delete[] map;
    for (int k = 0; k < perList.size(); k++)
      perList.at(k).clear();
    perList.clear();
  }
  return 0;
}

/*
5
3 10 10
0 0 0 0 0 0 0 0 0 0
1 0 1 0 1 0 0 0 0 0
1 0 3 0 1 1 0 0 0 1
1 1 1 0 1 2 0 0 0 9
1 1 4 0 1 1 0 0 1 1
1 1 4 1 1 1 2 1 1 1
1 1 5 1 1 1 1 2 1 1
1 1 6 1 1 1 1 1 2 1
1 1 1 1 1 1 1 1 1 5
1 1 7 1 1 1 1 1 1 1
2 9 10
0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0
0 1 0 0 0 0 0 0 0
0 1 0 0 0 0 0 0 0
1 1 0 0 1 0 0 0 0
1 1 0 1 1 1 0 1 0
1 1 0 1 1 1 0 1 0
1 1 1 1 1 1 1 1 0
1 1 3 1 6 1 1 1 1
1 1 1 1 1 1 1 1 1
3 6 7
1 1 0 0 0 0
1 1 0 0 1 0
1 1 0 0 4 0
4 1 0 0 1 0
1 5 1 0 1 6
1 2 8 1 1 6
1 1 1 9 2 1
4 4 15
0 0 0 0 
0 0 0 0 
0 0 0 0 
1 0 0 0 
1 0 0 0 
1 0 0 0 
1 0 0 0 
1 0 5 0 
1 1 1 0 
1 1 1 9 
1 1 1 1 
1 6 1 2 
1 1 1 5 
1 1 1 1 
2 1 1 2 
4 12 15
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
9 9 9 9 9 9 9 9 9 9 9 9
*/
```

## 4. Remark