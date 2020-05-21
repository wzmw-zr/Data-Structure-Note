# HZOJ 404题解

**题目描述：**

有一个仅由数字 0 与 1 组成的 n×m 格迷宫。若你位于一格 0 上，那么你可以移动到相邻 4 格中的某一格 1 上，同样若你位于一格 1 上，那么你可以移动到相邻 4 格中的某一格 0 上。

 你的任务是：对于给定的迷宫，询问从某一格开始能移动到多少个格子（包含自身）。

**分析：**

这是一道**“走地图”类型的连通性问题，适合使用深度优先搜索求解。**而“走地图”类型的连通性问题多数是需要一个标记数组(也可以是结构体中的标记位)来标记该位置是否访问过。

**代码：**

```c++
#include<iostream>
using namespace std;

int n, m, ans = 1, x, y;
int dir[4][2] = {1, 0, 0, 1, -1, 0, 0, -1};
char mmap[3005][3005];
int check[3005][3005];

void func(int x, int y) {
    check[x][y] = 1;
    for (int i = 0; i < 4; i++) {
        int tx = x + dir[i][0]; 
        int ty = y + dir[i][1];
        if (tx < 1 || ty < 1 || tx > n || ty > m) continue;
        if (check[tx][ty] != 1 && mmap[tx][ty] != mmap[x][y]) {
            check[tx][ty] = 1;
            ans++;
            func(tx, ty);
        }
    }
}

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++) cin >> (&mmap[i][1]);
    cin >> x >> y;
    // 由于包含自身，所以在起点处需要标记已经访问过
    check[x][y] = 1;
    func(x, y);
    cout << ans << endl;
    return 0;
}
```

