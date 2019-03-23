---
layout: post
title: "삼성 알고리즘 - 아기 상어"
excerpt_separator:  <!--more-->
categories:
 - Algorithm
tags:
  - 알고리즘
  - 삼성 소프트웨어 역량테스트
  - 백준
---

<!--more-->

# 출처

## 백준, <https://www.acmicpc.net/problem/16236>

---

## 1. 문제

[아기 상어](https://www.acmicpc.net/problem/16236)

---

## 2. 문제 해결 방법

1. 아기 상어 정보를 담는 클래스 생성
2. 조건에 맞는 물고기들을 담는 클래스 및 백터 생성
3. BFS 수행
4. 빈 칸이거나 물고기와 크기가 같다면 현재 시간에 1을 더하여 큐에 넣기
5. 물고기가 작다면 백터에 물고기 넣기
6. BFS가 끝나면 조건을 따져서 상어 클래스 업데이트
7. 조건을 따질 땐 문제에서 주어진대로 `comp`함수를 짜서 `sort` 수행
8. 3~7을 수행하는데 먹을 물고기 없으면 탈출, 상어 클래스의 현재 시간이 답

---

## 3. 코드

```cpp
// https://www.acmicpc.net/problem/16236
// 아기 상어

#include <algorithm>
#include <iostream>
#include <queue>
#include <vector>

using namespace std;

class Shark {
public:
  int row;
  int column;
  int size;
  int eatNum;
  int curTime;
};

class Fish {
public:
  int row;
  int column;
  int distance;
};
int answer;
int N;
int map[20][20];
int dir[4][2] = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}}; // 상하좌우
Shark shark;
vector<Fish> fishes;

bool comp(Fish &a, Fish &b) {
  if (a.distance < b.distance)
    return true;
  else if (a.distance == b.distance) {
    if (a.row < b.row) {
      return true;
    } else if (a.row == b.row) {
      if (a.column < b.column) {
        return true;
      } else
        return false;
    } else {
      return false;
    }
  } else {
    return false;
  }
}
bool isInside(int row, int column) {
  return (row >= 0 && row < N && column >= 0 && column < N);
}
void solve() {
  int curR, curC, nextR, nextC;
  int sz, eN, cT;
  bool check;
  int time = 0;
  bool visited[20][20];
  queue<Shark> q;

  while (true) {
    check = false;
    sz = shark.size;
    eN = shark.eatNum;
    for (int i = 0; i < N; i++) {
      for (int j = 0; j < N; j++) {
        visited[i][j] = false;
      }
    }
    visited[shark.row][shark.column] = true;
    q.push({shark.row, shark.column, shark.size, shark.eatNum, shark.curTime});
    while (!q.empty()) {
      curR = q.front().row;
      curC = q.front().column;
      cT = q.front().curTime;
      q.pop();
      for (int i = 0; i < 4; i++) {
        nextR = curR + dir[i][0];
        nextC = curC + dir[i][1];
        if (isInside(nextR, nextC) == false)
          continue;
        if (visited[nextR][nextC] == true)
          continue;
        if (map[nextR][nextC] == 0 || map[nextR][nextC] == sz) {
          visited[nextR][nextC] = true;
          q.push({nextR, nextC, sz, eN, cT + 1});
          continue;
        }
        if (map[nextR][nextC] > sz) {
          visited[nextR][nextC] = true;
          continue;
        }
        if (map[nextR][nextC] < sz) {
          check = true;
          visited[nextR][nextC] = true;
          fishes.push_back({nextR, nextC, cT + 1});
        }
      }
    }
    if (check == false) {
      break;
    }
    sort(fishes.begin(), fishes.end(), comp);
    map[fishes.at(0).row][fishes.at(0).column] = 0;
    eN++;
    if (sz == eN) {
      sz++;
      eN = 0;
    }
    shark = {fishes.at(0).row, fishes.at(0).column, sz, eN,
             fishes.at(0).distance};
    fishes.clear();
  }
  answer = shark.curTime;
  return;
}

int main(void) {
  cin >> N;
  answer = 0;
  for (int i = 0; i < N; i++) {
    for (int j = 0; j < N; j++) {
      cin >> map[i][j];
      if (map[i][j] == 9) {
        shark = {i, j, 2, 0, 0};
        map[i][j] = 0;
      }
    }
  }
  solve();
  cout << answer << endl;
}
/*
3
0 0 0
0 0 0
0 9 0

0

3
0 0 1
0 0 0
0 9 0

3

4
4 3 2 1
0 0 0 0
0 0 9 0
1 2 3 4

14

6
5 4 3 2 3 4
4 3 2 3 4 5
3 2 9 5 6 6
2 1 2 3 4 5
3 2 1 6 5 4
6 6 6 6 6 6

60

6
6 0 6 0 6 1
0 0 0 0 0 2
2 3 4 5 6 6
0 0 0 0 0 2
0 2 0 0 0 0
3 9 3 0 0 1

48

6
1 1 1 1 1 1
2 2 6 2 2 3
2 2 5 2 2 3
2 2 2 4 6 3
0 0 0 0 0 6
0 0 0 0 0 9

39
*/
```

## 4. Remark

* 현재 상태를 저장하는 BFS
* 물고기 먹으면 칸을 비워야 하는 것을 잊으면 안 됨
* 물고기 정렬하는 `comp`함수를 잘 짜야 함