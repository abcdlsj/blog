---
title: "LeetCode Pattern: BackTracking"
date: 2020-04-23T21:41:59+08:00
lastmod: 2020-04-23T21:41:59+08:00
draft: flase
keywords: ["BackTracking"]
description: "LeetCode Pattern BackTracking"
tags: ["LeetCode", "BackTracking"]
categories: ["learn"]
author: "abcdlsj"
---
> LeetCode 刷题总结之：回溯

<!--more-->

> 其实按理来说回溯法也只是一种基本方法（穷举法），不应该拿一个专题，但是回溯+剪枝真的非常漂亮，而且代码还是很有套路的。

## 39. 组合总和
[39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)

### 题目内容

<p>给定一个<strong>无重复元素</strong>的数组 <code>candidates</code> 和一个目标数 <code>target</code> ，找出 <code>candidates</code> 中所有可以使数字和为 <code>target</code> 的组合。</p>

<p><code>candidates</code> 中的数字可以无限制重复被选取。</p>

<p><strong>说明：</strong></p>

<ul>
	<li>所有数字（包括 <code>target</code>）都是正整数。</li>
	<li>解集不能包含重复的组合。 </li>
</ul>

<p><strong>示例 1:</strong></p>

<pre><strong>输入:</strong> candidates = <code>[2,3,6,7], </code>target = <code>7</code>,
<strong>所求解集为:</strong>
[
  [7],
  [2,2,3]
]
</pre>

<p><strong>示例 2:</strong></p>

<pre><strong>输入:</strong> candidates = [2,3,5]<code>, </code>target = 8,
<strong>所求解集为:</strong>
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]</pre>

### 解法

> 题目说`candidates`中的数字可以无限制重复被选取，但是不能包含重复的组合，怎么才能不重复组合呢，我们就需要一个`start`来表明取值的位置，每次取了值让`start + 1`再递归，但是这样就必须数组是有序的，所以我们要先排序，答案也就出来了。

```cpp
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end());
        vector<vector<int>> res;
        vector<int> path;
        backtrack(res, path, candidates, target, 0, 0);
        return res;
    }
    void backtrack(vector<vector<int>> &res, vector<int> &path, vector<int> &candidates, int target, int start, int sum) {
        //降低一下复杂度，剪枝
        if(sum > target) return;
        if(sum == target) res.push_back(path);
        for(int i = start; i < candidates.size(); i++) {
            path.push_back(candidates[i]);
            backtrack(res, path, candidates, target, i, sum + candidates[i]);
            path.pop_back();
        }
    }
 };
```
## 40. 组合总和 II
[40. 组合总和 II](https://leetcode-cn.com/problems/combination-sum-ii/)

### 题目内容
<p>给定一个数组 <code>candidates</code> 和一个目标数 <code>target</code> ，找出 <code>candidates</code> 中所有可以使数字和为 <code>target</code> 的组合。</p>

<p><code>candidates</code> 中的每个数字在每个组合中只能使用一次。</p>

<p><strong>说明：</strong></p>

<ul>
	<li>所有数字（包括目标数）都是正整数。</li>
	<li>解集不能包含重复的组合。 </li>
</ul>

<p><strong>示例 1:</strong></p>

<pre><strong>输入:</strong> candidates = <code>[10,1,2,7,6,1,5]</code>, target = <code>8</code>,
<strong>所求解集为:</strong>
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]
</pre>

<p><strong>示例 2:</strong></p>

<pre><strong>输入:</strong> candidates = [2,5,2,1,2], target = 5,
<strong>所求解集为:</strong>
[
  [1,2,2],
  [5]
]</pre>

### 解法

> 对于这道题和上面不同的是每个数字只能取一次，我们还是先排序，然后发现数组为`[1,1,2,5,6,7,10]`，两个`1`当然能够同时存在了，但是单独的时候只能算一个组合，对于第二次出现的同一数字，我们直接跳过其计算，但是怎么才能让他们能够同时存在呢，我们不是传入了一个`start`值吗，这个值代表在某段递归当中（以`candidates[start]`为初始值），如果大于`start`并且重复数字（举个例子，第一次的`1`会回溯，也就是会出现`存在`和`不存在`两种状态，当`第一次的1`不存在的时候递归到`第二次的1`就会重复），这样就会导致重复，解决办法就是对于每次的递归段初始位置`start`之后的都进行判断，防止重复。

