---
date: "2020-09-16T14:49:46+08:00"
title: "LeetCode-Pattern: Union Find"
authors: []
categories:
  - learn
tags:
  - LeetCode
  - Union Find
draft: false
toc: true
typora-root-url: ../../static
---

> LeetCode 刷题总结之：并查集

<!--more-->

> 最近这两天写了点并查集的题，之前还是太菜了，一直没去学习这种“大杀器”

## 模板

> 比较简单舒服的模板，初始化在主函数进行就可以了

```cpp
int count;
vector<int> father;
vector<int> ranks;
int findset(int x) {
    if (x != father[x]) {
        father[x] = findset(father[x]);
    }
    return father[x];
}
void unionset(int a, int b) {
    int x = findset(a), y = findset(b);
    if (x == y) return;
    if (ranks[x] > ranks[y]) {
        swap(x, y);
    }
    father[x] = y;
    ranks[y] += ranks[x];
    count--;
}
```

## 基础

#### [547. 朋友圈](https://leetcode-cn.com/problems/friend-circles/)

```cpp
class Solution {
public:
    int count;
    vector<int> father;
    vector<int> ranks;
    int findset(int x) {
        if (x != father[x]) {
            father[x] = findset(father[x]);
        }
        return father[x];
    }
    void unionset(int a, int b) {
        int x = findset(a), y = findset(b);
        if (x == y) return;
        if (ranks[x] > ranks[y]) {
            swap(x, y);
        }
        father[x] = y;
        ranks[y] += ranks[x];
        count--;
    }   
    int findCircleNum(vector<vector<int>>& M) {
        int n = M.size();
        if (n == 0) return 0;
        count = n;
        father.resize(n, -1);
        ranks.resize(n, 1);
        for (int i = 0; i < n; i++) father[i] = i;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (M[i][j] == 1) {
                    unionset(i, j);
                }
            }
        }

        return count;
    }
};
```

#### [990. 等式方程的可满足性](https://leetcode-cn.com/problems/satisfiability-of-equality-equations/)

```cpp
class Solution {
public:
    vector<int> fa;
    vector<long long> rank;
    int find(int x) {
        if (x != fa[x]) {
            fa[x] = find(fa[x]);
        }
        return fa[x];
    }
    void unionset(int a, int b) {
        int x = find(a), y = find(b);
        if (rank[x] > rank[y]) {
            swap(x, y);
        } 
        fa[x] = y;
        rank[y] += rank[x];
    }
    bool equationsPossible(vector<string>& equations) {
        fa.resize(27, -1);
        rank.resize(27, 1);
        set<pair<int, int>> set;
        for (int i = 0; i < 27; i++) fa[i] = i;
        for (auto& e : equations) {
            int a = e[0] - 'a', b = e[3] - 'a';
            if (e[1] == '=') {
                unionset(a, b);
            }
        }
        for (auto& e : equations) {
            int a = e[0] - 'a', b = e[3] - 'a';
            if (e[1] == '!') {
                if (find(a) == find(b)) {
                    return false;
                }
            }
        }
        return true;
    }
};
```

#### [128. 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

```cpp
class Solution {
public:
    unordered_map<int, int> fa;
    int find(int x) {
        if (fa.count(x)) return fa[x] = find(fa[x]);
        else return x;
    }
    int longestConsecutive(vector<int>& nums) {
        int ans = 0;
        for (auto& e : nums) fa[e] = e + 1;
        for (auto& e : nums) {
            int x = find(e + 1);
            ans = max(ans, x - e);
        }

        return ans;
    }
};
```

#### [200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

