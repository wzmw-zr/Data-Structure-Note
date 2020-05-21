# HZOJ 531题解

**题目描述：**

 小明过年的时侯去如姥姥家，除夕之夜，大家都想看春节联欢晚会，而可以依赖的就是一台旧电视。

 那一台旧电视不是遥控器控制的，上面有许多按钮，按下某一按钮，其他按钮都将被释放，只有被按的按钮工作（如果其他按钮本来就是释放的状态，那么它们保持不变，处于按下状态的按钮不能重复按下，这对下文依旧适用）。当小明到来的那一天，上面的许多按钮突然无法正常工作。现在按下某个按钮后，有一些按钮将被释放，而另外的一些按钮将不改变原状态。经过一番惨无人道的折腾，小明知道按下每一个按钮会产生什么样的效果。现在他只需要第3个按钮正常工作。

 现在帮助小明计算，从给定的状态到只有按钮3工作，而其他按钮都被释放这个最终状态所需按下的按钮序列的最短长度。

**分析：**

**这也是一道求最短搜索路径的搜索类的题目，适合使用广度优先搜索来处理，==这里可以使用位运算来节省空间==，实际上，==位运算可以用来优化那些用小数组存储状态的题目==。**

**代码：**

```c++
#include<iostream>
#include<queue>
using namespace std;

typedef struct Node {
    int status, step;
} Node;

int n;
int num[25];
int arr[25][25];
int check[2200000];

void init() {
    int t = 1;
    for (int i = 0; i < 21; i++) {
        num[i] = t; 
        t <<= 1;
    }
}

int main() {
    init();
    cin >> n;
    int start_status = 0;
    for (int i = 1; i <= n; i++) {
        int t;
        cin >> t;
        if (t == 1) start_status += num[i];
    }
    for (int i = 1; i <= n; i++) {
        cin >> arr[i][0];
        for (int j = 1; j <= arr[i][0]; j++) {
            cin >> arr[i][j];
            arr[i][24] += num[arr[i][j]];
        }
    }
    queue<Node> que;
    que.push({start_status, 0});
    check[start_status] = 1;
    while (!que.empty()) {
        Node tmp = que.front(); 
        que.pop();
        if (tmp.status == 8) {
            cout << tmp.step << endl;
            return 0;
        }
        for (int i = 1; i <= n; i++) {
            if ((tmp.status & num[i]) == 0) {
                int status_temp = tmp.status + num[i];
                status_temp &= ~(arr[i][24]);
                if (!check[status_temp]) {
                    check[status_temp] = 1;
                    que.push({status_temp, tmp.step + 1});
                }
            }
        }
    }
    return 0;
}
```



