---
date: "2020-07-24T13:45:06+08:00"
title: "ZuoChengYun Algorithm Notes (WIP)"
authors: []
categories:
  - learn
tags:
  - Algorithm
  - Interview
  - LeetCode
draft: false
toc: true
typora-root-url: ../../static
---

> 程序员代码面试指南刷题总结

<!--more-->

> 左神的这些题，很不错的
>
> -\- 07-24 :timer_clock: 13:48

### [1. 在两个长度相等的数组中查找上中位数](https://www.nowcoder.com/practice/08588d568e164e0a9958b5d6a3c351f5?tpId=101&rp=1&ru=%2Fta%2Fprogrammer-code-interview-guide&qru=%2Fta%2Fprogrammer-code-interview-guide%2Fquestion-ranking)

#### 题目描述

给定两个有序数组arr1和arr2，已知两个数组的长度都为N，求两个数组中所有数的上中位数。

上中位数：假设递增序列长度为n，若n为奇数，则上中位数为第n/2+1个数；否则为第n个数

[要求]

时间复杂度为O(logN)，额外空间复杂度为O(1)

#### 分析

时间复杂度 O(logN) 提示使用二分，空间复杂度 O(1)，则代表不可以直接复制到一个数组去求中位数 

下面是表示方法：

`arr1[], start1 = 0, end1 = arr1.size() - 1`

`arr2[], start2 = 0, end2 = arr2.size() - 1`

`len = (end1 - start1) + 1, mid1 = (end1 + start1) / 2, end2 = (end2 + start2) / 2`

- `arr1[mid1] == arr2[mid2]`
- `arr1[mid1] < arr2[mid2]`

- `arr1[mid1] > arr2[mid2]`

#### 代码

> 个人习惯使用 `#include <bits/stdc++.h>`

```cpp
#include <bits/stdc++.h>
using namespace std;

int main () {
    int n;
    while (cin >> n) {
        vector<int> arr1(n), arr2(n);
        for (int i = 0; i < n; i++) cin >> arr1[i];
        for (int i = 0; i < n; i++) cin >> arr2[i];
        
        int start1 = 0, end1 = n - 1, start2 = 0, end2 = n - 1, offset, mid1, mid2;

        while (start1 < end1) {
            mid1 = start1 + (end1 - start1) / 2;
            mid2 = start2 + (end2 - start2) / 2;
            offset = ((end1 - start1 + 1) & 1) ^ 1;
            if (arr1[mid1] > arr2[mid2]) {
                end1 = mid1;
                start2 = mid2 + offset;
            } else if (arr1[mid1] < arr2[mid2]) {
                start1 = mid1 + offset;
                end2 = mid2;
            } else {
                cout << arr1[mid1] << endl;
                return 0;
            }
        }

        cout << min(arr1[start1], arr2[start2]) << endl;
        return 0;
    }
}
```

