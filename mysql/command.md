##### 创建用户并授权

```mysql
mysql 5.8版本
mysql> CREATE USER 'root'@'%' IDENTIFIED BY 'root';
# 授予用户全部权限
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;

在mysql 5.8版本
授予用户权限不能直接使用GRANT，要先使用update语句更新host为%,然后再使用grant语句授权
update user set host='%' where user='meiduo_zyj';
GRANT EXECUTE,INSERT,SELECT,UPDATE ON meiduo_mall.* TO 'meiduo_zyj'@'%';
```

* 查询常数

  整合不同的数据源，用常数列作为这个表的标记，就需要查询常数,.在 SELECT 查询结果中增加一列固定的常数列。这列的取值是我们指定的，而不是从数据表中动态取出的

  `select '王者荣耀' as platform, name from heros;`

* Distinct 给指定字段去重,DISTINCT 需要放到所有列名的前面,DISTINCT 需要放到所有列名的前面

  ` select distinct attack_range from heros;`

* group by

``````mysql
select count(role_main) as num, role_main, avg(hp_max) from heros where hp_max > 6000 group by role_main having num > 5 order by num desc;
``````



使用多个字段进行分组，这就相当于把这些字段可能出现的所有的取值情况都进行分组,

在group by分组和having筛选分组之间还有一步使用聚集函数进行计算，在目前看到的having都是对cout聚集函数结果的筛选，想试试对其他聚集函数的筛选，然后对训练1做了点小改动：
select count(*) as c,role_main,avg(hp_max) as v from heros where hp_max > 6000 GROUP BY role_main HAVING c > 5 and v > 7000 order by c DESC;

* ORDER BY 

  * 排序的列名：ORDER BY 后面可以有一个或多个列名，如果是多个列名进行排序，会按照后面第一个列先进行排序，当第一列的值相同的时候，再按照第二列进行排序，以此类推
  * ORDER BY 后面可以注明排序规则，ASC 代表递增排序，DESC 代表递减排序。如果没有注明排序规则，默认情况下是按照 ASC 递增排序.
  * ORDER BY 可以使用非选择列进行排序，所以即使在 SELECT 后面没有这个列名，你同样可以放到 ORDER BY 后面进行排序。
  * ORDER BY 通常位于 SELECT 语句的最后一条子句，否则会报错。

  ```select name, hp_max from heros order by hp_max desc```

*  LIMIT

  limit 1，表示只取一条，limit1，1，第一个1表示索引，从0开始，第二个表示个数，合起来就是从第二位开始取一个数

* SELECT 的执行顺序

  * 关键字的顺序

  ```mysql
  select -> from -> where -> group by -> having -> order by -> limit
  ```

  * SELECT 语句的执行顺序

  ```
  FROM > WHERE > GROUP BY > HAVING > SELECT的字段 > DISTINCT > ORDER BY > LIMIT
  ```

  在 SELECT 语句执行这些步骤的时候，每个步骤都会产生一个虚拟表，然后将这个虚拟表传入下一个步骤中作为输入。需要注意的是，这些步骤隐含在 SQL 的执行过程中，对于我们来说是不可见的

* 对英雄名称和最大法力进行查询，按照最大生命从高到低排序，只返回 5 条记录即可

  `select name, mp_max from heros order by mp_max desc limit 5;`

* 比较运算符，逻辑运算符（and or not in）

`SELECT name, hp_max FROM heros WHERE hp_max BETWEEN 5399 AND 6811,BETWEEN是可以取到最大值和最小值的

当 WHERE 子句中同时存在 OR 和 AND 的时候，AND 执行的优先级会更高，也就是说 SQL 会优先处理 AND 操作符，然后再处理 OR 操作符

**查询主要定位或者次要定位是法师或是射手的英雄，同时英雄的上线时间不在 2016-01-01 到 2017-01-01 之间,并且按照最大生命最大法力和从高到低排序**

```mysql
select role_main, role_assist, hp_max, mp_max, birthdate from heros where (role_assist in ('法师', '射手') or
  role_main in ('法师','射手')) and DATE(birthdate) not between '2016-01-01' and '2017-01-01';
```

* 通配符

  %）代表零个或多个字符，而（_）只代表一个字符

  

### sql 函数

### 子查询

#### 关联子查询

如果子查询需要执行多次，即采用循环的方式，先从外部查询开始，每次都传入子查询进行查询，然后再将结果反馈给外部，这种嵌套的执行方式就称为关联子查询

#### 非关联子查询

子查询从数据表中查询了数据结果，如果这个数据结果只执行一次，然后这个数据结果作为主查询的条件进行执行，那么这样的子查询叫做非关联子查询。



