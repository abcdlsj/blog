---
title: "LeetCode Data-Structure: Link List"
date: 2020-06-22T08:52:45+08:00
draft: false
keywords: ["LeetCode Data-Structure: Link List"]
tags: ["Link List", "Data Structure", "LeetCode"]
categories: ["learn"]
author: "abcdlsj"
toc: true
typora-root-url: ../../static
---

> LeetCode 刷题总结：链表

<!--more-->

> <!--看到我 4 月 29 建的博文，我沉默了，把时间改成了今天（敲了很多，发现并没有任何意义，然后删了）-->
>
> <!---\- 06-22 :timer_clock: 09:22-->
>
> 再次更新，添加了几个题，也画了反转链表的过程（画得不好看。。。）
>
> 等把 LRU 添加之后，就差不多了，其它的东西之后慢慢遇到再添加
>
> -\- 06-30 :timer_clock: 14:05

## 浅谈链表的递归

> 用反转链表来举例

![reverse list](/img/reverseList.png)

其实也就是**两个点**：

- 注意好返回值
- 处理最小情况

## 题目

#### [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode* l3 = new ListNode(-1), *cur = l3;

        while (l1 && l2) {
            if (l1->val <= l2->val) {
                cur->next = l1;
                l1 = l1->next;
            } else {
                cur->next = l2;
                l2 = l2->next;
            }
            cur = cur->next;
        } 

        if (l1) cur->next = l1;
        if (l2) cur->next = l2;

        return l3->next; 
    }
};
```

**递归形式**

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        while (l1 && l2) {
            if (l1->val <= l2->val) {
                l1->next = mergeTwoLists(l1->next, l2);
                return l1;
            } else {
                l2->next = mergeTwoLists(l1, l2->next);
                return l2;
            }
        }

        return l1 == nullptr ? l2 : l1;
    }
};
```

#### [206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* pre = nullptr, *cur = head;

        while (cur) {
            ListNode* node = cur->next;
            cur->next = pre;
            pre = cur;
            cur = node;
        }

        return pre;
    }
};
```

**递归解法**

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return head;
        ListNode* node = reverseList(head->next);
        head->next->next = head;
        head->next = nullptr;
        return node;
    }
};
```

#### [234. 回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)

> 边快慢指针，边逆序前半部分
>
> 注意：
>
> 如果链表长度为奇数，fast 会停在最后一个元素，slow 会在中点；偶数，则停在空的位置，slow 在中间两个元素之中的左边
>

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        ListNode* pre = nullptr, *slow = head, *fast = head;

        while(fast && fast->next) {
            fast = fast->next->next;
            ListNode* nxt = slow->next;
            slow->next = pre;
            pre = slow;
            slow = nxt;
        }
        if (fast && fast->next == nullptr) slow = slow->next;

        while (pre && slow) {
            if (pre->val != slow->val) return false;
            pre = pre->next;
            slow = slow->next;
        }

        return true;
    }
};
```

#### [24. 两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

> n25 的简单版，这道题我记得两个月前写，思路都不是很清晰，现在非常清晰（没看自己之前的提交，一口气完成了解答，并且答案和之前写的题解相同，PS. 大多数题我都会提交自己认为最简单快捷的实现）
>
> 链表的题，一定一定要自己画下过程，知道流程之后基本大多数题都会有思路

**递归和非递归**

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return head;
        ListNode* node = head->next;
        head->next = swapPairs(node->next);
        node->next = head;
        return node;
    }
};
```

```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return head;
        ListNode* pre = new ListNode(-1), *cur = head, *nhead = pre;

        while (cur && cur->next) {
            ListNode* nxt = cur->next;
            cur->next = nxt->next;
            nxt->next = cur;
            pre->next = nxt;
            pre = cur;
            cur = cur->next;
        }

        return nhead->next;
    }
};
```

#### [25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

> 这道题真的很棒，不吹不黑

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* n1 = head, *n2 = head;
        for (int i = 0; i < k; i++) {
            if (n2 == nullptr) return head;
            n2 = n2->next; 
        }

        ListNode* node = reverse(n1, n2);
        n1->next = reverseKGroup(n2, k);

        return node;
    }

    ListNode* reverse(ListNode* begin, ListNode* end) {
        ListNode* pre = NULL, *cur = begin;

        while (cur != end) {
            ListNode* node = cur->next;
            cur->next = pre;
            pre = cur;
            cur = node;
        }

        return pre;
    }
};
```

#### [146. LRU缓存机制](https://leetcode-cn.com/problems/lru-cache/)

> 很重要的东西，但是难度比较低

```cpp
class LRUCache {
public:
    int cap;
    list<pair<int, int>> cache;
    unordered_map<int, list<pair<int, int>>::iterator> map; 
    LRUCache(int capacity) : cap(capacity) {}
    
    int get(int key) {
        auto it = map.find(key);
        if (it != map.end()) {
            cache.splice(cache.begin(), cache, it->second);
            return it->second->second;
        }
        return -1;
    }
    
    void put(int key, int value) {
        auto it = map.find(key);
        if (it != map.end()) {
            cache.erase(map[key]);
        } else {
            if (cap == cache.size()) {
                auto ele = cache.back();
                map.erase(ele.first);
                cache.pop_back();
            }
        }
        cache.push_front(make_pair(key, value));
        map[key] = cache.begin();
    }
};
```

**带有过期时间的 LRU 算法**

```cpp

```

#### [138. 复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

> 有丝分裂法（？）
>
> 复制一份，然后分裂

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;
    
    Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
*/

class Solution {
public:
    Node* copyRandomList(Node* head) {
        if (head == nullptr) return nullptr;
        Node* cur = head;
        while (cur) {
            Node* n = new Node(cur->val, cur->next, nullptr);
            Node* c = cur->next;
            cur->next = n;
            cur = c;
        }

        cur = head;
        while (cur) {
            if (cur->random) cur->next->random = cur->random->next;
            cur = cur->next->next;
        }

        cur = head;
        Node* ret = cur->next;
        
        while (cur->next) {
            Node* n = cur->next;
            cur->next = cur->next->next;
            cur = n;
        }

        return ret;
    }
};
```

> hashmap 法

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;
    
    Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
*/

class Solution {
public:
    Node* copyRandomList(Node* head) {
        if (head == nullptr) return nullptr;

        unordered_map<Node*, Node*> map;
        Node* cur = head;
        
        while (cur) {
            map[cur] = new Node(cur->val);
            cur = cur->next;
        }

        cur = head;
        
        while (cur) {
            map[cur]->next = map[cur->next];
            map[cur]->random = map[cur->random];
            cur = cur->next;
        }

        return map[head];
    }
};
```