```cpp
class Solution {
public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end());
        vector<vector<int>> res;
        vector<int> path;
        backtrack(res, path, candidates, target, 0, 0);
        return res;
    }
    void backtrack(vector<vector<int>> &res, vector<int> &path, vector<int> &candidates, int target, int start, int sum) {
        if(sum > target) return;
        if(sum == target) res.push_back(path);
        for(int i = start; i < candidates.size(); i++) {
            if(i > start && candidates[i] == candidates[i - 1]) continue;
            path.push_back(candidates[i]);
            backtrack(res, path, candidates, target, i + 1, sum + candidates[i]);
            path.pop_back();
        }
    }
};
```
## 216. 组合总和 III
[216. 组合总和 III](https://leetcode-cn.com/problems/combination-sum-iii/)

### 题目内容
<p>找出所有相加之和为 <em><strong>n</strong> </em>的 <strong><em>k </em></strong>个数的组合<strong><em>。</em></strong>组合中只允许含有 1 - 9 的正整数，并且每种组合中不存在重复的数字。</p>

<p><strong>说明：</strong></p>

<ul>
	<li>所有数字都是正整数。</li>
	<li>解集不能包含重复的组合。 </li>
</ul>

<p><strong>示例 1:</strong></p>

<pre><strong>输入:</strong> <em><strong>k</strong></em> = 3, <em><strong>n</strong></em> = 7
<strong>输出:</strong> [[1,2,4]]
</pre>

<p><strong>示例 2:</strong></p>

<pre><strong>输入:</strong> <em><strong>k</strong></em> = 3, <em><strong>n</strong></em> = 9
<strong>输出:</strong> [[1,2,6], [1,3,5], [2,3,4]]
</pre>

### 解法

> 一样

```cpp
class Solution {
public:
    vector<vector<int>> combinationSum3(int k, int n) {
        vector<vector<int>> res;
        vector<int> path;
        backtrack(res, path, 0, 1, k, n);
        return res;
    }
    void backtrack(vector<vector<int>> &res, vector<int> &path, int sum, int start, int k, int n) {
        if(sum > n) return;
        if(sum == n && path.size() == k) res.push_back(path);
        for(int i = start; i <= 9; i++) {
            path.push_back(i);
            backtrack(res, path, sum + i, i + 1, k, n);
            path.pop_back();
        }
    }
};
```
## 46. 全排列
[46. 全排列](https://leetcode-cn.com/problems/permutations/)

### 题目内容
<p>给定一个<strong> 没有重复</strong> 数字的序列，返回其所有可能的全排列。</p>

<p><strong>示例:</strong></p>

<pre><strong>输入:</strong> [1,2,3]
<strong>输出:</strong>
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]</pre>
### 解法 

> 这道题因为是全排列，每个位置不同都是不同的，所以不能用索引来保证不重复，因为只要保证不重复取一个值就不会重复，所以可以新建一个`vis`数组来保存取过的值。

```cpp
// Author: abcdlsj @ https://github.com/abcdlsj/Leetcode

class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> path;
        vector<bool> vis(nums.size(), false);
        backtrack(res, path, nums, vis);
        return res;
    }
    void backtrack(vector<vector<int>> &res, vector<int> path, vector<int> nums, vector<bool> vis) {
        if(path.size() == nums.size()) {
            res.push_back(path);
            return;
        }
        for(int i = 0; i < nums.size(); i++) {
            if(vis[i] == true) continue;
            path.push_back(nums[i]);
            vis[i] = true;
            backtrack(res, path, nums, vis);
            path.pop_back();
            vis[i] = false;
        }
    }
};
```
## 47. 全排列 II
[47. 全排列 II](https://leetcode-cn.com/problems/permutations-ii/)

### 题目内容
<p>给定一个可包含重复数字的序列，返回所有不重复的全排列。</p>

<p><strong>示例:</strong></p>

<pre><strong>输入:</strong> [1,1,2]
<strong>输出:</strong>
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]</pre>

### 解法

> 这个算是比较复杂的情况了，因为要保证不能重复，其实和之前排列组合2那道差不多，画图就好了，注意看判断的那个地方

```cpp
// Author: abcdlsj @ https://github.com/abcdlsj/Leetcode

class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> path;
        vector<bool> vis(nums.size(), false);
        sort(nums.begin(), nums.end());
        backtrack(res, path, nums, vis);
        return res;
    }
    void backtrack(vector<vector<int>> &res, vector<int> path, vector<int> nums, vector<bool> vis) {
        if(path.size() == nums.size()) {
            res.push_back(path);
            return;
        }
        for(int i = 0; i < nums.size(); i++) {
            if(vis[i] == true || (i > 0 && nums[i - 1] == nums[i] && vis[i - 1] == false)) continue;
            path.push_back(nums[i]);
            vis[i] = true;
            backtrack(res, path, nums, vis);
            path.pop_back();
            vis[i] = false;
        }
    }
};
```
## 78. 子集
[78. 子集](https://leetcode-cn.com/problems/subsets/)

### 题目内容
<p>给定一组<strong>不含重复元素</strong>的整数数组 <em>nums</em>，返回该数组所有可能的子集（幂集）。</p>

<p><strong>说明：</strong>解集不能包含重复的子集。</p>

<p><strong>示例:</strong></p>

<pre><strong>输入:</strong> nums = [1,2,3]
<strong>输出:</strong>
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]</pre>


### 解法

> 也是一样的套路，不能重复的子集即是不能重复的`组合`，只是这个组合没有限制，也不需要排序，只需要索引确保不会重复，保证每个数都有两个状态即可（`取`与`不取`）。

```cpp
// Author: abcdlsj @ https://github.com/abcdlsj/Leetcode

class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> path;
        backtrack(res, path, nums, 0);
        return res;
    }
    void backtrack(vector<vector<int>> &res, vector<int> &path, vector<int> nums, int start) {
        res.push_back(path);
        for(int i = start; i < nums.size(); i++) {
            path.push_back(nums[i]);
            backtrack(res, path, nums, i + 1);
            path.pop_back();
        }
    }
};
```
## 90. 子集 II
[90. 子集 II](https://leetcode-cn.com/problems/subsets-ii/)

### 题目内容
<p>给定一个可能包含重复元素的整数数组 <em><strong>nums</strong></em>，返回该数组所有可能的子集（幂集）。</p>

<p><strong>说明：</strong>解集不能包含重复的子集。</p>

<p><strong>示例:</strong></p>

<pre><strong>输入:</strong> [1,2,2]
<strong>输出:</strong>
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]</pre>


### 解法

> 重复的元素，然后不能重复组合，画图之后也就一目了然了。

```cpp
// Author: abcdlsj @ https://github.com/abcdlsj/Leetcode

class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> path;
        vector<bool> vis(nums.size(), false);
        sort(nums.begin(), nums.end());
        backtrack(res, path, nums, vis, 0);
        return res;
    }
    void backtrack(vector<vector<int>> &res, vector<int> path, vector<int> nums, vector<bool> vis, int start) {
        res.push_back(path);
        for(int i = start; i < nums.size(); i++) {
            if(i > start && nums[i] == nums[i - 1] && vis[i - 1] == false) continue;
            path.push_back(nums[i]);
            vis[i] = true;
            backtrack(res, path, nums, vis, i + 1);
            vis[i] = false;
            path.pop_back();
        }
    }
};
```

## 总结

> 能够总结出这一类穷举问题的回溯模板。

```python
result = []
def backtrack(路径, 选择列表):
   if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```

> 回溯法可能开始很难，但是就和`BFS DFS`一样只要心里面有解决问题的`模板`，那一般的问题就不算难了，当然一些很困难的回溯也很复杂，毕竟剪枝没有经验也是比较难看出来的。
