# Leetcode 23题解

**题目描述：**合并 *k* 个排序链表，返回合并后的排序链表。

**分析：**

第一思路：类似于归并排序，采用归并方法，时间复杂度为$O(nk)$，分了很多组会导致时间复杂度很高，是不合适的。

第二思路：使用归并的方法会导致链表重复了许多次无意义的比较和合并，对此可以使用堆，可以避免一堆数据被反复比较，此时的时间复杂度为$O(n\log{n})$。

**易错点与收获：**

1. 在debug的过程中，发现最终采用头插法的链表的最后一个元素的下一个指针不为空，所以得到一个教训：**链表使用头插法，刚开始和的头节点要设置为NULL。**
2. 堆的一个特性：**可以动态维护并且可以处理多个数据流，并且每个元素只会被处理一次**。相比之下，即使是归并排序，在处理多有序数据流时也很少会比堆优秀。

**代码：**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
typedef struct ListNode ListNode; 

typedef struct Heap {
    ListNode **data;
    int size, cnt;
} Heap;

Heap *init() {
    Heap *h = (Heap *) malloc(sizeof(Heap));
    h->data = (ListNode **) malloc(sizeof(ListNode *) * 1001);
    h->size = 1000;
    h->cnt = 0;
    return h;
}

void expand(Heap *h) {
    int expand_size = h->size;
    ListNode **p;
    while (expand_size) {
        p = (ListNode **) realloc(h->data, sizeof(ListNode *) * (h->size + expand_size + 5));
        if (p) break;
        expand_size >>= 1;
    }
    if (!p) return ;
    h->data = p;
    h->size += expand_size;
}

void swap(ListNode **a, ListNode **b) {
    ListNode *temp = *a;
    *a = *b;
    *b = temp;
}

void push_heap(Heap *h, ListNode *p) {
    if (h->size == h->cnt) expand(h);
    h->data[++h->cnt] = p;
    int ind = h->cnt;
    while (ind >> 1 && h->data[ind]->val > h->data[ind >> 1]->val) {
        swap(&h->data[ind], &h->data[ind >> 1]);
        ind >>= 1;
    }
}

ListNode *pop(Heap *h) {
    ListNode *ret = h->data[1];
    swap(&h->data[1], &h->data[h->cnt--]);
    int ind = 1;
    while ((ind << 1) <= h->cnt) {
        int temp = ind, l = ind << 1, r = ind << 1 | 1;
        if (l <= h->cnt && h->data[temp]->val < h->data[l]->val) temp = l;
        if (r <= h->cnt && h->data[temp]->val < h->data[r]->val) temp = r;
        if (temp == ind) break;
        swap(&h->data[ind], &h->data[temp]);
        ind = temp;
    }
    return ret;
}

void clear(Heap *h) {
    if (!h) return ;
    free(h->data);
    free(h);
}

ListNode *insert(ListNode *head, ListNode *p) {
    p->next = head;
    head = p;
    return head;
}

struct ListNode* mergeKLists(struct ListNode** lists, int listsSize){
    Heap *h = init();
    for (int i = 0; i < listsSize; i++) {
        ListNode *head = lists[i];
        while (head) {
            push_heap(h, head);
            head = head->next;
        }
    }
    // 链表使用头插法最需要注意的地方，初始化头节点指针为空，否则会导致段错误。
    ListNode *ret = NULL;
    while (h->cnt) ret = insert(ret, pop(h));
    return ret;
}
```

