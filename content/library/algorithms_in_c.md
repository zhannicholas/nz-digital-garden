---
title: "《C 算法》读书笔记"
date: 2018-06-02T10:07:28+08:00
draft: false
toc: true
tags:
  - 算法
categories:
  - 读书笔记
authors: Nicholas Zhan
---

# 二叉树

树是满足一定要求的顶点和边的非空集合。二叉树的每个节点至多有2个子节点。
一种表示方法：

```C++
struct Node{type key; Node *lchild, *richild;}
typedef Node *link;
```

这种表示方法只适合从根节点开始自顶向下的操作，而不适合自底向上的操作。不过可以在节点的定义中加入指向父节点的连接支持这种功能。
与二叉树类似的还有M叉树，它的每个节点最多只有M个节点。广义的树每个节点可以有任意多个子节点，可以用二叉树来表示它们，方法就是——“左孩子，右兄弟”。树的序列就形成了有序森林。
> 二叉树和有序森林之间存在一一的对应关系。

## 二叉树的一些数学性质

* 一棵二叉树有 **`N`** 个内部节点，有 **`N + 1`** 个外部节点（叶子节点）。
* 包含 **`N`** 个内部节点的二叉树有 **`2N`** 个链接： **`N - 1`** 个外部节点的链接和 **`N + 1`** 个内部节点的链接。
* 树中节点的所在的层是它的父节点的下一层（根节点位于第 **`0`** 层）。树的高度为树节点的最大层。树的路径长度为所有树节点的层总和：外部路径长度为所有外部节点的层总和，内部路径长度为所有内部节点的层总和。
  这里有一个计算树路径长度的简便方法：对于所有的 **`k`** , 求 **`k`** 与 **`k`** 层节点数之积的总和。
* 具有 **`N`** 个内部节点的二叉树的外部路径长度比内部路径长度大 **`2N`** 。
* 具有 **`N`** 个内部节点的二叉树的高度的最小值为 **`lgN`** ，最大值为 **`N - 1`** 。
  当树退化成只有一个叶子节点的时候，就是最坏的情况。
* 具有 **`N`** 个内部节点的二叉树内部路径长度最小值为 **`Nlg(N/4)`** ，最大值为 **`N(N - 1)/2`** 。

## 树的遍历

* 前序遍历
  根->左孩子->右孩子
* 中序遍历
  左孩子->根->右孩子
* 后序遍历
  左孩子->右孩子->根
* 层次遍历
  从上到下，从左到右

前序遍历（递归版）：

```C++
void preorderTraverse(link h, void visit(link)){
    if(h == root) return;
    visit(h);
    preorderTraverse(h -> lchild, visit);
    preorderTraverse(h -> rchild, visit);
}
```

前序遍历（非递归版）：

```C++
void preorderTraverse(link h, void visit(link)){
    stack<link> stk(maxn);
    s.push(h);
    while(!s.empty()){
        visit(s.top());
        s.pop();
        if(h -> lchild != null) s.push(h -> lchild);
        if(h -> rchild != null) s.push(h -> rchild);
    }
}
```

后序遍历和中序遍历只需要交换前序遍历中访问节点的顺序即可。
层次遍历：

```C++
void levelTraverse(link h, void visit(link)){
    queue<link> q(maxn);
    q.push(h);
    while(!q.empty()){
        visit(q.front());
        q.pop();
        if(h -> lchild != null) q.push(q -> lchild);
        if(h -> rchild != null) q.push(q -> rchild);
    }
}
```

计算树含有的节点数：

```C++
int count(link h){
    if(h == null) return 0;
    return count(h -> lchild) + count(h -> rchild) + 1;
}
```

计算树的高度：

```C++
int height(link h){
    if(h == null) return -1;
    return max(height(h -> lchild), height(h -> rchild)) + 1;
}
```

## 二叉搜索树

二叉树搜索树是一棵二叉树，它要么是一棵空树，要么具有以下性质：

1. 若任意节点的左子树不为空，则左子树上所有节点的值不大于它的根节点的值
2. 若任意节点的右子树不为空，则右子树上所有节点的值不小于它的根节点的值
3. 任意节点的左右子树都是二叉搜索树
4. 树中没有键值相等的节点

### 二叉搜索树中的查找

在二叉搜索树h中查找v的过程如下：

1. 若h是空树，则返回查找失败，否则：
2. 若x为根节点对应的数据值，则查找成功，否则：
3. 若x小于根节点对应的数据值，则查找左子树，否则：
4. 查找右子树

```C++
Item searchP(link h, type v){
    if(h == 0) return nullItem;
    type t = h -> item.getKey();
    if(t == v) return h -> item;
    if(v < t) searchP(h -> lchild, v);
    else searchP(h -> rchild, v);
}
```

### 在二叉搜索树中插入节点

在二叉搜索树h中插入节点v的过程如下，其中插入的节点总是叶子节点：

1. 若h是空树，则将v所指的节点作为根节点插入，否则：
2. 若v对应的数据值小于根节点对应的数据值，则在左子树中插入，否则：
3. 在右子树中插入

```C++
void insertP(link &h, Item x){
    if(h == 0) {h = new Node(x);return;}
    if(x.getKey() < h -> item.getKey()) insertP(h -> lchild, x);
    else insertP(h -> rchild, x);
}
```

上面的插入只适用于插入的节点最终是叶子节点的情况，可以通过这种方式来构造一棵树来对数据进行排序。对于插入的节点不一定到达叶子节点的情况，需要考虑其它的插入方法。旋转是树的一种基本变换，它允许交换树中根及其一个孩子的角色，同时保持节点中键的次序。
涉及到3个链接和两个节点。

> 右旋（**左孩子为轴，当前节点右旋**）。结果就是：原来的左孩子成为了新的根，原来左孩子的左孩子依旧是新根的左孩子，旧根的右孩子依旧是旧根的右孩子。旧根成为了新根的右孩子，原来左孩子的右孩子成为了旧根（新根的右孩子）的左孩子。

