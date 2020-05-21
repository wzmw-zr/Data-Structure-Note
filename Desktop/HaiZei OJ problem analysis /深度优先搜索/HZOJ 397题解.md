# HZOJ 307题解

**题目描述：**

9102 年，小明和李华正在怀旧的玩植物大战僵尸重置版，重置版多了一个道具—卫星地图，使用此道具后就能知道后院外（n 行 m 列）的每个格子有多少个僵尸， 0 代表这个格子上没有僵尸，其余数代表僵尸的个数。若某一个格子上有僵尸，且在这个格子上下左右的某个格子上也有僵尸，那么他们为同一波僵尸，现求后院外还有多少波僵尸。

**分析：**

这是一道**“走地图”类型的问题，即连通性问题。**“走地图”适合使用深度优先搜索。

> **“走地图”类型的连通性问题通常需要check数组来检查点是否访问过。**

**代码：**

```c++
#include<iostream>
#include<cstdio>
using namespace std;

// 可以使用check数组来记录点是否访问过
int n, m, mmap[105][105], ans;
int dir[4][2] = {1, 0, 0, 1, 0, -1, -1, 0};

void func(int x, int y) {
    for (int i = 0; i < 4; i++) {
        int tx = x + dir[i][0];
        int ty = y + dir[i][1];
        if (mmap[tx][ty]) {
            mmap[tx][ty] = 0;
            func(tx, ty);
        }
    }
}

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            cin >> mmap[i][j];
        }
    }
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            if (mmap[i][j] != 0) {
                ans++;
                mmap[i][j] = 0;
                func(i, j);
            }
        }
    }
    cout << ans << endl;
    return 0;
}
```



**关于`memset`函数用法的注意点：**

**`memset()`函数是按字节初始化数据**，因此只适合数组置0和-1,如果==**数组要全部置为1的话，只能使用一个循环。**==