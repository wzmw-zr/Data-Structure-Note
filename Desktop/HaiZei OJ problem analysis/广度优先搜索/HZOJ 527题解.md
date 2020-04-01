# HZOJ 527题解

**题目描述：**

 在一片广阔的土地上，有一个鸟人，他需要从这里穿过原野，回到基地。这片原野上，有平地 (P)、有湖泊 (L)，因为鸟人可以飞，所以呢，有的时候，他可以飞越湖泊。现在，鸟人需要用最快的时间，回到基地。

 假设原野是一个m∗n 的矩阵，有两种地形，用 P 和 L 表示。鸟人只能停留在平地上。他目前处在 (1,1) 这个位置，而目的地是 (m,n)，起点和目的地必为平地。他可以向上下左右四个方向移动，或者飞行。每移动一格需要 1 个单位时间。而飞行无论飞多远，都只需要 1 个单位时间。**飞行的途中不可以变方向，也就是说飞行也只能是上下左右四个方向。并且一次飞行最终必须降落在平地上**。当然，受到能量的限制，鸟人不能无限制的飞行，他总共最多可以飞行的距离为 D（总计飞行距离，不是一次飞行距离）。**现求飞到目的地的最短时间，若无法到达则输出 impossibleimpossible。**



**分析：**

这是一道求解最短时间 (路径)的问题，**==并且具有延展性的性质，即在一个运动方向上可以延展性移动。==**适合使用广度优先搜索解决问题。

**==对于具有延展性性质的广度优先搜索问题，在解题时要对延展性进行处理，将可一个位置处，依次对各个方向进行延展。==**

**代码：**

```c++
#include<iostream>
#include<queue>
using namespace std;

typedef struct Node {
    int x, y, s, d;
} Node;

int m, n, d;
int dir[4][2] = {1, 0, 0, 1, -1, 0, 0, -1};
char mmap[105][105];
int check[105][105][105];

int main() {
    cin >> m >> n >> d;
    for (int i = 1; i <= m; i++) cin >> &mmap[i][1]; 
    queue<Node> que;
    que.push({1, 1, 0, d});
    for (int i = 0; i <= d; i++) check[1][1][i] = 1;
    while (!que.empty()) {
        Node temp = que.front();
        que.pop();
        for (int i = 0; i < 4; i++) {
            for (int j = 2; j <= temp.d; j++) {
                int x = temp.x + j * dir[i][0];
                int y = temp.y + j * dir[i][1];
                if (x == n && y == m) {
                    cout << temp.s + 1 << endl;
                    return 0;
                }
                if (mmap[x][y] == 0) break;
                if (mmap[x][y] == 'P' && check[x][y][temp.d - j] == 0) {
                    check[x][y][temp.d - j] = 1;
                    que.push({x, y, temp.s + 1, temp.d - j});
                } 
            }
            int x = temp.x + dir[i][0];
            int y = temp.y + dir[i][1];
            if (x == n && y == m) {
                cout << temp.s + 1 << endl;
                return 0;
            }
            if (mmap[x][y] == 'P' && check[x][y][temp.d] == 0) {
                check[x][y][temp.d] = 1;
                que.push({x, y, temp.s + 1, temp.d});
            }
        }
    }
    cout << "impossible" << endl;
    return 0;
}
```



