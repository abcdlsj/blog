---
title: "LeetCode Pattern:Sliding Window"
date: 2020-04-21T10:07:14+08:00
lastmod: 2020-04-21T10:07:14+08:00
draft: false
keywords: ["Sliding Window"]
description: "LeetCode Pattern Sliding Window"
tags: ["LeetCode"]
categories: ["learn"]
author: "abcdlsj"
---
> Leecode 刷题总结之：滑动窗口
<!--more-->

> LeetCode 刷了 200+ 了，题刷的很少（easy 居多），还有一些算法没涉及到，很多题可能当时思路非常清楚，但是后面再遇到的时候都有点忘了，特别是一些很巧妙的方法（我还是忘不了扔鸡蛋~~~），我之前刷题都是把比较有意思的题放在了笔记里，然后上自己的一些思路，现在按照专题整理一下，顺便再思考一遍，顺便吐槽，实习太难找了（太菜），我还是继续看基础吧。。。

题目的内容来自我的 `leetcode` 爬虫，欢迎 `star`

[GitHub 地址](https://github.com/abcdlsj/Leetcode)

## [面试题59 - I. 滑动窗口的最大值（easy）](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)

### 题目内容

<p>给定一个数组 <code>nums</code> 和滑动窗口的大小 <code>k</code>，请找出所有滑动窗口里的最大值。</p>

<p><strong>示例:</strong></p>

<pre><strong>输入:</strong> <em>nums</em> = <code>[1,3,-1,-3,5,3,6,7]</code>, 和 <em>k</em> = 3
<strong>输出: </strong><code>[3,3,5,5,6,7]
<strong>解释:
</strong></code>
  滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7</pre>



<p><strong>提示：</strong></p>

<p>你可以假设 <em>k </em>总是有效的，在输入数组不为空的情况下，1 ≤ k ≤ 输入数组的大小。</p>

<p>注意：本题与主站 239 题相同：<a href="https://leetcode-cn.com/problems/sliding-window-maximum/">https://leetcode-cn.com/problems/sliding-window-maximum/</a></p>

### 解答

这是一道单调队列求解滑动窗口的问题

```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        deque<int> deque; vector<int> res;
        if(nums.size() == 0) return res;
        for(int i = 0; i < nums.size(); i++) {
            if(!deque.empty() && i - deque.front() + 1 > k) {
                deque.pop_front();
            }
            while(!deque.empty() && nums[i] > nums[deque.back()]) {
                deque.pop_back();
            }
            deque.push_back(i);
            if(i + 1 >= k) {
                res.push_back(nums[deque.front()]);
            }
        }
        return res;
    }
};

```

##  [3. 无重复字符的最长子串（medium）](https://leetcode-cn.com/problems/longest-substring-wimthout-repeating-characters/)

### 题目内容

<p>给定一个字符串，请你找出其中不含有重复字符的 <strong>最长子串 </strong>的长度。</p>

<p><strong>示例 1:</strong></p>

<pre><strong>输入: </strong>"abcabcbb"
<strong>输出: </strong>3
<strong>解释:</strong> 因为无重复字符的最长子串是 <code>"abc"，所以其</code>长度为 3。
</pre>

<p><strong>示例 2:</strong></p>

<pre><strong>输入: </strong>"bbbbb"
<strong>输出: </strong>1
<strong>解释: </strong>因为无重复字符的最长子串是 <code>"b"</code>，所以其长度为 1。
</pre>

<p><strong>示例 3:</strong></p>

<pre><strong>输入: </strong>"pwwkew"
<strong>输出: </strong>3
<strong>解释: </strong>因为无重复字符的最长子串是 <code>"wke"</code>，所以其长度为 3。
     请注意，你的答案必须是 <strong>子串 </strong>的长度，<code>"pwke"</code> 是一个<em>子序列，</em>不是子串。
</pre>

### 解答

#### 无优化

$$
时间复杂度：O(n^2)\quad
空间复杂度：O(1)
$$

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int start = 0, maxsize = 0;
        for(int right = 0; right < s.size(); right ++) {
            for(int left = start; left < right; left++) {
                if(s[left] == s[right]) {
                    start = left + 1;
                    break;
                }
            }
            maxsize = max(maxsize, right - start + 1);
        }
        return maxsize;
    }
};
```

#### map 优化

$$
时间复杂度：O(n)\quad
空间复杂度：O(n)
$$

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        map<char, int> map;
        int start = 0, ans = 0;
        for(int i = 0; i < s.size(); i++){
            if(map.find(s[i]) != map.end()) {
                start = max(map[s[i]] + 1, start);
            }
            ans = max(ans, i - start + 1);
            map[s[i]] = i;
        }
        return ans;
    }
};

```

## [面试题59 - II. 队列的最大值](https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof/)
