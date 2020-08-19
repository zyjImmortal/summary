### 排序算法



冒泡和插入是最近本的排序算法：

插入、归并是比较常考的，也是效率比较高的排序算法



* 冒泡排序



* 插入排序

#### 如何分析一个排序算法

###### 排序算法的执行效率

* 最好情况、最坏情况、平均情况时间复杂度
* 时间复杂度的系数、常数、低阶
* 比较次数、交换或者移动次数

###### 排序算法的内存消耗

​	算法的内存消耗可以通过空间复杂度来衡量，针对排序算法引入原地排序的概念，<font color=red>原地排序</font>算法指的是空间复杂度为O(1)的排序算法.



###### 排序算法的稳定性

如果待排序的序列中存在值相等的元素，经过排序之后，相等元素之间原有的先后顺序不变,<font color=red>稳定性</font>的重要性，在排序中，当排序的元素不是具体数字，是对象的时候，我们可能会按照对象的某个属性进行排序，比如订单，现在我们想让一个订单列表按照金额从小到大排序，而且订单金额相同的话，按照时间由近及远排序，那就可以先用时间排序，然后再按金额排序，这个即可保证金额从大到小，金额相同时间由近及远，因为再进行第二次排序的时候，金额相同的时候，位置是不变的。

#### 插入排序

将给定序列划分为两分区间，左边是已排好序的右边是未排序的，每次从右边取出一个数据，插入到左边已排序区间的合适位置。

``````python
def insert_sort(sequence):
    n = len(sequence)
    if len(sequence) <= 1:
        return
    for i in range(1, n):
        value = sequence[i]
        j = i - 1
        while j >= 0:
            # 这里j是从大到小的遍历,
            # 这个判断就在已排序区间里，把大于value的元素全部都向后移动
            if sequence[j] > value:
                sequence[j + 1] = sequence[j]
                j -= 1
            else:
                break

        sequence[j + 1] = value  # 插入元素
``````

#### 冒泡排序

冒泡排序只会操作相邻的两个元素，每次冒泡操作都会对相邻的两个元素进行比较，看他们是否满足大小关系，如果不满足就将他们交换，一次冒泡过程至少会让一个元素处在它该在的位置，重复N次就完成了N个元素的排序。注意：冒泡不是冒泡的元素，是比较操作，如果相邻两个元素满足大小关系，就不进行元的操作，而是针对指向后面两个元素继续比较。还有，一次冒泡是对整个序列进行一次全部的扫描，所以一个序列只需N次扫描。

``````python
def bubble_sort(sequence):
    n = len(sequence)
    if n <= 1:
        return
    for i in range(n):
        flag = False  # 是否数据交换操作的标志
        for j in range(n - i - 1):
            if sequence[j] > sequence[j + 1]:
                sequence[j], sequence[j + 1] = sequence[j + 1], sequence[j]
                flag = True  # 在一次冒泡过程中，有一次数据交换操作，就设为True
        if not flag:
            break
``````

#### 选择排序

和插入排序一样，也是划分为两个区间排序的和未排序的，区别是，选择排序每次从未排序区间取出最小的元素放入已排序区间

``````python
def select_sort(sequence):
    n = len(sequence)
    if len(sequence) <= 1:
        return
    for i in range(0, n):
        min_index = i
        min_value = sequence[i]
        # 每次从未排序区间找出最小的元素,和第一个元素进行交换
        for j in range(i, n):
            if min_value > sequence[j]:
                min_value = sequence[j]
                min_index = j
        sequence[i], sequence[min_index] = sequence[min_index], sequence[i]
``````

以上三种排序算法的比较：

* 时间复杂度

  时间复杂度都是O(n2)，没有太大差异

* 稳定性

  - 选择排序每次都要找剩余最小的元素和前面的进行交换，破坏了稳定性，其他两种排序算法没有破坏。

* 空间复杂度

  - 也都是原地排序算法，空间复杂度O(1)

* 性能比较

  - 选择排序和冒泡排序都要进行元素交换，而插入排序排序只需赋值一次即可，从这点上来说，插入排序的性能会更好一点

#### 归并排序

- 基本思想

核心是<font color=red>分治</font>，就是把一个复杂的问题分成两个或多个相同或相似的子问题，然后把子问题分成更小的子问题，直到子问题可以简单的直接求解，最原问题的解就是子问题解的合并。归并排序将分治的思想体现得淋漓尽致。

- 实现

一开始先把数组从中间划分成两个子数组，一直递归地把子数组划分成更小的子数组，直到子数组里面只有一个元素，才开始排序。

 排序的方法就是按照大小顺序合并两个元素，接着依次按照递归的返回顺序，不断地合并排好序的子数组，直到最后把整个数组的顺序排好

- 代码示例

``````python
def merge_sort(array):
    if len(array) == 1:
        return array
    mid = len(array) // 2
    left = merge_sort(array[:mid])
    right = merge_sort(array[mid:])
    return merge(left, right)
def merge(left, right):
    result = []
    while len(left) > 0 and len(right) > 0:
        if left[0] <= right[0]:
            result.append(left.pop(0))
        else:
            result.append(right.pop(0))
    result.extend(left)
    result.extend(right)
    return result
``````

``````java
public class MergeQuickSort {

    public static void mergeSort(int[] nums) {
        mergeSort(nums, 0, nums.length - 1);
    }

    public static void mergeSort(int[] nums, int p, int r) {
        if (p >= r) {
            return;
        }
        int mid = p + (p + r) / 2;
        mergeSort(nums, p, mid);
        mergeSort(nums, mid + 1, r);
        merge(nums, p, mid, r);
    }
   public static void mergerV2(int[] nums, int p, int mid, int r) {
        int[] copy = nums.clone();
        // 定义一个指针表示从什么位置开始修改原来的数组,i 指针表示左半边的起始位置，j 表示右半边的起始位置
        int k = p, i = p, j = mid + 1;
        while (k <= r) {
            if (i > mid) {
              // 当左边的指针大于mid,说明左边的数据已经遍历完成，把右边的数据放入nums
                nums[k++] = copy[j++];
            } else if (j >= r) {
              // 当右边的指针大于r,说明右边的数据已经遍历完成，把左边的数据放入nums
                nums[k++] = copy[i++];
            } else if (nums[j] < copy[i]) {
                nums[k++] = nums[j++];
            } else {
                nums[k++] = nums[i++];
            }
        }
    }
}
``````

- 复杂度分析
  - 空间复杂度
  - 时间复杂度
- 稳定性

#### 快速排序
