# 104. Maximum Depth of a Binary Tree
Given the root of a binary tree, return its maximum depth.

A binary tree's maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

## Approach

There are two main ways to approach this problem: a traditional loop, counting and tracking the nodes in each layer, and a recursive solution which counts the depth along each branch and returns the greatest one. Both will be reasonably performant since they follow the same general algorithm, but the traditional loop will consume considerably more memory by using a separate data structure to keep track of the nodes visited in each layer to retain references to nodes in the following layer. Given this, it makes most sense to implement a recursive algorithm which descends through all of the nodes, and once it reaches a leaf node, starts counting up until it gets to the top node. At each "junction," the greatest of the two branches will be considered the node's depth.

## Code

```c++
class Solution {
public:

    int result = 0;

    int maxDepth(TreeNode* root) {
        if (!root) return 0;
        if (!(root->right) && !(root->left)) return 1;
        int left = root->left? maxDepth(root->left) : 0;
        int right = root->right? maxDepth(root->right) : 0;
        return left > right ? left + 1 : right + 1;
    }
};
```
