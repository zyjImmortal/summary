stream介绍：

Stream 是对集合（Collection）对象功能的增强，它专注于对集合对象进行各种非常便利、高效的聚合操作（aggregate operation），或者大批量数据操作 (bulk data operation)。

Stream API 借助于同样新出现的 Lambda 表达式，极大的提高编程效率和程序可读性。同时它提供串行和并行两种模式进行汇聚操作，并发模式能够充分利用多核处理器的优势，使用 fork/join 并行方式来拆分任务和加速处理过程。

### 流的特性

- 不存储数据，而是按照特定的规则对数据进行计算，一般会输出结果
- 不会改变数据源，通常情况下会产生一个新的集合
- 具有延迟执行特性，只有调用终端操作时，中间操作才会执行
- 对流的操作分为终端操作和中间操作
  - 终端操作：会消费流，这种操作会产生一个结果的，如果一个流被消费过了，那它就不能被重用的
    - max、min、foreach，collect()，reduce，count
  - 中间操作：中间操作会产生另一个流。因此中间操作可以用来创建执行一系列动作的管道。一个特别需要注意的点是:中间操作不是立即发生的。相反，当在中间操作创建的新流上执行完终端操作后，中间操作指定的操作才会发生。所以中间操作是延迟发生的，中间操作的延迟行为主要是让流API能够更加高效地执行
    - filter、map、sorted
- 不可复用，对一个已经进行过终端操作的流再次调用，会抛出异常。

### 创建流的几种方式





### 什么是聚合操作

在传统的 J2EE 应用中，Java 代码经常不得不依赖于关系型数据库的聚合操作来完成诸如：

- 客户每月平均消费金额
- 最昂贵的在售商品
- 本周完成的有效订单（排除了无效的）
- 取十个数据样本作为首页推荐

这类的操作。

但在当今这个数据大爆炸的时代，在数据来源多样化、数据海量化的今天，很多时候不得不脱离 RDBMS，或者以底层返回的数据为基础进行更上层的数据统计。而 Java 的集合 API 中，仅仅有极少量的辅助型方法，更多的时候是程序员需要用 Iterator 来遍历集合，完成相关的聚合应用逻辑。这是一种远不够高效、笨拙的方法。在 Java 7 中，如果要发现 type 为 grocery 的所有交易，然后返回以交易值降序排序好的交易 ID 集合，我们需要这样写：

##### 清单 1. Java 7 的排序、取值实现

```java
List<Transaction> groceryTransactions = new Arraylist<>();
for(Transaction t: transactions){
 if(t.getType() == Transaction.GROCERY){
 groceryTransactions.add(t);
 }
}
Collections.sort(groceryTransactions, new Comparator(){
 public int compare(Transaction t1, Transaction t2){
 return t2.getValue().compareTo(t1.getValue());
 }
});
List<Integer> transactionIds = new ArrayList<>();
for(Transaction t: groceryTransactions){
 transactionsIds.add(t.getId());
}
```

而在 Java 8 使用 Stream，代码更加简洁易读；而且使用并发模式，程序执行速度更快。

##### 清单 2. Java 8 的排序、取值实现

```java
List<Integer> transactionsIds = transactions.parallelStream().
 filter(t -> t.getType() == Transaction.GROCERY).
 sorted(comparing(Transaction::getValue).reversed()).
 map(Transaction::getId).
 collect(toList());
```