![rotateR](/images/algorithms_in_c/rotateR.jpg)

```C++
void rotateR(link &h){
    link t = h -> lchild;
    h -> lchild = t -> rchild;
    t -> rchild = h;
    h = t;
}
```

> 左旋和右旋相反。**右孩子为轴，当前节点左旋**。原来的右孩子成为了新的根，旧根成为了新根的左孩子。原来的右孩子的左孩子成为了旧根的右孩子。

![rotateL](/images/algorithms_in_c/rotateL.jpg)

```C++
void rotateL(link &h){
    link t = h -> rchild;
    h -> rchild = h -> lchild;
    t -> lchild = h;
    h = t;
}
```

有了左旋和右旋之后，就能迅速得到在BST的根插入新节点的递归函数，再适当子树的根插入新项，然后通过旋转将它带到主树的根。

```C++
void insertT(link &h, Item x){
    if(h == 0){h = new Node(x);return;}
    if(x.getKey() < h -> item.getkey()){insertT(h -> lchild, x); rotateR(h);}
    else{insertT(h -> rchild,x); rotateL(h);}
}
```

### 在二叉搜索树中选择节点

可以采用快速排序划分的思想来选择BST中第 **`k`** 小的节点。不过这需要给结点增加一个计数域，然后还需要修改其他所有的函数。

```C++
Item selectR(link h, int k){
    if(h == 0) return nullItem;
    int c = (h -> lchild == 0) ? 0 : h -> lchild -> cnt;
    if(c > k) return selectR(h -> lchild, k);
    if(c < k) return selectR(h -> rchild, k - c - 1);
    return h -> item;
}
```

### 对二叉搜索树进行划分

可以将选择运算修改为划分运算，它重排树，利用左旋和右旋将第 **`k`** 小的元素放到根。

```C++
link partition(link &h, int k){
    int c = (h -> lchild == 0) ? 0 : h -> lchild -> cnt;
    if(c > k) {partition(h -> lchild, k); rotateR(h);}
    if(c < k) {partition(h -> rchild, k - c - 1); rotateL(h);}
}
```

### 在二叉搜索树中删除节点

从BST删除一个节点，首先检查该节点是否在其中一棵子树中。如果是则用递归删除节点后的结果替换子树。如果删除的节点在根部，则需要用合并两棵子树的结果替换原来的树。

```C++
link joinLR(link l, link r){
    if(r == 0) return l;
    partition(r, 0);
    r -> lchild = l;
    return r;
}

void removeR(link &h, type v){
    if(h == 0) return;
    type w = h -> item.getKey();
    if(v < w) removeR(h -> lchild, v);
    if(v > w) removeR(h -> rchild, v);
    if(v == w){
        link t = h;
        h = joinLR(h -> lchild, h -> rchild);
        delete t;
    }
}
```

### 合并两棵二叉搜索树

书中的一个线性时间的递归实现：首先，利用根插入将第一课BST的根插入到第二棵BST中。这会得到两棵键小于根的子树和两棵键大于根的子树。然后递归的合并根左子树的前一对与根右子树的后一对来得到结果。

```C++
link joinAB(link a, link b){
    if(a == 0) return b;
    if(b == 0) return a;
    insertT(b, a -> item);
    b -> lchild = jionAB(a -> lchild, b -> lchild);
    b -> rchild = jionAB(a -> rchild, b -> rchild);
    delete a;
    return b;
}
```

## 2-3-4树

2-3-4树可以在O(logN)的时间内完成查找、插入、和删除操作。

2-3-4树是一棵空树或者是具有以下三类节点的树：

* 2-节点
  它具有一个键，以及具有较小键的左子树和具有较大键的右子树的两个链接。
* 3-节点
  它具有两个键，以及具有较小键的左子树，较大键的右子树和介于节点键之间的中间子树的三个链接。
* 4-节点
  它具有三个键，以及由节点键对应的区间定义的键值的树的四个链接。

### 节点的插入处理

* 如果搜索结束的节点是2-节点，将其变为3-节点。
* 如果搜索结束的节点是3-节点，将其变为4-节点。
* 如果搜索结束的节点是4-节点，将其分裂成两个2-节点，并将中间键上移到节点的父亲（父节点不是4-节点）。但如果父节点也是4-节点呢？更好的一个方法是：在沿树向下的过程中，分解任何4-节点，保证搜索路径不在4-节点终止。具体做法是：每当遇到一个2-节点（父亲）连接到4-节点（孩子），就把它转化为一个3-节点连接到连个2节点；每当遇到一个3-节点连接到4-节点，就把它转换为一个4-节点连接到两个2-节点。

## 红黑树

2-3-4树易于理解，但实现困难。
红黑树是2-3-4树的一种简单抽象表达方式。其基本思想是将2-3-4树表示为标准的BST(仅有2-节点)，但为每个节点添加一个额外的信息位，来为3-节点和4-节点编码。

链接有两种不同的类型：

* 红链接
  红链接将包含3-节点和4-节点的小二叉树捆绑在一起。
* 黑链接
  黑链接将2-3-4树捆绑在一起。

![2-3-4-and-red-black-tree](/images/algorithms_in_c/2-3-4.jpg)

红黑树有两个本质特性：

1. 不用修改BST的标准搜索过程就能工作。
2. 它们与2-3-4树直接对应。因此可以用上2-3-4树的简单插入平衡过程。

如果某个节点有2个红孩子，则它是4-节点的一部分。红黑树的插入开销很小：仅当看到4-节点的时候才采取平衡措施。分解不同4-节点的具体做法可以对照下图来说：
![balance-red-black-tree](/images/algorithms_in_c/balance-red-black-tree.jpg)

* 左一：4-节点的父亲是一个2-节点。
  将中间键上移转换为一个3-节点与两个2-节点的连接（变色）。
* 左二：4-节点的父亲是一个3-节点，并且是它的右孩子。
  变色。
* 左三：4-节点的父亲是一个3-节点，并且是它的左孩子。
  先变色得到两个方向相同的红链接，然后右旋。
