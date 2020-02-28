# Leetcode 19题解

**题目描述：**

给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。



**分析：**

这是链表删除节点的题目。对于这种没有分为程序内部和内存内部，也没有虚拟头节点的题目而言，可以直接没有虚拟头节点直接操作，期间注意是否删除的就是头节点，**但是没有虚拟头节点的话，这操作是不统一的，所以可以自己创建一个虚拟头节点。**



**代码一：没有使用虚拟头节点**

```c++
struct ListNode* removeNthFromEnd(struct ListNode* head, int n){
    struct ListNode *p = head, *q = head, *del;
    n--;
    while (n--) p = p->next;
    if (!p->next) {
        del = head;
        head = head->next;
        free(del);
        return head;
    }
    while (p->next->next) p = p->next, q = q->next;
    del = q->next;
    q->next = del->next;
    free(del);
    return head;
}
```



**代码二：使用虚拟头节点**

```c++
typedef struct ListNode ListNode;

struct ListNode* removeNthFromEnd(struct ListNode* head, int n){
    // 创建虚拟头节点，这样子，从虚拟头节点走n步就到了实际的第n个节点，如果不给虚拟头节点，那么此时就到了第n+1个节点了
    ListNode ret, *p, *q;
    ret.next = head;
    p = q = &ret; 
    while (n--) p = p->next;
    while (p->next) p = p->next, q = q->next;
    p = q->next;
    q->next = p->next;
    free(p);
    return ret.next;
}
```

