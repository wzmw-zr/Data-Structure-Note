# Leetcode 203题解

**题目描述：**

删除链表中等于给定值 ***val\*** 的所有节点。

**分析：**

这就是链表的节点删除题目，定义一个虚拟头节点来统一操作。

**代码：**

```c++
typedef struct ListNode ListNode;
struct ListNode* removeElements(struct ListNode* head, int val){
    ListNode first, *p, *q;
    first.next = head;
    p = &first;
    while (p && p->next) {
        if (p->next->val == val) {
            q = p->next;
            p->next = q->next;
            free(q);
        } else {
            p = p->next;
        }
    }
    return first.next;
}
```

