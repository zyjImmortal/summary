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

上篇文章中我们用BannerCreateDto这个对象了来接收前端传递的参数，那时是手动添加的get和set方法，这里我们用lombok注解来简化掉之前的样板代码：

```java
@Setter
@Getter
public class BannerCreateDto {
    private String name;
    private Integer pos;
}
```

接口的定义还是和之前一样。不同做任何修改：

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

重新运行程序，访问接口

![image-20200513215504732](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589378105.png)还是和之前一样，能够请求成功并正常的返回数据。

一般情况下，我们会针对返回的数据，来单独定义对象来进行描述，这里我们也定义一个

```java
@Builder
@Getter
public class BannerResponseDto {
    private int id;
    private String name;
    private Integer pos;
}
```

这里用到了@Builder、@Getter，在接口中构造对象并返回：

```java
@PostMapping(value = "/create")
public BannerResponseDto createBanner(@RequestBody BannerCreateDto dto){

    return BannerResponseDto.builder()
            .id(500)
            .name(dto.getName())
            .pos(dto.getPos())
            .build();
}
```

可以看到lombok的@Builder注解，能够让类通过构建者模式去创建对象，省去了大量的set代码，而且可读性也很好。

访问接口，看看数据返回的时候正常：

![image-20200513220111657](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589378471.png)

与之前是一样的，能够正常返回数据，那接下来我们就正式看一下，spring-boot如何进行参数校验了。

###  参数校验

参数校验就是说对前端传过来的数据进行合理性校验，看他能否满足我们的业务规则，那这些对参数进行校验的代码是该放在那里呢，是在Controller里还是说单独存放，又获取采用其他什么方式呢？

#### 参数校验该怎么做

首先要明确的一点，在Controller，不应该有大量逻辑判断的代码，为什么呢，我们可想想当参数较多的时候，对每个参数进行合理性校验的话，那代码量该有多少，那就会严重污染Controller，导致维护起来是很艰难的，所以应该要有一个合理的机制能把校验的代码抽离出来，从而保证Controller的简洁性。

在java的世界里有这么个概念叫做 JSR，是Java Specification Requests的首字母缩写，是编程语言Java规范请求或者说是Java语言的说明书，既然是说明书那就一定有各个版本，在JSR-303版本中，提出了Bean Validation的验证规范，目前主要有两个两个框架实现了Bean Validation规范，一个事自带的javax.validation.api和hibernate-validator框架，而hibernate-validator框架应用的更加广泛一点。

那我们就用这种Bean Validation这种验证框架进行参数的验证

#### 实战

```java
@RestController
@Validated
public class BannerController {


    @GetMapping("/v3/banner")
    public Map<String, Object> getBannerDetailV2(@RequestParam Integer id, @RequestParam @Max(10) Integer pos){
        Map<String, Object> body = new HashMap<>();
        body.put("id", id);
        body.put("pos", pos);
        return body;
    }
}
```

上面代码中，@Max(10)注解来验证输入参数的pos，限制其输入参数最大值为10，运行程序我们来测试一下：

![image-20200513230808654](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589382488.png)

访问接口http://localhost:8081/v3/banner?id=12&pos=33后，看到我们控制台的输出提示输入不能超过10，这里的这个message不是我写的哈，这是java国际化后的效果能够根据你所在地区决定显示什么语言。

当然这里我们也可以自定义错误的message：

```java
@GetMapping("/v3/banner")
public Map<String, Object> getBannerDetailV2(@RequestParam Integer id,
                                             @RequestParam @Max(value = 10, message = "超过10了，赶紧看看哇") Integer pos){
    Map<String, Object> body = new HashMap<>();
    body.put("id", id);
    body.put("pos", pos);
    return body;
}
```

还是上面的请求路径，看看控制台输出：

![image-20200513231126756](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589382687.png)

可以看到控制台输出了我们自定义的内容。**这里有一点要注意，要想使得校验生效，比如在Controller上方打上@Validated注解，至于为什么我们后面分解**。

上面的验证都是比较基础的数据类型，但是如果现在要有验证更加复杂一点的java对象呢，又该如何操作，那接下来我们再看看这部分的内容。

### java对象进行验证

##### 单个对象

还是用上面的BannerResponseDto来进行演示

```java
import org.hibernate.validator.constraints.Length;
@Setter
@Getter
public class BannerCreateDto {
    @Length(min = 2, max = 4, message = "banner名称必须是2-10个字符")
    private String name;
    private Integer pos;
}
```

对于这些基础类型的成员变量仍然使用这些基础注解进行校验定义，上面我们使用了 @Length注解来定义banner名称的字符长度范围，并定义发生错误时提示的message,这里还需要在Controller里额外的处理一下：

```java
@PostMapping("/v3/banner/create")
public BannerResponseDto createBanner(@RequestBody @Validated BannerCreateDto dto){

    return BannerResponseDto.builder()
            .id(500)
            .name(dto.getName())
            .pos(dto.getPos())
            .build();
}
```

@Validated BannerCreateDto这里必须要加@Validated注解，否则无法触发校验机制。

##### 嵌套

如果某个类的成员变量也是一个自定义对象，那校验该是什么样的呢,下面我们来看一下。

先定义一个新的对象，用来接收banner到的素材信息

```java
@Getter
@Setter
public class MaterialDto {
    @Length(min = 2, max = 5, message = "素材的名称长度范围必须在2-5之内")
    private String name;
}
```

然后banner对象增加一个成员变量

```java
@Setter
@Getter
public class BannerCreateDto {
    @Length(min = 2, max = 4, message = "banner名称必须是2-10个字符")
    private String name;
    private Integer pos;

    @Valid
    private MaterialDto materialDto;
}

```

这里有一点，要想使得这种级联的关系能够触发校验机制，必须 @Valid注解进行标记，我们来请求一下url看看效果

![image-20200513234439584](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589384680.png)

可以看到校验没通过，再看看控制台输出内容：

![image-20200513234506001](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589384706.png)

输出了我们定义的错误信息。



### 总结

本篇文章主要介绍了里面lombok的使用，以及校验规范介绍和校验框架基本注解的使用，还有自定义对象校验，下篇文章我们将通过自定义注解来实现更加个性化的校验规则，敬请期待。