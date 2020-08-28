---
date: "2020-08-02T13:38:32+08:00"
title: "LeetCode Weekly Contest: 200"
authors: []
categories:
  - learn
tags:
  - Contest
  - LeetCode
draft: false
toc: true
typora-root-url: ../../static
---

> LeetCode Weekly Contest: 200

<!--more-->

#### [5476. 找出数组游戏的赢家](https://leetcode-cn.com/problems/find-the-winner-of-an-array-game/)

> 如果 `k > arr.size() - 1`，直接返回最大值
>
> 剩下的处理，可以模拟
>
> 也可以直接向后找，让两个数中的后一个是较大的值

```cpp
class Solution {
public:
    int getWinner(vector<int>& arr, int k) {
        int n = arr.size(), cnt = 0; 
        if (k > n - 1) return *max_element(arr.begin(), arr.end());

        int i = 0;
        for (; i < arr.size() - 1 && cnt < k; i++) {
            if (arr[i] > arr[i + 1]) {
                arr[i + 1] = arr[i];
                ++cnt;
            } else {
                cnt = 1;
            }
        }

        return arr[i];
    }
};
```

#### [5477. 排布二进制网格的最少交换次数](https://leetcode-cn.com/problems/minimum-swaps-to-arrange-a-binary-grid/)

> 首先算出每行的后缀 0
>
> 然后就是贪心计算了（事后找的题解）
>
> 从当前行，向下找不符合的，然后从不符合的地方向下，找到刚好符合的，然后一个一个的交换到当前行，如果一直到 n 都没找到，那么直接返回 -1

```cpp
class Solution {
public:
    int minSwaps(vector<vector<int>>& grid) {
        int n = grid.size(), ans = 0; 
        vector<int> cnt(n);
        for (int i = 0; i < n; i++) {
            for (int j = n - 1; j >=0 ; j--) {
                if (grid[i][j] == 0) {
                    cnt[i]++;
                } else {
                    break;
                }
            }
        }

        for (int i = 0; i < n - 1; i++) {
            if (cnt[i] >= n - i - 1) continue;
            else {
                int j = i;
                for (; j < n; j++) {
                    if (cnt[j] >= n - i - 1) break;
                }
                if (j == n) return -1;
                for (; j > i; j--) {
                    swap(cnt[j], cnt[j - 1]);
                    ans++;
                }
            }
        }

        return ans;
    }
};
```

