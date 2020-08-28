+++
title = "LeetCode Pattern: Binary Search"
date = 2020-06-01T22:05:26+08:00
lastmod = 2020-08-17T22:05:26+08:00
tags = ["LeetCode", "Binary Search"]
categories = ["learn"]
toc = true
draft = false

+++

> LeetCode 刷题总结之：二分查找

<!--more-->

> 在[计算机科学](https://zh.wikipedia.org/wiki/计算机科学)中，**二分查找算法**（英语：binary search algorithm），也称**折半搜索算法**（英语：half-interval search algorithm）[[1\]](https://zh.wikipedia.org/wiki/二分搜尋演算法#cite_note-1)、**对数搜索算法**（英语：logarithmic search algorithm）[[2\]](https://zh.wikipedia.org/wiki/二分搜尋演算法#cite_note-FOOTNOTEKnuth1998§6.2.1_("Searching_an_ordered_table"),_subsection_"Binary_search"-2)，是一种在[有序数组](https://zh.wikipedia.org/wiki/有序数对)中查找某一特定元素的搜索[算法](https://zh.wikipedia.org/wiki/算法)。搜索过程从数组的中间元素开始，如果中间元素正好是要查找的元素，则搜索过程结束；如果某一特定元素大于或者小于中间元素，则在数组大于或小于中间元素的那一半中查找，而且跟开始一样从中间元素开始比较。如果在某一步骤数组为空，则代表找不到。这种搜索算法每一次比较都使搜索范围缩小一半。
>
> 二分查找算法在情况下的复杂度是对数时间。
>
> -\- wikipedia

> <!--只有 PUSH，才有 POP-->
>
> <!---\- 沃兹基 · 硕德（06-01）-->
>
> <!--咕咕！咕咕咕咕（神啊！救救我吧）-->
>
> <!---\- 06-22 :timer_clock: 09:55-->
>
> <!--还是要面对的。。。。-->
>
> <!---\- 07-28 :timer_clock: 22:30-->
>
> <!--弄了半天，晕了。。。。-->
>
> <!--不管了，暂时不写了，等下次温故知新-->
>
> <!---\- 07-30 :timer_clock: 17:47-->
>
> <!--添加了几道查找距离/速度的二分-->
>
> <!---\- 08-17 :timer_clock: 11:15-->

## 浅谈二分查找

> 二分查找看着简单，细节就是魔鬼

> 首先，一个有序数组查找一个数字，怎么写呢？

测试主函数

```cpp
int main() {
  vector<int> arr = {1, 2, 3, 4, 5, 6, 7, 8};
  cout << binary_search(arr, 6) << endl;
  cout << binary_search(arr, 0) << endl;
  cout << binary_search(arr, 9) << endl;
  cout << binary_search(arr, 1) << endl;
  cout << binary_search(arr, 8) << endl;
}
```

`binary_search` 函数

这样写肯定是可以的，测试通过

```cpp
int binary_search(vector<int> nums, int target) {
  int le = 0, ri = nums.size() - 1;
  while (le <= ri) {
    int mid = le + (ri - le) / 2;
	if (nums[mid] == target) {
	  return mid;
	} else if (nums[mid] < target) {
      le = mid + 1;
    } else {
      ri = mid - 1;
    }
  }
  return -1;
}
```

这样也可以

```cpp
int binary_search(vector<int> nums, int target) {
  int le = 0, ri = nums.size() - 1;
  while (le < ri) {
    int mid = le + (ri - le) / 2;
	if (nums[mid] == target) {
	  return mid;
	} else if (nums[mid] < target) {
      le = mid + 1;
    } else {
      ri = mid;
    }
  }

  if (nums[le] == target) return le;
  return -1;
}
```

我都是直接初始化 `ri = nums.size() -1`，左闭右闭区间，而不是混用 `ri = nums.size()`，因为二分的写法真的很多，初始化可以定下来，然后结合题目单独分析

至于循环，这里有两种思路（想法来自于 weiwei 的算法笔记 https://www.yuque.com/liweiwei1419/algo/wkmtx4）

**思路 1：在循环体内部查找元素**：`while (left <= right)`；

循环退出的条件是 `left == right + 1` ，也就是说如果跳出循环，那么 `left == right` 这个点已经被判断了

**思路 2：在循环体内部排除元素**：`while (left < right)`；

循环退出的条件是 `left == right`，也就是说如果跳出循环，那么 `left == right` 这个点并没有被判断，所以最后需要单独判断 `nums[left]` 是否符合题意

**核心是判断下一次搜索区间**

## 搜索旋转数组

> 这个类型的题基本上都会用到二分搜索

#### [33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

> 很不错的题（我都忘了好几次了）

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int le = 0, ri = nums.size() - 1;

        while (le <= ri) {
            int mid = le + (ri - le) / 2;

            if (nums[mid] == target) return mid; 
            // 发生这个条件的时候，肯定代表 [le, mid] 之间是有序的，我们只能判断有序部分
            if (nums[mid] >= nums[le]) {
                // 为什么要 nums[le] <= target;
                // 注意查找区间
                if (nums[mid] > target && nums[le] <= target) {
                    ri = mid - 1;
                } else {
                    le = mid + 1;
                }
            } else { // [mid, ri] 之间有序
                if (nums[mid] < target && nums[ri] >= target) {
                    le = mid + 1;
                } else {
                    ri = mid - 1;
                }
            }
        }  

        return -1;
    }
};
```

#### [153. 寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

> 有个评论写得很很好，“前面一堆０，后面一堆１，然后寻找第一个１的二分问题”

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int le = 0, ri = nums.size() - 1;
        
        while (le < ri) {
            int mid = le + (ri - le) / 2;
            if (nums[mid] < nums[ri]) {
                ri = mid;
            } else {
                le = mid + 1;
            }
        }

        return nums[le];
    }
};
```

