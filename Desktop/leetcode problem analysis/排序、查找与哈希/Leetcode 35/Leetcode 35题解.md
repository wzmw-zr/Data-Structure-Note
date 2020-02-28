# Leetcode 35题解

**题目描述：**给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

**分析：**这是一个查找类型的题目，在查找类型题目中出现已排序等字眼意味着这题可能会使用到二分查找。

**代码：**

```c++
int searchInsert(int* nums, int numsSize, int target){
    int l = 0, r = numsSize - 1, mid;
    while (l <= r) {
        int mid = (l + r) >> 1;
        if (nums[mid] == target) return mid;
        if (nums[mid] < target) l = mid + 1;
        else r = mid - 1;
    }
    return l;
}
```

