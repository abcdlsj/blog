---
title: "LeetCode Pattern: Dynamic Programming"
date: 2020-05-24T20:49:11+08:00
lastmod: 2020-05-24T20:49:11+08:00
draft: flase
keywords: ["Dynamic Programming"]
description: "LeetCode Pattern dynamic Programming"
tags: ["LeetCode", "Dynamic Programming"]
categories: ["learn"]
author: "abcdlsj"
---
> LeetCode 刷题总结之：动态规划（WIP）

<!--more-->

> **动态规划**（英语：Dynamic programming，简称DP）是一种在[数学](https://zh.wikipedia.org/wiki/数学)、[管理科学](https://zh.wikipedia.org/wiki/管理科学)、[计算机科学](https://zh.wikipedia.org/wiki/计算机科学)、[经济学](https://zh.wikipedia.org/wiki/经济学)和[生物信息学](https://zh.wikipedia.org/wiki/生物信息学)中使用的，通过把原问题分解为相对简单的子问题的方式求解复杂问题的方法。
>
> 动态规划常常适用于有重叠子问题[[1\]](https://zh.wikipedia.org/wiki/动态规划#cite_note-1)和[最优子结构](https://zh.wikipedia.org/w/index.php?title=最优子结构&action=edit&redlink=1)性质的问题，动态规划方法所耗时间往往远少于朴素解法。
>
> 动态规划背后的基本思想非常简单。大致上，若要解一个给定问题，我们需要解其不同部分（即子问题），再根据子问题的解以得出原问题的解。
>
> 通常许多子问题非常相似，为此动态规划法试图仅仅解决每个子问题一次，从而减少计算量：一旦某个给定子问题的解已经算出，则将其[记忆化](https://zh.wikipedia.org/wiki/记忆化)存储，以便下次需要同一个子问题解之时直接查表。这种做法在重复子问题的数目关于输入的规模呈[指数增长](https://zh.wikipedia.org/wiki/指數增長)时特别有用。

内容来自 [zh-wiki]([https://zh.wikipedia.org/wiki/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92)

> 咕咕咕
>
> -\- 2020-06-01

### 单串/双串

#### [300. 最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int maxlen = 0;
        vector<int> dp(nums.size(), 1);
        for (int i = 0; i < nums.size(); i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
            maxlen = max(maxlen, dp[i]);
        }

        return maxlen;
    }
};
```

#### [1143. 最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence/)

> 这道很经典，写这个总结时刚好 190 周周赛日，周赛困难题就是这道题的变体，只需要变一下转移方程（还有一些特殊情况），本来这周这是我唯一 AK 的机会，但是最后那道没写出来，动规还是要多练习，这个总结的部分动规我没写过，趁这个机会多学习一下。

```cpp
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int n = text1.length(), m = text2.length();
        vector<vector<int>> dp(n + 1, vector<int> (m + 1, 0));

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (text1[i - 1] == text2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        return dp[n][m];
    }
};
```

### 经典题

#### [53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int ans = nums[0];
        vector<int> dp(nums);
        
        for (int i = 1; i < nums.size(); i++) {
            dp[i] = max(dp[i], dp[i - 1] + dp[i]);
            ans = max(ans, dp[i]);
        }

        return ans;
    }
};
```

#### [152. 乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)

> 这道题，我开始写的时候很懵逼，因为这道题最重要的一点是，乘积可以是负数，是负数的时候，如果下一个是负数，那么乘出来就是正数；如果下一个是正数，乘出来就是负数；所以需要保存两个值，最大值和最小值，如果当前是负数，那么需要交换最大值和最小值，这样保证了计算后最大值永远是最大值。

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        if (nums.size() == 0) return 0;
        int ans = INT_MIN, maxv = 1, minv = 1;
        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] < 0) swap(maxv, minv);
            maxv = max(maxv * nums[i], nums[i]);
            minv = min(minv * nums[i], nums[i]);

            ans = max(ans, maxv);
        }
        
        return ans;
    }
};
```

### 打家劫舍系列

#### [198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/)

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int ans = 0;
        vector<int> dp(nums.size());

        for (int i = 0; i < nums.size(); i++) {
            dp[i] = max(i >= 1 ? dp[i - 1] : 0, (i >= 2 ? dp[i - 2] : 0) + nums[i]);
            ans = max(ans, dp[i]);
        }

        return ans;
    }
};
```

