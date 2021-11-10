# B树

B树是多叉平衡搜索树，树高通常很低，所以查询效率很高，一般来说，我们规定一个正整数$m$，B树需要满足如下条件：

+ 除了根节点，所有节点的value (or key)数量在$[m,2m]$之间。
+ 需要满足搜索树的条件，此略。

关于B树的性质：根据B树插入和删除操作的定义以及需要满足的条件，可以证明B tree所有叶子节点的高度都是一样的。

下面对B树的数据结构与代码实现进行分析。

## 1. 结构定义：

```c++
const int M = 2;

struct Node {
    int cnt;
    int vals[2 * M + 1];
    Node *next[2 * M + 2];
};


struct BTree {
    Node *_root;
};
```

这里的`M`就是与节点中key / value数量限制相关的m。



## 2. 查找

B树的查找操作和一般二叉搜索树一样，不过需要注意在单个节点中寻找第一个大于等于待查找val的位置，通常使用二分查找提高查找速度，该操作在插入和删除中也会用到，因为在实际代码中M的值往往会很大。

```c++
int get_ind(Node *root, int val) {
    int l = 0, r = root->cnt;
    while (l < r) {
        int mid = (l + r) / 2;
        if (root->vals[mid] < val) l = mid + 1;
        else r = mid;
    }
    return l;
}

bool search(Node *root, int val) {
    if (!root) return false;
    int ind = get_ind(root, val);
    if (root->vals[ind] == val) return true;
    return search(root->next[ind], val);
}
```



## 3. 插入 + 调整的split操作

B树的插入操作与查找过程类似，而且插入的一定是叶子节点。不过插入过程以及对应的维护平衡的过程中，会遇到节点中value / key的数量超过$2M$的情况，即有$2M +1$个value / key，此时，此时需要对节点进行split操作，将一个$2M+1$个value的节点分解成2个M个value的节点和一个中间值mid，mid可以插入父节点对应位置，回溯过程中调整。

注意root需要单独判断调整。

```c++
void insert(int val) {
    _root = insert(_root, val);
    if (_root->cnt == (2 * M + 1)) {
        auto &&[left, right, mid] = split(_root);
        _root->clear();
        _root->vals[0] = mid;
        _root->cnt++;
        _root->next[0] = left;
        _root->next[1] = right;
    }
}

Node *insert(Node *root, int val) {
    if (!root) return root;
    int ind = get_ind(root, val);
    if (root->vals[ind] == val) return root;
    if (!root->next[ind]) root->insert(ind, val);
    else root->next[ind] = insert(root->next[ind], val);
    root = maintain(root, ind);
    return root;
}

tuple<Node *, Node *, int> split(Node *node) {
    cout << "split : " << node << endl;
    int mid = node->vals[M];
    Node *left = new Node();
    Node *right = new Node();
    for (int i = 0; i < M; i++) left->vals[i] = node->vals[i];
    for (int i = 0; i <= M; i++) left->next[i] = node->next[i];
    left->cnt = M;
    for (int i = 0; i < M; i++) right->vals[i] = node->vals[i + M + 1];
    for (int i = 0; i <= M; i++)  right->next[i] = node->next[i + M + 1];
    right->cnt = M;
    return {left, right, mid};
}

Node *merge(int mid, Node *left, Node *right) {
    cout << "merge " << "mid = " << mid << "left = " << left << " right = " << right << endl;
    Node *temp = new Node();
    for (int i = 0; i < left->cnt; i++) temp->vals[i] = left->vals[i];
    temp->vals[left->cnt] = mid;
    for (int i = 0; i < right->cnt; i++) temp->vals[i + left->cnt + 1] = right->vals[i];
    temp->cnt = left->cnt + right->cnt + 1;
    for (int i = 0; i <= left->cnt; i++) temp->next[i] = left->next[i];
    for (int i = 0; i <= right->cnt; i++) temp->next[i + left->cnt + 1] = right->next[i];
    return temp;
}

Node *maintain(Node *root, int ind) {
    if (!root || !root->next[ind]) return root;
    cout << root <<  " ind = " << ind << endl;
    if (root->next[ind]->cnt == (2 * M + 1)) {
        auto &&[left, right, mid] = split(root->next[ind]);
        root->insert(ind, mid);
        delete root->next[ind];
        root->next[ind] = left;
        root->next[ind + 1] = right;
        return root;
    }
    if (root->next[ind]->cnt == (M - 1)) {
        if (ind < root->cnt) {
            if (root->next[ind + 1]->cnt > M) {
                cout << root->next[ind] << endl;
                root->next[ind]->insert(M - 1, root->vals[ind]);
                cout << root->next[ind] << endl;
                root->vals[ind] = root->next[ind + 1]->vals[0];
                root->next[ind]->next[M] = root->next[ind + 1]->next[0];
                root->next[ind + 1]->erase(0);
            } else {
                Node *temp = merge(root->vals[ind], root->next[ind], root->next[ind + 1]);
                delete root->next[ind];
                delete root->next[ind + 1];
                root->erase(ind);
                root->next[ind] = temp;
            }
        } else {
            if (root->next[ind - 1]->cnt > M) {
                int t = root->next[ind - 1]->cnt;
                root->next[ind]->insert(0, root->vals[ind - 1]);
                root->vals[ind - 1] = root->next[ind - 1]->vals[t - 1];
                root->next[ind]->next[0] = root->next[ind - 1]->next[t];
                root->next[ind - 1]->erase(t - 1);
            } else {
                Node *temp = merge(root->vals[ind - 1], root->next[ind - 1], root->next[ind]);
                delete root->next[ind - 1];
                delete root->next[ind];
                root->erase(root->cnt - 1);
                root->next[root->cnt] = temp;
            }
        }
    }
    return root;
}
```



