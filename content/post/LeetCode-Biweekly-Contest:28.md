---
date: "2020-06-14T11:16:07+08:00"
title: "LeetCode Biweekly Contest: 28"
categories:
  - learn
tags:
  - Contest
  - LeetCode
draft: false
toc: true
---

> LeetCode Biweekly Contest 28

> 现在开始每周记录一下周赛的情况，因为到了瓶颈了，现在主要的目的是达到每次周赛稳三题的情况（大半年前 50 题的时候，就可以 2 题，现在还在冲稳三题。。。）
>
> 本周双周赛前面两个比较简单，第三个写了半天还是错的（写出来可以前 300，甚至 200，靠着手速前 2 个，都 700+。。。太丢脸了），最后一个当然不用说了（日常放弃，下次可以试试）
>
> 周赛放弃了（吃饭去了。。。签了到就溜了，下次一定！）
>
> 这里就记录一下双周赛第三题好了（虽然很不负责任，但是写一道题多爽）

#### [5423. 找两个和为目标值且不重叠的子数组](https://leetcode-cn.com/problems/find-two-non-overlapping-sub-arrays-each-with-target-sum/)

这道题我当时思路是：

1. 首先前缀和。

2. 用 `map` 存储 `{当前前缀和, 下标 i}` ，最后放入 `map[0] = -1;`  。

3. 遍历 arr，求 `map.find(arr[i] - target)` 如果找到，那就取子数组首尾下标，`map[arr[i] - target] 和 i` ，计算长度用 `vectot cnt` 保存，判断是否重叠，如果当前 `map[arr[i] - target] < 上次的 i `，那就因为重叠跳过（问题在这）。 然后返回 `cnt` 最小的两个值。

我判断重叠的地方是错误的，比赛时我没仔细想，知道之后时间不太够了（懒），就没继续写。

举个例子，比如 ` arr = [3, 1, 1, 1, 1, 1, 2, 1], target = 4` 。

按照我的写法，会出现下面的错误：

当计算了 `[3, 1 ,(1, 1, 1, 1),  2, 1]` 之后，下次出现的  `[3, 1 ,1, 1, 1, (1,  2, 1)]` 就会被跳过，但是因为 `下次的长度 < 上次的长度` ，所以答案会出现错误 。  

比赛时因为给的 case 比较特殊，发现的比较晚（考虑问题不够全面，没有刻意去训练这个方面，需要加强）。

解决办法也有，就是直接存储下标 `cnt.push_back({le, ri});` ，然后最后处理重叠，会变得比较复杂，所以比赛的时候也没去写，想着后面看下大佬的简单写法。

> 等我多看点答案综合一下下午继续写
>
> -\- Sunday 11:55
>
> 随便看了一下题解（其他时间看动漫去了。。。），`滑动窗口 + multimap` 算是比较好的解法了，比较容易理解（其实我就看了这一个答案。。。），我开始的想法也差不多这个，但是我不知道 `multimap` 。。。
>
> -\- Sunday 14:30

```cpp
class Solution {
public:
    int minSumOfLengths(vector<int>& arr, int target) {
        multimap<int, int> vec;
        int le = 0, ri = 0, sum = 0, ans = INT_MAX;

        while (ri < arr.size()) {
            // sum 增加，ri 右移
            sum += arr[ri]; ri++;
            if (sum < target) continue;
            // 收缩 le，直到可以下次递增
            while (sum > target) {
                sum -= arr[le]; le++;
            }
            if (sum == target) {
                // {长度， 左边界}
                vec.insert({ri - le, le});
            }
        }

        for (auto r1 = vec.begin(); r1 != vec.end(); r1++) {
            if (r1->first * 2 >= ans) break;
            auto r2 = r1;

            // 定 r1，找到合适的第一个 r2
            while ((++r2) != vec.end()) {
                // r1 在 r2 左边，然后 r1 长度大于 r1 r2 之间间隔
                if (r1->second < r2->second && r1->second + r1->first > r2->second) continue;
                // r2 在左边
                if (r2->second < r1->second && r2->second + r2->first > r1->second) continue;
                ans = min(ans, r1->first + r2->first);
                break;
            }
        }

        return ans == INT_MAX ? -1 : ans;
    }
};
```

比较容易看懂，我就不写啥了

> 学会了一手 `multimap` ，之前一直没有好的方法储存键值对，一直都是傻乎乎的用 `vector<pair<int, int>>` 。。。