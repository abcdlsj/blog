---
date: "2020-08-09T00:01:37+08:00"
title: "LeetCode Biweekly Contest:32 && Weekly Contest: 201"
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

> LeetCode 双周赛 32 + 周赛 201 解题报告

## 双周赛

### K 次操作转变字符串

> 我最开始用的每次 +26，导致 tle 了好几次（下次千万不要刷新了，刷新之前的错误都没了，导致改了代码我以为我又可以了，结果提交就 tle），之后换成 map，就没啥问题了

```cpp
class Solution {
public:
    bool canConvertString(string s, string t, int k) {
        if (s.size() != t.size()) return false;
        map<int, int> map;
        
        for (int i = 0; i < s.size(); i++) {
            int x = t[i] - s[i];
            x = x < 0 ? x + 26 : x;
            map[x]++;
        }
        
        for (auto ele : map) {
            if (ele.first == 0) continue;
            int x = ele.first, y = ele.second;
            for (int i = y - 1; i >= 0; i--) {
                if (x + i * 26 > k) return false;
            }
        }  
        return true;
    }
};
```

**赛后发现这样简单点**

```cpp
class Solution {
public:
    bool canConvertString(string s, string t, int k) {
        int n = s.size(), m = t.size();
        if (m != n) return false;
        unordered_map<int, int> umap;
        
        for (int i = 0; i < n; i++) {
            if (s[i] == t[i]) continue;
            int tmp = (t[i] - s[i] + 26) % 26;
            umap[tmp]++;
            if (tmp + (umap[tmp] - 1) * 26 > k) return false;
        }
        
        return true;
    }
};
```

### 平衡括号字符串的最少插入次数

> 写的时候开始想复杂了，没写出来（脑袋一直萦绕奇怪的解法），赛后发现我是傻逼，直接处理不就好了。。。

```cpp
class Solution {
public:
    int minInsertions(string s) {
        int le = 0, ans = 0;
        
        for (int i = 0; i < s.size(); i++) {
            if (s[i] == '(') {
                le++;
            } else {
                if (i + 1 < s.size() && s[i + 1] == ')') {
                    if (le == 0) {
                        ans++;
                    } else {
                        le--;
                    }
                    i++;
                } else {
                    ans++;
                    if (le == 0) {
                        ans++;
                    } else {
                        le--;
                    }
                }
            }
        }
        
        return ans + le * 2;
    }
};
```

## 周赛

> 提交前三道，比较简单，但是排名低，写得比较慢
>
> 不去看大佬的题解了，就不优化了（好想 AK 一次周赛

### 整理字符串

> 直接处理就好了

```cpp
class Solution {
public:
    string makeGood(string s) {
        string res;
        stack<char> stack;
        
        for (int i = 0; i < s.size(); i++) {
            if (!stack.empty() && (stack.top() == (s[i] + 32) || (stack.top() + 32) == s[i])) {
                stack.pop();
            } else {
                stack.push(s[i]);
            }
        }
            
        while (!stack.empty()) {
            res += stack.top();
            stack.pop();
        }
        
        return string(res.rbegin(), res.rend());
    }
};
```

### 找出第 N 个二进制字符串中的第 K 位

> 查找规律就知道，每次把 `K` 定位到上一个 `S` 的位置，然后记录下中间翻转的次数 `cnt`，最后一次翻转就好了

```cpp
class Solution {
public:
    char findKthBit(int n, int k) {
        vector<int> lens(n, 1);
        for (int i = 1; i < n; i++) {
            lens[i] += 2 * lens[i - 1];
        }
        
        int cnt = 0;
        char c;
        
        for (int i = n - 1; i >= 0; i--) {
            int tmp = (lens[i] + 1 ) / 2;
            if (tmp == 1 && k == 1) {
                c = '0';
                break;
            } 
            if (k == tmp) {
                c = '1';
                break;
            } else if (k < tmp) {
                continue;
            } else {
                int x = k - tmp;
                k = tmp - x;
                cnt++;
            }
        }
        
        // cout << cnt << endl;
        while (cnt--) {
            if (c == '1') c = '0';
            else c = '1';
        }
        
        return c;
    }
};
```

### 和为目标值的最大数目不重叠非空子数组数目

```cpp
class Solution {
public:
    int maxNonOverlapping(vector<int>& nums, int target) {
        unordered_map<int, int> map;
        for (int i = 1; i < nums.size(); i++) {
            nums[i] += nums[i - 1];
        }
        
        map[0] = -1;
        
        int ans = 0, le = INT_MAX, ri = INT_MIN;
        for (int i = 0; i < nums.size(); i++) {
            if (map.find(nums[i] - target) != map.end()) {
                int x = map[nums[i] - target] + 1, y = i;     
                if (y < le || x > ri) {
                    ans++;
                    le = min(le, x);
                    ri = max(ri, y);
                }
            }
            map[nums[i]] = i;
        }
        
        return ans;
    }
};
```

