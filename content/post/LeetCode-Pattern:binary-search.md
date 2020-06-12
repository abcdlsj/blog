+++
title = "LeetCode Pattern: Binary Search"
date = 2020-06-01T22:05:26+08:00
lastmod = 2020-06-01T22:05:26+08:00
tags = ["LeetCode", "Binary Search"]
categories = ["learn"]
imgs = []
cover = ""  # image show on top
readingTime = true  # show reading time after article date
toc = true
comments = false
justify = false  # text-align: justify;
single = false  # display as a single page, hide navigation on bottom, like as about page.
license = ""  # CC License
draft = false

+++

> LeetCode 刷题总结之：二分查找（WIP）

<!--more-->

> 在[计算机科学](https://zh.wikipedia.org/wiki/计算机科学)中，**二分查找算法**（英语：binary search algorithm），也称**折半搜索算法**（英语：half-interval search algorithm）[[1\]](https://zh.wikipedia.org/wiki/二分搜尋演算法#cite_note-1)、**对数搜索算法**（英语：logarithmic search algorithm）[[2\]](https://zh.wikipedia.org/wiki/二分搜尋演算法#cite_note-FOOTNOTEKnuth1998§6.2.1_("Searching_an_ordered_table"),_subsection_"Binary_search"-2)，是一种在[有序数组](https://zh.wikipedia.org/wiki/有序数对)中查找某一特定元素的搜索[算法](https://zh.wikipedia.org/wiki/算法)。搜索过程从数组的中间元素开始，如果中间元素正好是要查找的元素，则搜索过程结束；如果某一特定元素大于或者小于中间元素，则在数组大于或小于中间元素的那一半中查找，而且跟开始一样从中间元素开始比较。如果在某一步骤数组为空，则代表找不到。这种搜索算法每一次比较都使搜索范围缩小一半。
>
> 二分查找算法在情况下的复杂度是对数时间。
>
> -\- wikipedia

> 只有 PUSH，才有 POP
>
> -\- 沃兹基 · 硕德

#### [287. 寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)

> 这道题用二分简直了，虽然复杂度就那样，但是思路很牛逼

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int le = 1, ri = nums.size() - 1, cnt, mid;

        while (le < ri) {
            cnt = 0;
            mid = le + (ri - le) / 2;

            for (auto num : nums) {
                if (num <= mid) cnt++;
            }

            if (cnt > mid) {
                ri = mid;
            } else {
                le = mid + 1;
            }
        }

        return le; 
    }
};
```

