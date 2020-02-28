# Leetcode 278题解

**题目描述：**你是产品经理，目前正在带领一个团队开发新的产品。不幸的是，你的产品的最新版本没有通过质量检测。由于每个版本都是基于之前的版本开发的，所以错误的版本之后的所有版本都是错的。

假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。

**分析：**这是查找类型的题目，而且这道题目具有明显的元素划分界限，同时要求寻找第一个(也可以是最后一个)满足某种性质的元素，那么我们可以使用二分查找来进行检索，效率优于顺序查找。

**代码：**

```c++
// Forward declaration of isBadVersion API.
bool isBadVersion(int version);

long firstBadVersion(long n) {
    long l = 1, r = n + 1, mid;
    while (l < r) {
        mid = (l + r) >> 1;
        if (isBadVersion((int) mid)) r = mid;
        else l = mid + 1;
    } 
    return l;
}
```

