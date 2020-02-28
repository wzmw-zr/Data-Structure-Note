# Leetcode 142题解

**题目描述：**

给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

**含环链表可以使用==Floyd算法==进行求解，分成两个阶段：一，找出列表中是否有环 ;二，使用相遇节点来找到环的入口。**



**分析：**

1. 对于**==链表中是否含有环，可以转换为追击问题，使用快慢指针==**的思想。

2. 链表中环的性质：

（1）**从环中任一节点出发，到再次遇到该节点，可得到环的长度**

（2）**获得了环的长度之后，** **可以让指针p从链表头节点出发，走环的长度后，指针q也从头节点出发，到p，q指针相等时**，就**找到了环的起始位置。**

> 思路二：这实际上也是一道查重问题，可以使用哈希来解决。

**代码：**

```c++
typedef struct ListNode ListNode;

struct ListNode *detectCycle(struct ListNode *head) {
    if (!head) return NULL;
    // 指定快慢指针
    ListNode *p = head, *q = head;
    do {
        p = p->next;
        q = q->next;
        // 快指针由于一次会处理两个节点，所以需要判断当前节点和下一个结点是否为空，只要有一个为空就退出循环
        if (!q || !q->next) return NULL;
        q = q->next;
    } while (p != q);
    //判断有环之后计算环的长度
    int cnt = 0;
    do {
        cnt++;
        p = p->next;
    } while (p != q);
    // 寻找环的第一个节点
    p = head, q = head;
    while (cnt--) q = q->next;
    while (p != q) p = p->next, q = q->next;
    return p;
}
```

