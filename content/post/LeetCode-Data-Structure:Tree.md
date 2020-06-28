---
title: "LeetCode Data-Structure: Tree"
date: 2020-06-08T21:52:45+08:00
lastmod: 2020-06-08T21:52:45+08:00
draft: false
keywords: ["Tree"]
description: "LeetCode Data Structure Tree"
tags: ["Tree", "LeetCode", "Data Structure"]
categories: ["learn"]
author: "abcdlsj"
toc: true
---

> LeetCode 刷题总结：树

<!--more-->

> 博文 5 月 14 建的，十几天过去了，懒逼还没写完。。。不就是总结吗，明天一定写完（一定？）
>
> -\- 2020-06-01
>
> 尴尬，这两天考试 + 作业 + 实验报告
>
> -\- 2020-06-05
>
> 写差不多了，除了一些很难的题，唉，接下来还有好多实验报告和课设（06-22 告诉你，不是”好多“。。。）
>
> -\- 2020-06-08

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

## 生成二叉树

#### [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

```cpp
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        return build(preorder, inorder, 0, 0, inorder.size() - 1);
    }
    TreeNode* build(vector<int>& preorder, vector<int>& inorder, int root, int begin, int end) {
        if (begin > end) return NULL;
        
        int i = begin;
        while (i < end && inorder[i] != preorder[root]) i++;
        TreeNode* rootNode = new TreeNode(preorder[root]);
        rootNode->left = build(preorder, inorder, root + 1, begin, i - 1);
        rootNode->right = build(preorder, inorder, root + i - begin + 1, i + 1, end);

        return rootNode;
    }
};
```

#### [106. 从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

```cpp
class Solution {
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        return buildHelper(inorder, postorder, postorder.size() - 1, 0, inorder.size() - 1);
    }
    TreeNode* buildHelper(vector<int> inorder, vector<int> postorder, int root, int begin, int end) {
        if (begin > end) return nullptr;

        int i = begin;
        while (i < inorder.size() && inorder[i] != postorder[root]) i++;
        TreeNode* cur = new TreeNode(postorder[root]);
        cur->right = buildHelper(inorder, postorder, root - 1, i + 1, end);
        cur->left = buildHelper(inorder, postorder, root - (end - i) - 1, begin, i - 1);  
        return cur;
    } 
};
```

#### [449. 序列化和反序列化二叉搜索树](https://leetcode-cn.com/problems/serialize-and-deserialize-bst/)

> 先序遍历一遍，然后排序先序遍历，就是中序遍历了，然后就是 n105 题

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
class Codec {
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        string s;
        stack<TreeNode*> stack;
        if (root != nullptr) stack.push(root);
        while (!stack.empty()) {
            TreeNode* cur = stack.top(); stack.pop();
            s += to_string(cur->val) + " ";
            if (cur->right) stack.push(cur->right);
            if (cur->left) stack.push(cur->left);
        }
        s = s.substr(0, s.size() - 1);
        return s;
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        vector<int> preorder;
        stringstream ss(data);
        string tmp;
        while (ss >> tmp) {
            int t = stoi(tmp);
            preorder.push_back(t);
        }

        vector<int> inorder(preorder);
        sort(inorder.begin(), inorder.end());

        return build(preorder, inorder, 0, 0, inorder.size() - 1);
    }
    
    TreeNode* build(vector<int>& preorder, vector<int>& inorder, int root, int begin, int end) {
        if (begin > end) return NULL;
        int i = begin;
        while (i < end && inorder[i] != preorder[root]) i++;
        TreeNode* rootNode = new TreeNode(preorder[root]);
        rootNode->left = build(preorder, inorder, root + 1, begin, i - 1);
        rootNode->right = build(preorder, inorder, root + i - begin + 1, i + 1, end);
        return rootNode;
    }
};

// Your Codec object will be instantiated and called as such:
// Codec codec;
// codec.deserialize(codec.serialize(root));
```

#### [297. 二叉树的序列化与反序列化](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)

> 这个这样写会简单些，用先序遍历来序列化二叉树

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
class Codec {
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        if (root == nullptr) return "#_";

        string res = to_string(root->val) + "_";
        res += serialize(root->left);
        res += serialize(root->right);
        return res;
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        stringstream ss(data);
        string tmp;
        queue<string> queue;

        while (getline(ss, tmp, '_')) queue.push(tmp);
        return helper(queue);
    }

    TreeNode* helper(queue<string>& queue) {
        string val = queue.front(); queue.pop();

        if (val == "#") return nullptr;
        TreeNode* head = new TreeNode(stoi(val));
        head->left = helper(queue);
        head->right = helper(queue);
        return head;
    }
};

// Your Codec object will be instantiated and called as such:
// Codec codec;
// codec.deserialize(codec.serialize(root));
```

## 二叉树相关

