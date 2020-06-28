---
date: "2020-06-21T20:42:22+08:00"
title: "LeetCode Pattern: Other Questions (WIP)"
authors: []
categories:
  - learn
tags:
  - LeetCode
draft: false
toc: true
typora-root-url: ../../static
---

> LeetCode 刷题总结之瞎几把总结

<!--more-->

>  LeetCode 很多是有套路模板的题，但是也有很多没什么套路，反而单纯的是对技巧以及对数据结构和算法的理解

> 开始重刷 LeetCode 之路了，发现很多题之前看了题解去写的，现在去写还是会有点懵逼（其实好很多了，写过的题很多都可以直接看到问题的本质，最后还是熟练度的问题）
>
> 摘取一些无法分类，但是很有意思的题
>
> -\- 06-21 :timer_clock: 20:46

## 位运算

#### [136. 只出现一次的数字](https://leetcode-cn.com/problems/single-number/)

> 应该是去年炒起来的一道题，我想起来我去年第一次写的时候，刚开始就想到了异或做法。。。（因为当时在写 CS:APP Lab1，要是当时坚持好好刷 CS:APP 就好了，我现在都没怎么写 Lab）

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;

        for (auto num : nums) {
            ans ^= num;
        }

        return ans;
    }
};
```



#### [137. 只出现一次的数字 II](https://leetcode-cn.com/problems/single-number-ii/)

> 构造一个使 x 出现三次时为 0 的式子
>
> 0 ^ x = x, x ^ x = 0
> x & ~x = 0, x & ~0 =x

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int a = 0, b = 0;
        for(auto x : nums){
            a = (a ^ x) & ~b;
            b = (b ^ x) & ~a;
        }
        return a;
    }
};
```



## other

#### [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        int st = 0, ed = 0;

        for (int i = 0; i < s.size(); i++) {
            int l1 = expand(s, i, i);
            int l2 = expand(s, i, i + 1);
            int len = max(l1, l2);
            if (len > (ed - st)) {
                st = i - (len - 1) / 2;
                ed = i + len / 2;
            }
        }

        return s.substr(st, ed - st + 1);
    }

    int expand(string s, int tle, int tri) {
        for (; tle >= 0 && tri < s.size() && s[tle] == s[tri]; tle--, tri++)
            ;
        return tri - tle - 1;
    }
}; 
```

#### [67. 二进制求和](https://leetcode-cn.com/problems/add-binary/)

```cpp
class Solution {
public:
    string addBinary(string a, string b) {
        string s;
        int i = a.size() - 1, j = b.size() - 1, c = 0;
        while (i >= 0 || j >= 0 || c == 1) {
            c += i >= 0 ? a[i--] - '0' : 0;
            c += j >= 0 ? b[j--] - '0' : 0;
            s.push_back((c & 1) + '0');
            c >>= 1;
        }

        reverse(s.begin(), s.end());
        return s;
    }
};
```

