# Leetcode 20题解

**题目描述：**

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
注意空字符串可被认为是有效字符串。



**分析：**

这是一类具有完全包含关系的问题，可以使用栈，也可以使用递归(使用系统栈).



**代码：**

```c++
bool isValid(char * s){
    int len = strlen(s);
    // 简单的时候直接使用数组来实现栈，而不将栈的所有操作都写出来
    char *stack = (char *) calloc(sizeof(char), (len + 5));
    int top = -1, flag = 1;
    while (s[0]) {
        switch (s[0]) {
            case '(':
            case '[':
            case '{': stack[++top] = s[0]; break;
            // 一定要注意这里右边匹配时要确定好正确的栈顶匹配的元素，不是当前字符等于栈顶元素
            case ')': flag = (top != -1 && stack[top--] == '('); break;
            case ']': flag = (top != -1 && stack[top--] == '['); break;
            case '}': flag = (top != -1 && stack[top--] == '{'); break;
        }
        if (!flag) return false;
        s++;
    }
    if (top != -1) return false;
    free(stack);
    return true;
}
```

