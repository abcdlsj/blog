---
title: "LeetCode Data-Structure: Link List (WIP)"
date: 2020-06-22T08:52:45+08:00
draft: false
keywords: ["LeetCode Data-Structure: Link List"]
tags: ["Link List", "Data Structure", "LeetCode"]
categories: ["learn"]
author: "abcdlsj"
toc: true
---

> LeetCode 刷题总结：链表

<!--more-->

> 看到我 4 月 29 建的博文，我沉默了，把时间改成了今天（敲了很多，发现并没有任何意义，然后删了）
>
> -\- 06-22 :timer_clock: 09:22

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

```



#### [24. 两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

> n25 的简单版，这道题我记得两个月前写，思路都不是很清晰，现在非常清晰（没看自己之前的提交，一口气完成了解答，并且答案和之前写的题解相同，PS. 大多数题我都会提交自己认为最简单快捷的实现）
>
> 链表的题，一定一定要自己画下过程，和回溯一样，知道流程之后基本大多数题都会有思路

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

