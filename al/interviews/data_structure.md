### 常用数据结构

#### 字符串

​	分析字符串问题的时候，处理方式

* 一般考虑单个字符处理
* 将字符串转变为字符数组进行处理，比如字符串反转

#### 数组

##### 优点

* 构建简单
* 能在O(1)的时间复杂度能，根据索引查到数据

##### 缺点

* 构建时必须分配一段连续的空间
* 查询某个元素是否存在时需要遍历整个数组，耗费O(n)的时间
* 删除和添加某个元素时，同样需要耗费O(n)的时间

##### 解题思路

所以说那个数组辅助算法结解决问题时，需要考虑，这些缺点是否会阻碍算法的复杂度以及空间复杂度

#### 链表

* 优点

* 缺点

  

### 高级数据结构

#### 优先队列

- 特点

能保证每次取出的数据都是优先级别最高的，优先级是可自定义的，比如数据的数值越大优先级越高，或者值越小，或者通过计算手段得到

- 应用场景

从一堆杂乱无章的数据当中按照一定的顺序（或者优先级）逐步地筛选出部分乃至全部的数据

- 实现

优先队列的本质是一个二叉堆结构，是用数组实现的一个二叉堆结构。堆在英文里叫``` Binary Heap```，它是利用一个<font color=red>数组结构</font> 来实现的完全二叉树。换句话说，<font color=red>优先队列的本质是一个数组</font> ，数组里的每个元素既有可能是其他元素的父节点，也有可能是其他元素的子节点，而且，每个父节点只能有两个子节点，很像一棵二叉树的结构。

<font color=red> 牢记下面优先队列有三个重要的性质</font>

1.  数组里的第一个元素 ```array[0] ```拥有最高的优先级别
2. 给定一个下标``` i```，那么对于元素 ```array[i]``` 而言：
   - 它的父节点所对应的元素下标是``` (i-1)/2```
   - 它的左孩子所对应的元素下标是 ```2×i + 1```
   - 它的右孩子所对应的元素下标是 ```2×i + 2```
3. 数组里每个元素的优先级别都要高于它两个孩子的优先级别



------

**优先队列最基本的操作有两个**

1. 向上筛选（sift up / bubble up）

   - 当有新的数据加入到优先队列中，新的数据首先被放置在二叉堆的底部。

   - 不断进行向上筛选的操作，即如果发现该数据的优先级别比父节点的优先级别还要高，那么就和父节点的元素相互交换，再接着往上进行比较，直到无法再继续交换为止

   *时间复杂度*：由于二叉堆是一棵完全二叉树，并假设堆的大小为``` k```，因此整个过程其实就是沿着树的高度往上爬，所以只需要``` O(logk)``` 的时间

2. 向下筛选（sift down / bubble down)

   - 当堆顶的元素被取出时，要更新堆顶的元素来作为下一次按照优先级顺序被取出的对象，需要将堆底部的元素放置到堆顶，然后不断地对它执行向下筛选的操作
   - 将该元素和它的两个孩子节点对比优先级，如果优先级最高的是其中一个孩子，就将该元素和那个孩子进行交换，然后反复进行下去，直到无法继续交换为止

   *时间复杂度*：整个过程就是沿着树的高度往下爬，所以时间复杂度也是 ```O(logk)```。

   因此，无论是添加新的数据还是取出堆顶的元素，都需要``` O(logk) ```的时间

* 初始化

  优先队列的初始化是一个最重要的时间复杂度，是分析运用优先队列性能时必不可少的，也是经常容易弄错的地方

  **解法**：在创建这个堆的过程中，二叉树的大小是从``` 1``` 逐渐增长到``` n ```的，所以整个算法的复杂度经过推导，最终的结果是 ```O(n)```