```cpp
class Solution {
public:
    int count = 0;
    vector<int> father;
    vector<int> ranks;
    int findset(int x) {
        if (x != father[x]) {
            father[x] = findset(father[x]);
        }
        return father[x];
    }
    void unionset(int a, int b) {
        int x = findset(a), y = findset(b);
        if (x == y) return;
        if (ranks[x] > ranks[y]) {
            swap(x, y);
        }
        father[x] = y;
        ranks[y] += ranks[x];
        count--;
    }   
    vector<int> dx = {1, 0, -1, 0}, dy = {0, 1, 0, -1};
    int numIslands(vector<vector<char>>& grid) {
        if (grid.size() == 0 || grid[0].size() == 0) return 0;
        int m = grid.size(), n = grid[0].size();
        father.resize(m * n, -1);
        ranks.resize(m * n, 1);
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') count++;
                father[i * n + j] = i * n + j;
            }
        }
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    for (int k = 0; k < 4; k++) {
                        int ii = dx[k] + i, jj = dy[k] + j;
                        if (ii < 0 || ii >= m || jj < 0 || jj >= n || grid[ii][jj] == '0') continue;
                        unionset(i * n + j, ii * n + jj);
                    }
                }
            }
        }
        return count;
    }
};
```

#### [130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/)

> 这道题需要设置一下 `isborder = n * m`，如果这个点是在边界上（`if (i == 0 || i == m - 1 || j == 0 || j == n - 1)`），我们就合并 `unionset(i * n + j, isborder);`
>
> 最后把所有没和边界合并的点置为 `X`

```cpp
class Solution {
public:
    vector<int> father;
    vector<int> ranks;
    int findset(int x) {
        if (x != father[x]) {
            father[x] = findset(father[x]);
        }
        return father[x];
    }
    void unionset(int a, int b) {
        int x = findset(a), y = findset(b);
        if (x == y) return;
        if (ranks[x] > ranks[y]) {
            swap(x, y);
        }
        father[x] = y;
        ranks[y] += ranks[x];
    }   
    vector<int> dx = {1, 0, -1, 0}, dy = {0, 1, 0, -1};
    void solve(vector<vector<char>>& board) {
        if (board.size() == 0 || board[0].size() == 0) return ; 
        int m = board.size(), n = board[0].size(), isborder = n * m;
        father.resize(m * n + 1, -1);
        ranks.resize(m * n + 1, 1);
        for (int i = 0; i < m * n; i++) father[i] = i; 
        father[n * m] = n * m;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 'X') continue;
                if (i == 0 || i == m - 1 || j == 0 || j == n - 1) {
                    unionset(i * n + j, isborder); 
                    continue;
                }
                for (int k = 0; k < 4; k++) {
                    int ii = i + dx[k], jj = j + dy[k];
                    if (ii < 0 || ii >= m || jj < 0 || jj >= n || board[ii][jj] == 'X') continue;
                    unionset(i * n + j, ii * n + jj);
                }
            }
        }

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (findset(i * n + j) != findset(isborder)) {
                    board[i][j] = 'X';
                }
            }
        }

        return ;
    }
};
```

#### [1319. 连通网络的操作次数](https://leetcode-cn.com/problems/number-of-operations-to-make-network-connected/)



#### [684. 冗余连接](https://leetcode-cn.com/problems/redundant-connection/)

```cpp
class Solution {
public:
    vector<int> fa;
    vector<int> rank;
    int findset(int x) {
        if (x != fa[x]) {
            fa[x] = findset(fa[x]);
        }
        return fa[x];
    }
    void unionset(int a, int b) {
        int x = findset(a), y = findset(b);
        if (rank[x] > rank[y]) {
            swap(x, y);
        } 
        fa[x] = y;
        rank[y] += rank[x];
    }
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        int n = edges.size();
        fa.resize(n + 1, -1);
        rank.resize(n + 1, 1);
        for (int i = 0; i <= n; i++) fa[i] = i;
        for (auto& e : edges) {
            int a = e[0], b = e[1];
            if (findset(a) != findset(b)) {
                unionset(a, b);
            } else {
                return {a, b};
            }
        }
        return {-1, -1};
    }
};
```

#### [685. 冗余连接 II](https://leetcode-cn.com/problems/redundant-connection-ii/)

> 有向图



## 带权值

#### [399. 除法求值](https://leetcode-cn.com/problems/evaluate-division/)

#### [721. 账户合并](https://leetcode-cn.com/problems/accounts-merge/)

#### [765. 情侣牵手](https://leetcode-cn.com/problems/couples-holding-hands/)

#### [952. 按公因数计算最大组件大小](https://leetcode-cn.com/problems/largest-component-size-by-common-factor/)