# Leetcode 669题解

**题目描述：**

给定一个二叉搜索树，同时给定最小边界L 和最大边界 R。通过修剪二叉搜索树，使得所有节点的值在[L, R]中 (R>=L) 。你可能需要改变树的根节点，所以结果应当返回修剪好的二叉搜索树的新的根节点。

**分析：**

简单的二叉搜索树的删除操作。

**代码：**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     struct TreeNode *left;
 *     struct TreeNode *right;
 * };
 */
// 获取节点前驱
struct TreeNode *predecessor(struct TreeNode *root) {
    if (!root) return NULL;
    struct TreeNode *left = root->left;
    while (left->right) left = left->right;
    return left;
}

// 正常的删除操作
struct TreeNode *delete_node(struct TreeNode *root, int val) {
    if (!root) return ;
    root->left = delete_node(root->left, val);
    root->right = delete_node(root->right, val);
    if (!root->left || !root->right) {
        struct TreeNode *ret = root->left ? root->left : root->right;
        free(root);
        return ret;
    } else {
        struct TreeNode *temp = predecessor(root);
        root->val = temp->val;
        root->left = delete_node(root->left, temp->val);
    }
    return root;
}

struct TreeNode* trimBST(struct TreeNode* root, int L, int R){
    if (!root) return root;
    root->left = trimBST(root->left, L, R);
    root->right = trimBST(root->right, L, R);
    if (root->val < L || root->val > R) {
        if (!root->left || !root->right) {
            struct TreeNode *ret = root->left ? root->left : root->right;
            free(root);
            return ret;
        } else {
            // 对于度为2的节点还是得要单独调用删除函数
            struct TreeNode *temp = predecessor(root);
            root->val = temp->val;
            root->left = delete_node(root->left, temp->val);
        }
    }
    return root;
}
```

