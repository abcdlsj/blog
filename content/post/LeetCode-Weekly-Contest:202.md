---
date: "2020-08-16T11:58:06+08:00"
title: "LeetCode-Weekly-Contest:202"
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

> LeetCode Weekly Contest: 202

<!--more-->

> 这周差点忘记了，过了十分钟才参加的（当时都有点不想参加了），然后解决了前三题，花了 35 min（PS. 第三题比第四题难），然后看着第四题懵逼了，其实一点不难（当时在别人家把电脑放在膝盖上写的。。），最后排名也一般 500 +（没有内推资格。。。），这周难度不大，很有机会 AK 的，没把握好机会，希望下次排名好点吧（想去面试微软，但是我感觉怎么着也得稳前 200 才有机会）

### 5489. 两球之间的磁力

> 二分，主要是 `间隔 = 小球数 - 1`

```cpp
class Solution {
public:
    int maxDistance(vector<int>& position, int m) {
        sort(position.begin(), position.end());
        int le = 0, ri = position[position.size() - 1] - position[0];

        while (le <= ri) {
            int mid = le + (ri - le) / 2;
            if (check(position, m, mid)) {
                le = mid + 1;
            } else {
                ri = mid - 1;
            }
        }

        return le - 1;
    }

    bool check(const vector<int>& position, int m, int dis) {
        int count = 0, prepos = position[0];
        for (int i = 1; i < position.size(); i++) {
            if (position[i] - prepos >= dis) {
                count++;
                prepos = position[i];
                if (count == m - 1) {
                    return true;
                }
            }
        }

        return false;
    }
};
```

### 5490. 吃掉 N 个橘子的最少天数

> 没写出来，其实很简单

```cpp
class Solution {
public:
    unordered_map<int, int> map;
    int minDays(int n) {
        if (n <= 1) return n;
        if (map.count(n)) return map[n];
        int res = min(minDays(n / 2) + n % 2 + 1, minDays(n / 3) + n % 3 + 1);
        map[n] = res;
        
        return res;
    }
};
```

