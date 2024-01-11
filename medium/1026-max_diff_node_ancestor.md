# 1026. Maximum Difference Between Node and Ancestor

Given the root of a binary tree, find the maximum value `v` for which there exist different nodes `a` and `b` where `v = |a.val - b.val|` and `a` is an ancestor of `b`.

A node `a` is an ancestor of `b` if either: any child of `a` is equal to `b` or any child of `a` is an ancestor of `b`.

(Available on LeetCode [here](https://leetcode.com/problems/maximum-difference-between-node-and-ancestor/description))

## Intuition

As with the vast majority of tree problems, a recursive solution seems ideal for this problem at first glance. Recursion would offer a performant, readable, and comprehensive way to traverse any given B-tree. Since any ancestor node could be half of the solution for any of its descendant nodes, a naive solution would be to keep track of the values of all ancestor nodes and compare all of them to each of their descendant nodes. This would yield dismal performance, however, with a time complexity of $O(n^2)$.

Quite fortunately, there is a much better and more intuitive solution. By observing that the maximal difference between any two nodes in a branch is necessarily between the greatest and least values along that branch, we can reduce the number of nodes to which we must compare each descendant node to only 2: the greatest value encountered so far, and the least. If the node we're observing is outside of the range of those two values, we've found a new solution along its localized branch! If it isn't, we can safely ignore it and move on to its child nodes.

## Code

```c++
class Solution {
public:
    int maxAncestorDiff(TreeNode* root) {
        return recurse(root, root->val, root->val);
    }

    int recurse(TreeNode* target, int ancMin, int ancMax) {
        ancMin = min(ancMin, target->val);
        ancMax = max(ancMax, target->val);

        return max({ancMax - target->val, target->val - ancMin, target->left? recurse(target->left, ancMin, ancMax) : 0, target->right? recurse(target->right, ancMin, ancMax) : 0});
    }
};
```

This was my proof-of-concept code for the algorithm described above. A single recursive function observes each node and determines whether it is the greatest or least node encountered in its branch. If it is, the newly found difference between it and its extrema counterpart is returned. Otherwise, the existing maximal solution is returned.

It has some shortcomings, though: the use of the initializer list variant of the `max` function is suboptimally memory hungry and adds to the call stack unnecessarily. It also isn't very nice to read, which is arguably considerably more important. To address those concerns, I wrote a second implementation which calls no external math functions, and does all of its comparisons locally.

```c++
class Solution {
public:

    int result = 0;

    int maxAncestorDiff(TreeNode* root) {
        recurse(root, root->val, root->val);
        return result;
    }

    void recurse(TreeNode* target, int ancMin, int ancMax) {

        if (!target) return;

        if (target->val > ancMax) {
            ancMax = target->val;
        }
        else if (target->val < ancMin) {
            ancMin = target->val;
        }

        int localResult = ancMax - ancMin;
        if (localResult > result) result = localResult;

        recurse(target->left, ancMin, ancMax);
        recurse(target->right, ancMin, ancMax);
    }
};
```

In addition to addressing the concerns above, the new solution doesn't pass back the optimal solution that it finds, and instead updates a property of its encapsulating `Solution` object. This isn't performance-critical in the age of out-of-ordder execution and good optimizing compilers, but it does avoid polluting the call stack without reason, and memory is money. By implementing those changes, this solution shaves 1.1 MB of memory use off of the LeetCode benchmarks.
