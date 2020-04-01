# HZOJ 81题解

**题目描述：**

 小明看完了电影，是时候回家了，可是这时他突然得知小米之家的小米9现货开卖了，这款手机小明已经想了一个多月，知道这个消息后的他十分兴奋，一定要在回家之前先去小米之家买手机（城市中有一个或多个小米之家），请计算小明从电影院到任意一个小米之家买手机后回家的最短距离（只能朝上下左右四个方向行走，除了障碍物外，其他地方都可以通过），数据保证可以买完手机后回家。

**分析：**

这也是一道广度优先搜索的题目，但是具有两个阶段，**因此这是多阶段广度优先搜索题目。==多阶段广度优先搜索的查重可以只用一个check数组，参照Linux下的掩码，使用位运算来设置与检查check数组中数字对应的阶段。==**

**代码：**

```c++
#include<iostream>
#include<queue>
using namespace std;

typedef struct Node {
    int x, y, s, f;
} Node;

int n, m;
char mmap[2005][2005];
int check[2005][2005];
int dir[4][2] = {1, 0, 0, 1, -1, 0, 0, -1};

int main() {
    cin >> n >> m;
    queue<Node> que;
    for (int i = 1; i <= n; i++)  {
        for (int j = 1; j <= m; j++) {
            cin >> mmap[i][j];
            if (mmap[i][j] == 'S') {
                que.push({i, j, 0, 0});
                check[i][j] = 1;
            }
        }
    }
    while (!que.empty()) {
        Node temp = que.front();
        que.pop();
        for (int i = 0; i < 4; i++) {
            int x = temp.x + dir[i][0];
            int y = temp.y + dir[i][1];
            if (temp.f == 0 && check[x][y] & 1) continue;
            if (temp.f == 1 && check[x][y] & 2) continue;
            if (temp.f == 1 && mmap[x][y] == 'T') {
                cout << temp.s + 1 << endl;
                return 0;
            }
            if (mmap[x][y] == '.' || mmap[x][y] == 'S' || mmap[x][y] == 'T') {
                que.push({x, y, temp.s + 1, temp.f});
                check[x][y] += temp.f + 1;
            }
            if (mmap[x][y] == 'P') {
                que.push({x, y, temp.s + 1, 1});
                check[x][y] = 3;
            }
        }
    }
    return 0;
}
```

