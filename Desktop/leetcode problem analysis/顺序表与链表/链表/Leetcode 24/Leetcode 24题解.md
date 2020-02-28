# Leetcode 24题解

**题目描述：**

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。



**分析：**

这是一道对链表的指针进行改变以修改链表的结构的操作。

关于修改链表的结构，比较直接的想法就是在遍历的过程中，一边遍历一边修改，可能还有一些特殊情况需要单独处理。

**注重虚拟头节点在化简操作上的作用，Leetcode上的链表都是没有虚拟头节点的，在一些考察指针操作的题目上，使用虚拟头节点可以统一操作。**

==使用虚拟头节点就是为了应对对头节点的操作。==

**易错点：**

虽然说这是链表元素两两交换，但是要**注意可能会出现的链表元素数量为奇数情况。**



**代码一：没有使用虚拟头节点**

```c++
struct ListNode* swapPairs(struct ListNode* head){
    if (!head) return head;
    struct ListNode *first = head, *second = head->next, *pre;
    if (!second) return head;
    first->next = second->next;
    second->next = first;
    head = second;
    pre = first;
    while (pre->next) {
        first = pre->next;
        second = first->next;
        if (!second) return head;
        first->next = second->next;
        second->next = first;
        pre->next = second;
        pre = pre->next->next; 
    }
    return head;
}
```



**代码二：使用虚拟头节点**

```c++
typedef struct ListNode ListNode;
struct ListNode* swapPairs(struct ListNode* head){
    // 使用虚拟头节点
	ListNode ret, *p, *q;
    ret.next = head;
    p = &ret;
    q = head;
    // 防止出现奇数的情况，当q->next == NULL时，说明这个是奇数长度的，当q == NULL时，说明链表为偶数长度，这两种情况下都是退出
    while (q && q->next) {
        p->next = q->next;
        q->next = p->next>next;
        p-next->next = q;
        p = q;
        q = q->next;
    }
    return ret.next;
}
```

