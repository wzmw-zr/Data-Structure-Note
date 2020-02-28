# Leetcode 38题解

**题目描述：**「外观数列」是一个整数序列，从数字 1 开始，序列中的每一项都是对前一项的描述。

```
1
11
21
1211
111221
```

**分析：**原本以为这个是一个可能需要使用哈希来解决的题目，但是最后使用了不断遍历更新的来获取每个阶段的数列。也许还有更好的解法。**这道题可以根据直观感受来求解。**虽然这道题被分到了查找类型中，我并没有感觉到曾么运用查找，或许这算是顺序查找？还是说正常的遍历操作可以看作是顺序查找？

**代码：**

```c++
char * countAndSay(int n){
    char *str = (char *) calloc(sizeof(char), 100);
    str[0] = '1';
    while (--n) {
        char c, *s = (char *) calloc(sizeof(char), strlen(str) * 2);
        int cnt = 0, ind = 0;
        // 分段记录信息，并更新新的字符串
        for (int i = 0; i < strlen(str); i++) {
            if (!i) {
                c = str[i];
                cnt++;
            } else {
                if (str[i] == c) {
                    cnt++;
                } else {
                    s[ind++] = cnt + '0'; 
                    s[ind++] = c;
                    c = str[i];
                    cnt = 1;
                }
            }
        }
        // 加上这一步是为了处理最后一串字符，这里容易被遗漏
        s[ind++] = cnt + '0';
        s[ind++] = c;
        free(str);
        str = s;
    }
    return str;
}
```

