# HZOJ 398题解

**题目描述：**

 有一个 n 行 m 列的棋盘( 1<n,m≤400 )，在某个点上有一个马，要求你计算出马到达棋盘上任意一个点最少要走几步。

**分析：**

这道题目要求求解最少步数，**也就是最短距离，适合使用广度优先搜索。**

**代码：**

```c++
#include<iostream>
#include<queue>
using namespace std;

typedef struct Node {
    int x, y, step;
} Node;

int n, m, x, y;
int dir[8][2] = {1, 2, 1, -2, 2, 1, 2, -1, -1, 2, -1, -2, -2, 1, -2, -1};
int mmap[405][405];

int main() {
    for (int i = 0; i < 405; i++) {
        for (int j = 0; j < 405; j++){
            mmap[i][j] = -1;
        }
    }
    cin >> n >> m >> x >> y;
    queue<Node> que;
    que.push({x, y, 0});
    mmap[x][y] = 0;
    while (!que.empty()) {
        Node temp = que.front();
        que.pop();
        for (int i = 0; i < 8; i++) {
            int x = temp.x + dir[i][0];
            int y = temp.y + dir[i][1];
            if (x < 1 || y < 1 || x > n || y > m || mmap[x][y] != -1) continue;
            mmap[x][y] = temp.step + 1;
            que.push({x, y, temp.step + 1});
        }
    }
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            j != 1 && cout << " ";
            if (mmap[i][j] == -2) cout << 0;
            else cout << mmap[i][j];
        }
        cout << endl;
    }
    return 0;
}
```



