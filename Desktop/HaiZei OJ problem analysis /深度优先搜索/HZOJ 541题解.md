# HZOJ 541 题解

**题目描述：**

贝丽斯和她的姐姐艾丽斯想从谷仓走到她们最喜爱的牧场。她们在同一时间离开谷仓，也在同一时间到达最喜爱的牧场。

 整个农场共有 N 个牧场，1 号牧场就是谷仓，N 号牧场是她们最喜爱的牧场。整个农场是建在一个山坡上的，如果 X<Y，则代表 X 号牧场比 Y 牧场要高。有 M 条路径连接一堆牧场。然而，由于每条路径都很陡，每条路只能向下山的方向走。比如，一条连接 5 号和 8 号农场的路只能从 5 走到 8 而不能反过来，因为那样就是向山上走了。每对牧场之间最多有一条路径，故M≤N(N−1)/2。

 贝丽斯和艾丽斯可能需要不同的时间来走过一条路径。例如，贝丽斯可能花 10 个单位的时间，而艾丽斯会花 20 个单位，而且她们只在路径上花时间，在牧场上是不花时间的。

 请帮助决定贝丽斯和艾丽斯至少要花多少时间，使她们能同时到达她们最喜爱的农场。

**分析：**

**这题与图论相关，==实质上是求从源点到目标点的所有路径的各自代价，显而易见，这可以用深度优先搜索解决==，用临接矩阵存储单个人在不同牧场之间的行走时间。**

**==因此，图论中与从源点到目标节点的路径相关的问题，也都是可以用深度优先搜索求解。==**

**代码：**

```c++
#include<iostream>
#include<algorithm>
using namespace std;

int n, m, atime[2][20][20], a, b, c, d, ans[2][100000], ans_num[2];

void func(int pepole, int locate, int cost) {
    if (locate == n) {
        ans[pepole][ans_num[pepole]] = cost;
        ans_num[pepole]++;
        return ;
    }
    for (int i = locate + 1; i <= n; i++) {
        if (atime[pepole][locate][i]) {
            func(pepole, i, cost + atime[pepole][locate][i]);
        }
    }
}

int main() {
    cin >> n >> m;
    for (int i = 0; i < m; i++) {
        cin >> a >> b >> c >> d;
        atime[0][a][b] = atime[0][b][a] = c;
        atime[1][a][b] = atime[1][b][a] = d;
    }
    func(0, 1, 0);
    func(1, 1, 0);
    sort(ans[0], ans[0] + ans_num[0]);
    sort(ans[1], ans[1] + ans_num[1]);
    for (int i = 0; i < ans_num[0]; i++) {
        for (int j = 0; j < ans_num[1]; j++) {
            if (ans[0][i] == ans[1][j]) {
                cout << ans[0][i] << endl;
                return 0;
            }
        }
    }
    cout << "IMPOSSIBLE" << endl;
    return 0;
}
```

