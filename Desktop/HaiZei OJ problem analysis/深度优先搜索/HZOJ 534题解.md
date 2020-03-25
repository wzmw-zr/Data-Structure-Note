# HZOJ 534 题解

**题目描述：**

 给出 n 件物品，每个物品有一个体积 Vi，从中取出若干件物品能够组成的不同的体积和有多少种可能。例如，n=3 , Vi={1,3,4}，那么输出 6，6 种不同的体积和为 1,3,4,5,7,8。

**分析：**

这是排列枚举类型的题目，可以使用深度优先搜索(递归求解)。

**代码：**

```c++
#include<iostream>
#include<set>
using namespace std;

int n, num[25], check[1000] = {1}, ans;

void func(int s, int sum) {
    if (check[sum] == 0) {
        ans++;
        check[sum] = 1;
    } 
    for (int i = s; i <= n; i++) {
        sum += num[i];
        func(i + 1, sum);
        sum -= num[i];
    }
}

int main() {
    cin >> n;
    for (int i = 0; i < n; i++) cin >> num[i];
    func(0, 0);
    cout << ans << endl;
    return 0;
}
```

