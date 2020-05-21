# HZOJ 540 题解

**题目描述：**

 李华要过生日了，正好小明有两张购物券，所以他决定购买 N 件礼物送给李华。小明选好了 N 件礼物，并且总价值正好为两张购物券之和，但是结账时，小明被告知，一次只能使用一张购物券，不找零，不补现，这就意味着，小明购买的 N 件物品中，必须有若干物品的总价值正好等于其中一张购物券的价值。现求小明能不能用购物券顺利的购物。($N \le 40$)

**分析：**

**由于$N \le 40$，强行递归会造成超时，解题思路是缩小问题规模，将物品分成两部分，深度优先搜索所有的合法的和，之后对两堆结果中找是否有和等于给定值的。**

**==因此，此题给出的一个启发，在数据规模不大但也不小的情况下，缩小数据规模，分成两个部分进行深度优先搜索，找到所有排列与值。==**

**代码：**

```c++
#include<iostream>
#include<algorithm>
using namespace std;

int n, cost, num[45], set[2][2100000], cnt[2];

void func(int set_num, int start, int end, int sum) {
    if (sum > cost) return ;
    for (int i = start; i < end; i++) {
        sum += num[i];
        set[set_num][cnt[set_num]] = sum;
        cnt[set_num]++;
        func(set_num, i + 1, end, sum);
        sum -= num[i];
    }
}

int main() {
    while (cin >> n >> cost) {
        cnt[0] = cnt[1] = 0;
        for (int i = 0; i < n; i++) {
            cin >> num[i];
        } 
        func(0, 0, n >> 1, 0);
        func(1, n >> 1, n, 0);
        sort(set[1], set[1] + cnt[1]);
        int flag = 0;
        for (int i = 0; i < cnt[0]; i++) {
            int target = cost - set[0][i];
            int l = 0, r = cnt[1] - 1;
            while (l <= r) {
                int mid = (l + r) >> 1;
                if (set[1][mid] == target) {
                    cout << "Yes" << endl;
                    flag = 1;
                    break;
                }
                if (set[1][mid] < target) l = mid + 1;
                else r = mid - 1;
            }
            if (flag == 1) break;
        }
        if (flag == 0) cout << "No" << endl;
    }
    return 0;
}
```



