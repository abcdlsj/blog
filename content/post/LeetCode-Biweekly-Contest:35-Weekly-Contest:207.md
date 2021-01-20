---
date: "2020-09-20T11:59:38+08:00"
title: "LeetCode-Biweekly-Contest:35-Weekly-Contest:207"
authors: []
categories:
  - learn
tags:
  - Contest
  - LeetCode
draft: true
toc: true
typora-root-url: ../../static
---

> LeetCode 双周赛 35 + 周赛 207 解题报告

<!--more-->

> 本周的我太菜了，双周赛 1 题，周赛 2 题。。。
>
> 虽然都有思路（除第四题），但是细节错误太多，导致思路基本正确，代码结构也正确，但是会有错误

## 双周赛

#### [5505. 所有排列中的最大和](https://leetcode-cn.com/problems/maximum-sum-obtained-of-any-permutation/)

> 直接统计，会爆，需要使用差分（PS. 现在才开始学习差分，可想而知我之前刷题质量多低）

```cpp
class Solution {
public:
    const int e = 1e9 + 7;
    int maxSumRangeQuery(vector<int>& nums, vector<vector<int>>& requests) {
        vector<int> cnt(nums.size() + 1, 0);
        for (auto &e : requests) {
            cnt[e[0]] += 1;
            cnt[e[1] + 1] -= 1;
        }
        for (int i = 1; i < cnt.size(); i++) {
            cnt[i] += cnt[i - 1];
        }
        long long ans = 0;
        sort(cnt.rbegin(), cnt.rend());
        sort(nums.rbegin(), nums.rend());

        for (int i = 0; i < cnt.size() && cnt[i] != 0; i++) {
            ans += cnt[i] * nums[i];
        }
        ans %= e;
        return ans;
    }
};
```

#### [5504. 使数组和能被 P 整除](https://leetcode-cn.com/problems/make-sum-divisible-by-p/)

```cpp
class Solution {
public:
    int minSubarray(vector<int>& nums, int p) {
        long long sum = 0;
        for (auto& e : nums) sum += e;
        if (sum % p == 0)return 0;
        int rest = sum % p, n = nums.size(), ans = INT_MAX;
        sum = 0;
        unordered_map<int, int> map; // 储存前缀和
        map[0] = -1;

        for (int i = 0; i < n; i++) {
            sum = (sum + nums[i]) % p;
            int target = (sum - rest + p) % p;
            if (map.find(target) != map.end()) {
                ans = min(ans, i - map[target]);
            }
            map[sum % p] = i;
        }

        return ans >= n ? -1 : ans;
    }
};
```

## 周赛

#### [5520. 拆分字符串使唯一子字符串的数目最大](https://leetcode-cn.com/problems/split-a-string-into-the-max-number-of-unique-substrings/)

> 暴力解答

```cpp
class Solution {
public:
    int maxUniqueSplit(string s) {
        unordered_map<string, int> map;
        return helper(s, map);
    }
    int helper(string s, unordered_map<string, int>& map) {
        int n = s.size(), ans = 0;
        for (int i = 1; i <= n; i++) {
            string ss = s.substr(0, i);
            if (!map.count(ss)) {
                map[ss]++;
                ans = max(ans, helper(s.substr(i, s.size() - i), map) + 1);
                map.erase(ss);
            }
        }
        return ans;
    }
};
```

#### [5521. 矩阵的最大非负积](https://leetcode-cn.com/problems/maximum-non-negative-product-in-a-matrix/)

> 比赛时候边界没处理好，所以没 AC

```cpp
class Solution {
public:
    const int e = 1e9 + 7;
    int maxProductPath(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<vector<long long>>> dp(m + 1, vector<vector<long long>> (n + 1, vector<long long> (2, 0)));
        dp[0][0][0] = grid[0][0];
        dp[0][0][1] = grid[0][0];
        for (int i = 1; i < m; i++) {
            dp[i][0][0] = grid[i][0] * dp[i - 1][0][0];
            dp[i][0][1] = grid[i][0] * dp[i - 1][0][1];
        }

        for (int j = 1; j < n; j++) {
            dp[0][j][0] = grid[0][j] * dp[0][j - 1][0];
            dp[0][j][1] = grid[0][j] * dp[0][j - 1][1];
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                int x = grid[i][j];
                if (x < 0) {
                    dp[i][j][0] = min(dp[i - 1][j][1], dp[i][j - 1][1]) * x;
                    dp[i][j][1] = max(dp[i - 1][j][0], dp[i][j - 1][0]) * x;
                } else {
                    dp[i][j][0] = max(dp[i - 1][j][0], dp[i][j - 1][0]) * x;
                    dp[i][j][1] = min(dp[i - 1][j][1], dp[i][j - 1][1]) * x;
                }
            }
        }
        if (dp[m - 1][n - 1][0] < 0) return -1;
        return dp[m - 1][n - 1][0] % e;
    }
};
```

