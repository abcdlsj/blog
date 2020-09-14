---
title: "LeetCode Pattern: BackTracking"
date: 2020-04-23T21:41:59+08:00
lastmod: 2020-04-23T21:41:59+08:00
draft: flase
tags: ["LeetCode", "BackTracking"]
categories: ["learn"]
author: "abcdlsj"
toc: true
---
> LeetCode 刷题总结之：回溯

<!--more-->

> 其实按理来说回溯法也只是一种基本方法（穷举法），不应该拿一个专题，但是回溯+剪枝真的非常漂亮，而且代码还是很有套路的。

> 距离我写这个博文过去了 3 个月，写完这篇博文之后的 3 个月我收获了什么？
>
> 收获了遇到同类型题目（回溯取子集，元素重复或者不重复）就能秒杀的能力，所有的回溯取子集的题目都逃不出下面的解法，但是有些回溯题目还是不能够 Pattern Matching，遇到变体，就想不到怎么解。
>
> 因为并没有总结出方法论，这也是我刷题的弊端，没有以点到面，而是零散的去刷不同的题目，总是回头刷之前的题，但是下一次遇到，也不能很快的写出来。
>
> 截至刚刚，总刷题量为 500，个人觉得真的理解并且解法没有重复的题目也就 100 不到，也就是说刷题效率其实非常低，到了这个程度，了解并且拓展一道题远远比刷 2、3 道题所获得还要多，接下来的刷题应该会采取多方法刷题，希望养成自己解题的思维模式，多训练自己解题的能力。
>
> 看来要把正确率刷低一点了（
>
> 简历不敢投，基础不会，这咋找工作啊。。。我是 FW 。。。
>
> 话说为啥 hugo 不支持内嵌 html block 啊。。。我才发现我从爬虫中复制的题目内容全部失效的。。。算了，下次不放题目了。。。
>
> -\- 07-23 :timer_clock: 17:43
>
> 时隔 3 个月，一样可以学习到新东西，希望可以一直保持对算法的兴趣，唉
>
> -\- 07-28 :timer_clock: 12:00

## 39. 组合总和
[39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)

### 题目内容

给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的数字可以无限制重复被选取。

说明：所有数字（包括 target）都是正整数。
解集不能包含重复的组合。

示例 1：输入：candidates = [2,3,6,7], target = 7,
所求解集为：
[
  [7],
  [2,2,3]
]

示例 2：输入：candidates = [2,3,5], target = 8,
所求解集为：
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]

提示：1 <= candidates.length <= 30

1 <= candidates[i] <= 200

candidate 中的每个元素都是独一无二的。

1 <= target <= 500


### 解法

> 题目说`candidates`中的数字可以无限制重复被选取，但是不能包含重复的组合，怎么才能不重复组合呢，我们就需要一个`start`来表明取值的位置，每次取了值从 `start` 再递归，这样就可以无限次选取，但是重复的数字只能够向之后取，保证不包含重复的组合，但是这样就必须数组是有序的，所以我们要先排序，为什么需要有序，因为我们假设的是向左取会出现重复组合，如果不排序，这个假设不会必然成立，答案也就出来了。

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

给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的每个数字在每个组合中只能使用一次。

说明：所有数字（包括目标数）都是正整数。
解集不能包含重复的组合。 

示例 1:输入: candidates = [10,1,2,7,6,1,5], target = 8,
所求解集为:
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]

示例 2:输入: candidates = [2,5,2,1,2], target = 5,
所求解集为:
[
  [1,2,2],
  [5]
]

### 解法

> 对于这道题和上面不同的是每个数字只能取一次，我们还是先排序，然后发现数组为`[1,1,2,5,6,7,10]`，我们不是传入了一个`start`值吗，这个值代表在某段递归当中（以`candidates[start]`为初始值），如果大于`start`并且重复数字，这样就会导致重复，解决办法就是对于每次的递归段初始位置`start`之后的都进行判断，防止重复。

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

找出所有相加之和为 n 的 k 个数的组合。组合中只允许含有 1 - 9 的正整数，并且每种组合中不存在重复的数字。

说明：所有数字都是正整数。
解集不能包含重复的组合。 

示例 1:输入: k = 3, n = 7
输出: [[1,2,4]]

示例 2:输入: k = 3, n = 9
输出: [[1,2,6], [1,3,5], [2,3,4]]

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

给定一个 没有重复 数字的序列，返回其所有可能的全排列。

示例:输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]

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

给定一个可包含重复数字的序列，返回所有不重复的全排列。

示例:输入: [1,1,2]
输出:
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]


### 解法

> 这个算是比较复杂的情况了，因为要保证不能重复，其实和之前排列组合 2 那道差不多，画图就好了，注意看判断的那个地方
>
> ------
>
> 这道题和之前的 `组合总和 II` 很像，甚至这道题的做法，一样可以放到那道题，也可以运行，结果正确
>
> 但是这道题用之前的做法却不正确
>
> 因为之前那道题是 `求和`，代表每个数字只是 `值`，相同数字的 `值`，不管任何位置，没有区别
>
> 但是这个地方是全排列，每个不同位置都是不同的，就算数字一样也是不同的，所以，不能够用 `start` 盲目的跳过，而是应该用 `vis` 标记，并且判断会重复的情况
>
> `vis[i] == true`  代表这个值已经用过了，是在当前的 `path` 中，所以跳过
>
> `vis[i - 1] == false && nums[i - 1] == nums[i]` 代表之前的相同数字以及进行过递归回溯了，那么这种情况就要放弃，因为如果继续下去，会取到之前的值，和之前的不就重复了吗
>
> 画出递归树，可以非常明显的看到，或者懒得画，可以去评论区看 `weiwei` 的题解，很清晰
>
> 全排列应该使用 `vis` 数组 ，因为它们每次递归都需要从第一个元素开始，但是不能够重复
>
> -\- 07-28 :timer_clock: 12:00

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

给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

说明：解集不能包含重复的子集。

示例:输入: nums = [1,2,3]
输出:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]

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

给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

说明：解集不能包含重复的子集。

示例:输入: [1,2,2]
输出:
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]

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
>
> ```python
> result = []
> def backtrack(路径, 选择列表):
> if 满足结束条件:
>      result.add(路径)
>      return
> for 选择 in 选择列表:
>  做选择
>  backtrack(路径, 选择列表)
>  撤销选择
> ```

> 回溯法可能开始很难，但是就和`BFS/DFS`一样只要心里面有解决问题的`模板`，那一般的问题就不算难了，当然一些很困难的回溯也很复杂，回溯的节点不好找，还有就是时间复杂度要求严格的题，毕竟巧妙的剪枝没有经验也是比较难看出来的。