* 左四：4-节点的父亲是一个3-节点，并且是它的中间孩子。
  先变色得到两个方向不同的红链接，然后右旋得到两个方向相同的红链接，然后再左旋。

用红黑树表示法实现2-3-4树的插入操作，首先要给修改节点定义，加入颜色位（用 **`1`** 表示红节点， **`0`** 表示黑节点）。在沿树向下的路径中（递归调用之前），检查4-节点，并通过切换所有3-节点的颜色位来分裂它们。当到达底部时，为被插入的项新建一个红节点并返回它的指针。在沿向上的路径中（递归调用之后），检查是否需要执行一次旋转操作：如果路径上有两个相同方向的红链接，则从上方节点进行一次旋转，然后切换颜色位，以形成一个正确的4-节点；如果路径上有两个方向不同的红链接，则从下方的节点执行一次旋转，以简化为另一种情况，留作向上的下一步处理。

```C++
int getColor(link x){if(x == 0) return 0; return x -> color;}
void insertRB(link &h, Item x, int sw){
    if(h == 0){h = new Node(x); return;}
    if(getColor(h -> lchild) && getColor(h -> rchild)){  // 变色, 分裂4-节点为3-节点
        h -> color = 1;
        h -> lchild -> color = 0;
        h -> rchild -> color = 0;
    }
    if(x.getKey() < h -> item.getKey()){  // 向左子树插入
        insertRB(h -> lchild, x, 0);
        if(getColor(h) && getColor(h -> lchild) && sw)  // 两个方向相同的红链接
            rotateR(h);
        if(getColor(h -> lchild) && getColor(h -> lchild -> lchild)){
            rotateR(h);
            h -> color = 0;
            h -> rchild -> colot = 1;
        }
    }
    else{  // 向右子树插入
        inserRB(h -> rchild, x, 1);
        if(getColor(h) && getColor(h -> rchild) && !sw)  // 左旋
            rotateL(h);
        if(getColor(h -> rchild) && getColor(h -> rchild -> rchild)){
            rotateL(h);
            h -> color = 0;
            h -> lchild -> color = 1;
        }
    }
}
```

红黑树的结构性定义：

红黑树是每个节点都带有颜色的BST，颜色为红色或黑色。除了BST的基本要求外，它还必须满足以下要求：

1. 节点要么是红色，要么是黑色
2. 根节点是黑色
3. 所有叶子节点都是黑色
4. 每个红节点必须有两个黑色的子节点（也就是说：不能有两个连续的红链接）。
5. 从任一节点到其每个叶子节点的所有简单路径都包含相同数目的黑节点。


# 哈希表

>键索引搜索方法中，表中的第 **`i`** 个位置保存了键为 **`i`** 对应的项，以便达到快速访问的目的。它将键作为数组的索引，并且依赖于同一范围内不同整数的键作为表的索引。这种方法不适用于更一般的键。哈希方法扩展了键索引搜索，它通过算术运算把键转换为表地址，以达到快速访问的目的。

使用哈希的搜索算法包括两个部分：

* 哈希函数：将键转化为表地址
* 冲突调节：解决映射到同一表地址之间的键的冲突

哈希函数：如果我们有一个长度为 **`M`** 的表，哈希函数要做的就是把键转换为 **`[0, M-1]`** 之间的整数。

## 取模哈希函数

一种常用的方法就是选择一个素数 **`M`** 做为表长，然后： **`h(x) = k mod M`** 。其中 **`k`** 为键所对应的整数。
对于整数键，还可以采用乘法取模法：**`h(x) = [k * α] mod M`** 。 **`α`** 常设为黄金比（0.618033...）。

## 冲突调节

### 链地址法

为每个散列地址建一个链表，将散列到同一个地址的关键字放入相应的链表中。适合难以预测填入散列表的元素个数并且内存不是太充足的情况。

### 线性探测法

如果能够预测填入散列表的元素个数并且内存充足，那么可以使用线性探测法。
当产生冲突时，检查表中的下一个位置，直到找到一个空的位置，然后将项放进去。
线性探测法一次探测可以辨别三种可能的结果：

* 如果表位置包含一个与搜索键匹配的项，则命中；
* 如果表位置为空，则搜索失败；
* 如果表位置包含一个与搜索键不匹配的项，则继续探测更高地址直到出现以上两种情况。

### 再哈希法

再哈希法的基本策略和线性探测法一致。不同的是，他不检查冲突之后的每一个位置，而是使用第二个散列函数得到用于探测序列的固定增量。

## 总结

* 线性探测法是罪最快的（前提：内存足够大，保证哈希表是稀疏的）
* 再哈希法对内存的使用效率最高，但是需要额外的时间来计算第二个哈希函数
* 链地址法最易实现和部署

# 排序

为了方便增加代码的灵活性，我采取了书中作者的部分方法。主要是用 **`type`** 代替了具体的数据类型，以及定义了两个用于比较的宏。通过改变下面内容， 可以很容易的进行其它数据类型的排序。

```C++
typedef int type;
#define less(A, B) (A < B)
#define equal(A, B) (A == B)
```

下面的 **`exchange()`** 函数用来交换两个元素：

```C++
void exchange(type &a, type &b){type t = a; a = b; b = t;}
```

还有用来测试算法正确性的驱动函数，它通过读取用户输入的数字 **`n`** , 产生 **`n`** 个10000以内的随机数作为测试数据。然后调用相应的排序算法并输出排序结果。

```C++
int main(){
    int n;
    scanf("%d", &n);
    type *a = new type[n];
    for(int i = 0;i < n;i++) a[i] = 10000 * (1.0 * rand() / RAND_MAX);
    //selectSort(a, 0, n - 1);
    //insertSort1(a, 0, n - 1);
    //insertSort2(a, 0, n - 1);
    //bubbleSort(a, 0, n - 1);
    //shakerSort(a, 0, n - 1);
    //shellSort(a, 0, n - 1);
    //countSort(a, 0, n - 1);
    //quickSort(a, 0, n - 1);
    //tri_quickSort(a, 0, n - 1);
    //mergeSort(a, 0, n - 1);
    //mergeTD(a, 0, n - 1);
    //oddEvenSort(a, 0, n - 1);
    //heapSort(a, 0, n - 1);
    //radixSort(a, 0, n - 1);
    for(int i = 0;i < n;i++) printf("%-6d", a[i]);
    printf("\n");
    delete []a;
    system("pause");
    return 0;
}
```

