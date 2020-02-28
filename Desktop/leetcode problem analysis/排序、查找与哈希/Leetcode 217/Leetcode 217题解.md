# Leetcode 217题解

**题目描述：**给定一个整数数组，判断是否存在重复元素。如果任何值在数组中出现至少两次，函数返回 true。如果数组中每个元素都不相同，则返回 false。

**分析：**这是查找类型的问题，由于题目中的“判断是否重复”字眼，可以推之，这题使用哈希来解决比较容易。

**易错点：**

1. 题目中说是**“整型数组”，那么就意味着可能会出现负数，如果使用除留取余法的话，需要先将负数转换为正数来处理。**
2. leetcode经典坑之一：**数组为空数组的情况**

**代码：**

```c++
typedef struct Node {
    int val;
    struct node *next;
} Node;

typedef struct HashTable {
    Node **data;
    int size;
} HashTable;

Node *initNode(int val) {
    Node *p = (Node *) malloc(sizeof(Node));
    p->val = val;
    p->next = NULL;
    return p;
}

HashTable *initHashTable(int size) {
    HashTable *h = (HashTable *) malloc(sizeof(HashTable));
    h->data = (Node **) calloc(sizeof(Node *), (size << 1));
    h->size = size;
    return h;
}

int find(HashTable *h, int val) {
    if (!h->data[(val & 0x7fffffff) % h->size]) return 0;
    Node *head = h->data[(val & 0x7fffffff)% h->size]; 
    while (head && head->val != val) head = head->next; 
    if (head) return 1;
    return 0;
}

void insert(HashTable *h, int val) {
    Node *temp = initNode(val);
    temp->next = h->data[(val & 0x7fffffff) % h->size];
    h->data[(val & 0x7fffffff) % h->size] = temp;
}

bool containsDuplicate(int* nums, int numsSize){
    if (!numsSize) return false;
    HashTable *h = initHashTable(numsSize);
    for (int i = 0; i < numsSize; i++) {
        if (find(h, nums[i])) return true;
        insert(h, nums[i]);
    }
    return false;
}
```

