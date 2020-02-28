# Leetcode 234题解

**题目描述：**

请判断一个链表是否为回文链表。

**分析：**

翻转链表后与原链表进行比较，这样子的时空复杂度就是$O(n)$。

但是我们可以将链表分为前后两部分，只对后半部分翻转，之后前后比较，这样子空间复杂度就变成了$O(1)$。



**代码：**

```c++
typedef struct ListNode ListNode;

 int get_length(ListNode *head) {
     int n = 0;
     while (head) n++, head = head->next;
     return n;
}

ListNode *reverse(ListNode *head, int n) {
    ListNode ret, *p = head, *q;
    while (n--) p = p->next;
    ret.next = NULL;
    while (p) {
        q = p->next;
        p->next = ret.next;
        ret.next = p;
        p = q;
    }
    return ret.next;
}

bool isPalindrome(struct ListNode* head){
    int len = get_length(head);
    ListNode *p = head, *q;
    // 为了应对奇数的情况，选择上取整
    // 同时还有空数组，数组长度为1的情况需要考虑
    // 但是这里由于是从头节点开始向后走先找到后一半的开始节点，这么做就已经可以避免空数组和数组长度为1的情况。
    // 选择一种合适的操作，以及操作顺序可以避免一些特殊情况
    q = reverse(head, (len + 1) >> 1);
    while (q) {
        if (p->val - q->val) return false;
        p = p->next;
        q = q->next;
    }
    return true;
}
```