## 选择排序

### 步骤

1. 首先，找出数组中最小的元素并用首位置的元素和它交换
2. 然后， 找出数组中次小的元素并用第二个位置的元素和它交换
3. 重复此步骤，直到整个数组排序完成。

### 具体实现

对于从 **`left`** 到 **`right - 1`** 的每个 **`i`** ,用 **`a[i]`**, **`a[i + 1]`**, ..., **`a[right]`** 中的最小元素进行交换。当索引 **`i`** 从左向右遍历时，其左边的元素所处的位置就是其在数组中的最终位置。
所以，当 **`i`** 到达最右端时，整个数组排序完成。

```C++
void selectSort(type a[], int left, int right){
    for(int i = left;i < right;i++){
        int minp = i;  // 假定未排序序列中的第一个为最小值
        for(int j = i + 1;j <= right;j++)
            if(less(a[j], a[minp])) minp = j;
        exchange(a[i], a[minp]);
    }
}
```

## 插入排序

### 步骤

对于未排序的序列，每次取其中的一个数。然后在已排序的序列中从后向前扫描， 找到合适的位置并插入。

### 说明

和选择排序一样，在排序过程中，当前索引左边的元素已经有序，但这并不是他们的最终位置，如果碰到了比它们更小的元素，它们还必须后移，为较小的元素腾出位置。

```C++
void insertSort1(type a[], int left, int right){
    for(int i = left + 1;i <= right;i++)
        for(int j = i;j > left;j--)
            if(less(a[j], a[j - 1])) exchange(a[j], a[j - 1]);
}
```

### 改进

> 与选择排序不同的时，插入排序的运行时间主要取决于输入中键的初始顺序。
当我们碰到的键不大于正被插入的键时，就可以停止 **`less()`** 和 **`exchange()`** 运算，因为左边的序列时已经排序好的了。特别地，当 **`less(a[j - 1], a[j])`** 为真时，我们可以直接跳出内层循环。
不难发现，**`j > left`** 的测试通常是多余的（只有在插入元素时当前看到的最小元素并且到达了数组的起始处，它才为真）。一种改进方法是：让键在 **`a[left]`** 到 **`a[N]`** 中保持有序，并在 **`a[0]`** 中放入一个标记键，它至少与数组中的最小键相同。然后通过测试是否碰到了最小键来同时测试 **`less(a[j - 1], a[j])`** 和 **`j > left`** 这两个条件，让内循环更小，程序更快。
对同一个元素的连续交换效率不高，如果进行两次或者更多的交换，中间变量 **`t`** 的值并没有改变。在第二次或以后的交换中，先保存再重新载入 **`t`** 的值就是浪费时间。

#### 具体做法

* 将数组中最小值放到第一个位置，作为标记。
* 在内循环中，进行单个赋值，而不是连续交换。
* 当正被插入的元素已经就位时，终止内循环。对于每个 **`i`** ，把大于 **`a[i]`** 的排序表 **`a[left], ..., a[i - 1]`** 的所有元素整体向右移动一个位置，再把 **`a[i]`** 放入适当的位置。这样就完成了整个排序的过程。

```C++
void insertSort2(type a[], int left, int right){
    for(int i = left + 1, minp = left;i <= right;i++){
        if(less(a[i], a[left])) minp = i;
        exchange(a[minp], a[left]);
    }
    for(int i = left + 2;i <= right;i++){
        int j = i;
        type v = a[i];  // 先保存a[i]，确保它不会被右移的元素覆盖
        //把大于a[i]的所有元素整体向右移动一个位置
        for(;less(v, a[j - 1]);j--) a[j] = a[j - 1];
        a[j] = v;  // 放入a[i]到适当的位置
    }
}
```

## 冒泡排序

### 步骤

1. 从左到右比较相邻的两个元素，如果第一个比第二个大，就交换它们
2. 对每一对元素重复第1步，结束时，最大的元素在排序序列的最右端。
3. 对除最后一个以外的所有元素重复以上步骤。
4. 重复直到没有任何一对数字需要比较。

代码如下：

```C++
void bubbleSort(type a[], int left, int right){
    for(int i = left;i <= right;i++){
        for(int j = left;j <= right - i - 1;j++)
            if(less(a[j + 1], a[j])) exchange(a[j + 1], a[j]);
    }
}
```

## 摇摆排序

冒泡排序改良版。将单向扫描数组改成从头到尾，再从尾到头的交替方式

```C++
void shakerSort(type a[], int left, int right){
    for(int i = left;i <= right;i++){
        for(int j = left;j <= right - i - 1;j++)
            if(less(a[j + 1], a[j])) exchange(a[j + 1], a[j]);
        for(int j = right - i - 1;j > i;j--)
            if(less(a[j], a[j - 1])) exchange(a[j], a[j - 1]);
    }
}
```

## 希尔排序

希尔排序又叫缩小增量排序，它是插入排序扩展。
> 插入排序慢， 因为它一次只交换相邻的两个元素（步长为1）。如果最小键位于数组尾部，则将它移动到正确位置需要N步。
为了让元素能够能快的到达正确的位置，改变步长。每隔h取一个元素，可以得到一些h-有序序列。然后改变步长继续操作，最终当步长为1的时候，希尔排序变成插入排序，这就保证了排序能够完成。

如果不使用标记，则在插入排序中，将步长由“1”换成“h”(也就是将每个“1”换成“h”)，得到的程序对序列进行h-排序。增加一个外循环来改变增量h，就可以得到最终程序。程序中选取的增量序列为：1，4，13，40，121，364，1093，3280，9841……

