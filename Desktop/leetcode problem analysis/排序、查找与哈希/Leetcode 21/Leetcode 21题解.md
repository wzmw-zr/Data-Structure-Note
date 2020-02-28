# Leetcode 21题解

**题目描述：**将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**分析：**这实际上就是归并排序中的合并操作

**代码：**

```c++
struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2){
    struct ListNode *p = NULL, *q;
    while (l1 || l2) {
        if (l1 && (!l2 || l1->val < l2->val)) {
            // 注意这里的头节点指针为空的情况，一定要更新l1，否则在后面会形成l1的自己闭环，导致死循环，下面的l2也是。
            if (!p) p = l1, q = p, l1 = l1->next;
            else {
                p->next = l1;
                l1 = l1->next;
                p = p->next;
            }
        } else {
            if (!p) p = l2, q = p, l2 = l2->next;
            else {
                p->next = l2;
                l2 = l2->next;
                p = p->next;
            }
        }
    }
    return q;
}
```

