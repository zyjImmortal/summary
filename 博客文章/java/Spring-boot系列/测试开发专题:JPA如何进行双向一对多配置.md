本片文章我们主要介绍spring-boot如何进行JPA的配置以及如何进行实体间的一对多配置。

### 依赖准备

要在spring-boot使用jpa需要在项目中有进入相关的依赖，pom文件里加入下面内容

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

项目的配置文件中需要对数据库链接以及jpa进行配置：

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/missyou?characterEncoding=utf-8&serverTimezone=GMT%2B8
    username: root
    password: 12345678
  jpa:
    hibernate:
      ddl-auto: update # 只针对新增的entity创建表
    properties:
      hibernate:
        show_sql: true  # 在对数据库进行操作的时候打印出sql，方便在生产环境排查问题
        format_sql: true  # 打印sql的时候进行格式化，看起来方便
```

### jpa实战

* 一对多关系

先定义两个实体类Banner和BannerItem，一个Banner可以对应多个BannerItem，属于典型的一对多的关系

```java
@Entity
public class Banner {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;
    @Column(length = 16)
    private String name;

    @Transient  // 表明这个字段不会映射到表中的字段
    private String description;
    private String img;
    private String title;

    @OneToMany
    private List<BannerItem> items;  // 关联属性，导航属性

}
```



```java
@Entity
public class BannerItem {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String img;
    private String keyword;
    private Short type;
    private String name;
}
```

先对上面代码中的一些注解进行说明：

@Entity标记当前类为一个实体，对应数据库中的一张表，用来表示这张表信息，类名默认就是表名，jpa会根据命名规则自动小写并加下划线，比如BannerItem实体，生成的表名就是banner_item

@Id表名标记的字段将作为主键

@GeneratedValue(strategy = GenerationType.IDENTITY) 设置id为自增长

@Column(length = 16) 设置字段的属性，比如长度、是否为空、是否唯一等

@OneToMany可以使用两个实体建立一对多的关系，也就是一个Banner可以包含多个BannerItem

@Transient注解表示被标记的当前字段不会映射到数据库表的字段，也就是说生成的表中不会包含这个字段

此时运行程序，就会在数据库中创建对应的表：

![image-20200517142949967](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589696990.png)

这里生成了三张表，banner表和banner_item表我们知道是对应的Banner实体和BannerItem实体，那banner_items这张表是怎么回事呢？

在解释这张表之前，我们先来看一下这个表的结构：

![image-20200517143242451](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589697162.png)

表里只有两个字段，banner_id和items_id，分别是banner的id和banner_item的id，这就是说这张表相当于一张关系表，在多对多的时候我们才需要一张关系表，而现在只是一对多，为什么会产生这张表呢？

这是因为在上面的实体定义的时候，我们只是给这两个实体建立了关系，并没有任何一个字段来表明哪一个BannerItem属于哪一个Banner，那JPA看到你没有做，那就我来做，他会自动帮你创建这样一张关系表来维护这种归属关系。那有没有办法去掉这张表呢，当然有。

这个时候就需要新增一个外键字段了，来表明一个banner_item到底是归属于哪一个banner，我们来修改一下上面的实体定义：

Banner实体里做如下修改：

```java
@OneToMany
@JoinColumn(name = "bannerId")
private List<BannerItem> items; 
```
@JoinColumn注解指定外键

BannerItem实体增加一个字段：bannerId

```java
private Long bannerId;
```

删除之前的表，重新运行程序

![image-20200517144540479](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589697940.png)

这个时候生成的表就只有两个了，在banner_item表里会新增一个外键字段。

下面我们通过一个程序实例来验证上面的内容：

1、构造数据

表结构已经构造好了，现在我们填充一些数据，来使用JPA进行数据查询

```mysql
INSERT INTO missyou.banner (id, img, name, title) VALUES (1, 'http://sss.jpg', '顶部banner', '顶部banner');
INSERT INTO missyou.banner (id, img, name, title) VALUES (2, 'http://aaa.jpg', '顶部banner2', '顶部banner2');

INSERT INTO missyou.banner_item (id, banner_id, img, keyword, name, type) VALUES (1, 1, 'http://www.jpj', '衣服', '阿迪促销', 1);
INSERT INTO missyou.banner_item (id, banner_id, img, keyword, name, type) VALUES (2, 1, 'http://www.jpj', '鞋', '阿迪促销', 1);

```

2、新建Controller\service\repository

BannerRepository仓储层,主要负责读取数据库

```java
@Repository
public interface BannerRepository extends JpaRepository<Banner, Long> {

    Banner findOneById(Long id);

    Banner findOneByName(String name);
}
```

sevice层，具体的业务逻辑

```java
@Service
public class BannerServiceImpl implements BannerService{

    @Autowired
    private BannerRepository bannerRepository;

    @Override
    public Banner getByName(String name) {
        return bannerRepository.findOneByName(name);
    }
}
```

controller层，负责接收客户端请求返回数据

```java
@GetMapping("/banner/name/{name}")
public Banner getBannerByName(@PathVariable String name){
    return bannerService.getByName(name);
}
```

请求接口：

![image-20200517150608899](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589699169.png)

可以看到数据已经查询出来了。

* 双向一对多

上面的示例是根据banner_id去查关联的一组banner_item，但是有些场景我们可能需要通过某个item_id去查是属于哪个banner，那这就是一种的反向的一对多，接下来我们看一下这总反向的一对多如何配置。

我们之前配置了一个items属性

```java
@OneToMany
@JoinColumn(name = "bannerId")
private List<BannerItem> items; 
```

这个属性就可以帮我们定位到某个banenr关联的一组bannerItem，可以叫做导航属性，那么反过来，如何从一个item定位到一个Banner，怎么设置导航属性？



首先需要在BannerItem实体类里面增加一个banner属性，并使用如下注解进行标记

```java
@ManyToOne
@JoinColumn(name = "bannerId")
private Banner banner;
```

修改Banner实体

```java
@OneToMany(mappedBy = "banner")
private List<BannerItem> items;
```

这里其实就是把@JoinColumn(name = "bannerId")移动到BannerItem里面。并且在BanenrItem里要删除掉之前定义的外键字段bannerId。

这是因为在设置双向一对多的关系时，会默认在banner_item表里生成一个外键，如果自己再写一个就会造成重复，程序就会报错。

那如果自己很想指定，也可以，进行如下配置

```java
@ManyToOne
@JoinColumn(insertable = false, updatable = false, name = "bannerId")
private Banner banner;
```

### 总结

以上就是我们介绍的关于在spring-boot中如何进行实体间的单向一对多和双向一对多的配置，关于要在实际项目如何使用哪一种方式，还是要结合自己的业务，一般不需要进行双向的配置，而且一般都不会去设置物理外键，后面我们再讨论如何进行多对多的配置以及如何去除物理外键。