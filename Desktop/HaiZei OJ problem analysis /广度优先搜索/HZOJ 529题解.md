# HZOJ 529题解

**题目描述：**

 给出一张n∗m 的地图，在地图上有一只虫，样子却很像龙，而且嘴能快速的直射喷出一种毒液，瞬间杀死敌人。

 现在假设虫的初始位置在 x1,y1，另外在 x2,y2 处有一个敌人。假设虫移动一步需要单位 1 的时间，而杀死敌人不需要时间，并且虫的毒液射程无穷大，但毒液不能穿透障碍物，虫可以向四个方向移动，向八个方向攻击，求虫**最少**需要多少时间才能消灭敌人。

**分析：**

这是一个**查找两个点之间是否具有连通性的问题，而且要求找最少时间的，==并且这是具有延展性的，所以适合使用广度优先搜索。==**

**代码：**

```c
#include<iostream>
#include<queue>
#include<cstring>
using namespace std;

typedef struct Node {
    int x, y, step;
} Node;

int n, m;
int dir[8][2] = {1, 0, 0, 1, -1, 0, 0, -1, 1, 1, 1, -1, -1, 1, -1, -1};
char mmap[130][130];
int check[130][130];


int func() {
    int a, b, c, d;
    cin >> c >> d >> a >> b;
    if (!a) return 0;
    memset(check, 0, sizeof(check));
    for (int i = 0; i < 8; i++) {
        for (int j = 1; 1; j++) {
            int x = c + j * dir[i][0];
            int y = d + j * dir[i][1];
            if (mmap[x][y] != 'O') break;
            check[x][y] = 2;
        }
    }
    check[c][d] = 2;
    if (check[a][b] == 2) {
        cout << 0 << endl;
        return 1;
    }
    queue<Node> que;
    que.push({a, b, 0});
    check[a][b] = 1;
    while (!que.empty()) {
        Node temp = que.front();
        que.pop();
        for (int i = 0; i < 4; i++) {
            int x = temp.x + dir[i][0];
            int y = temp.y + dir[i][1];
            if (check[x][y] == 2) {
                cout << temp.step + 1 << endl;
                return 1;
            }
            if (mmap[x][y] == 'O' && check[x][y] != 1) {
                check[x][y] = 1;
                que.push({x, y, temp.step + 1});
            }
        }
    }
    cout << "impossible!" << endl;
    return 1;
}

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++) cin >> &mmap[i][1];
    // 对于测试数据组数未知的一种较好的解决方法，将输入用例使用函数来处理，其结果代表了用例是否结束
    while (func());
    return 0;
}
```

