# HZOJ 235题解

**题目描述：**

 从 1−n 这 n 个整数中随机选取任意多个，每种方案里的数从小到大排列，按字典序输出所有可能的选择方案。

**分析：**

**==穷举式搜索，排列式枚举，都是使用深度优先搜索的典型题目。==**

**找到所有可能的选择方案，所有的路径，满足某种性质的方案等等,都可以采用深度优先搜索的方法。**

==对于不是树这种天然可以存储数据的深度优先搜索，一般还是需要使用一个数组来记录路径的。==

==深度优先搜索适合用来解决树状结构的枚举问题，并且深度优先搜索的到头而返和逐层向下的性质，可以做到不重不漏。==

**代码：**

```c++
#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<cstring>
#include<ctime>
using namespace std;

int num[15], cnt, n;

void p() {
    for (int i = 0; i < cnt; i++) {
        if (i) cout << " ";
        cout << num[i];
    }
    cout << endl;
}

void func(int s) {
    for (int i = s; i <= n; i++) {
        num[cnt++] = i;
        p();
        func(i + 1);
        cnt--;
    } 
}

int main() {
    cin >> n;
    func(1);
    return 0;
}
```

