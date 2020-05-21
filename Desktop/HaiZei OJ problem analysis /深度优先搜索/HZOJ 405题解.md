# HZOJ 405题解

**题目描述：**

 有一个仅由数字 0 与 1 组成的 n×m 格迷宫。若你位于一格 0 上，那么你可以移动到相邻 4 格中的某一格 1 上，同样若你位于一格 1 上，那么你可以移动到相邻 4 格中的某一格 0 上。

 你的任务是：对于给定的迷宫，询问 k 次从某一格开始能移动到多少个格子（包含自身）。

**分析：**

这也是“走迷宫”类型的连通性问题，实际上还是求连通分量，不过这里由于查询操作过多，所以，事先求好所有的连通分量并且更新内部元素的相关值，可以节省时间。

**==关于获取一条完整的联通分量，可以使用队列保存结果(栈也可以)。==**多条连通分量就可以这么解决。

**代码：**

```c++
#include<iostream>
#include<cstring>
#include<queue>
using namespace std;

typedef struct Node {
    int x, y;
} Node;

int n, m, k, x, y, ans;
char mmap[3005][3005];
int check[3005][3005];
int dir[4][2] = {1, 0, 0, 1, -1, 0, 0, -1};
queue<Node> que;

void func(int x, int y) {
    for (int i = 0; i < 4; i++) {
        int tx = x + dir[i][0];
        int ty = y + dir[i][1];
        if (check[tx][ty] == 0 && mmap[tx][ty] != 0 && mmap[tx][ty] != mmap[x][y]) {
            ans++;
            check[tx][ty] = 1;
            que.push({tx, ty});
            func(tx, ty);
        }
    }
}

int main() {
    cin >> n >> m >> k;
    for (int i = 1; i <= n; i++) cin >> (&mmap[i][1]);
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            if (check[i][j] == 0) {
                ans = 1;
                check[i][j] = 1;
                // 使用简单结构体的骚操作
                que.push({i, j});
                func(i, j);
                while (!que.empty()) {
                    Node temp = que.front();
                    check[temp.x][temp.y] = ans;
                    que.pop();
                }
            }
        }
    }
    for (int i = 1; i <= k; i++) {
        cin >> x >> y;
        cout << check[x][y] << endl;
    }
    return 0;
}
```

