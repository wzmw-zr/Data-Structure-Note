# Leetcode 349题解

**题目描述：**给定两个数组，编写一个函数来计算它们的交集。

**分析：**关于求两个数组的交集问题，可以参考归并排序中的合并操作，由于题目给的数据不是有序的，需要先进行排序，又因为交集是不允许有重复元素的，所以还需要查找操作，在排完序之后交集实际上也是有序的，那么就可以使用二分查找。

**易错点：**

leetcode经典坑：数组为空

**代码：**

```c++
void merge(int *num, int l, int r) {
    int mid = (l + r) >> 1, temp[r - l + 1];
    int x = l, y = mid + 1, i = 0;
    while (x <= mid || y <= r) {
        if (x <= mid && (y > r || num[x] < num[y])) temp[i++] = num[x++];
        else temp[i++] = num[y++];
    }
    memcpy(num + l, temp, sizeof(int) * (r - l + 1));
}

void merge_sort(int *num, int l, int r) {
    if(l == r) return ;
    int mid = (l + r) >> 1;
    merge_sort(num, l, mid); 
    merge_sort(num, mid + 1, r);
    merge(num, l, r);
}

int find(int *num, int target, int size) {
    int l = 0, r = size - 1, mid;
    while (l <= r) {
        mid = (l + r) >> 1;
        if (num[mid] == target) return 1;
        if (num[mid] < target) l = mid + 1;
        else r = mid - 1;
    }
    return 0;
}


int* intersection(int* nums1, int nums1Size, int* nums2, int nums2Size, int* returnSize){
    // 防止两个数组有一个为空
    if (!nums1Size || !nums2Size) {
        *returnSize = 0;
        return NULL;
    }
    int *temp = (int *) malloc(sizeof(int) * (nums1Size + nums2Size));
    merge_sort(nums1, 0, nums1Size - 1);
    merge_sort(nums2, 0, nums2Size - 1);
    int p1 = 0, p2 = 0;
    *returnSize = 0;
    while (p1 < nums1Size && p2 < nums2Size) {
        if (nums1[p1] == nums2[p2]) {
            if (!find(temp, nums1[p1], *returnSize)) {
//这里实际上还是存在疑问，*和++的优先级问题，为了防止类似的错误，之后这里加上括号
                temp[(*returnSize)++] = nums1[p1];
            } 
            p1++, p2++;
        } else if (nums1[p1] < nums2[p2]) p1++;
        else p2++;
    }
    return temp;
}
```

