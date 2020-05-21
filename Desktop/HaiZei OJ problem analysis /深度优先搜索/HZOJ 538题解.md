# HZOJ 538 题解

**题目描述：**

读入一个用邻接矩阵存储的无向图，输出它的深度优先遍历序列。（以 1 为起点，按照编号越小权值越大的规则）

**分析：**

直白的图的深度遍历问题，**深度优先遍历为了避免重复，都是需要一个check数组来表示该位置是否访问过。**

**代码：**

```c++
#include<iostream>
using namespace std;

int n, num[25][25], check[25], flag;

void func(int x) {
    if (flag == 1) {
        cout << '-';
    }
    flag = 1;
    cout << x;
    for (int i = 1; i <= n; i++) {
        if (num[x][i] && check[i] == 0) {
            check[i] = 1;
            func(i);
        }
    }
}

int main() {
    cin >> n;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            cin >> num[i][j];
        }
    }
    for (int i = 1; i <= n; i++) {
        if (check[i] == 0) {
            check[i] = 1;
            func(i);
        }
    }
    return 0;
}
```