```C++
void shellSort(type a[], int left, int right){
    int h, i, j;
    for(h = 1;h < (right - left) / 3;h = h * 3 + 1);
    for(;h > 0;h /= 3){  // 调整增量
        for(i = left + h;i <= right;i += h){
            j = i;
            type v = a[i]; // 保存a[i]
            for(;j >= left + h && less(v, a[j - h]);j -= h) a[j] = a[j - h]; // 向后移动h个元素
        a[j] = v;  // 把a[i]放到正确位置
        }
    }
}
```

## 键索引计数排序

键索引排序把键当作索引进行排序，而不是把键当作被比较的抽象项。比如：排序一个包含N个项的文件，项的键为0~M-1之间的整数。我们可以用每个值来对键的个数进行计数，然后在第二遍扫描中使用计算出来的数将项移到正确的位置。通俗的讲：加入你们班有30个人，统计出来有5个人的绩点比你高，那么你的绩点就排在第6位。用这个方法可以得到其它人的排名，也就排好了序。对于重复值，需要特殊处理。
不过键索引基数排序局限于待排序数据的范围。

### 步骤

1. 首先，计数每个值的键的数量
2. 然后，小计小于或等于每个值的键数。
3. 接着，使用这些计数作为索引分拣键，比如 **`cnt[i]`** 表示小于 **`i`** 的个数，那个 **`a[i]`** 位于 **`aux[ant[i]]`**
4. 写回原数组

代码如下：

```C++
void countSort(type a[], int left, int right){
    int i, j, M = 10000; // 键必须是小于M的整数
    int N = right - left + 1, cnt[M];
    type *aux = new type[N];
    for(j = 0;j < M;j++) cnt[j] = 0; // 将计数初始化为0
    for(i = left;i <= right;i++) cnt[a[i] + 1]++;  //统计小于a[i]的值的出现频率
    for(j = left;j < M;j++) cnt[j + 1] += cnt[j]; // 得到小于等于计数器对应计数值键的数量,将频率转换为索引
    for(i = left;i <= right;i++) aux[cnt[a[i]]++] = a[i]; // 将键分布到辅助数组中
    for(i = left;i <= right;i++) a[i] = aux[i];  // 回写
    delete []aux;
}
```

## 快速排序

快速排序是一种分治方法。它的工作方式是：将待排序的序列划分为两组，然后独立排序各个部分划分的准确位置取决于输入中元素的初始顺序。
方法的关键在于划分过程，它将数组重排，使下面3个条件成立：

1. 对于某一个 **`i`** 值，元素 **`a[i]`** 处于数组中的最终位置;
2. **`a[i]`** 左边的元素都不大于 **`a[i]`** ;
3. **`a[i]`** 右边的元素都不小于 **`a[i]`** ;

### 划分方法

1. 首先，任意选定 **`a[right]`** 作为划分元素（它将处于在数组中的最终位置）。
2. 从左向右扫描，直到发现一个大于划分元素的元素；同时从右向左扫描，直到发现一个小于划分元素的元素，然后交换这两个元素。
3. 按这种方式继续划分。确保左指针左边的元素都小于划分元素，右指针右边的元素都大于划分元素。
4. 当左右指针相遇或者相互经过时，交换 **`a[right]`** 和右半部分最左边的元素（由左指针指向的元素），划分结束。

```C++
int partition(type a[], int left, int right){
    int i = left - 1, j = right;
    type v = a[right];  // a[right]为划分元素
    for(;;){
        for(;less(a[++i], v););
        for(;less(v, a[--j]);) if(j == left) break;  // 避免划分元素为序列中的最小元素的情况发生
        if(j <= i) break;
        exchange(a[i], a[j]);
    }
    exchange(a[i], a[right]);  //交换a[right]和a[i]
    return i;
}
```

如果数组中只有一个元素，不需要进行任何运算。否则，调用 **`partition()`** 函数处理这个数组，它将 **`a[i]`** 放到最终位置 **`(left < i <= right)`** 并且重排其它元素，让递归调用能够正确完成整个排序过程。

```C++
void quickSort(type a[], int left, int right){
    if(right <= left) return;
    int i = partition(a, left, right);
    quickSort(a, left, i - 1);
    quickSort(a, i + 1, right);
}
```

### 改进（三元素中值划分）

三元素中值划分使用一个更有可能在中间位置出现的元素作为划分元素。它从序列中选出三个元素的样本，然后用这三个元素的中值作为划分元素。分别从数组中的左、中、右选取一个元素。接着排序这三个元素，然后用 **`a[right - left]`** 交换中间那一个，再对 **`a[left + 1],...,a[right - 2]`** 运行划分算法。

### 应对大量重复键的情况---三路划分

>当排序序列中的重复键较多的时候，快速排序的低下性能让人难以接受。
可以把序列分为三部分，分别是：小于划分元素的部分、等于划分元素的部分、大于划分元素的部分。
修改标准划分方案如下：将在左边部分碰到的和划分元素相等的键放到序列的左端，将右边部分碰到的和划分元素相等的键放到序列右端。
然后，当指针交叉而且相等键的位置已知的时候，将所有与划分元素相等的键交换到位
![三路划分](/images/algorithms_in_c/三路划分.png)

#### 一点说明

程序将数组划分为三部分：小于划分元素的部分（ **`a[left],..., a[j]`** ）, 等于划分元素的部分（ **`a[j + 1],..., a[i - 1]`** ）,大于划分元素的部分( **`a[i],...,a[right]`** )。示意图如下：

```C++
void tri_quickSort(type a[], int left, int right){
    if(right <= left) return;
    type v = a[right];  // 划分元素
    int i = left, j = right - 1, p = left, q = right - 1;
    for(;;){
        for(;less(a[i], v);i++);
        for(;less(v, a[j]);j--) if(j == left) break;  // 避免划分元素为序列中的最小元素的情况发生
        if(j <= i) break;
        exchange(a[i], a[j]);
        if(equal(a[i], v)){p++;exchange(a[i], a[p]);}
        if(equal(a[j], v)){q--;exchange(a[j], a[q]);}
    }
    exchange(a[i], a[right]);
    i--;j++;
    int k;
    for(k = left;k < p;k++,i--) exchange(a[k], a[i]);
    for(k = right - 1;k > q;k--, j++) exchange(a[k], a[j]);
    tri_quickSort(a, left, i);
    tri_quickSort(a, j, right);
}
```

