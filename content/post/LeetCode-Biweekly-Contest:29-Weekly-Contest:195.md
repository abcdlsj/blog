---
date: "2020-06-28T07:47:41+08:00"
title: "LeetCode Biweekly Contest:29 && Weekly Contest:195"
authors: []
categories:
  - learn
tags:
  - LeetCode
draft: false
toc: true
typora-root-url: ../../static
---

> LeetCode 双周赛 29 + 周赛 195 解题报告

> 两周一次的欢乐双周赛时间，双周赛昨天忘记了，过了半个小时去写的。。前几个没什么难度，第四个状压 dp 恕我无能为力（dp 永远的神！），周赛还没开始，感觉难度应该会难一些，争取 3 题！（实际上，每次如果我卡的时间太久。。我就没写了。。。可能还剩一个小时。。。）
>
> -\- Sunday 08:03
>
> 裂开。。。网络炸了。。。
>
> 艹，不写了！！！（会不会太水了。。。）
>
> 算了，这次周赛就放弃了，等明天写了补题解吧
>
> -\- Sunday 10:52

## Biweekly Contest

#### [5434. 删掉一个元素以后全为 1 的最长子数组](https://leetcode-cn.com/problems/longest-subarray-of-1s-after-deleting-one-element/)

> 这是第三题
>
> 找到 0 的位置，然后扩散到两边，算出长度

```cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums) {
        int n = nums.size(), ans = 0;
        if (accumulate(nums.begin(), nums.end(), 0) == n) return n - 1; 

        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] == 1) continue;
            int le = i - 1, ri = i + 1;
            while (le >= 0 && nums[le] == 1) le--;
            while (ri < nums.size() && nums[ri] == 1) ri++;
            ans = max(ans, ri - le - 2);
        }

        return ans;
    }
};
```

## Weekly Contest