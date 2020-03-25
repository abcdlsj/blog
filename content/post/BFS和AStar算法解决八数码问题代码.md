+++
title = "BFS和A*算法解决八数码问题简单代码"
date = 2019-05-20
lastmod = 2019-05-20T14:51:14+08:00
tags = ["Arithmetic"]
categories = ["learn"]
draft = false
+++

> 多的不写了,具体网上都有,直接代码奉上，代码测试过可以使用，写的不好请见谅。。。

<!--more-->
## BFS

```cpp
#include <iostream>
#include <queue>
#include <vector>
using namespace std;

struct State {
  int s[3][3];
  int index, pre;
  int g;
  void Display() {
    for (int i = 0; i < 3; ++i) {
      for (int j = 0; j < 3; ++j)
        cout << s[i][j] << ' ';
      cout << endl;
    }
    cout << endl;
  }
  //重载<,便于以后比较
  friend bool operator<(State n1, State n2) { return n1.g > n2.g; }
  //比较两个值是否一样
  bool equal(State goal) {
    for (int i = 0; i < 9; ++i)
      if (s[i / 3][i % 3] != goal.s[i / 3][i % 3])
        return false;
    return true;
  }
  // 0的位置 返回x,y坐标 x:行 y:列
  int find_0(int n) {
    for (int i = 0; i < 3; i++)
      for (int j = 0; j < 3; j++)
        if (s[i][j] == 0) {
          if (n == 0)
            return i;
          else
            return j;
        }
  }
};
//初始和目标
State init, goal;
//开闭表
priority_queue<State> openTable;                       //优先队列open表
vector<State> closeTable;                              // vector close表
int direct[4][2] = {{1, 0}, {-1, 0}, {0, -1}, {0, 1}}; //移动上下左右

//启发估值函数
int f(State node) {
  int result = node.g;
  for (int i = 0; i < 9; ++i)
    if (node.s[i / 3][i % 3] != goal.s[i / 3][i % 3])
      result++;
  return result;
}
//判断能否移动
bool isMove(int goalX, int goalY) {
  if (goalX < 0 || goalX >= 3)
    return false;
  if (goalY < 0 || goalY >= 3)
    return false;
  return true;
}
bool isCloseHave(State node) {
  State tmp;
  for (vector<State>::iterator ite = closeTable.begin();
       ite != closeTable.end(); ++ite) {
    tmp = (State)*ite;
    if (tmp.equal(node))
      return true;
  }
  return false;
}
int ha_va(State n, State m) {
  int ss = 0, ee = 0;
  for (int i = 0; i < 9; ++i) {
    for (int j = 0; j < i; j++) {
      if (n.s[j / 3][j % 3] != 0 && n.s[j / 3][j % 3] < n.s[i / 3][i % 3])
        ++ss;
      if (m.s[j / 3][j % 3] != 0 && m.s[j / 3][j % 3] < m.s[i / 3][i % 3])
        ++ee;
    }
  }
  if (!(ss & 1) == (ee & 1)) {
    printf(
        "无解,奇偶性不同,(数前面比它小的数目之和),初始状态:%d\t目标状态:%d\n",
        ss, ee);
    return 0;
  } else
    return 1;
}
State AStar(State start) {
  start.g = 0;
  start.pre = -1;
  openTable.push(start);
  while (!openTable.empty()) {
    //找到最优状态
    State preState =
        openTable.top(); //前面重载<定义相反,g大的反而在后面,所以top是最小值
    openTable.pop();
    //插入close表
    preState.index = closeTable.size();
    closeTable.push_back(preState);
    //找到
    if (preState.equal(goal))
      return preState;
    //
    for (int i = 0; i < 4; ++i) {
      State cur = preState;
      int zeroX = cur.find_0(0);
      int zeroY = cur.find_0(1);
      //准备移动的位置
      int goalX = direct[i][0] + zeroX;
      int goalY = direct[i][1] + zeroY;
      if (isMove(goalX, goalY)) {
        //交换
        int num = cur.s[goalX][goalY];
        cur.s[goalX][goalY] = 0;
        cur.s[zeroX][zeroY] = num;
        //判重
        if (!isCloseHave(cur)) {
          //新状态置入队列
          cur.g = f(cur);
          cur.pre = preState.index;
          openTable.push(cur);
        }
      }
    }
  }
}
State inputSta() {

  State Node;
  for (int i = 0; i < 9; ++i)
    cin >> Node.s[i / 3][i % 3];
  return Node;
}
//回溯展示过程
void DisPlayPath(int index) {
  if (index == -1)
    return;
  State node = closeTable[index];
  DisPlayPath(node.pre);
  printf("->\n");
  node.Display();
}
//主函数
int main() {
  cout << "初始状态:\n";
  init = inputSta();
  //  inital.Display();
  cout << "目标状态:\n";
  //	goal.Display();
  goal = inputSta();
  if (!ha_va(init, goal))
    return 0;
  State result = AStar(init);
  cout << "找到解答,过程如下\n";
  DisPlayPath(result.index);
  return 0;
}
```
## A*

