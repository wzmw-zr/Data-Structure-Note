# HZOJ 303题解

**题目描述：**

假设我们有一个矩阵，其元素值非 00 即 11：

``` 
a11 … … a1m

… … … … …

an1 … … anm 
```

定义 aij 与 akl 之间的距离为 D(aij,akl)=abs(i−k)+abs(j−L)。

现求每个元素到最近的元素值为 1 的元素的距离。

**分析：**

**这是一个求解最短距离的问题，可以使用广度优先搜索，相当于树的层序遍历。==此处由于数据规模大，不适合每个点开始搜，而是从所有终点1开始进行广度优先搜索。==**

**==广度优先搜索的一个转换角度：将终点当作起点考虑，可以减少很多时间复杂度。并且由于其最先者必为最优的性质，一开始将所有终点加入队列，然后一个个开始走，率先走到的一定是最短的。==**

**代码：**

```c++
#include<iostream>
#include<queue>
using namespace std;

typedef struct Node {
    int x, y, step;
} Node;

int n, m;
int dir[4][2] = {1, 0, 0, 1, -1, 0,0, -1};
char mmap[1005][1005];
int ans[1005][1005];

int main() {
    queue<Node> que;
    cin >> n >> m;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            cin >> mmap[i][j];
            if (mmap[i][j] == '1') {
                que.push({i, j, 0}); 
                ans[i][j] = -1;
            }
        }
    }
    while (!que.empty()) {
        Node temp = que.front();
        que.pop();
        for (int i = 0; i < 4; i++) {
            int x = temp.x + dir[i][0];
            int y = temp.y + dir[i][1];
            if (x < 1 || y < 1 || x > n || y > m || ans[x][y]) continue;
            ans[x][y] = temp.step + 1;
            que.push({x, y, ans[x][y]});
        }
    }
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            if (j != 1) cout << " ";
            if (ans[i][j] == -1) cout << 0;
            else cout << ans[i][j];
        }
        cout << endl;
    }
    return 0;
}
```





