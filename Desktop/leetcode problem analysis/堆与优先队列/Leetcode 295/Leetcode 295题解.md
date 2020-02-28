# Leetcode 295题解

**题目描述：**求数据流的中位数，插入与查找操作并存。



**分析：**

这是一道查找类型的问题，数据流意味着数据量非常大，中位数意味着这需要进行排序，而一般的排序算法适合解决数量固定的数据，并且不支持动态查找。所以采用堆来解决这种问题。

**堆可以用来解决数据流的中位数问题，这时单单靠一个堆是不行的，需要一个大顶堆和一个小顶堆。**大顶堆用来存储小的一半数据，小顶堆用来存储大的一半数据。

**考虑到中位数的特性，我们需要让小一半的大顶堆的元素数量和大一半的小顶堆的元素数量相等或者多1。**



**易错点：**

每次在插入新元素时如何维护两个堆是容易出错。这里采用的思路如下：

1. 当新数据大于大一半的小顶堆堆顶元素时：
   + 若两个堆元素数量一样，弹出大一半的小顶堆堆顶元素插入另一个堆中，新元素入小顶堆
   + 若小一半的大顶堆数量比大一半的小顶堆多一个，那么新元素插入大一半的小顶堆
2. 否则：
   + 若两个堆元素数量一样，就将新元素插入小一半的大顶堆中
   + ==若**小一半的大顶堆元素数量比大一半的小顶堆元素数量多一个**，**先弹出小一半的大顶堆堆顶元素，和新元素比较，小的入小一半，大的入大一半。**==



**代码：**

```c++
typedef struct Heap {
    int *data, size, cnt;
} Heap;

typedef struct {
    //left是大顶堆，right是小顶堆
    Heap *left, *right;
} MedianFinder;

void swap(int *a, int *b) {
    int temp =*a;
    *a = *b;
    *b = temp;
}

Heap *initHeap() {
    Heap *h = (Heap *) malloc(sizeof(Heap));
    h->data = (int *) calloc(sizeof(int), 10001);
    h->size = 10000;
    h->cnt = 0;
    return h;
}

// 扩容操作，防止空间不够用
void expand(Heap *h) {
    if (!h) return ;
    int exp_size = h->size, *p;
    while (exp_size) {
        p = (int *) realloc(h->data, sizeof(int) * (h->size + exp_size + 5)); 
        if (p) break;
        exp_size >>= 1;
    }
    if (p) {
        h->data = p;
        h->size += exp_size;
    }
}

MedianFinder* medianFinderCreate() {
    MedianFinder *m = (MedianFinder *) malloc(sizeof(MedianFinder)); 
    m->left = initHeap(); 
    m->right = initHeap();
    return m;
}

// 大顶堆，小顶堆的插入删除操作
void max_insert(Heap *h, int val) {
    if (h->cnt == h->size) expand(h);
    h->data[++h->cnt] = val;
    int ind = h->cnt;
    while (ind >> 1 && h->data[ind] > h->data[ind >> 1]) {
        swap(&h->data[ind], &h->data[ind >> 1]);
        ind >>= 1;
    } 
}

int max_pop(Heap *h) {
    int ret = h->data[1];
    h->data[1] = h->data[h->cnt--];
    int ind = 1;
    while ((ind << 1) <= h->cnt) {
        int temp = ind, l = ind << 1, r = ind << 1 | 1;
        if (l <= h->cnt && h->data[temp] < h->data[l]) temp = l;
        if (r <= h->cnt && h->data[temp] < h->data[r]) temp = r;
        if (ind == temp) break;
        swap(&h->data[ind], &h->data[temp]);
        ind = temp;
    }
    return ret;
}

void min_insert(Heap *h, int val) {
    if (h->cnt == h->size) expand(h);
    h->data[++h->cnt] = val;
    int ind = h->cnt;
    while (ind >> 1 && h->data[ind] < h->data[ind >> 1]) {
        swap(&h->data[ind], &h->data[ind >> 1]);
        ind >>= 1;
    } 
}

int min_pop(Heap *h) {
    int ret = h->data[1];
    h->data[1] = h->data[h->cnt--];
    int ind = 1;
    while ((ind << 1) <= h->cnt) {
        int temp = ind, l = ind << 1, r = ind << 1 | 1;
        if (l <= h->cnt && h->data[temp] > h->data[l]) temp = l;
        if (r <= h->cnt && h->data[temp] > h->data[r]) temp = r;
        if (ind == temp) break;
        swap(&h->data[ind], &h->data[temp]);
        ind = temp;
    }
    return ret;
}

// 新加元素
void medianFinderAddNum(MedianFinder* obj, int num) {
    // 先处理第一、第二个元素
    if (obj->right->cnt == 0) {
        min_insert(obj->right, num);
        return ;
    }
    if (obj->left->cnt == 0) {
        if (obj->right->data[1] > num) max_insert(obj->left, num);
        else {
            obj->left->data[1] = obj->right->data[1];
            obj->right->data[1] = num;
            obj->left->cnt++;
        }
        return ;
    }
    // 新元素大于大一半的小顶堆的堆顶元素时
    if (num > obj->right->data[1]) {
        // 两个堆元素数量相同，弹出大一半的小顶堆堆顶元素入小一半，新元素入大一半
        if (obj->left->cnt == obj->right->cnt) {
            int number = min_pop(obj->right);
            max_insert(obj->left, number);
            min_insert(obj->right, num);
        }
        // 否则新元素入大一半的小顶堆
        else {
            min_insert(obj->right, num);
        }
    // 新元素小于等于大一半的小顶堆的堆顶元素
    } else {
        //两堆元素数量相等，加入小一半的大顶堆
        if (obj->left->cnt == obj->right->cnt) {   
            max_insert(obj->left, num);
        } else {
            // 否则，弹出小一半的堆顶元素，与新元素比较，小的加入小一半，大的加入大一半
            int number = max_pop(obj->left);
            if (number < num) swap(&number, &num);
            min_insert(obj->right, number);
            max_insert(obj->left, num);
        }
    }
}

double medianFinderFindMedian(MedianFinder* obj) {
    if (obj->left->cnt < obj->right->cnt) return (double) obj->right->data[1];
    if (obj->left->cnt > obj->right->cnt) return (double) obj->left->data[1];
    return ((double) obj->left->data[1] + (double) obj->right->data[1]) / 2;
}

void clearHeap(Heap *h) {
    if (!h) return ;
    free(h->data);
    free(h);
}

void medianFinderFree(MedianFinder* obj) {
    if (!obj) return ;
    clearHeap(obj->left);
    clearHeap(obj->right);
    free(obj);
}
```