## 4. 删除 + 调整中的merge操作

B树的删除操作也和正常的二叉搜索树类似： 

+ 如果要删除的值在叶子节点，直接删除就行。
+ 如果要删除的值不在叶子节点，找到节点前驱，前驱覆盖该值，再进入对应子树删除前驱。

而在删除操作与调整操作中会出现节点中value / key的数量小于$M$的情况，即$M-1$。此时如果对应的ind不是最后有效的指针域，则代表可以从右侧指针域中找数字或者合并： 

+ 右侧指针域元素数量大于M，将`root->vals[ind]`插入`root->next[ind]`最后，然后`root->next[ind + 1]->vals[0]`赋给`root->vals[ind]`，再调整指针域，将`root->next[ind + 1]->next[0]`给左侧的兄弟，之后要删除`root->next[ind + 1]`中的第一个值与指针域。
+ 右侧指针域中元素数量等于M，合并`root->next[ind], root->vals[ind], root->next[ind + 1]`，具体操作略。

如果ind是最后有效的指针域，则和前面的指针域及对应上方值执行类似操作。

注意root需要单独判断处理。

```c++
Node *merge(int mid, Node *left, Node *right) {
    cout << "merge " << "mid = " << mid << "left = " << left << " right = " << right << endl;
    Node *temp = new Node();
    for (int i = 0; i < left->cnt; i++) temp->vals[i] = left->vals[i];
    temp->vals[left->cnt] = mid;
    for (int i = 0; i < right->cnt; i++) temp->vals[i + left->cnt + 1] = right->vals[i];
    temp->cnt = left->cnt + right->cnt + 1;
    for (int i = 0; i <= left->cnt; i++) temp->next[i] = left->next[i];
    for (int i = 0; i <= right->cnt; i++) temp->next[i + left->cnt + 1] = right->next[i];
    return temp;
}

Node *maintain(Node *root, int ind) {
    if (!root || !root->next[ind]) return root;
    cout << root <<  " ind = " << ind << endl;
    if (root->next[ind]->cnt == (2 * M + 1)) {
        auto &&[left, right, mid] = split(root->next[ind]);
        root->insert(ind, mid);
        delete root->next[ind];
        root->next[ind] = left;
        root->next[ind + 1] = right;
        return root;
    }
    if (root->next[ind]->cnt == (M - 1)) {
        if (ind < root->cnt) {
            if (root->next[ind + 1]->cnt > M) {
                cout << root->next[ind] << endl;
                root->next[ind]->insert(M - 1, root->vals[ind]);
                cout << root->next[ind] << endl;
                root->vals[ind] = root->next[ind + 1]->vals[0];
                root->next[ind]->next[M] = root->next[ind + 1]->next[0];
                root->next[ind + 1]->erase(0);
            } else {
                Node *temp = merge(root->vals[ind], root->next[ind], root->next[ind + 1]);
                delete root->next[ind];
                delete root->next[ind + 1];
                root->erase(ind);
                root->next[ind] = temp;
            }
        } else {
            if (root->next[ind - 1]->cnt > M) {
                int t = root->next[ind - 1]->cnt;
                root->next[ind]->insert(0, root->vals[ind - 1]);
                root->vals[ind - 1] = root->next[ind - 1]->vals[t - 1];
                root->next[ind]->next[0] = root->next[ind - 1]->next[t];
                root->next[ind - 1]->erase(t - 1);
            } else {
                Node *temp = merge(root->vals[ind - 1], root->next[ind - 1], root->next[ind]);
                delete root->next[ind - 1];
                delete root->next[ind];
                root->erase(root->cnt - 1);
                root->next[root->cnt] = temp;
            }
        }
    }
    return root;
}

int predecessor(Node *root, int ind) {
    Node *temp = root->next[ind];
    while (temp->next[temp->cnt]) temp = temp->next[temp->cnt];
    return temp->vals[temp->cnt - 1];
}

void erase(int val) {
    _root = erase(_root, val);
    if ((_root->cnt == 0) && _root->next[0]) {
        Node *temp = _root->next[0];
        delete _root;
        _root = temp;
    }
}

Node *erase(Node *root, int val) {
    if (!root) return root;
    int ind = get_ind(root, val);
    if (root->vals[ind] == val) {
        if (!root->next[ind]) {
            cout << root << endl;
            root->erase(ind);
            cout << root << endl;
        }
        else {
            int pre = predecessor(root, ind);
            root->vals[ind] = pre;
            root->next[ind] = erase(root->next[ind], pre);
        }
    } else root->next[ind] = erase(root->next[ind], val);
    root = maintain(root, ind);
    return root;
}
```





