# HZOJ 530题解

**题目描述：**

 年轻的拉尔夫开玩笑地从一个小镇上偷走一辆车，但他没想到的是那车属于警察局。并且车上装有用于发身车子移动路线的装置。那个装置太旧了，以至于只能发射关于那辆车的移动路线的方向信息。

 通过使用一张小镇的地图，帮助警察局找到那车。表示出该车最终所有可能的位置。

 小镇的地图是矩形的，上面的符号用来标明那儿可以行车和那儿不行。“.” 表示小镇上那块地方是可以行车的，符号“X” 表示此处不能行车。拉尔夫所开小车的初始位置，用字符的 “∗”，表示，且汽车能从初始位置通过。汽车能向四个方向移动：向北（上），南（下），西（左），东（右）；拉尔夫所开小车的行动路线是通过一组给定的方向来描述的，在每个给定的方向，拉尔夫驾驶小车通过小镇上的一个或更多的可行车地点。

**分析：**

**这是一道==带有延展性的搜索类题目，适合使用广度优先搜索==，这道题需要注意不同层的点可以重复，但是同一层不允许重复，这中==层间可重，层内不重==广度优先搜索题目，其去重是按层去重的，每到一个新的阶段都要清空check数组，==之所以允许层间重复，是因为广度优先搜索具有处理完一层后才处理下一层的性质。即处理当前层时不需要考虑上一层。==**

**代码：**

```c
#include<iostream>
#include<cstdlib>
#include<cstring>
#include<string>
#include<queue>
using namespace std;

typedef struct Node {
    int x, y;
} Node;

int n, m, cnt;
int dir[4][2] = {-1, 0, 1, 0, 0, -1, 0, 1};
char mmap[55][55];
int check[55][55];

int main() {
    cin >> n >> m;
    queue<Node> que;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            cin >> mmap[i][j];
            if (mmap[i][j] == '*') {
                que.push({i, j});    
                mmap[i][j] = '.';
                //check[i][j] = 1;
            }
        }
    }
    cin >> cnt;
    while (cnt--) {
        string t;
        cin >> t;
        int dir_num = 0;
        if (t == "NORTH") {
            dir_num = 0;
        }
        if (t == "SOUTH") {
            dir_num = 1;
        }
        if (t == "WEST") {
            dir_num = 2;
        } 
        if (t == "EAST") {
            dir_num = 3;
        }
        memset(check, 0, sizeof(check));
        int times = que.size();
        for (int i = 0; i < times; i++) {
            Node tmp = que.front();
            que.pop();
            for (int j = 1; 1; j++) {
                int x = tmp.x + j * dir[dir_num][0];
                int y = tmp.y + j * dir[dir_num][1];
                if (mmap[x][y] != '.') break;
                if (!check[x][y]) {
                    que.push({x, y});
                    check[x][y] = 1;
                }
            }
        }
    }
    while (!que.empty()) {
        Node tmp = que.front();
        que.pop();
        mmap[tmp.x][tmp.y] = '*';
    }
for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= m; j++) {
        cout << mmap[i][j];
    }
    cout << endl;
}
    return 0;
}

```

