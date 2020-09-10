---
date: "2020-08-19T12:01:09+08:00"
title: "Knapsack-Problem"
authors: []
categories:
  -
tags:
  - Dynamic Programming
  - Knapsack
draft: true
toc: true
typora-root-url: ../../static
---

> **背包问题**（Knapsack problem）是一种[组合优化](https://zh.wikipedia.org/wiki/组合优化)的[NP完全](https://zh.wikipedia.org/wiki/NP完全)问题。问题可以描述为：给定一组物品，每种物品都有自己的重量和价格，在限定的总重量内，我们如何选择，才能使得物品的总价格最高。问题的名称来源于如何选择最合适的物品放置于给定背包中。
>
> 相似问题经常出现在商业、[组合数学](https://zh.wikipedia.org/wiki/组合数学)，[计算复杂性理论](https://zh.wikipedia.org/wiki/计算复杂性理论)、[密码学](https://zh.wikipedia.org/wiki/密码学)和[应用数学](https://zh.wikipedia.org/wiki/应用数学)等领域中。
>
> 也可以将背包问题描述为[决定性问题](https://zh.wikipedia.org/wiki/決定性問題)，即在总重量不超过*W* 的前提下，总价值是否能达到*V*。
>
> -\- wiki

> 题目来源 LintCode
>
> https://www.lintcode.com/problem/?tag=backpack

###  92. 背包问题

https://www.lintcode.com/problem/backpack/description

二维 DP

```cpp
class Solution {
public:
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A: Given n items with size A[i]
     * @return: The maximum size
     */
    int backPack(int m, vector<int> &A) {
        // write your code here
        vector<vector<int>> dp(A.size() + 1, vector<int>(m + 1, 0));
        
        for (int i = 1; i <= A.size(); i++) {
            for (int j = 1; j <= m; j++) {
                if (j < A[i - 1]) {
                    dp[i][j] = dp[i - 1][j];
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - A[i - 1]] + A[i - 1]);
                }
            }
        }
        
        return dp[A.size()][m];
    }
};
```

优化

```cpp
class Solution {
public:
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A: Given n items with size A[i]
     * @return: The maximum size
     */
    int backPack(int m, vector<int> &A) {
        // write your code here
        vector<int> dp(A.size() + 1);
        
        for (int i = 1; i <= A.size(); i++) {
            for (int j = m; j >= m; j++) {
                if (j < A[i - 1]) {
                    dp[i] = dp[i - 1];
                } else {
                    dp[i] = max(dp[i - 1], dp[i - 1] + A[i - 1]);
                }
            }
        }
        
        return dp[A.size()];
    }
};
```

### 125. 背包问题 II

https://www.lintcode.com/problem/backpack-ii/description

```cpp
class Solution {
public:
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A: Given n items with size A[i]
     * @param V: Given n items with value V[i]
     * @return: The maximum value
     */
    int backPackII(int m, vector<int> &A, vector<int> &V) {
        // write your code here
        int n = A.size();
        
        vector<vector<int>> dp(n + 1, vector<int>(m + 1, 0));
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (j < A[i - 1]) {
                    dp[i][j] = dp[i - 1][j];
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - A[i - 1]] + V[i - 1]);
                }
            }
        }
        
        return dp[n][m];
    }
};
```