## 5. 完整代码

```c++
#include <iostream>
#include <string>
#include <vector>
#include <queue>
#include <stack>
#include <map>
#include <set>
#include <unordered_map>
#include <unordered_set>
#include <algorithm>
#include <cstring>
#include <cstdlib>
#include <tuple>
using namespace std;

const int M = 2;

struct Node {
    int cnt;
    int vals[2 * M + 1];
    Node *next[2 * M + 2];

    Node() : cnt(0) {
        memset(vals, 0, sizeof(vals));
        memset(next, 0, sizeof(next));
    }

    void insert(int ind, int val) {
        for (int i = cnt - 1; i >= ind; i--) vals[i + 1] =  vals[i];
        for (int i = cnt; i >= ind; i--) next[i + 1] = next[i];
        vals[ind] = val;
        cnt++;
    }

    void erase(int ind) {
        if (ind < cnt - 1) {
            for (int i = ind; i < cnt - 1; i++) vals[i] = vals[i + 1];
            for (int i = ind; i < cnt; i++) next[i] = next[i + 1];
        }
        vals[cnt - 1] = 0;
        next[cnt] = nullptr;
        cnt--;
    }

    void clear() {
        cnt = 0;
        memset(vals, 0, sizeof(vals));
        memset(next, 0, sizeof(next));
    }
};

ostream &operator<<(ostream &out, Node *node) {
    out << "(";
    for (int i = 0; i < node->cnt; i++) out << node->vals[i] << " ";
    out << ")";
    return out;
}

struct BTree {
    Node *_root;

    BTree() : _root(new Node()) {}

    bool search(int val) {
        return search(_root, val);
    }

    int get_ind(Node *root, int val) {
        int l = 0, r = root->cnt;
        while (l < r) {
            int mid = (l + r) / 2;
            if (root->vals[mid] < val) l = mid + 1;
            else r = mid;
        }
        return l;
    }

    bool search(Node *root, int val) {
        if (!root) return false;
        int ind = get_ind(root, val);
        if (root->vals[ind] == val) return true;
        return search(root->next[ind], val);
    }

    void insert(int val) {
        _root = insert(_root, val);
        if (_root->cnt == (2 * M + 1)) {
            auto &&[left, right, mid] = split(_root);
            _root->clear();
            _root->vals[0] = mid;
            _root->cnt++;
            _root->next[0] = left;
            _root->next[1] = right;
        }
    }

    Node *insert(Node *root, int val) {
        if (!root) return root;
        int ind = get_ind(root, val);
        if (root->vals[ind] == val) return root;
        if (!root->next[ind]) root->insert(ind, val);
        else root->next[ind] = insert(root->next[ind], val);
        root = maintain(root, ind);
        return root;
    }

    tuple<Node *, Node *, int> split(Node *node) {
        cout << "split : " << node << endl;
        int mid = node->vals[M];
        Node *left = new Node();
        Node *right = new Node();
        for (int i = 0; i < M; i++) left->vals[i] = node->vals[i];
        for (int i = 0; i <= M; i++) left->next[i] = node->next[i];
        left->cnt = M;
        for (int i = 0; i < M; i++) right->vals[i] = node->vals[i + M + 1];
        for (int i = 0; i <= M; i++)  right->next[i] = node->next[i + M + 1];
        right->cnt = M;
        return {left, right, mid};
    }

    Node *merge(int mid, Node *left, Node *right) {
        cout << "merge " << "mid = " << mid << "left = " << left << " right = " << right << endl;
        Node *temp = new Node();
        for (int i = 0; i < left->cnt; i++) temp->vals[i] = left->vals[i];
        temp->vals[left->cnt] = mid;
        for (int i = 0; i < right->cnt; i++) temp->vals[i + left->cnt + 1] = right->vals[i];
        temp->cnt = left->cnt + right->cnt + 1;
        for (int i = 0; i <= left->cnt; i++) temp->next[i] = left->next[i];
        for (int i = 0; i <= right->cnt; i++) temp->next[i + left->cnt + 1] = right->next[i];
        return temp;
    }
    
    Node *maintain(Node *root, int ind) {
        if (!root || !root->next[ind]) return root;
        cout << root <<  " ind = " << ind << endl;
        if (root->next[ind]->cnt == (2 * M + 1)) {
            auto &&[left, right, mid] = split(root->next[ind]);
            root->insert(ind, mid);
            delete root->next[ind];
            root->next[ind] = left;
            root->next[ind + 1] = right;
            return root;
        }
        if (root->next[ind]->cnt == (M - 1)) {
            if (ind < root->cnt) {
                if (root->next[ind + 1]->cnt > M) {
                    cout << root->next[ind] << endl;
                    root->next[ind]->insert(M - 1, root->vals[ind]);
                    cout << root->next[ind] << endl;
                    root->vals[ind] = root->next[ind + 1]->vals[0];
                    root->next[ind]->next[M] = root->next[ind + 1]->next[0];
                    root->next[ind + 1]->erase(0);
                } else {
                    Node *temp = merge(root->vals[ind], root->next[ind], root->next[ind + 1]);
                    delete root->next[ind];
                    delete root->next[ind + 1];
                    root->erase(ind);
                    root->next[ind] = temp;
                }
            } else {
                if (root->next[ind - 1]->cnt > M) {
                    int t = root->next[ind - 1]->cnt;
                    root->next[ind]->insert(0, root->vals[ind - 1]);
                    root->vals[ind - 1] = root->next[ind - 1]->vals[t - 1];
                    root->next[ind]->next[0] = root->next[ind - 1]->next[t];
                    root->next[ind - 1]->erase(t - 1);
                } else {
                    Node *temp = merge(root->vals[ind - 1], root->next[ind - 1], root->next[ind]);
                    delete root->next[ind - 1];
                    delete root->next[ind];
                    root->erase(root->cnt - 1);
                    root->next[root->cnt] = temp;
                }
            }
        }
        return root;
    }

    int predecessor(Node *root, int ind) {
        Node *temp = root->next[ind];
        while (temp->next[temp->cnt]) temp = temp->next[temp->cnt];
        return temp->vals[temp->cnt - 1];
    }

    void level_print() {
        using PNI = pair<Node *, int>;
        queue<PNI> que;
        que.push(PNI(_root, 0));
        while (!que.empty()) {
            auto temp = que.front();
            que.pop();
            cout << temp.first << " " << temp.second << endl;
            for (int i = 0; i <= temp.first->cnt; i++) {
                if (!temp.first->next[i]) break;
                que.push(PNI(temp.first->next[i], temp.second + 1));
            }
        }
    }

    void erase(int val) {
        _root = erase(_root, val);
        if ((_root->cnt == 0) && _root->next[0]) {
            Node *temp = _root->next[0];
            delete _root;
            _root = temp;
        }
    }

    Node *erase(Node *root, int val) {
        if (!root) return root;
        int ind = get_ind(root, val);
        if (root->vals[ind] == val) {
            if (!root->next[ind]) {
                cout << root << endl;
                root->erase(ind);
                cout << root << endl;
            }
            else {
                int pre = predecessor(root, ind);
                root->vals[ind] = pre;
                root->next[ind] = erase(root->next[ind], pre);
            }
        } else root->next[ind] = erase(root->next[ind], val);
        root = maintain(root, ind);
        return root;
    }
};


int main() {
    BTree btree;
    int n;
    cin >> n;
    for (int i = 1; i <= n; i++) btree.insert(i);
    btree.level_print();
    cout << endl;
    while (1) {
        cout << "value to erase : ";
        cin >> n;
        btree.erase(n);
        btree.level_print();
        cout << endl;
    }
    return 0;
}
```





