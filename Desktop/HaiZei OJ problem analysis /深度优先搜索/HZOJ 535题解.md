# HZOJ 535题解

**题目描述：**

 在一个 w∗h 的矩形广场上，每一块 1∗1 的地面都铺设了红色或黑色的瓷砖。小明现在站在某一块黑色的瓷砖上，他可以从此处出发，移动到上下左右四个相邻的且是黑色的瓷砖上。现在，他想知道，通过重复上述移动所能经过的黑色瓷砖数。

**分析：**

这是“走地图”类型的连通性问题。实际上就是求当前连通分量中的元素个数。

**代码：**

```c++
#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<cstring>
using namespace std;

int h, w, sx, sy, ans = 1;
char mmap[55][55];
int dir[4][2] = {0, 1, 1, 0, 0, -1, -1, 0};

void func(int x, int y) {
    for (int i = 0; i < 4; i++) {
        int tx = x + dir[i][0];
        int ty = y + dir[i][1];
        if (mmap[tx][ty] == '.') {
            mmap[tx][ty] = 0;
            ans++;
            func(tx, ty);
        }
    }
}

int main() {
    cin >> h >> w;
    for (int i = 1; i <= w; i++) {
        cin >> (&mmap[i][1]);
    }
    for (int i = 1; i <= w; i++) {
        for (int j = 1; j <= h; j++) {
            if (mmap[i][j] == '@') sx = i, sy = j;
        }
    }
    func(sx, sy);
    cout << ans << endl;
    return 0;
}
```

