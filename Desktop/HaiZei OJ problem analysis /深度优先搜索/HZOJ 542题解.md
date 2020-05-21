# HZOJ 542题解

**题目描述：**

现有一块大奶酪，它的高度为 h，它的长度和宽度我们可以认为是无限大的，奶酪中间有许多半径相同的球形空洞。我们可以在这块奶酪中建立空间坐标系，在坐标系中， 奶酪的下表面为 z=0，奶酪的上表面为 z=h。

 现在，奶酪的下表面有一只小老鼠 Jerry，它知道奶酪中所有空洞的球心所在的坐标。如果两个空洞相切或是相交，则 Jerry 可以从其中一个空洞跑到另一个空洞，特别地，如果一个空洞与下表面相切或是相交，Jerry 则可以从奶酪下表面跑进空洞；如果一个空洞与上表面相切或是相交，Jerry则可以从空洞跑到奶酪上表面。

 位于奶酪下表面的 Jerry 想知道，在不破坏奶酪的情况下，能否利用已有的空洞跑到奶酪的上表面去?

 空间内两点P1(x1,y1,z1)、P2(x2,y2,z2)的距离公式如下：

$dist(P1,P2)= \sqrt{(x1−x2)^2+(y1−y2)^2+(z1−z2)^2}$



**分析：**

**这是一道搜索类的题目，搜索类型的题目最直接的就是深度优先搜索，并且可以看出，这实际上就是一道图论问题，可以将两个圆孔是否相切或相交等价为两个节点之间是否有边。**

**关于==搜索类型的问题，一部分与图论相关，一个核心问题是如何将给出的信息转变为图的信息与存储，做题的话临接矩阵是不错的选择。==**



**代码：**

```c++
#include<iostream>
#include<algorithm>
#include<cstring>
#include<cmath>
using namespace std;


int t, n, h, r, qiu[1005][3], low[1005], low_num, up[1005], arr[1005][1005], check[1005];


int func(int now) {
    if (up[now]) return 1; 
    for (int i = 1; i <= n; i++) {
        if (arr[now][i] && !check[i]) {
            check[i] = 1;
            if (func(i)) return 1;
        }
    }
    return 0;
}

int main() {
    cin >> t;
    while (t--) {
        memset(low, 0, sizeof(low));
        memset(up, 0, sizeof(up));
        memset(arr, 0, sizeof(arr));
        memset(check, 0, sizeof(check));
        low_num = 0;
        cin >> n >> h >> r;
        for (int i = 1; i <= n; i++) {
            cin >> qiu[i][0] >> qiu[i][1] >> qiu[i][2];
            if (qiu[i][2] <= r) {
                low[low_num++] = i;
            }
            if (qiu[i][2] + r >= h) {
                up[i] = 1;
            }
            for (int j = 1; j <= i; j++) {
                int t0 = qiu[i][0] - qiu[j][0];
                int t1 = qiu[i][1] - qiu[j][1];
                int t2 = qiu[i][2] - qiu[j][2];
                if (sqrt(t0 * t0 + t1 * t1 + t2 * t2) <= 2 * r) {
                    arr[i][j] = arr[j][i] = 1;
                }
            }
        }
        int flag = 0;
        for (int i = 0; i <= low_num; i++) {
            if (check[i] == 0) {
                check[i] = 1;
                if (func(i)) {
                    cout << "Yes" << endl;
                    flag = 1;
                    break;
                }
            }
        }
        if (flag == 0) cout << "No" << endl;
    }
    return 0;
}
```