> 部分是二叉搜索树，部分不是，二叉搜索树只是特殊的二叉树而已；二叉树的方法适用于二叉搜索树，反之则不可以
>
> 一个方法或者思路，如果它在二叉搜索树这种特定情况下不起作用，那么对于一般二叉树来说它肯定不起作用

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
#### [面试题 04.06. 后继者](https://leetcode-cn.com/problems/successor-lcci/)

> 二叉搜索树的后继者，也就是找中序遍历的后一个节点

**易懂写法**

```cpp
class Solution {
public:
    TreeNode* inorderSuccessor(TreeNode* root, TreeNode* p) {
        TreeNode *res = nullptr, *pre = nullptr;
        inorderTraversal(root, pre, p, res);
        return res;
    }

    void inorderTraversal(TreeNode* root, TreeNode*& pre, TreeNode* p, TreeNode*& res) {
        if (root == nullptr || res != nullptr) return;
        inorderTraversal(root->left, pre, p, res);
        if (pre == p) res = root;
        pre = root;
        inorderTraversal(root->right, pre, p, res);
    }
};
```

**简洁写法**

```cpp
class Solution {
public:
    TreeNode* inorderSuccessor(TreeNode* root, TreeNode* p) {
        if (root == nullptr) return nullptr;
        TreeNode* res = inorderSuccessor(root->left, p);
        if (res != nullptr) return res;
        if (root->val > p->val) return root;
        return inorderSuccessor(root->right, p);
    }
};
```

其中 `root` 节点相当于遍历了整个树的节点，所以只要找到大于 `p->val` 的节点，就返回，就有 `res != nullptr`，之后的遍历全部都会返回 `res`；

这个方法非常巧妙的用了递归

#### [面试题33. 二叉搜索树的后序遍历序列](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)

> 二叉搜索树的后序遍历

```cpp
class Solution {
public:
    bool verifyPostorder(vector<int>& postorder) {
        return isVerify(postorder, 0, postorder.size() - 1);
    }
    bool isVerify(vector<int>& postorder, int left, int right) {
        if (left > right) return true;

        int idx = left, rootValue = postorder[right];
        while (idx < right && postorder[idx] < rootValue) idx++;

        for (int i = idx; i < right; i++) {
            if (postorder[i] < rootValue) {
                return false;
            }
        }

        if (!isVerify(postorder, left, idx - 1) || !isVerify(postorder, idx, right - 1)) {
            return false;
        }

        return true;
    }
};
```

#### [116. 填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

> 对于这道题，`最小的事情就是连接 node->left 和 node->right`，但是这样会出现中间的一部分没法连接，所以需要处理一下

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

class Solution {
public:
    Node* connect(Node* root) {
        if (root == nullptr) return nullptr;
        
        Node* le = connect(root->left);
        Node* ri = connect(root->right);
        while (le && ri) {
            le->next = ri;
            le = le->right;
            ri = ri->left;
        }
        
        return root;
    }
};
```

#### [117. 填充每个节点的下一个右侧节点指针 II](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/)

> 借助层次遍历能很快的写出来

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

class Solution {
public:
    Node* connect(Node* root) {
        queue<Node*> queue;
        if (root != nullptr) queue.push(root);

        while (!queue.empty()) {
            int size = queue.size();
            while (size--) {
                Node* cur = queue.front(); queue.pop();
                if (size) {
                    Node* tmp = queue.front();
                    cur->next = tmp;
                }

                if (cur->left) queue.push(cur->left);
                if (cur->right) queue.push(cur->right);
            }
        }

        return root;
    }
};
```

## 浅谈树的递归

> 我单独准备总结一下`二叉树中的递归`，因为在树中的递归过程是很有意思的，因为树最主要的特点就是每一个节点也是一棵树。
>
> 对于二叉树的题来说，一般思路就是 **选择一个节点要做的事情（最小的事情），然后递归**

> 先看这道题

#### [104. 二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

> 浅显易懂

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        return root == nullptr? 0 : max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
};
```

拓展 [110. 平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)

> 题目中判断平衡二叉树的条件是：一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过1
>
> 前面知道了怎么求树高度，那么这道题要做的`最小的事情`可以变成判断 `abs(Depth(LeftTree) - Depth(RightTree)) <= 1` ，然后呢？单独这样是不行的，因为还要依次判断每个子树
>
> 代码如下：

```cpp
class Solution {
public:
    bool isBalanced(TreeNode* root) {
        return root == nullptr? true : abs(getDepth(root->left) - getDepth(root->right)) <= 1 && isBalanced(root->left) && isBalanced(root->right);
    }
    int getDepth(TreeNode* node) {
        return node == nullptr? 0 : max(getDepth(node->left), getDepth(node->right)) + 1;
    }
};
```

#### [226. 翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

> 目的是为了翻转这棵树，那么核心就是交换节点的左右子树，那么就能写出下面代码了。

```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == nullptr) return nullptr;

        swap(root->left, root->right);
        root->left = invertTree(root->left);
        root->right = invertTree(root->right);

        return root;
    }
};
```

## 总结

> 没啥好写的，希望继续努力（少拖延）！