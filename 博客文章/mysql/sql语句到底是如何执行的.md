### 前言

在谈论sql语句是如何执行的之前，我们先来看一下下面这几个问题：

1、mysql框架有几个组件，都有什么作用

2、mysql框架中的server层和存储引擎层各是什么作用

3、you have an error in your SQL syntax这个报错是在哪个阶段抛出的

4、对于表的操作权限验证是在哪里进行的

5、执行器的执行查询语句的流程是什么

带着这几个问题我们开始本片文章。

### MySQL的架构

当我们执行这样一条sql语句的时候，

`select articleTitle from b3_solo_article where oId=1577502208782;`有没有考虑过在mysql的内部具体的执行过程是什么。

下面我们先给出mysql的架构示意图，然后再逐步进行解释。

![image-20200608231346620](/Users/zhouyajun/Library/Application%20Support/typora-user-images/image-20200608231346620.png)

总来来说MySQL可以分为两层分别是server层和存储引擎层

##### server层