## 奇偶排序

### 步骤

1. 选取所有为奇数列（下标为1,3,5...)的元素与其右侧元素比较，将小的放在前面
2. 选取所有为偶数列（下标为2,4,6...)的元素与其右侧元素比较，将小的放在前面
3. 重复1和2直到所有序列有序为止。

### 实现

```C++
void oddEvenSort(type a[], int left, int right){
    int i;
    bool oddSorted = false, evenSorted = false;
    while(!oddSorted || !evenSorted){
        oddSorted = true;
        evenSorted = true;
        for(i = left;i < right;i += 2){
            if(less(a[i + 1], a[i])){
                exchange(a[i + 1], a[i]);
                evenSorted = false;
            }
        }
        for(i = left + 1; i < right;i += 2){
            if(less(a[i + 1], a[i])){
                exchange(a[i + 1], a[i]);
                oddSorted = false;
            }
        }
    }
}
```

## 归并排序

### 归并

数组 **`a`** 的前半部分（ **`a[left],...,a[m]`** ）有序，后半部分（ **`a[m + 1],...,a[right]`** ）有序。归并这两部分，使整个数组有序。一般需要一个辅助数组 **`aux`** ,先把结果存到 **`aux`** 中，然后把排序结果从 **`aux`** 写回到 **`a`** 中。
一种常用的归并方法：

```C++
void merge0(type a[], int left, int m, int right){
    int len = right - left + 1;
    type *aux = new type[len];
    int i = left, j = m + 1, k = 0;
    while(i <= m && j <= right) aux[k++] = less(a[i], a[j]) ? a[i++] : a[j++];
    while(i <= m) aux[k++] = a[i++];
    while(j <= right) aux[k++] = a[j++];
    for(i = left, k = 0;i <= right;i++, k++) a[i] = aux[k];
    delete []aux;
}
```

另一种归并方法：
将前半部分复制到 **`aux`** 中，然后将后半部分（ **`a[m + 1],...,a[right]`** ）逆序复制到 **`aux`** 中,使两个部分最大元素背靠背位于 **`aux`** 中间，形成双调序列。这样，两个部分中的最大元素分别成为一个标记。

```C++
void merge1(type a[], int left, int m, int right){
    int len = right - left + 1;
    type *aux = new type[len];
    int i, j , k = 0;
    for(i = left;i <= m;i++) aux[k++] = a[i];  // 复制前半部分到aux
    for(j = right;j >= m + 1;j--) aux[k++] = a[j];  // 逆序复制后半部分到aux
    i = 0;j = len - 1;
    for(k = left;k <= right;k++){
        if(less(aux[i], aux[j])) a[k] = aux[i++];  // 归并，最大元素为各自的标记
        else a[k] = aux[j--];
    }
    delete []aux;
}
```

### 排序

将数组分为两部分： **`a[left],..., a[m]`** 和 **`a[m + 1], ..., a[right]`** 。然后对这两个数组进行独立排序（通过递归调用），然后将这两个有序序列归并到最终的序列。

```C++
void mergeSort(type a[], int left, int right){
    int m = (left + right) / 2;
    if(right <= left) return;
    mergeSort(a, left, m);
    mergeSort(a, m + 1, right);
    merge1(a, left, m, right);  // 归并
}
```

### 巴切奇偶归并排序

首先要讲两个函数：
完全混洗(shuffle):将数组 **`a[left],...,a[right]`** 分为两半，前一半进入结果中的偶数编号位置，后一半进入结果中的奇数编号位置。
逆完全混洗(unshuffle):偶数编号位置的元素进入结果的前一半，奇数编号位置的元素进入结果的后一半。
这两个函数仅对带有偶数个元素的子数组使用。

```C++
void shuffle(type a[], int left, int right){
    int i, j, m = (left + right) / 2, len = right - left + 1;
    type *aux = new type[len];
    for(i = left, j = 0;i <= m;i++, j += 2){
        aux[j] = a[i];
        aux[j + 1] = a[m + 1 + i - left];
    }
    for(i = left, j = 0;i <= right;i++, j++) a[i] = aux[j];
    delete []aux;
}

void unshuffle(type a[], int left, int right){
    int i, j, m = (left + right) / 2, len = right - left + 1;
    int ma = len / 2;
    type *aux = new type[len];
    for(i = left, j = 0;i <= right;i += 2, j++){
        aux[j] = a[i];
        aux[ma + j] = a[i + 1];
    }
    for(i = left, j = 0;i <= right;i++, j++) a[i] = aux[j];
    delete []aux;
}
```

#### 巴切的奇偶归并网络

这个网络输入两个已排好序的序列，对这两个序列进行归并排序。首先对这两个序列进行逆混洗，然后分别归并前后部分，接着再混洗，最后进行一次(1,2)、(3,4)...这些相邻元素的比较交换得到排序结果。这里的代码只适合元素个数为 **`2^n`** 的序列。

```C++
void mergeTD(type a[], int left, int right){
    int i, m = (left + right) / 2;
    quickSort(a, left, m); quickSort(a, m + 1, right);
    if(left + 1 == right)
        if(less(a[right], a[left])) exchange(a[left], a[right]);
    if(left + 2 > right) return;  // 不多于两个元素
    unshuffle(a, left, right);  // 逆混洗
    mergeTD(a, left, m);
    mergeTD(a, m + 1, right);
    shuffle(a, left, right);  // 混洗
    for(i = left + 1;i < right;i += 2)  //比较交换
        if(less(a[i + 1], a[i])) exchange(a[i], a[i + 1]);
}
```

## 堆排序

### 堆

