---
date: "2020-06-21T14:45:25+08:00"
title: "LeetCode Weekly Contest: 194"
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

> LeetCode Weekly Contest: 194

<!--more-->

> 丢脸！我才写了一道。。。（t2 t3 都不难，思路都很正确，主要还是 stl 熟练度问题，还有就是吃饭时间比较奇葩，每次写到 11 点，就要准备吃饭了。。。）

## 5441. 保证文件名唯一

> 比较简单的一道，当时比赛时懵了，没写出来（有点离谱了）

```cpp
class Solution {
public:
    vector<string> getFolderNames(vector<string>& names) {
        unordered_map<string, int> m;
        vector<string> res(names.size());
        
        for (int i = 0; i < names.size(); i++) {
            if (!m.count(names[i])) {
                m[names[i]]++;
                res[i] = names[i];
            } else {
                int num = m[names[i]];
                while (m.count(names[i] + "(" + to_string(num) + ")")) {
                    num++;
                }
                res[i] = names[i] + "(" + to_string(num) + ")";
                m[names[i]] = num + 1;
                m[res[i]]++;
            }
        }
        
        return res;
    }
};
```

## 5442. 避免洪水泛滥

> 比赛时，一个小细节没弄好，就没提交
>
> 当时我是使用的 queue 储存可以排水的点，然后如果遇到会溢出的时候，就从排水点取一个来排水，但是这样效果不是很好，因为顺序访问不好解决，看了排名前面的大佬的解法，我发现有一个想法和我一样，但是人家用的 set 和 upper_bound 完美解决我的痛点，然后经过我的修改，代码如下：

```cpp
class Solution {
public:
    vector<int> avoidFlood(vector<int>& rains) {
        unordered_map<int, int> m;
        set<int> s;
        vector<int> res(rains.size(), -1);
        
        for (int i = 0; i < rains.size(); i++) {
            if (rains[i] == 0) {
                s.insert(i);
                continue;
            } else if (m.count(rains[i])) {
                auto it = s.upper_bound(m[rains[i]]);
                if (it == s.end()) return {};
                res[*it] = rains[i];
                s.erase(it);
            }
            
            m[rains[i]] = i;
        }
        
        for (auto it : s) res[it] = 1;
        
        return res;
    }
};
```

> upper_bound 和 lower_bound 使用比较少，我不知道 set 可以使用 upper_bound 。。。合适的话，也去了解下，看下源码之类的