```cpp
#include <iostream>
#include <queue>
#include <vector>
using namespace std;

struct State {
  int s[3][3];
  int index, pre;
  int g;
  void Display() {
    for (int i = 0; i < 3; ++i) {
      for (int j = 0; j < 3; ++j)
        cout << s[i][j] << ' ';
      cout << endl;
    }
    cout << endl;
  }
  //重载<,便于以后比较
  friend bool operator<(State n1, State n2) { return n1.g > n2.g; }
  //比较两个值是否一样
  bool equal(State goal) {
    for (int i = 0; i < 9; ++i)
      if (s[i / 3][i % 3] != goal.s[i / 3][i % 3])
        return false;
    return true;
  }
  // 0的位置 返回x,y坐标 x:行 y:列
  int find_0(int n) {
    for (int i = 0; i < 3; i++)
      for (int j = 0; j < 3; j++)
        if (s[i][j] == 0) {
          if (n == 0)
            return i;
          else
            return j;
        }
  }
};
//初始和目标
State init, goal;
//开闭表
priority_queue<State> openTable;                       //优先队列open表
vector<State> closeTable;                              // vector close表
int direct[4][2] = {{1, 0}, {-1, 0}, {0, -1}, {0, 1}}; //移动上下左右

//启发估值函数
int f(State node) {
  int result = node.g;
  for (int i = 0; i < 9; ++i)
    if (node.s[i / 3][i % 3] != goal.s[i / 3][i % 3])
      result++;
  return result;
}
//判断能否移动
bool isMove(int goalX, int goalY) {
  if (goalX < 0 || goalX >= 3)
    return false;
  if (goalY < 0 || goalY >= 3)
    return false;
  return true;
}
bool isCloseHave(State node) {
  State tmp;
  for (vector<State>::iterator ite = closeTable.begin();
       ite != closeTable.end(); ++ite) {
    tmp = (State)*ite;
    if (tmp.equal(node))
      return true;
  }
  return false;
}
int ha_va(State n, State m) {
  int ss = 0, ee = 0;
  for (int i = 0; i < 9; ++i) {
    for (int j = 0; j < i; j++) {
      if (n.s[j / 3][j % 3] != 0 && n.s[j / 3][j % 3] < n.s[i / 3][i % 3])
        ++ss;
      if (m.s[j / 3][j % 3] != 0 && m.s[j / 3][j % 3] < m.s[i / 3][i % 3])
        ++ee;
    }
  }
  if (!(ss & 1) == (ee & 1)) {
    printf(
        "无解,奇偶性不同,(数前面比它小的数目之和),初始状态:%d\t目标状态:%d\n",
        ss, ee);
    return 0;
  } else
    return 1;
}
State AStar(State start) {
  start.g = 0;
  start.pre = -1;
  openTable.push(start);
  while (!openTable.empty()) {
    //找到最优状态
    State preState =
        openTable.top(); //前面重载<定义相反,g大的反而在后面,所以top是最小值
    openTable.pop();
    //插入close表
    preState.index = closeTable.size();
    closeTable.push_back(preState);
    //找到
    if (preState.equal(goal))
      return preState;
    //
    for (int i = 0; i < 4; ++i) {
      State cur = preState;
      int zeroX = cur.find_0(0);
      int zeroY = cur.find_0(1);
      //准备移动的位置
      int goalX = direct[i][0] + zeroX;
      int goalY = direct[i][1] + zeroY;
      if (isMove(goalX, goalY)) {
        //交换
        int num = cur.s[goalX][goalY];
        cur.s[goalX][goalY] = 0;
        cur.s[zeroX][zeroY] = num;
        //判重
        if (!isCloseHave(cur)) {
          //新状态置入队列
          cur.g = f(cur);
          cur.pre = preState.index;
          openTable.push(cur);
        }
      }
    }
  }
}
State inputSta() {

  State Node;
  for (int i = 0; i < 9; ++i)
    cin >> Node.s[i / 3][i % 3];
  return Node;
}
//回溯展示过程
void DisPlayPath(int index) {
  if (index == -1)
    return;
  State node = closeTable[index];
  DisPlayPath(node.pre);
  printf("->\n");
  node.Display();
}
//主函数
int main() {
  cout << "初始状态:\n";
  init = inputSta();
  //  inital.Display();
  cout << "目标状态:\n";
  //  goal.Display();
  goal = inputSta();
  if (!ha_va(init, goal))
    return 0;
  State result = AStar(init);
  cout << "找到解答,过程如下\n";
  DisPlayPath(result.index);
  return 0;
}
```
