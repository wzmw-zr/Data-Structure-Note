# Leetcode 703题解

**题目描述：**

求数据流中的第K大元素。函数需要一个同时接收整数 `k` 和整数数组`nums` 的构造器，它包含数据流中的初始元素。每次调用 `KthLargest.add`，返回当前数据流中第K大的元素。

**分析：**

这题算是一道排序题，但是，由于题目中说到了数据流，并且要求动态更新，**正常的排序算法都是一次性成型，无法动态更新，故而无法有效处理数据流**。而**堆的数据结构是可以处理数据流并且动态更新的**，所以使用堆的数据结构。

虽然堆只有大顶堆，小顶堆，但是我们可以利用它们的性质来进行问题的求解。

这里构造一个小顶堆，每次新来一个元素，先判断其与堆顶元素的大小，若小于堆顶元素，则丢弃，否则赋值给堆顶元素，更新堆。

**易错点：**

1. 堆的结构操作上的易错点：自上向下、自下向上调整时的下标更新。

   (**一定要根据堆的调整规则来进行调整，自下向上和自上向下都有其规则！！！**)

2. Leetcode 经典坑：数组为空

3. 开始时的构造数组元素没有k个

**代码：**

```c++
typedef struct {
    int *data, size, cnt;
} KthLargest;

void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

// 自上而下更新的操作
void update(KthLargest *h, int p) {
    int ind = p;
    while ((ind << 1) <= h->cnt) {
        int temp = ind, l = ind << 1, r = ind << 1 | 1;
        if (l <= h->cnt && h->data[temp] > h->data[l]) temp = l;
        if (r <= h->cnt && h->data[temp] > h->data[r]) temp = r;
        if (temp == ind) return ;
        swap(&h->data[ind], &h->data[temp]);
        ind = temp;
    } 
}

KthLargest* kthLargestCreate(int k, int* nums, int numsSize) {
    KthLargest *p = (KthLargest *) malloc(sizeof(KthLargest));  
    p->data = (int *) malloc(sizeof(int) * (k + 1));
    p->size = k;
    p->cnt = 0;
    for (int i = 0; i < numsSize; i++) {
        // 开始构造堆时是自下而上调整，这是为了防止构造数组数量不够
        if (p->cnt < k) {
            p->data[++p->cnt] = nums[i];
            int ind = p->cnt;
            while (ind >> 1 && p->data[ind] < p->data[ind >> 1]) {
                swap(&p->data[ind], &p->data[ind >> 1]); 
                ind >>= 1;
            }
        } else {
            //数量够了之后就是与堆顶比较，决定是否更新
            if (p->data[1] > nums[i]) continue;
            p->data[1] = nums[i];
            update(p, 1);
        }
    }
    return p;
}

int kthLargestAdd(KthLargest* p, int val) {
    // 这是构造数组数量不够的情况，此时先继续构造，自下而上调整
    if (p->cnt < p->size) {
        p->data[++p->cnt] = val;
        int ind = p->cnt;
        while (ind >> 1 && p->data[ind] < p->data[ind >> 1]) {
            swap(&p->data[ind], &p->data[ind >> 1]); 
            ind >>= 1;
        }
    } else {
        if (p->data[1] < val) {
            p->data[1] = val;
            update(p, 1);
        }
    }
    return p->data[1];
}

void kthLargestFree(KthLargest* obj) {
    if (!obj) return ;
    free(obj->data);    
    free(obj);
}
```

