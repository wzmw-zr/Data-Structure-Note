# Leetcode 4题解

**题目描述：**给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。nums1 和 nums2 不会同时为空。

**分析：**最直接的思路就是合并两个数组，然后通过下标直接找到中位数，但是这个的时间复杂度是$O(m+n)$级别的，这里先实现这种方法，至于$O(\log{(m+n)})$级别的时间复杂度应当是使用折半查找。

**$O(m+n)$复杂度的代码：**

```c++
double findMedianSortedArrays(int* nums1, int nums1Size, int* nums2, int nums2Size){
    int temp[nums1Size + nums2Size + 5], l = 0, r = 0, i = 0;
    while (l < nums1Size || r < nums2Size) {
        if (l < nums1Size && (r >= nums2Size || nums1[l] < nums2[r])) temp[i++] = nums1[l++];
        else temp[i++] = nums2[r++];
    }
    if ((nums1Size + nums2Size) & 1) return (double) temp[(nums1Size + nums2Size) >> 1];
    int mid = (nums1Size + nums2Size) >> 1;
    return ((double)temp[mid - 1] + (double)temp[mid]) / 2;
}
```

