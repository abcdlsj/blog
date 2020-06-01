---
title: "LeetCode Data-Structure: Tree"
date: 2020-05-14T21:52:45+08:00
lastmod: 2020-05-14T21:52:45+08:00
draft: false
keywords: ["Tree"]
description: "LeetCode Data Structure Tree"
tags: ["Tree", "LeetCode", "Data Structure"]
categories: ["learn"]
author: "abcdlsj"
---

> LeetCode 刷题总结：树(WIP) 

<!--more-->

> 十几天过去了，懒逼还没写完。。。不就是总结吗，明天一定写完（一定？）
>
> -\- 2020-06-01

## 遍历二叉树

以 LeetCode 题目中的 `binary tree` 为代表，结构如下

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
```

### 先序遍历

#### [144. 二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

**递归**

```cpp
class Solution {
public:
    vector<int> ans;
    vector<int> preorderTraversal(TreeNode* root) {
        if (root) {
            ans.push_back(root->val);
            preorderTraversal(root->left);
            preorderTraversal(root->right);
        }
        return ans;
    }
};
```

**非递归**

```cpp
class Solution {
public:
    vector<int> res;
    vector<int> preorderTraversal(TreeNode* root) {
        stack<TreeNode*> stack;
        if (root) {
            stack.push(root);
        } else {
            return res;
        }
        while (!stack.empty()) {
            TreeNode* cur = stack.top(); stack.pop();
            res.push_back(cur->val);
            if (cur->right) stack.push(cur->right);
            if (cur->left) stack.push(cur->left);
        }
        return res;
    }
};
```

### 中序遍历

#### [94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

**递归**

```cpp
class Solution {
public:
    vector<int> res;
    vector<int> inorderTraversal(TreeNode* root) {
        if (root) {
            inorderTraversal(root->left);
            res.push_back(root->val);
            inorderTraversal(root->right);
        }
        return res;
    }
};
```

**非递归**

```cpp
class Solution {
public:
    vector<int> res;
    vector<int> inorderTraversal(TreeNode* root) {
        stack<TreeNode*> stack;
        TreeNode* cur = root;
        while (!stack.empty() || cur) {
            while (cur) {
                stack.push(cur);
                cur = cur->left;
            }
            TreeNode *node = stack.top(); stack.pop();
            res.push_back(node->val);
            if (node->right) cur = node->right;
        }
        return res;
    }
};
```

###  后序遍历

#### [145. 二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

**递归**

```cpp
class Solution {
public:
    vector<int> res;
    vector<int> postorderTraversal(TreeNode* root) {
        if (root) {
            postorderTraversal(root->left);
            postorderTraversal(root->right);
            res.push_back(root->val);
        }
        return res;
    }
};
```

**非递归**

> 对于第一个栈来说，每次都是 **左 => 右** 入，**右 => 左** 出
>
> 对于第二个栈来说就刚好是 **右 => 左** 入，出的话就得到答案了

```cpp
class Solution {
public:
    vector<int> res;
    vector<int> postorderTraversal(TreeNode* root) {
        stack<TreeNode*> stack1, stack2;
        if (root) {
            stack1.push(root);
        } else {
            return res;
        }
        while (!stack1.empty()) {
            TreeNode* cur = stack1.top(); stack1.pop();
            stack2.push(cur);
            if (cur->left) stack1.push(cur->left);
            if (cur->right) stack1.push(cur->right);
        }
        while (!stack2.empty()) {
            res.push_back(stack2.top()->val);
            stack2.pop();
        }
        return res;
    }
};
```

### Morris 遍历



## 二叉搜索树相关

定义

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
```

#### [700. 二叉搜索树中的搜索](https://leetcode-cn.com/problems/search-in-a-binary-search-tree/)

**递归**

```cpp
class Solution {
public:
    TreeNode* searchBST(TreeNode* root, int val) {
        if (root == NULL) return root;
        if (val < root->val) return searchBST(root->left, val);
        if (val > root->val) return searchBST(root->right, val);

        return root;
    }
};
```

**非递归**

```cpp
class Solution {
public:
    TreeNode* searchBST(TreeNode* root, int val) {
        if (root == NULL) return root;
        while (root != NULL && root->val != val) {
            if (val < root->val) root = root->left;
            else if (val > root->val) root = root->right;
        }
        return root;
    }
};
```

**拓展，非二叉搜索树**

```cpp
class Solution {
public:
    TreeNode* searchBST(TreeNode* root, int val) {
        if (root == NULL || root->val == val) return root;

        TreeNode* snodeLeft = searchBST(root->left, val);
        TreeNode* snodeRight = searchBST(root->right, val);

        if (snodeLeft != NULL) return snodeLeft;
        if (snodeRight != NULL) return snodeRight;  

        return NULL;
    }
};
```

#### [235. 二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (p->val < root->val && q->val < root->val) {
            root = lowestCommonAncestor(root->left, p, q);
        } else if (p->val > root->val && q->val > root->val) {
            root = lowestCommonAncestor(root->right, p, q);
        }
        return root;
    }
};
```

**公共祖先的拓展，非二叉搜索树 [236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)**

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (root == NULL || p == root || q == root) return root;
        TreeNode* leftAncestor = lowestCommonAncestor(root->left, p, q);
        TreeNode* rightAncestor = lowestCommonAncestor(root->right, p, q);

        if (leftAncestor && rightAncestor) return root;
        if (leftAncestor == NULL) return rightAncestor;
        return leftAncestor;
    }
};
```
## 浅谈树的递归

> 我单独准备总结一下`二叉树中的递归`，因为在树中的递归过程是很有意思的，因为树最主要的特点就是每一个节点也是一棵树。

