# Leetcode 219题解

**题目描述：**Given an array of integers and an integer k, find out whether there are two distinct indices i and j in the array such that nums[i] = nums[j] and the absolute difference between i and j is at most k.

**分析：**这是查找类型的题目，而且题目要求的是要找到值相同而下标之间差的绝对值最多为k，通过nums[i] = nums[j]，可以看出这是在找重复元素，因此，使用哈希会较为方便。

这题的中文描述有点问题，之后存在疑问的题目看英文的。

**代码：**

```c++
typedef struct Node {
    int ind, val;
    struct Node *next;
} Node;

typedef struct HashTable {
    Node **data;
    int size;
} HashTable;

Node *initNode(int ind, int val) {
    Node *p = (Node *) malloc(sizeof(Node));
    p->ind = ind;
    p->val = val;
    p->next = NULL;
    return p;
}

HashTable *initHashTable(int size) {
    HashTable *h = (HashTable *) malloc(sizeof(HashTable));
    h->data = (Node **) calloc(sizeof(Node *), size << 1);
    h->size = size;
    return h;
}

int find(HashTable *h, int ind, int val, int k) {
    int temp = val & 0x7ffffff;
    if (!h->data[temp % h->size]) return -1;
    Node *head = h->data[temp % h->size];
    int min = INT32_MAX;
    while (head) {
        if (head->val == val && head->ind < min) {
            // 这里就是找到了满足条件，返回-2表示已经找到了满足要求的
            if (ind - head->ind <= k) return -2;
            min = head->ind;
        }
        head = head->next;
    }
    if (min != INT32_MAX) return min;
    return -1;
}

void insert(HashTable *h, int ind, int val) {
    int temp = val & 0x7fffffff;
    Node *p = initNode(ind, val);
    p->next = h->data[temp % h->size];
    h->data[temp % h->size] = p;
}

bool containsNearbyDuplicate(int* nums, int numsSize, int k){
    if (!numsSize) return false;
    HashTable *h1 = initHashTable(numsSize);
    for (int i = 0; i < numsSize; i++) {
        int ind = find(h1, i, nums[i], k);
        // -1表示不存在，-2表示找到了满足要求的，其余的表示有但是不满足要求
        if (ind == -1) insert(h1, i, nums[i]);
        else if (ind == -2) return true;
        else {
            insert(h1, i, nums[i]);
        }
    }
    return false;
}

```

