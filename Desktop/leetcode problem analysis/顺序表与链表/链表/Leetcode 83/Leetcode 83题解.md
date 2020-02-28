# Leetcode 83题解

**题目描述：**

给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

**分析：**

正常的链表节点的删除操作，而且这道题也用不到虚拟头节点。

**小技巧：**

在进行遍历链表找节点删除时，有时候用`p && p->next`来判断是否为空，这算是一个化简代码的技巧。

**代码：**

```c++
struct ListNode* deleteDuplicates(struct ListNode* head){
    if (!head) return head;
    struct ListNode *first = head, *second;
    // 关于需要遍历链表找节点删除的操作，通常是需要同时判断当前节点指针和下一个节点指针是否为空，那么就p && p->next来判断，这算是一个技巧吧。
    while (first && first->next) {
        if (first->val - first->next->val) first = first->next;
        else {
            second = first->next;
            first->next = second->next;
            free(second);
        }
    }
    return head;
}
```

