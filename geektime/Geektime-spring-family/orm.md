* jpa和spring data jpa 的关系是什么呢

PA是规范，Hibernate是JPA的一种实现，Spring Data JPA可以帮助大家更方便地使用JPA，它底层用了Hibernate。Hibernate替我们做了ORM的工作，简单的场景中，你并不需要手写SQL

Hibernate是一款开源的对象关系映射框架

* JPA  Java Persistence API

为对象关系模型映射提供了一种基于POJO的持久化模型，简化数据持久化代码的编写，屏蔽了不同持久化API的差异

JDBC是各种操作的基础，JPA是个规范，Hibernate是JPA的一种实现，Spring Data JPA用的是Hibernate，MyBatis是另一种ORM的框架，Hibernate不用自己手写SQL，但其实复杂的HQL写到最后跟写SQL也没啥大差别了。

* 注解

@Entity

@Table

@MappedSuperclass

@Id 主键

