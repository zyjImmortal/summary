上文我们讨论了spring-boot如何去获取前端传递过来的参数，那传递过来总不能直接使用，需要对这些参数进行校验，符合程序的要求才会进行下一步的处理，所以本篇文章我们主要讨论spring-boot中如何进行参数校验。

### lombok使用介绍

在介绍参数校验之前，先来了解一下lombok的使用，因为在接下来的实例中或有不少的对象创建，但是又不想写那么多的getter和setter，所以先介绍一下这个很强大的工具的使用。

Lombok 是一个可以通过简单的注解形式来帮助我们简化消除一些必须有但显得很臃肿的Java代码的工具，通过使用对应的注解，可以在编译源码的时候生成对应的方法。

#### 添加maven依赖

在pom文件中添加如下内容：

```java
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.10</version>
</dependency>
```

#### 基础注解

`@Getter`给类增加get方法

`@Setter`给类增加set方法

`@Builder`给类增加构建者模式

`@AllArgsConstructor`给类增加全参构造方法

`@NoArgsConstructor`给类增加无参数构造方法

`@RequiredArgsConstructor`按照必填属性增加构造方法

`@NonNull`变量值不能为空

这里只简单列一下常用注解的含义，具体的使用方式在参数校验的实例中体现。

#### 实战



### 参数校验

JSR  规范

JSR-269

JSR-303