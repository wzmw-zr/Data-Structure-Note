# HZOJ 83 题解

**题目描述：**

给定一个正整数，把它用正整数分成$m$份，求有多少种不同的分法。

**分析：**

求方法总数，采用深度优先搜索，**深度优先搜索可以先用树状图来模拟搜索过程，但不用建立树。**

**==关于解决深度优先搜索遇到的计算重复情况：记录当前的数字，保证数字是按顺序出现的，这样就保证不会重复。==**

当不需要输出具体的方法中的数据的时候，在进行深度优先搜索的时候，可以不用额外数组。但是如果需要记录深度优先搜索的路径，那么就需要额外的数组。

**代码：**

```c++
#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<cstring>
#include<ctime>
using namespace std;
#define MAX_N 100
#define MAX_M 8
int n, m, num[MAX_N + 1];

int total(int s, int n, int m) {
    if (m == 0) {
        if (n == 0) return 1;
        return 0;
    }
    int ret = 0;
    for (int i = s; i <= n; i++) {
        ret += total(i, n - i, m - 1); 
    }
    return ret;
}

int main() {
    cin >> n >> m;
    cout << total(1, n, m) << endl;
    return 0;
}

```

