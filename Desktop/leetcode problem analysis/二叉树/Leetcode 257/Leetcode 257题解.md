# Leetcode 257题解

**题目描述：**给定一个二叉树，返回所有从根节点到叶子节点的路径。

**分析：**二叉树的题目通常是具有完全包含关系的，一个二叉树从根节点到叶子节点的所有路径，可以看作是根节点与根节点左右孩子到叶子节点的所有路径的组合，这样就分析出了这道题的完全包含关系。

**易错点与注意点：**

在写字符串时采用了`sprintf`函数，**==使用`sprintf`函数向字符串中写数据时，需要注意写入数据中如果包含数字，注意数字长度，防止数字长度过长导致写入字符串超出空间，报段错误或者`heap-buffer-overflow`==。为了解决这个问题，可以将字符串空间开大，保证有足够的空间存储数字。**

**代码：**

```c++
// 计算路径数，就是计算叶子节点数量
int count_path(struct TreeNode *root) {
    if (!root->left && !root->right) return 1;
    if (!root->left) return count_path(root->right); 
    if (!root->right) return count_path(root->left);
    return count_path(root->left) + count_path(root->right);
}

//最大深度，在开辟字符串空间时使用
int max_height(struct TreeNode *root) {
    if (!root) return 0;
    int left = max_height(root->left);
    int right = max_height(root->right);
    return 1 + (left > right ? left : right);
}

char ** binaryTreePaths(struct TreeNode* root, int* returnSize){
    // 空节点返回NULL，返回路径数为0
    if (!root) {
        *returnSize = 0;
        return NULL;
    }
    // 叶子节点返回路径数为1, 路径上就只有叶子节点的数据。
    if (!root->left && !root->right) {
        *returnSize = 1;
        char **ret = (char **) malloc(sizeof(char *) * 1);
        ret[0] = (char *) malloc(sizeof(char) * 5);
        sprintf(ret[0], "%d", root->val);
        return ret;
    }
    int left, right;
    char **left_path, **right_path;
    left_path = binaryTreePaths(root->left, &left);
    right_path = binaryTreePaths(root->right, &right);
    int path_cnt = count_path(root), h = max_height(root);
   	// 新建返回的字符串数组，并初始化
    char **ret = (char **) malloc(sizeof(char *) * path_cnt);
    for (int i = 0; i < path_cnt; i++) {
        ret[i] = (char *) calloc(sizeof(char), 10 * h);    
        sprintf(ret[i], "%d->", root->val);
    }
    int i, j;
    // 根据左右孩子返回的路径数组，生成新的返回路径数组
    for (i = 0; i < left; i++) strcat(ret[i], left_path[i]);
    for (int j = 0; j < right; j++) strcat(ret[i + j], right_path[j]);
    *returnSize = left + right;
    return ret;
}
```

