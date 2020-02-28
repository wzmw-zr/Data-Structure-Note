# Leetcode 206题解

**题目描述：**反转一个单链表。



**分析：**

==注意链表类题目使用虚拟头节点来化简操作！！！==反转链表可以使用头插法。

**头插法可以应用于链表的反转上，可以避免复杂的链表指针改变。**

==使用虚拟头节点来化简操作，使用头插法(**头插法就是使用虚拟头节点的**)来简化一部分复杂的指针改变！！！==

> 关于链表头插法的应用：
>
> 链表头插法的意义在于插入的时间复杂度为$O(1)$，并且头插法插入的元素是最新的，而最先插入的会在后面，这种先后顺序翻转的性质可以被用于链表的翻转，==当然这种性质也是可以用于操作系统中的历史记录上的。==



**代码：**

```c++
typedef struct ListNode ListNode;
struct ListNode* reverseList(struct ListNode* head){
    //使用虚拟头节点化简操作
    // 使用头插法时，就是需要使用虚拟头节点的。
    ListNode ret, *p, *q;
    ret.next = NULL;
    p = head;
    while (p) {
        q = p->next;
        p->next = ret.next;
        ret.next = p;
        p = q;
    }
    return ret.next;
}
```