大顶堆——堆中不存在大于根键的结点。与之对应的由小顶堆。这里使用大顶堆。
如果用数组来保存堆，如果下标从 **`0`** 开始，很容易知道位置 **`i`** 处结点的父亲位于 **`(i - 1) / 2`** , 反之位置 **`i`** 处结点的孩子位于 **`(2i + 1)`** 和**`(2i + 2)`** 处。
堆中的第 **`i`** 个元素大于等于第 **`(2i + 1)`** 个元素和 **`(2i + 2)`** 个元素。
有关堆的很多算法都是首先对堆做一个简单的修改，这可能违反堆的条件，然后遍历堆，同时修正堆，确保整个堆满足堆的条件。
修正堆的情况有两种，一种是在堆底部添加新节点，然后需要向上遍历调整堆；另一种是用一个新节点替换掉根节点，然后需要向下遍历调整堆。
如果是由于节点的键变得大于它的父亲而违反了堆的性质，则可以通过交换该节点和它的父亲的位置。交换后，节点大于它的两个孩子（
一个是原来的父亲，一个是原来父亲的另一个孩子），但它仍有可能大于现在的父亲，因此需要继续调整，直到遇到一个真正比它大的父节点或者到达根的位置才结束。
如果是由于节点的键变得小于它的一个或者两个孩子而违反了堆的性质。则可以通过交换此节点和它的大孩子来进行修改。这可能导致孩子
的违规，然后就按照这种方式继续调整，直到到达不小于它的所有孩子的节点或者叶子节点才结束。

### 自底向上堆化

向上遍历堆，只要 **`a[k/2]`**  <  **`a[k]`** 就交换 **`k`** 处和 **`k/2`** 处的节点的位置。继续此过程，或者直到到达根节点为止

```C++
void fixUp(type a[], int left, int right){
    int s = right;
    int f = s / 2 - 1;
    while(f >= left){
        if(a[f] < a[s]) exchange(a[f], a[s]);
        s = f;
        f = s / 2 - 1;
    }
}
```

### 自顶向下堆化

向下遍历堆， 交换位置 **`k`** 处的节点和它孩子中较大的那个节点（如果有需要的话），当位置 **`k`** 处的节点不小于它的孩子或者到达了底端就停止。
需要注意的是：如果 **`N`** 为偶数，且 **`k = N/2`** 时， 它只有一个孩子节点。

```C++
void fixDown(type a[], int left, int right){
    int f = left;
    int s = 2 * f + 1; //左孩子
    while(s <= right){  // 没有到达底端
        if(s + 1 <= right && less(a[s], a[s + 1])) s++; // 找大孩子
        if(!less(a[f], a[s])) break;  // 已经满足堆的条件，跳出
        exchange(a[s], a[f]);   // 交换
        f = s;  // 继续
        s = 2 * f + 1;
    }
}
```

### 排序方法

移出堆顶元素，然后调整堆。重复直到堆中只有一个元素。

```C++
void heapSort(type a[], int left, int right){
    int N = right - left + 1;
    int i = N / 2 - 1; // 初始化i为最后一个父节点，从最后一个父节点开始调整，因为所有的叶子节点都是堆了
    for(;i >= 0;i--) fixDown(a, i, N); // 建大顶堆
    for(i = right;i >= left;){
        exchange(a[left], a[i]);  // 把根节点交换到最后
        fixDown(a, left, --i);  // 调整
    }
}
```

## 基数排序

>引入：当我们在电话簿中查找某个人的电话时，我们通常只输入前几个字母，然后就能得到电话号码所在的页。为了在排序算法中取得相似效率，可以将比较键的抽象转化为另一种抽象。将这些键分解为一系列定长片段或字节。然后每次处理其中的一个片段，这种排序方法叫做基数排序。基数排序算法把键当作以R为基数的数值系统表示的数，R可取不同的值，分别处理这些数中的单个数字。
基数排序有两种：一种时从左到右按顺序检查键中的位，称为最高位基数排序。另一种采用从右到左的顺序，称为最低为基数排序。

以16进制为例，可以通过右移运算取得int(这里int为32 位)型数组 **`a[i]`** 的各个字节的数字:最低位(a[i] >> 0) & 0xff 、次低位(a[i] >> 8) & 0xff 以及(a[i] >> 16) & 0xff 和(a[i] >> 24) & 0xff。我们需要256(0xff - 0x00 + 1 = 256)个桶。稍微修改键索引计数的程序就得到了基数排序的程序。

```C++
void radix(int b, type a[], int left, int right){
    int i, j, M = 256;
    int N = right - left + 1, cnt[M];
    type *aux = new type[N];
    for(j = 0;j < M;j++) cnt[j] = 0; // 初始化计数为0
    for(i = left;i <= right;i++) cnt[((a[i] >> b * 8) & 0xff) + 1]++;  // 统计出现频率
    for(j = left;j < M;j++) cnt[j + 1] += cnt[j]; //得到小于等于计数器对应计数值键的数量,将频率转换为索引
    for(i = left;i <= right;i++) aux[cnt[(a[i] >> b * 8) & 0xff]++] = a[i]; //将键分不到辅助数组中
    for(i = left;i <= right;i++) a[i] = aux[i]; // 回写
    delete []aux;
}

void radixSort(type a[], int left, int right){
    radix(0, a, left, right);
    radix(1, a, left, right);
    radix(2, a, left, right);
    radix(3, a, left, right);
}
```

# 并查集

假定有一个整数对序列，其中每个整数代表某种类型的一个对象，而且将 **`p-q`** 解释为“p与q连通”。关系是可传递的，如果 **`p-q`** 和 **`q-r`** ，则有
**`p-r`** 。我们的目标是编写一段程序，从集合中过滤额外连接对。当程序输入一个连接对 **`p-q`** ，若之前的连接对不能通过传递关系推导出 **`p-q`** ，则输出
**`p-q`** ， 否则忽略 **`p-q`** ，读取下一个整数对。

过程示例:

3-4    3-4

4-0    4-0

3-0

4-1    4-1

5-6    5-6

0-5    0-5

3-5

2-9    2-9

