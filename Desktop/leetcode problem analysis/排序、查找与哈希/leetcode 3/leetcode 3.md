 # Leetcode 3题解

**题目描述：**给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

**分析：**这是查找类型的问题，在查找类型的问题中，涉及到 **“不重复”**等字眼的，一般就是使用哈希表进行求解。

**易错点：**当一个字符已经出现过一次时，要从这个字符上次出现的位置的下一位开始向后处理，因为从**上一次出现的位置到现在的位置中间的部分是没有重复字符的。**

**实现代码：**

```c++
// 定义此结构是为了能够找到上一次出现的重复字符的下标。
typedef struct Node {
    char c;
    int ind;
} Node;

typedef struct HashTable {
    Node **str;
    int size, cnt;
} HashTable; 

Node *NewNode(char c, int ind) {
    Node *node = (Node *) malloc(sizeof(Node));
    node->c = c;
    node->ind = ind;
    return node;
}

HashTable *init(int n) {
    HashTable *h = (HashTable *) malloc(sizeof(HashTable));
    h->str = (Node **) calloc(sizeof(Node *), (n + 1));
    h->size = n;
    h->cnt = 0;
    return h;
}

int insert(HashTable *h, char c, int ind) {
    if (!h) return 0;
    h->str[c] = NewNode(c, ind); 
    h->cnt++;
    return 1;
}

int find(HashTable *h, char c) {
    if (h->str[c]) return 1;
    return 0;
}

void clear(HashTable *h) {
    if (!h) return ;
    free(h->str);
    free(h);
}

int lengthOfLongestSubstring(char * s){
    HashTable *h = init(10000);
    int max_l = 0, cur_l = 0;
    //遍历字符串
    for (int i = 0; s[i]; i++) {
        // 如果有重复的字符，那么记录下此字符上一次出现的位置，从此位置的下一个开始向后遍历
        // 一定要注意遇到一次重复字符就要清空一次，否则多次遇到重复字符遗留的字符会对后面造成影响
        if (find(h, s[i]))  {
            if (cur_l > max_l) max_l = cur_l;
            i = h->str[s[i]]->ind;
            memset(h->str, 0, sizeof(char) * (h->size) + 1);
            cur_l = 0;
        } else {
            insert(h, s[i], i);
            cur_l++;
        }
    }
    if (cur_l > max_l) max_l = cur_l;
    return max_l;
}
```



