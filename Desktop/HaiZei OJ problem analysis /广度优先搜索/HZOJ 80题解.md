

# HZOJ 80题解

**题目描述：**

 小明刚刚参加完期中考试，“这次又能得班级第一了”，他沾沾自喜，想起之前一直努力学习的自己，他决定去西城红场看个电影放松一下。现在小明想从学校走到电影院，因为市政大力修路和挖地铁，有些道路不允许步行，请判断小明能否走到电影院（只能朝上下左右四个方向行走），如果能到，则输出最短步数，如果不能到，则输出 No。

**分析：**

**这就是求解最短路径的问题，使用广度优先搜索，既然要求步数，那么就建立结构体，其中有一个步数(路径长度)的变量就行。**

**代码：**

```c++
#include<iostream>
#include<queue>
using namespace std;

typedef struct Node {
    int x, y, step;
} Node;

int n, m;
int dir[4][2] = {1, 0, 0, 1, -1, 0, 0, -1};
char mmap[505][505];

queue<Node> que;

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            cin >> mmap[i][j];
            if (mmap[i][j] == 's') que.push({i, j, 0});
        }
    }
    while (!que.empty()) {
        Node temp = que.front();
        que.pop();
        for (int i = 0; i < 4; i++) {
            int x = temp.x + dir[i][0];
            int y = temp.y + dir[i][1];
            if (mmap[x][y] == 'g') {
                cout << temp.step + 1 << endl;
                return 0;
            }
            if (mmap[x][y] == '.') {
                mmap[x][y] = 0;
                que.push({x, y, temp.step + 1});
            }
        }
    }
    cout << "No" << endl;
    return 0;
}
```

