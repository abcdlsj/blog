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
> 摘取一些设计题，和一些没有特定解法但是很有意思的题
>
> -\- 06-21 :timer_clock: 20:46

## 位运算

#### [136. 只出现一次的数字](https://leetcode-cn.com/problems/single-number/)

> 应该是去年炒起来的一道题，我想起来我去年第一次写的时候，刚开始就想到了异或做法。。。（因为当时在写 CS:APP Lab1，要是当时坚持刷 CS:APP Lab 就好了，后面就越来越懒了）

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

```cpp
0 ^ x = x;
x ^ x = 0;
x & ~x = 0;
x & ~0 =x;
```

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

> 不推荐构造式子，很容易忘记怎么写
>
> 可以使用下面这种写法，计算每个数在这个位的 1 的个数，然后 % 3，如果不为 0，代表 ans 在这个位置有 1
>
> 最后合在一起就好了

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;

        for (int i = 0; i < 32; i++) {
            int mask = 1 << i, cnt = 0;

            for (int j = 0; j < nums.size(); j++) {
                if ((nums[j] & mask) != 0) cnt++;
            }

            if (cnt % 3 != 0) {
                ans |= mask;
            }
        }

        return ans;
    }
};
```

## 求两个数组的第 K 大问题

#### [4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int m = nums1.size(), n = nums2.size(), len = m + n;

        return (getKthIn2Arr(nums1, nums2, 0, 0, (len + 1) / 2) + getKthIn2Arr(nums1, nums2, 0, 0, (len + 2) / 2)) / 2.0; 
    }

    int getKthIn2Arr(const vector<int>& nums1, const vector<int> nums2, int i, int j, const int& k) {
        if (i >= nums1.size()) return nums2[j + k - 1];
        if (j >= nums2.size()) return nums1[i + k - 1];

        if (k == 1) {
            return min(nums1[i], nums2[j]);
        }

        int min1 = i + k / 2 - 1 >= nums1.size() ? INT_MAX : nums1[i + k / 2 - 1];
        int min2 = j + k / 2 - 1 >= nums2.size() ? INT_MAX : nums2[j + k / 2 - 1];

        if (min1 < min2) {
            return getKthIn2Arr(nums1, nums2, i + k / 2, j, k - k / 2);
        } 

        return getKthIn2Arr(nums1, nums2, i, j + k / 2, k - k / 2);
    }
};
```

## 归并排序求逆序对

#### [剑指 Offer 51. 数组中的逆序对](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

> 非常推荐去看题解区 [weiwei 的题解](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/solution/bao-li-jie-fa-fen-zhi-si-xiang-shu-zhuang-shu-zu-b/)（PS. 官方的视频就是 weiwei 录的）
>
> 因为复杂度要求比较严格，所以需要做点小优化：
>
> - 需要传入 tmp 数组，避免重复拷贝的浪费
> - `if (nums[mid + 1] > nums[mid]) return leftAns + rightAns;` 

```cpp
class Solution {
public:
    int reversePairs(vector<int>& nums) {
        if (nums.size() == 0) return 0;
        vector<int> tmp(nums.size());

        return mergeSort(nums, 0, nums.size() - 1, tmp);
    }

    int merge(vector<int>& nums, int left, int mid, int right, vector<int>& tmp) {
        int i = left, j = mid + 1, count = 0;

        for (int k = left; k <= right; k++) tmp[k] = nums[k];
        for (int k = left; k <= right; k++) {
	        if (i > mid) {
	            nums[k] = tmp[j];
	            j++;
	        } else if (j > right) {
	            nums[k] = tmp[i];
	            i++;
	        } else if (tmp[i] <= tmp[j]) {
	            nums[k] = tmp[i];
	            i++;
	        } else {
	            nums[k] = tmp[j];
	            j++;
	            count += (mid - i + 1);
	        }
        }

        return count;
    }

    int mergeSort(vector<int>& nums, int left, int right, vector<int>& tmp) {
        if (left == right) return 0;

        int mid = left + (right - left) / 2;
        int leftAns = mergeSort(nums, left, mid, tmp);
        int rightAns = mergeSort(nums, mid + 1, right, tmp);

        if (nums[mid + 1] > nums[mid]) return leftAns + rightAns;
        int crossAns = merge(nums, left, mid, right, tmp);

        return leftAns + rightAns + crossAns;
    }
};
```

#### [315. 计算右侧小于当前元素的个数](https://leetcode-cn.com/problems/count-of-smaller-numbers-after-self/)

> 和上面一样的题，但是需要返回数组

## 抽屉原理达成目的

> 最近在写一些数组的遍历或者计数的时候，抽屉原理遇到很多次

#### [442. 数组中重复的数据](https://leetcode-cn.com/problems/find-all-duplicates-in-an-array/)

```cpp
class Solution {
public:
    vector<int> findDuplicates(vector<int>& nums) {
        vector<int> res;

        for (int i = 0; i < nums.size(); i++) {
            while (nums[i] != nums[nums[i] - 1]) {
                swap(nums[i], nums[nums[i] - 1]);
            }
        }

        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] != i + 1) res.push_back(nums[i]);
        }

        return res;        
    }
};
```

#### [287. 寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)



