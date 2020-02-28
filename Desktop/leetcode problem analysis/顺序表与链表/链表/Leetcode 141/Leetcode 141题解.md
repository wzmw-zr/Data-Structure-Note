# Leetcode 141题解

**题目描述：**给定一个链表，判断链表中是否有环。



**分析：**

方法一：每到达一个节点，判断其下一个节点的指针是否指向该节点之前的节点，但是这样子的话时间复杂度为$O(n^2)$。

方法二：使用快慢指针的概念，当快指针能够遇到慢指针的时候，说明有环，而如果快慢指针只要有一个为空则说明没有环，时间复杂度为$O(n)$。

方法三：判断是否有环实际上就是判断节点是否重复出现，可以使用哈希来解决。

**当链表中含有环的时候，最常用的方法是使用快慢指针将其转换为追击问题**，即Floyd算法思想。

****



**代码：**

```c++
typedef struct ListNode ListNode;

bool HasCycle(struct ListNode *head) {
    if (!head) return false;
    // 指定快慢指针
    ListNode *p = head, *q = head;
    do {
        p = p->next;
        q = q->next;
        // 快指针由于一次会处理两个节点，所以需要判断当前节点和下一个结点是否为空，只要有一个为空就退出循环
        if (!q || !q->next) return false;
        q = q->next;
    } while (p != q);
    //检测到有环，返回true
    return true;
}
```

