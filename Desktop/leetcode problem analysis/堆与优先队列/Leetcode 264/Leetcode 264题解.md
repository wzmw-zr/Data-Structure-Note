# Leetcode 264题解

**题目描述：**

丑数就是只包含质因数 `2, 3, 5` 的**正整数**，找出第 `n` 个丑数。

**分析：**

这是查找类型的题目，直接思路就是从最基础的2,3,5开始对于每个数字都乘以2,3,5加入一个集合，**这种具有动态生成特点的查找、排序类型的题目适合使用堆来求解**。这里使用小顶堆，每次弹出堆顶元素后乘以2,3,5再插入。

但是这里采用的方法会有重复数据被插入，**解决重复数据的方法之一，哈希表，**或者使用后期处理。

**代码：**

```c++
typedef struct Heap {
    long *data;
    int size, cnt;
} Heap;

Heap *init() {
    Heap *h = (Heap *) malloc(sizeof(Heap));
    h->data = (long *) malloc(sizeof(long) * 10001);
    h->size = 10000;
    h->cnt = 0;
    return h;
}

void swap(long *a, long *b) {
    long temp = *a;
    *a = *b;
    *b = temp;
}

void expand(Heap *h) {
    if (!h) return ;
    int expand_size = h->size, *p;
    while (expand_size) {
        p = (long *) realloc(h->data, sizeof(long) * (h->size + expand_size + 5));
        if (p) break;
        expand_size >>= 1;
    }
    if (expand_size) {
        h->data = p;
        h->size += expand_size;
    }
}

void push_heap(Heap *h, long val) {
    if (h->size == h->cnt) expand(h);
    h->data[++h->cnt] = val;
    int ind = h->cnt;
    while (ind >> 1 && h->data[ind] < h->data[ind >> 1]) {
        swap(&h->data[ind], &h->data[ind >> 1]);
        ind >>= 1;
    }
}

long pop_heap(Heap *h) {
    long ret = h->data[1];
    h->data[1] = h->data[h->cnt--];
    int ind = 1;
    while ((ind << 1) <= h->cnt) {
        int temp = ind , l = ind << 1, r = ind << 1 | 1;
        if (l <= h->cnt && h->data[temp] > h->data[l]) temp = l;
        if (r <= h->cnt && h->data[temp] > h->data[r]) temp = r;
        if (temp == ind) break;
        swap(&h->data[ind], &h->data[temp]);
        ind = temp;
    }
    return ret;
}

void clear(Heap *h) {
    if (!h) return ;
    free(h->data);
    free(h);
}

int nthUglyNumber(int n){
    Heap *h = init();
    push_heap(h, 1);
    // 按照这种方法获得的数字是有重复的，所以需要一个变量来记录上一个数据，如果相同就一直弹出堆顶元素
    long pre = 0, ret;
    while (n) {
        ret = pop_heap(h);
        if (ret == pre) continue;
        push_heap(h, ret * 2);
        push_heap(h, ret * 3);
        push_heap(h, ret * 5);
        pre = ret;
        n--;
    }
    clear(h);
    return (int) ret;
}
```