#### [154. 寻找旋转排序数组中的最小值 II](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int le = 0, ri = nums.size() - 1;

        while (le < ri) {
            int mid = le + (ri - le) / 2;

            if (nums[mid] < nums[ri]) {
                ri = mid;
            } else if (nums[mid] > nums[ri]) {
                le = mid + 1;
            } else {
                ri--;
            }
        }

        return nums[le];
    }
};
```

## 二分搜索

#### [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

> 神一般的题目，这道和 33 题，153，154 如果弄懂，我感觉差不多了
>
> 这道题，写法也好几种，管那么多干嘛，写出来就行了（

按照思路一查找，思路二排除的想法

可以这样写，第一个循环排除，如果不存在直接返回，存在，第二个循环查找

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> ret = {-1, -1};
        if (nums.size() == 0) return ret;

        int le = 0, ri = nums.size() - 1;

        while (le < ri) {
            int mid = le + (ri - le) / 2;

            if (nums[mid] < target) {
                le = mid + 1;
            } else {
                ri = mid;
            }
        }

        if (nums[le] != target) return ret;
        ret[0] = le;

        le = 0, ri = nums.size() - 1;
        while (le <= ri) {
            int mid = le + (ri - le) / 2;

            if (nums[mid] <= target) {
                le = mid + 1;
            } else {
                ri = mid - 1;
            }
        }

        if (nums[le - 1] == target) ret[1] = le - 1;
        return ret;
    }
};
```

这样写也是对的，反正考虑好会出错的情况，然后添加判断条件，其实都可以解决

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> ret = {-1, -1};
        if (nums.size() == 0) return ret;

        int le = 0, ri = nums.size() - 1;

        while (le <= ri) {
            int mid = le + (ri - le) / 2;

            if (nums[mid] < target) {
                le = mid + 1;
            } else {
                ri = mid - 1;
            }
        }

        if (le >= nums.size() || nums[le] != target) return ret;
        ret[0] = le;

        le = 0, ri = nums.size() - 1;
        while (le <= ri) {
            int mid = le + (ri - le) / 2;

            if (nums[mid] <= target) {
                le = mid + 1;
            } else {
                ri = mid - 1;
            }
        }

        if (nums[le - 1] == target) ret[1] = le - 1;
        return ret;
    }
};
```

## 其他用法

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

#### [875. 爱吃香蕉的珂珂](https://leetcode-cn.com/problems/koko-eating-bananas/)

> 套路题（PS. 202 周赛第三题同类型）

```cpp
class Solution {
public:
    int minEatingSpeed(vector<int>& piles, int H) {
        int maxn = *max_element(piles.begin(), piles.end());
        int le = 1, ri = maxn;
        while (le <= ri) {
            int mid = le + (ri - le) / 2;
            if (check(piles, mid, H)) {
                ri = mid - 1;
            } else {
                le = mid + 1;
            }
        }

        return le;
    }

    bool check(const vector<int>& piles, int speed, int H) {
        int count = 0;
        for (int i = 0; i < piles.size(); i++) {
            count += piles[i] / speed + (piles[i] % speed == 0 ? 0 : 1);
            if (count > H) return false;
        }
        return true;
    }
};
```

#### [1011. 在 D 天内送达包裹的能力](https://leetcode-cn.com/problems/capacity-to-ship-packages-within-d-days/)

> 和吃香蕉一样

```cpp
class Solution {
public:
    int shipWithinDays(vector<int>& weights, int D) {
        int le = *max_element(weights.begin(), weights.end()),
            ri = accumulate(weights.begin(), weights.end(), 0);
        
        while (le < ri) {
            int mid = le + (ri - le) / 2;
            if (check(weights, D, mid)) {
                ri = mid;
            } else {
                le = mid + 1;
            }
        }

        return le;
    }

    bool check(const vector<int>& weights, int D, int speed) {
        int idx = 0;
        for (int i = 0; i < D; i++) {
            int sum = speed;
            if (sum < weights[idx]) return false;
            while (sum >= weights[idx]) {
                sum -= weights[idx];
                idx++;
                if (idx == weights.size()) {
                    return true;
                }
            }
        }

        return false;
    }    
};
```

## References

https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/solution/er-fen-cha-zhao-suan-fa-xi-jie-xiang-jie-by-labula/

https://labuladong.gitbook.io/algo/gao-pin-mian-shi-xi-lie/koko-tou-xiang-jiao