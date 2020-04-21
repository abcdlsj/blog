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

这是一道单调队列求解滑动窗口的问题，下面是笔记

> 因为我们必须要`实时更新`窗口最大值，所以必须`保存索引`，这道题是需要得到窗口最大值，对于一个值来说，如果它是最大值，则它是一段范围的最大值，相当于其势力范围，只要超过这个范围，它就没“权”了，超过范围就不能保留，因为涉及到范围，这也就是保存索引的原因，那么我们就可以构造一个单调队列，其`deque.front()`是最大值，后面还有一群`小弟`，当老大`暴毙（超出范围）`的时候，小弟就是老大了，如果 `i - deque.front() + 1 > k` 代表超过`deque.front()`的势力范围了，则弹出`deque.front()`，如果当前值比`deque.back()`大，则可以把`deque.back()`弹出，直到当前值比`deque.back()`小，之所以可以弹出比当前值小的`deque.back()`是因为`deque.back()`没用了，它不会是最大值了，有一个比它靠后，还比它大的数，当`i + 1 >= k`的时候，插入最大值`nums[deque.front()]`。
>
> 拿小弟和大哥来形容还挺形象，老大暴毙（超过范围），小弟上位，新的小弟来篡权（插入新的值）。

$时间复杂度：O(n)\quad空间复杂度：O(n)$

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

## [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-wimthout-repeating-characters/)（ [面试题48. 最长不含重复字符的子字符串](https://leetcode-cn.com/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/)）

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

这是一道比较简单的滑动窗口，使用`map`可以更快的找到上次出现的字母的位置

#### 无优化

$时间复杂度：O(n^2)\quad空间复杂度：O(1)$

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

$时间复杂度：O(n)\quad空间复杂度：O(n)$

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

## [76. 最小覆盖子串](https://leetcode-cn.com/problems/minimum-window-substring/)

### 解决
> 滑动窗口题，用两个`unordered_map`分别记录`left => right`窗口中出现的`有效值`和`T`中出现的字符，`T`中出现的字符才是`有效值`，当`window`中包含了全部`needs`值，就可以缩减`left`，当不符合时，又`right`让窗口符合条件。

$M、N分别是S、T的长度，时间复杂度：O(M+N)$

```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        int left = 0, right = 0, match = 0, start = 0, minlen = INT_MAX;
        unordered_map<char, int> windows, needs;
        for(auto c : t) needs[c]++;
        while(right < s.size()) {
            char cright = s[right];
            if(needs.count(cright)) {
                windows[cright]++;
                if(windows[cright] == needs[cright]) match++;
            }
            while(match == needs.size()) {
                if(right - left + 1 < minlen) {
                    start = left;
                    minlen = right - left + 1;
                }
                char cleft = s[left];
                if(needs.count(cleft)) {
                    windows[cleft]--;
                    if(windows[cleft] < needs[cleft]) match--;
                }
                left++;
            }
            right++;
        }
        return minlen == INT_MAX ? "" : s.substr(start, minlen);
    }
}
```