我们的问题是设计一个程序，它通过已有的信息，来判断新的对象是否连通。整数可能代表一个大型网络中的计算机，整数对可能代表网络中的连通情况。同样，整数也可以代表一个电网中的触电， 整数对就是电线……这些问题的规模可能都非常巨大，算法的好坏直接决定了开销的大小。

# 并和查

并查集主要有以下两个操作:

1. 并： 合并两个集合
2. 查： 判断两个元素是否属于同一个集合

# 解决方案

我们可以通过查找（union）和并集（union）运算来解决连通性问题。每当从输入读取一个新的 **`p-q`** 对后，分别进行 **`p`** 和 **`q`** 的查找， 如果它们位于同一个集合中，就直接分析下一个 **`p-q`** 对。否则，就进行并集操作并输出。
最可能想到的是依次保存每一个 **`p-q`** 对，然后进行遍历，判断它们是否连通。可问题的规模可能很大，这时候这个方法就捉襟见肘了。我们其实并不用保存所有的  **`p-q`** 对，使用一个整数数组来保存实现find和union操作的必备信息即可。

## 快速查找（quick-find）简单算法

> 假设A和B是朋友，B和C是朋友，A和B互不认识，但他们通过B这个共同的朋友联系在了一块儿。C还会有朋友D……,所有能通过某种朋友关系建立联系的朋友就构成了一个朋友圈。设想每个朋友圈都有一个圈主，圈主唯一的确定了这个圈子。为了简化问题，每当有一个新的人加入圈子，他就成为了这个圈子的圈主。
于是我们可以用一个数组 **`A[]`** 来进行存储。如果 **`i`** 代表一个人，那么 **`A[i]`** 就是他的圈主。现在我们来判断 **`p`** 和 **`q`** 是不是位于同一个圈子, 解决最开始提出的问题。

使用一个整数数组 **`A[]`** , 它具有如下性质：当且仅当 **`A[p] = A[q]`** 时，p与q连通。首先用 **`i`** 初始化 **`A[i]`** , 为了对 **`p`** 和 **`q`** 实现并集的运算，遍历数组，将所有和 **`A[p]`** 值相同项的替换为 **`A[q]`** 的值。

```C++
#include<cstdio>
const int N = 10000;

int main(){
    int p, q, A[N];
    for(int i = 0;i < N;i++) A[i] = i;
    while(scanf("%d-%d", &p, &q) == 2){
        if(A[p] == A[q]) continue;
        for(int i = 0, t = A[p];i < N;i++){
            if(A[i] == t) A[i] = A[q];
        }
        printf("%d-%d\n", p, q);
    }
}
```

可以用树来表示quick-find的过程。如下图中左半部分所示，每输入一个 **`p-q`** 对， **`A[q]`** 就成为父节点。下图的右半部分描述的是quick-union的过程， 每次只有一个值发生了改变。也就是 **`q`** 成为了 **`p`** 的父节点。
![union-and-find](/images/algorithms_in_c/union-and-find.jpg)

## 快速并集（quick-union）算法

> 前面的快速查找算法能够正确解决问题，当面对百万级的输入，效率就不行了。下面是quick-find的互补方法——quick-union。
可以用树来描述描述p和q是不是位于同一个集合，如果p和q有相同的根节点，那么他们位于同一个集合。如果p和q不在同一个集合中，
为了形成并集，使p和q拥有相同的根节点即可。
> 和quick_find的不同在于：
* 这里 **`A[i]`** 的值指向的是它的父亲在数组中的下标（比如 **`A[1] = 2`** ， 1的父亲在数组中的下标为2）。
* 根节点的值指向它本身，即：若 **`i`** 为根节点，则 **`A[i] = i`** ;根节点总是存在的。
* 通过分别通过 **`i`** 和 **`j`** 递归查找 **`p`** 和 **`q`** 的根节点。

用下面的代码替换quick-find中的 **`while`** 循环体

```C++
int i , j;
for(i = p;i != A[i];i = A[i]);
for(j = q;j != A[j];j = A[j]);
if(i == j) continue;  //p和q的根相同，说明p和q连通
A[i] = j;  // 进行并集操作
printf("%d-%d\n", p, q);
```

## 加权快速并集（weighted-quick-union）算法

> quick-union是对quick-find的一种改进，但仍然有缺陷。每次进行并集操作就相当于把一棵树的树根链接到另一棵树的树根上去。
而找到根节点所化的时间和当前结点到根节点的距离有关，距离越短，for循环执行的次数就越少
如果能够直接把较小的树的根直接连接到较大的树的根上，就能缩短时间。
于是我们可以设置辅助数组 **`S[]`** 来跟踪每个结点子树中结点的数量，然后总是把较小的树的根连接到较大的树的根上去。

```C++
#include<cstdio>
const int N = 10000;

int main(){
    int p, q, A[N], S[N];
    for(int i = 0;i < N;i++){A[i] = i;S[N] = 1;}
    while(scanf("%d-%d", &p, &q) == 2){
        int i , j;
        for(i = p;i != A[i];i = A[i]);
        for(j = q;j != A[j];j = A[j]);
        if(i == j) continue;  //p和q的根相同，说明p和q连通
        A[i] = j;  // 进行并集操作
        if(S[i] < S[j]){A[i] = j;S[j] += S[i];}  // 寻找大树
        else {A[j] = i;S[i] += S[j];}
        printf("%d-%d\n", p, q);
    }
}
```

## 路径压缩

> 如果能够在并集操作中，使每个结点都能直接指向树根，那么将大大节省开销。最终的结果就是几乎把整棵树压平，接近于理想情况。
可以对上面的算法做一个简单的改进——通过某种方法让 **`A[i]`** 存储的不再是它父亲在数组中的下标，而是越过父节点更靠近根节点的结点在数组中的下标。比如父结点的父节点。

对代码进行一下调整：

```C++
for(i = p;i != A[i];i = A[i]) A[i] = A[A[i]];
for(j = q;j != A[j];j = A[j]) A[i] = A[A[i]];
```
