---
date: "2020-06-28T07:47:41+08:00"
title: "LeetCode Biweekly Contest:29 && Weekly Contest:195"
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

> LeetCode 双周赛 29 + 周赛 195 解题报告

<!--more-->

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
>
> 不用明天了。。。
>
> -\- Sunday 15:42

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

> 第二题
>
> 大佬的写法和我差距好大。。。思路一样，但是我的代码就是不够简洁，总是重复判断就像下面这道题，我用的 map 存储，导致复杂了，而且最后判断是否能够成对，我是一个一个判断。。。实际上没必要，观之大佬的实现十分简洁，对于负数的处理也很有经验（我也这样处理的。。。只是是单独拿出来的，写出来不够简洁），而且开的数组，最后遍历也变得很简单，最后的判断 `mod[0] % 2 == 0`，这种情况我没注意到，这也是我没 ac 的原因

 ```cpp
class Solution {
public:
    bool canArrange(vector<int>& arr, int k) {
        vector<int> mod(k);

        for (auto a : arr) {
            mod[(k + a % k) % k]++;
        }

        for (int i = 1; i < mod.size(); i++) {
            if (mod[i] != mod[k - i]) return false;
        }

        return mod[0] % 2 == 0;
    }
};
 ```

> 第三题
>
> 看的大佬题解，与大佬实现基本一致（抄）
>
> 排序之后，`le` 是最小值，`ri` 是最大值
>
> `le ~ ri` 之间序列有 `pow(2, ri - le)` 个子序列
>
> 我现在才知道不用写快速幂（实际上让我现在写快速幂我也写不出来，太久没写了），直接上数组就好了。。。

```cpp
class Solution {
public:
    int mod = 1000000007;
    int numSubseq(vector<int>& nums, int target) {
        int n = nums.size();
        sort(nums.begin(), nums.end());
        vector<int> mul2(n);
        mul2[0] = 1;
        for (int i = 1; i < n; i++) {
            mul2[i] = mul2[i - 1] << 1;
            if (mul2[i] >= mod) {
                mul2[i] -= mod;
            }
        }

        int ri = n - 1, ans = 0;

        for (int le = 0; le < n; le++) {
            while (le <= ri && nums[le] + nums[ri] > target) {
                --ri;
            }

            if (le > ri) break;

            ans += mul2[ri - le];
            if (ans >= mod) {
                ans -= mod;
            }
        }

        return ans;
    }
};
```