#### [213. 打家劫舍 II](https://leetcode-cn.com/problems/house-robber-ii/)

> 不难，分两种情况就好了。

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.size() == 0) return 0;
        if (nums.size() == 1) return nums[0];
        
        int ans = 0;
        vector<int> dp(nums.size());

        // 取开头，不取结尾
        for (int i = 0; i < nums.size() - 1; i++) {
            dp[i] = max(i >= 1 ? dp[i - 1] : 0, (i >=2 ? dp[i - 2] : 0) + nums[i]);
            ans = max(ans, dp[i]);
        }

        // 不取开头，取结尾
        dp[0] = 0;
        for (int i = 1; i < nums.size(); i++) {
            dp[i] = max(i >= 1 ? dp[i - 1] : 0, (i >=2 ? dp[i - 2] : 0) + nums[i]);
            ans = max(ans, dp[i]);
        }

        return ans;
    }
};
```

### 买卖股票系列

#### [121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

**这里可以讨论一些关于初始值设置的问题**

> 下面这个是正确的，因为设置 `fprice = prices[0]` ，所以必须单独讨论为空的情况。

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if (prices.size() == 0) return 0; 
        int fprice = prices[0], ans = 0;

        for (int i = 1; i < prices.size(); i++) {
            ans = max(ans, prices[i] - fprice);
            fprice = min(fprice, prices[i]);
        }

        return ans;
    }
};
```

> 如果我们把初始值设置为 `fprice = INT_MAX` ，那么可以去掉开始的判断，这也就是很多初始值设置为 `INT_MAX` 的原因了。

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int fprice = INT_MAX, ans = 0;

        for (int i = 0; i < prices.size(); i++) {
            ans = max(ans, prices[i] - fprice);
            fprice = min(fprice, prices[i]);
        }

        return ans;
    }
};
```

#### [122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

> 使用贪心求差值是比较简单的，动态规划也是简单，但是不是那么好想了，我看了 `weiwei` 的题解才写出来动规解法。。。
>
> 总的来说就是 `cash && hold` ；`cash` 代表持有现金时的 收益 ， `hlod` 代表持有股票时的 收益；

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int prehold = INT_MIN, hold = INT_MIN, cash = 0, precash = 0;
    
        for (int i = 0; i < prices.size(); i++) {
            cash = max(precash, prehold + prices[i]);
            hold = max(prehold, precash - prices[i]);

            prehold = hold;
            precash = cash;
        }

        return cash;
    }
};
```

#### [123. 买卖股票的最佳时机 III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)

> 这道题和上面那道基本一致，但是要求最多买卖两次。

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int hold1 = INT_MIN, hold2 = INT_MIN, release1 = 0, release2 = 0;

        for (int i = 0; i < prices.size(); i++) {
            release1 = max(release1, hold1 + prices[i]);
            hold1 = max(hold1, -prices[i]);
            
            release2 = max(release2, hold2 + prices[i]);
            hold2 = max(hold2, release1 - prices[i]);
        }

        return release2;
    }
};
```

#### [188. 买卖股票的最佳时机 IV](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)

> 这道题最多完成 k 笔交易，这个时候会感觉到可以总结出一种秒杀所有买卖股票问题的转移方程，具体的分析可以看 labuladong 的题解，在参考链接。

```cpp

```





参考链接：

大佬的动规分类

https://leetcode-cn.com/circle/article/NfHhXD/

weiwei哥 的 LeetCode 仓库（强烈推荐）

https://github.com/liweiwei1419/LeetCode-Solution-in-Good-Style

labuladong 大佬的算法小抄（强烈推荐）

https://labuladong.gitbook.io/algo/dong-tai-gui-hua-xi-lie