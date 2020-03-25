# HZOJ 80题解

**题目描述：**

 小明刚刚参加完期中考试，“这次又能得班级第一了”，他沾沾自喜，想起之前一直努力学习的自己，他决定去西城红场看个电影放松一下。现在小明想从学校走到电影院，因为市政大力修路和挖地铁，有些道路不允许步行，请判断小明能否走到电影院（只能朝上下左右四个方向行走），如果能到，则输出最短步数，如果不能到，则输出 No。

**分析：**

这是**一类走地图的问题**。也是**寻找某种性质的路径的问题。**适合使用深度优先搜索、广度优先搜索来进行搜索。

**深度优先搜索适合找所有满足要求的路径或者判断是否有路径，而广度优先搜索是和找最短路径，也可以判断是否有路径。**

**代码：**

```c++
#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<cstring>
using namespace std;

int n, m, sx, sy;
char mmap[505][505];
// 定义的一个方向数组
int dir[4][2] = {1, 0, 0, 1, -1, 0, 0, -1};

int func(int x, int y) {
    int ret = 0;
    for (int i = 0; i < 4; i++) {
        int tx = x + dir[i][0];
        int ty = y + dir[i][1];
        if (mmap[tx][ty] == 'g') return 1;
        if (mmap[tx][ty] == '.') {
            mmap[tx][ty] = '*';
            ret |= func(tx, ty);
        }
    }
    return ret;
}

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            cin >> mmap[i][j];
            if (mmap[i][j] == 's') {
                sx = i;
                sy = j;
            }
        }
    }
    if (func(sx, sy)) cout << "Yes" << endl;
    else cout << "No" << endl;
}
```

