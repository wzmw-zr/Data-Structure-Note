# HZOJ 537题解

**题目描述：**

 有很多人在门口排队，每个人将会被发到一个有效的通行密码作为门票。一个有效的密码由 L 个小写字母组成，至少有一个元音 (a,e,i,o,u) 和两个辅音，并且是按字母表顺序出现的，例如 abc是有效的，而cba 不是。

 现在给定一个期望长度 L 和 C 个小写字母，输出所有有效密码。

**分析：**

这是排列枚举类型的问题，可以使用深度优先搜索(递归)求解，不过实现需要对输入字符集进行排序。

**代码：**

```c++
#include<iostream>
#include<algorithm>
using namespace std;

int l, c, ans_cnt, fu, yuan, cnt;
char letter[30], ans[30];

int func(int s, int left) {
    if (left == 0) {
        if (yuan >= 1 && fu >= 2) {
            for (int i = 0; i < ans_cnt; i++) cout << ans[i];
            cout << endl;
            cnt++;
            if (cnt == 25000) {
                return -1;
            }
        }
        return 0;
    }
    for (int i = s; i < c; i++) {
        ans[ans_cnt] = letter[i];
        ans_cnt++;
        int f = 0;
        if (letter[i] == 'a' || letter[i] == 'e' || letter[i] == 'o' || letter[i] == 'i' || letter[i] == 'u') {
            yuan++;
            f = 1;
        } else {
            fu++;
        }
        if (func(i + 1, left - 1) == -1) return -1;
        if (f == 1) yuan--;
        else fu--;
        ans_cnt--;
    }
    return 0;
}

int main() {
    cin >> l >> c;
    for (int i = 0; i < c; i++) {
        cin >>letter[i];
    }
    sort(letter, letter + c);
    func(0, l);
    return 0;
}
```

**在使用深度优先搜索(递归)时，递归下降和递归回溯过程中的操作有时候是成对出现的，需要注意。**