#### [41. 缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/)

> 也是抽屉原理

```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        for (int i = 0; i < nums.size(); i++) {
            while (nums[i] > 0 && nums[i] <= nums.size() && nums[i] != nums[nums[i] - 1]) {
                swap(nums[i], nums[nums[i] - 1]);
            }
        }

        for (int i = 0; i < nums.size(); i++) {
            if (i + 1 != nums[i]) return i + 1;
        }
        
        return nums.size() + 1;
    }
};
```

## 前缀和 + 哈希表

> 写了好几道了，应该记录一下

#### [560. 和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

```cpp
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int count = 0, sum = 0;
        unordered_map<int, int> map;

        map[0] = 1; 
        for (auto item : nums) {
            sum += item;
            if (map.find(sum - k) != map.end()) {
                count += map[sum - k];
            }

            map[sum]++;
        }

        return count;
    }
};

```

#### [1248. 统计「优美子数组」](https://leetcode-cn.com/problems/count-number-of-nice-subarrays/)

> 和上面那道完全一样

```cpp
class Solution {
public:
    int numberOfSubarrays(vector<int>& nums, int k) {
        int sum = 0, ans = 0;
        unordered_map<int, int> map;
        map[0] = 1;

        for (auto num : nums) {
            sum += num % 2;
            if (map.find(sum - k) != map.end()) {
                ans += map[sum - k];
            }
            map[sum]++;
        }

        return ans;
    }
};
```

## No Pattern

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

#### [48. 旋转图像](https://leetcode-cn.com/problems/rotate-image/)

> 一定要保证交换没有相交，`row` 和 `col` 取值需要关注
>
> `M x N`  和 `N x N` 修改一下 `row` 和 `col` 取值就可以了 

```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();
        int row = (n - 2) / 2, col = (n - 1) / 2;
        for (int i = 0; i <= row; i++) {
            for (int j = 0; j <= col; j++) {
                swap(matrix[i][j], matrix[j][n - i - 1]);
                swap(matrix[i][j], matrix[n - i - 1][n - j - 1]);
                swap(matrix[i][j], matrix[n - j - 1][i]);
            }
        }
    }
};
```

还可以直接转置之后翻转每一层

```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();

        for (int i = 0; i < n; i++) {
            for (int j = i; j < n; j++) {
                swap(matrix[i][j], matrix[j][i]);
            }
        }

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n / 2; j++) {
                swap(matrix[i][j], matrix[i][n - j - 1]);
            }
        }
    }
}
```

#### [31. 下一个排列](https://leetcode-cn.com/problems/next-permutation/)

> 去看看题解第二个就明白了

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int idx = nums.size() - 1;
        while (idx > 0 && nums[idx - 1] >= nums[idx]) idx--;
        sort(nums.begin() + idx, nums.end());

        if (idx == 0) return ;
        for (int i = idx; i < nums.size(); i++) {
            if (nums[i] > nums[idx - 1]) {
                swap(nums[i], nums[idx - 1]);
                break;
            }
        }
    }
};
```

#### [剑指 Offer 64. 求1+2+…+n](https://leetcode-cn.com/problems/qiu-12n-lcof/)

```cpp
class Solution {
public:
    int sumNums(int n) {
        n && (n += sumNums(n - 1));
        return n;
    }
};
```

## 设计题

### 栈

```cpp

```

### 队列

#### [622. 设计循环队列](https://leetcode-cn.com/problems/design-circular-queue/)

```cpp
class MyCircularQueue {
public:
    vector<int> data;
    int head, tail, capacity;
    /** Initialize your data structure here. Set the size of the queue to be k. */
    MyCircularQueue(int k) {
        capacity = k;
        head = tail = -1;
        data.resize(capacity);
    }
    
    /** Insert an element into the circular queue. Return true if the operation is successful. */
    bool enQueue(int value) {
        if (isFull()) return false;
        
        if (isEmpty()) head = 0;
        tail = (tail + 1) % capacity;
        data[tail] = value;

        return true;
    }
    
    /** Delete an element from the circular queue. Return true if the operation is successful. */
    bool deQueue() {
        if (isEmpty()) return false;
        if (head == tail) {
            head = -1;
            tail = -1;
            return true;
        }

        head = (head + 1) % capacity;
        
        return true;
    }
    
    /** Get the front item from the queue. */
    int Front() {
        if (isEmpty()) return -1;
        return data[head];
    }
    
    /** Get the last item from the queue. */
    int Rear() {
        if (isEmpty()) return -1;
        return data[tail];
    }
    
    /** Checks whether the circular queue is empty or not. */
    bool isEmpty() {
        return head == -1;
    }
    
    /** Checks whether the circular queue is full or not. */
    bool isFull() {
        return (tail + 1) % capacity == head;
    }
};

/**
 * Your MyCircularQueue object will be instantiated and called as such:
 * MyCircularQueue* obj = new MyCircularQueue(k);
 * bool param_1 = obj->enQueue(value);
 * bool param_2 = obj->deQueue();
 * int param_3 = obj->Front();
 * int param_4 = obj->Rear();
 * bool param_5 = obj->isEmpty();
 * bool param_6 = obj->isFull();
 */
```

