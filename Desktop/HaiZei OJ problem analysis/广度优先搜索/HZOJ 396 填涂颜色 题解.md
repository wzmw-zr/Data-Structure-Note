# HZOJ 396 填涂颜色 题解

## 题目描述

 由数字 0 组成的方阵中，有一任意形状闭合圈，闭合圈由数字 1 构成，围圈时只走上下左右 4 个方向。现要求把闭合圈内的所有空间都填写成 2。



## 分析

很明显，这是一道关于连通性的问题，非封闭的0区域一定是有机会越界的，而那些封闭区域是没有机会越界的，因此可以使用**深度优先搜索**，找到0的联通区域，并获得其是否封闭，并且使用一个队列保存搜索过程中加入的元素的坐标。如果封闭，那么就逐个出队列，相应位置值为2。

**一个易错点：**check数组值在何处修改，**check数组如果在当前位置为0时，立即置为1！！！千万记得要置为1！！！**



## 代码

```c++
#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<cstring>
#include<string>
#include<queue>
#include<stack>
#include<algorithm>
using namespace std;

typedef struct Node {
    int x, y;
} Node;

int n;
int mmap[35][35];
int check[35][35];
int dir[4][2] = {1, 0, -1, 0, 0, 1, 0, -1};

void output() {
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            j != 1 && cout << " ";
            cout << mmap[i][j];
        }
        cout << endl;
    }
}

int closure(queue<Node> &que, int x, int y) {
    int ret = 1;
    que.push({x, y});
    // 每遇到一个0的，check[x][y]=1
    check[x][y] = 1;
    for (int i = 0; i < 4; i++) {
        int x_t = x + dir[i][0];
        int y_t = y + dir[i][1];
        if (x_t <= 0 || x_t > n || y_t <= 0 || y_t > n) {
            ret = 0;
            continue;
        }
        if (check[x_t][y_t]) continue;
        if (check[x_t][y_t] == -1) return 0;
        // 实际上，这里我们就应该置check[x_t][y_t]=1
        if (!mmap[x_t][y_t]) ret &= closure(que, x_t, y_t);
    }
    return ret;
}

void handle(int x, int y) {
    if (check[x][y]) return ;
    if (mmap[x][y] == 1) {
        // 遇到1的日常置为1
        check[x][y] = 1;
        return ;
    }
    queue<Node> que;
    // 实际上，这里我们就应该置check[x][y]=1
    if (!closure(que, x, y)) {
        while (!que.empty()) {
            Node temp = que.front();
            que.pop();
            check[temp.x][temp.y] = -1;
        }
        return ;
    }
    while (!que.empty()) {
        Node temp = que.front();
        que.pop();
        mmap[temp.x][temp.y] = 2;
        check[temp.x][temp.y] = 1;
    }
}

int main() {
    cin >> n;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            cin >> mmap[i][j];
        }
    }
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            handle(i, j);
        }
    }
    output();
    return 0;
}
```

