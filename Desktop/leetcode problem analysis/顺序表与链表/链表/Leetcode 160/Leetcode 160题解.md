# Leetcode160题解

**题目描述：**

编写一个程序，找到两个单链表相交的起始节点。

**分析：**

方法一(暴力法)：在一个链表中遍历节点，然后到另一个链表中去顺序查找，判断这个节点是否存在。

方法二：如果两个链表相交，那么一定是在后半部分，因此，可以**先将两个链表进行对齐操作，之后向后检查，判断当前节点指针是否一致。**

方法三：由于这也是查重的操作，哈希也是可以的。



**代码：方法二**

```c++
#define swap(a, b) {\
    __typeof(a) __temp = a;\
    a = b, b = __temp;\
}

typedef struct ListNode ListNode;

struct ListNode *getIntersectionNode(struct ListNode *headA, struct ListNode *headB) {
    int cntA = 0, cntB = 0;
    ListNode *p = headA, *q = headB;
    while (p) cntA++, p = p->next;
    while (q) cntB++, q = q->next;
    int m = cntA - cntB;
    p = headA, q = headB;
    if (m < 0) {
        swap(p, q);
        m = -m;
    }
    // 对齐操作
    while (m--) p = p->next;
    while (p != q) p = p->next, q = q->next;
    return p;
}
```

