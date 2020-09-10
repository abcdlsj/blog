---
date: "2020-09-07T10:44:09+08:00"
title: "GeeksforGeeks-Dynamic-Programming-20"
authors: []
categories:
  - learn
tags:
  - Dynamic Programming
  - GeeksforGeeks
draft: false
toc: true
typora-root-url: ../../static
---

> https://www.geeksforgeeks.org/top-20-dynamic-programming-interview-questions/

> 缩进格式可能会不统一。。。不想修改了（有些是 `LeetCode` 网页写的，有些是本地写的，我的本地 `C style` 是 `Google`，所以缩进是 `2`）。。。尽量本地 `format` 之后放上来
>
> 直接放的题目链接就是 `GeeksforGeeks` 的链接，`LeetCode` 会在链接的地方加上 `LeetCode`
>
> -\- 09-08 :timer_clock: 21:17
>
> 一些简单的题就不写了，比如 `爬楼梯` 这种
>
> -\- 09-10

### Longest Common Subsequence

> **最长公共子序列**（**LCS**）是一个在一个序列集合中（通常为两个序列）用来查找所有序列中最长[子序列](https://zh.wikipedia.org/wiki/子序列)的问题。这与查找[最长公共子串](https://zh.wikipedia.org/wiki/最长公共子串)的问题不同的地方是：子序列不需要在原序列中占用连续的位置 。最长公共子序列问题是一个经典的[计算机科学](https://zh.wikipedia.org/wiki/计算机科学)问题，也是[数据比较](https://zh.wikipedia.org/w/index.php?title=数据比较&action=edit&redlink=1)程序，比如Diff工具，和[生物信息学](https://zh.wikipedia.org/wiki/生物信息学)应用的基础。它也被广泛地应用在[版本控制](https://zh.wikipedia.org/wiki/版本控制)，比如Git用来调和文件之间的改变。
>
> -\- wiki
>
> [Longest Common Subsequence](https://www.geeksforgeeks.org/dynamic-programming-set-4-longest-common-subsequence/)
>
> 学会这个完全可以写一个 `diff` 工具！！！

```cpp
int lcs(string s, string t, int m, int n) {
  if (m == 0 || n == 0) {
    return 0;
  }
  if (s[m - 1] == t[n - 1]) {
    return 1 + lcs(s, t, m - 1, n - 1);
  }
  return max(lcs(s, t, m, n - 1), lcs(s, t, m - 1, n));
}

// dp
int lcs_dp(string s, string t) {
  int m = s.size(), n = t.size();
  vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

  for (int i = 0; i <= m; i++) {
    for (int j = 0; j <= n; j++) {
      if (i == 0 || j == 0) dp[i][j] = 0;
      else if (s[i - 1] == t[j - 1]) {
        dp[i][j] = dp[i - 1][j - 1] + 1;
      } else {
        dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
      }
    }
  }
  // print_vec(dp);
  return dp[m][n];
}
```

### Longest Increasing Subsequence 

> 在[计算机科学](https://zh.wikipedia.org/wiki/计算机科学)中，**最长递增子序列**（**longest increasing subsequence**）问题是指，在一个给定的数值序列中，找到一个子序列，使得这个子序列元素的数值依次递增，并且这个子序列的长度尽可能地大。最长递增子序列中的元素在原序列中不一定是连续的。许多与数学、[算法](https://zh.wikipedia.org/wiki/算法)、[随机矩阵理论](https://zh.wikipedia.org/w/index.php?title=随机矩阵理论&action=edit&redlink=1)、[表示论](https://zh.wikipedia.org/wiki/表示论)相关的研究都会涉及最长递增子序列。[[1\]](https://zh.wikipedia.org/wiki/最长递增子序列#cite_note-1)解决最长递增子序列问题的算法最低要求O(*n* log *n*)的时间复杂度，这里*n*表示输入序列的规模。[[2\]](https://zh.wikipedia.org/wiki/最长递增子序列#cite_note-schensted-2)在[计算机科学](https://zh.wikipedia.org/wiki/计算机科学)中，**最长递增子序列**（**longest increasing subsequence**）问题是指，在一个给定的数值序列中，找到一个子序列，使得这个子序列元素的数值依次递增，并且这个子序列的长度尽可能地大。最长递增子序列中的元素在原序列中不一定是连续的。许多与数学、[算法](https://zh.wikipedia.org/wiki/算法)、[随机矩阵理论](https://zh.wikipedia.org/w/index.php?title=随机矩阵理论&action=edit&redlink=1)、[表示论](https://zh.wikipedia.org/wiki/表示论)相关的研究都会涉及最长递增子序列。[[1\]](https://zh.wikipedia.org/wiki/最长递增子序列#cite_note-1)解决最长递增子序列问题的算法最低要求O(*n* log *n*)的时间复杂度，这里*n*表示输入序列的规模。[[2\]](https://zh.wikipedia.org/wiki/最长递增子序列#cite_note-schensted-2)
>
> -\- wiki

[Longest Increasing Subsequence](https://www.geeksforgeeks.org/dynamic-programming-set-3-longest-increasing-subsequence/)

```cpp
class Solution {
public:
  int lengthOfLIS(vector<int> &nums) {
    int n = nums.size(), ans = 0;
    vector<int> dp(n, 1);

    for (int i = 0; i < n; i++) {
      for (int j = 0; j < i; j++) {
        if (nums[i] > nums[j]) {
          dp[i] = max(dp[i], dp[j] + 1);
        }
      }
      ans = max(dp[i], ans);
    }

    return ans;
  }
};
```

```cpp
class Solution {
public:
  int lengthOfLIS(vector<int> &nums) {
    vector<int> dp;

    for (auto &ele : nums) {
      auto find = lower_bound(dp.begin(), dp.end(), ele);
      if (find == dp.end()) {
        dp.push_back(ele);
      } else {
        *find = ele;
      }
    }

    return dp.size();
  }
};
```

### Edit Distance

> 很多 DP 可以先写递归方法，会理解深刻（个人拙见）

[Edit Distance](https://www.geeksforgeeks.org/dynamic-programming-set-5-edit-distance/)

这样写过不了的

```cpp
class Solution {
public:
  int min3(int a, int b, int c) { return min(min(a, b), c); }
  int minDistance(string word1, string word2) {
    int m = word1.size(), n = word2.size();

    return helper(word1.c_str(), word2.c_str(), m, n);
  }

  int helper(const string &word1, const string &word2, int i, int j) {
    if (i == 0 || j == 0)
      return max(i, j);
    if (word1[i - 1] == word2[j - 1]) {
      return helper(word1, word2, i - 1, j - 1);
    }

    return 1 + min3(helper(word1, word2, i - 1, j - 1),
                    helper(word1, word2, i - 1, j),
                    helper(word1, word2, i, j - 1));
  }
};
```

使用 map 缓存之后，能够通过

这个地方 `e` 的取值比较特殊，因为这道题没有太长的字符串

```cpp
class Solution {
public:
  int e = 1e4 + 1;
  unordered_map<int, int> map;
  int min3(int a, int b, int c) { return min(min(a, b), c); }
  int minDistance(string word1, string word2) {
    int m = word1.size(), n = word2.size();

    return helper(word1.c_str(), word2.c_str(), m, n);
  }

  int helper(const string &word1, const string &word2, int i, int j) {
    if (map[i * e + j] != 0) {
      return map[i * e + j];
    }

    if (i == 0 || j == 0)
      return max(i, j);

    if (word1[i - 1] == word2[j - 1]) {
      map[i * e + j] = helper(word1, word2, i - 1, j - 1);
    } else {
      map[i * e + j] = 1 + min3(helper(word1, word2, i - 1, j - 1),
                                helper(word1, word2, i - 1, j),
                                helper(word1, word2, i, j - 1));
    }

    return map[i * e + j];
  }
};
```

DP

```cpp
class Solution {
public:
  int minDistance(string word1, string word2) {
    int m = word1.size(), n = word2.size();
    if (m == 0 || n == 0) return max(m, n);
    vector<vector<int>> dp(m + 1, vector<int>(n + 1));

    for (int i = 0; i <= m; i++) {
      dp[i][0] = i;
    }

    for (int i = 0; i <= n; i++) {
      dp[0][i] = i;
    }

    for (int i = 1; i <= m; i++) {
      for (int j = 1; j <= n; j++) {
        if (word1[i - 1] == word2[j - 1]) {
          dp[i][j] = dp[i - 1][j - 1];
        } else {
          dp[i][j] = min(min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
        }
      }
    }

    return dp[m][n];
  }
};
```

### Minimum Partition

> 划分两个集合让两个集合差最小
>
> 网上查了一下，发现 `LeetCode 416` 和这道题很像（`416` 是被分在之后的 `子集和` 问题的，所以这个地方不放题解了）

[Minimum Partition](https://www.geeksforgeeks.org/partition-a-set-into-two-subsets-such-that-the-difference-of-subset-sums-is-minimum/)

递归解法

```cpp
int helper(vector<int> &arr, int idx, int cur, int sumall) {
  if (idx == 0) {
    return abs((sumall - cur) - cur);
  }

  return min(helper(arr, idx - 1, cur + arr[idx - 1], sumall),
             helper(arr, idx - 1, cur, sumall));
}

int mininum_partition(vector<int> &arr) {
  return helper(arr, arr.size(), 0, accumulate(arr.begin(), arr.end(), 0));
}
```

DP

```cpp
int mininum_partition_dp(vector<int> &arr) {
  int sum = accumulate(arr.begin(), arr.end(), 0);
  vector<vector<bool>> dp(arr.size() + 1, vector<bool>(sum + 1));
  for (int i = 0; i <= arr.size(); i++) {
    dp[i][0] = true;
  }
  for (int j = 0; j <= sum; j++) {
    dp[0][j] = false;
  }

  for (int i = 1; i <= arr.size(); i++) {
    for (int j = 1; j <= sum; j++) {
      if (j < arr[i - 1]) {
        dp[i][j] = dp[i - 1][j];
      } else {
        dp[i][j] = dp[i - 1][j] || dp[i - 1][j - arr[i - 1]];
      }
    }
  }

  int diff = sum;
  for (int j = sum / 2; j >= 0; j--) {
    if (dp[arr.size()][j]) {
      diff = sum - 2 * j;
      return diff;
    }
  }

  return sum;
}
```

### Longest Path In Matrix

[Longest Path In Matrix](https://www.geeksforgeeks.org/find-the-longest-path-in-a-matrix-with-given-constraints/)

#### [329. 矩阵中的最长递增路径](https://leetcode-cn.com/problems/longest-increasing-path-in-a-matrix/)

```cpp

```



### subset Sum Problem

> **子集和问题**（英语：**Subset sum problem**），又称**子集合加总问题**，是[计算复杂度理论](https://zh.wikipedia.org/wiki/计算复杂度理论)和[密码学](https://zh.wikipedia.org/wiki/密碼學)中一个很重要的问题。问题可以描述为：给一个整数[集合](https://zh.wikipedia.org/wiki/集合)，问是否存在某个非空[子集](https://zh.wikipedia.org/wiki/子集)，使得子集内中的数字和为某个特定数值。例：给定集合{−7, −3, −2, 5, 8}，是否存在子集和为0的集合?答案是YES，因为[子集](https://zh.wikipedia.org/wiki/子集){−3, −2, 5}的数字和是0。这个问题是[NP完全](https://zh.wikipedia.org/wiki/NP完全)问题，且或许是最容易描述的NP完全问题。
>
> 一个等价的问题是：给一个整数[集合](https://zh.wikipedia.org/wiki/集合)和另一个整数*s*，问是否存在某个非空[子集](https://zh.wikipedia.org/wiki/子集)，使得子集中的数字和为*s*。子集合加总问题可以想成是[背包问题](https://zh.wikipedia.org/wiki/背包問題)的一个特例。
>
> -\- wiki

[Subset Sum Problem](https://www.geeksforgeeks.org/dynamic-programming-subset-sum-problem/)

#### [416. 分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/)

> 解法：
>
> 容量为 sum / 2 的背包问题

```cpp
class Solution {
public:
  bool canPartition(vector<int> &nums) {
    int n = nums.size(), sum = accumulate(nums.begin(), nums.end(), 0);
    if (sum % 2)
      return false;
    sum = sum / 2;
    vector<vector<int>> dp(n + 1, vector<int>(sum + 1));
    for (int i = 0; i <= n; i++) {
      dp[i][0] = true;
    }

    for (int i = 1; i <= n; i++) {
      for (int j = 1; j <= sum; j++) {
        if (j < nums[i - 1]) {
          dp[i][j] = dp[i - 1][j];
        } else {
          dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i - 1]];
        }
      }
    }

    return dp[n][sum];
  }
};
```

