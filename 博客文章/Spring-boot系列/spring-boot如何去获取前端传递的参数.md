本文主要讨论spring-boot如何获取前端传过来的参数，这些参数主要有两大类，一类是URL里的参数，一个是请求body里的参数

### url里的参数

通过url里传过来的参数一般有三种方式，下面我们来看一下

* 路径参数

路径参数就是说在请求路径里携带了几个参数，比如有一个查询banner详情的接口，/v2/banner/123,这里的123就是参数，可以表示banner的ID。

下面我们设计了一个简陋的接口，来演示路径参数的获取

```java
@RestController
@RequestMapping(value = "/v2/banner")
public class BannerController {

    @GetMapping("/{id}")
    public Integer getBannerDetail(@PathVariable Integer id){
        return id;
    }
}
```

获取banner详情完整接口路径是/v2/banner/{id},这里的id就是路径参数，在方法的传参里，用@PathVariable注解标明那个参数用来接收请求路径传递过来的值,通过postman发个请求看看

![image-20200512231356185](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589296436.png)

这里要注意，接口路由里定义的参数名要和方法参数里的定义名字要一样，如果不一样需要通过@PathVariable来指定**

```java
@GetMapping("/{id}")
public Integer getBannerDetail(@PathVariable(name = "id") Integer id12){
    return id12;
}
```

* 查询参数

查询参数就是请求的url里？号后面跟的键值对,比如/v2/banner?id=123,下面我们重新定义一个借口，采用查询参数的形式获取banner详情

![image-20200512231905390](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589296746.png)

请求url里的查询参数通过@RequestParam注解可以和方法里的参数进行一一对应，比如现在有这么个`url：/v2/banner?id=123&pos=3`，那么接口的参数定义就得是`@RequestParam Integer id, @RequestParam Integer pos`,请求上面定义的接口看看返回值

![image-20200512232224434](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589296944.png)

* 路径参数和查询参数结合

路径参数和查询时可以同时使用的，那在定义接口的时候，同时使用这两种注解就行，比如下面的接口

```java
@GetMapping("/{id}")
public Map<String, Object> getBannerDetail(@PathVariable Integer id, @RequestParam Integer pos){
    Map<String, Object> body = new HashMap<>();
    body.put("id", id);
    body.put("pos", pos);
    return body;
}
```

请求一下接口

![image-20200512232535782](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589297135.png)

### 请求body里的参数

先来思考一个问题，当前端传递过来一个json的数据，我们的方法通过什么样的形式能够接受到参数呢？

一般来说有两种形式，Map和自定义对象，下面我们分别来介绍一下。

* Map

设计简单的banner创建接口，参数通过Map来接收，创建成功后，添加一个id，然后将传递的数据和id一起返回

```java
@PostMapping(value = "/create")
public Map<String, Object> createBanner(@RequestBody Map<String, Object> banner){
    banner.put("id", 1243);
    return banner;
}
```

这里@RequestBody的这个注解来将body里的参数，转化成一个map，请求一下接口看看正常不：

![image-20200512233539544](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589297739.png)

上面我们使用的Map,它是一个接口,在具体接收数据的时候,肯定会用一个Map的实现类，这就会设计到装箱拆箱的过程，那么频繁的装箱拆箱会有一定到的性能损耗，所以这种方式一般并不推荐使用。

* 自定义对象Dto

我们将接收请求参数的对象称作为数据传输对象，一般定义为XXXDto,这里我们定义一个创建banner时用来接收参数的对象BannerCreateDto.

```java
public class BannerCreateDto {
    private String name;
    private Integer pos;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getPos() {
        return pos;
    }

    public void setPos(Integer pos) {
        this.pos = pos;
    }
}
```

在Controller具体使用：

```java
@PostMapping(value = "/create")
public Map<String, Object> createBanner(@RequestBody BannerCreateDto dto){
    Map<String, Object> res = new HashMap<>();
    res.put("id", 10000);
    res.put("name", dto.getName());
    res.put("pos", dto.getPos());
    return res;
}
```

用@RequestBody标记BannerCreateDto对象，spring-boot会自动调用对象的getter和setter给他赋值。我们请求一下看看：

![image-20200512234718559](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589298438.png)



### 总结

以上就是我们对spring-boot进行参数接收时处理方式的讨论，包括路径参数以及查询参数，还有body参数的接收，下篇文章我们将讨论一下参数的校验，敬请关注！！