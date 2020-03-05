# Size Balanced Tree

Size Balanced Tree的思想和AVL树的思想类似，但是两者的自平衡条件不同。

## 一、Size Balanced Tree基本原理

### 1.自平衡条件

Size Balanced Tree维护的性质如下：对于每一个节点t，同时满足

$Size(t->left) \ge Size(t->right->left)$

$Size(t->left) \ge Size(t->right->right)$

$Size(t->right) \ge Size(t->left->left)$

$Size(t->right) \ge Size(t->left->right)$

换句话就是，**每个节点所在的子树的节点个数不小于其兄弟的两个孩子所在子树的节点个数。**这是对每个节点的左右子树的节点数量做了限制。



### 2.旋转操作

Size Balanced Tree的旋转操作也是由基本的左旋和右旋组成，这和AVL树一样。



### 3.失衡情况及处理

Size Balanced Tree的失衡情况与AVL类似，也是分成LL，LR，RR，RL型失衡,这里的含义是哪个孩子的哪棵子树的节点数量多。

#### (1)LL型失衡

失衡条件：$size[t->left->left] > size[t->right]$

和AVL树一样进行右旋操作，之后进行调整，需要调整的有两个节点。

**由于LL型在大右旋调整之后，新的根节点和新根节点的右孩子都发生了孩子的变换，必须对这两个节点进行维护。**

#### (2)LR型失衡

失衡条件：$size[t->left->right] > size[t->right]$

和AVL树一样进行左旋加右旋操作，操作的节点与操作顺序都一样，并且每次旋转之后都要调整。

**==实际上，平衡二叉树的旋转后调整正确而通用的写法是写在旋转操作的函数之外。==**

**LR型失衡是先进行小左旋，之后进行大右旋，新的根节点和其左右孩子的子树都发生了改变，所以需要维护。**

#### (3)RR型失衡

失衡条件：$size[t->right->right] > size[t->left]$

和AVL树一样进行左旋操作，之后进行调整。

旋转之后的维护与LL型类似。

#### (4)RL型失衡

失衡条件：$size[t->right->left]>size[t->left]$

和AVL树一样进行右旋加左旋操作，每次旋转之后进行调整。

旋转之后的维护与LR型类似。



**和AVL不一样的是，SBTree只有在插入时才可能触发调整，而不需要在删除节点以后进行调整。**



## 二、Size Balanced Tree结构定义

Size Balanced Tree的结构定义和AVL树的结构定义类似，只是AVL中的表示书高的项被换成了表示子树节点个数的项。

```c++
typedef struct Node {
    int value, cnt;
    struct Node *lchild, *rchild;
} Node;
```



## 三、Size Balanced Tree结构操作

Size Balanced Tree的结构操作基本上是和AVL树类似的。

### 1.Size Balanced Tree插入操作

```c++
void update(Node *root) {
    int l = root->lchild->cnt;
    int r = root->rchild->cnt;
    root->cnt = 1 + l + r;
}

// AVL树可以将旋转后处理操作放在旋转函数中是因为AVL树的平衡条件十分严格，并且在做出指定操作所需的旋转序列之后就确定是平衡的
// 但是SBTree并不是，因为在旋转操作之后SBTree中有节点的孩子发生了变化，需要先维护好这些节点，因此，在旋转后依旧可能会失衡的平衡二叉树，调整与更新操作放在旋转函数外面
Node *left_rotate(Node *root) {
    Node *temp = root->rchild;
    root->rchild = temp->lchild;
    temp->lchild = root;
    return temp;
}

Node *right_rotate(Node *root) {
    Node *temp = root->lchild;
    root->lchild = temp->rchild;
    temp->rchild = root;
    return temp;
}

Node *maintain(Node *root) {
    int ll, lr, rl, rr, lg, rg;
    ll = root->lchild->lchild->cnt;
    lr = root->lchild->rchild->cnt;
    rl = root->rchild->lchild->cnt;
    rr = root->rchild->rchild->cnt;
    // 由于旋转会造成节点的子树的改变，可能会引发失衡，需要对这些节点进行维护
    // 注意点，在所有旋转函数外部进行维护调整
    if (root->rchild->cnt < ll) {
        root = right_rotate(root);
        root->rchild = maintain(root->rchild);
        root = maintain(root);
    } else if (root->rchild->cnt < lr) {
        root->lchild = left_rotate(root->lchild);
        root = right_rotate(root);
        root->lchild = maintain(root->lchild);
        root->rchild = maintain(root->rchild);
        root = maintain(root);
    } else if (root->lchild->cnt < rr) {
        root = left_rotate(root);
        root->lchild = maintain(root->lchild);
        root = maintain(root);
    } else if (root->lchild->cnt < rl){
        root->rchild = right_rotate(root->rchild);
        root = left_rotate(root);
        root->lchild = maintain(root->lchild);
        root->rchild = maintain(root->rchild);
        root = maintain(root);
    }
    // 更新子树节点个数信息
    update(root);
    return root;
}

Node *insert(Node *root, int value) {
    if (root == NIL) return getNewNode(value);
    if (root->value == value) return root;
    if (root->value < value) root->rchild = insert(root->rchild, value);
    else root->lchild = insert(root->lchild, value);
    return maintain(root);
}
```





## 四、Size Balanced Tree 的应用

Size Balanced Tree和AVL树相比在均摊时间复杂度上没有差别，每次查询、插入、删除的时间复杂度都是$O(\log{n})$。

在实际运用中，Size Balanced Tree 在查询操作较多的情况下会有效率上的优势。

另外，Size Balanced Tree维护了每个节点所在子树的大小(子树内节点个数)，相比其他二叉查找树而言，更便于求解第k大元素，或者求解元素的秩等类似问题。

### 1. Size Balanced Tree求解第k大/小元素

由于Size Balanced Tree维护了节点所在子树的节点个数，加上平衡二叉排序树本身具有的中序遍历就可以获得顺序序列，根节点的左子树中元素都小于根节点，右子树中元素都大于根节点，这个性质，可以**通过根节点的左孩子所在子树的节点个数，加上1,就可以得到当前根节点的排序位数，进而可以采用类似二分查找的操作来进行查找。**

```c++
int select(SBTNode *node, int k) {
    int rank = node->lchild->size + 1;
    if (rank == k) {
        return node->data;
    } else if (k < rank) {
        return select(node->lchild, k);
    } else {
        return select(node->rchild, k - rank);
    }
}
```



