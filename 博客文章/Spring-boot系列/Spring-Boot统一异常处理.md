上文我们讨论了java异常以及如何使用Spring-Boot捕获异常,但是没有去说捕获异常后该如何进一步处理,这篇文章我们将对这个遗留的问题进行讨论.

### 统一错误响应定义

我们希望在程序发生异常的时候,能够给用户返回一个比较友好且明确的信息,对于api接口来说,一种比较好的格式是json,类似于下面这种格式

```json
{
	"code": "10001",
	"message": "消息",
	"uri":"Get /v2/banner"
}
```

所以需要一个对象来描述这种数据格式：

```java
public class UnifyResponse {
    private int code;
    private String message;
    private String requestUri;

    public UnifyResponse(int code, String message, String requestUri){
        this.code = code;
        this.message = message;
        this.requestUri = requestUri;
    }
}
```

### 错误响应处理

上文我们谈到从开发者的角度来说，异常分为已知异常和未知异常，针对不同的异常使用不同的异常处理函数进行处理，我们之前定义两个处理函数

```java
@ControllerAdvice
public class GlobalExceptionAdvice {

    /**
     * 处理未知异常
     * @param req
     * @param ex
     */
    @ExceptionHandler(value = Exception.class)
    public void handleHttpException(HttpServletRequest req, Exception ex){
        System.out.println("发生异常了");
    }

    /**
     * 处理已知异常
     * @param req
     * @param ex
     */
    @ExceptionHandler(value = HttpException.class)
    public void handleHttpException(HttpServletRequest req, HttpException ex){

        System.out.println("发生了 HttpException");
    }
}
```

当未知异常发生时，需要将异常信息存储进`UnifyResponse`，序列化后返回给用户

```java
@ExceptionHandler(value = Exception.class)
    public UnifyResponse handleHttpException(HttpServletRequest req, Exception ex){
        String uri = req.getRequestURI();
        String method = req.getMethod();
        System.out.println(ex.getMessage());
        return new UnifyResponse(9999, "服务器错误", method + " " + uri);
    }
```

对于未知异常我们也不知道发生了什么，所以这里的code码就定义一个通用的，虽然Exception里面有message，但是这里不建议将这个异常里的message返回给用户，这位可能涉及到代码结构的一些东西，而且即使将这个信息返回给前端，他也不知道是啥问题，没什么意义，所以可以将这个message写到日志里，方便后面问题查询。

返回给用户的message可以自定义一个通用的，比如服务器错误什么的。

我们来测试一下，在Controller里抛出一个Exception：

```java
 @RequestMapping(value = "/v2/banner", method = {RequestMethod.GET})
    public String test() throws Exception{
        throw new Exception("我抛出来的");
    }
```

然后再浏览器里访问，发现出错了

![image-20200510110158872](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589079719.png)

这里的这个异常，看不太懂，回到异常处理方法当中去，我们直接是返回UnifyResponse对象，如果这里返回的是一个字符串，那会不会出错呢，再是试一下看看,结果还是会报这个错，也就是说无论这里返回自定义对象还是字符串，都会出现问题，那就是说spring-boot压根儿就可能不识别我们返回的东西。

在spring-boot里有一个注解@ResponseBody，可以将我们的返回值，绑定的响应的body上，我们来试一下看看能否解决这个问题。

会发现，上面改的返回String是可以成功的，但是返回UnifyResponse对象还是报错，而且报错和之前的还不一样

![image-20200510114815064](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589082495.png)

刚才报的还是404的错误，现在变成了500，哪里错了呢。

我们来看一下UnifyResponse的定义，我们定义了三个私有的成员变量，但是确没有定义getter方法，那在序列化的时候是无法获取到成员变量的值的，所以报错，这里我们加上：

![image-20200510152644882](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589095605.png)

然后在运行程序，访问路由：

![image-20200510115919104](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589083159.png)

返回的响应就和我们预期的一样了，但是从上面的图中，看到返回的状态码是200，这显然是不对的，因为服务器已经出错了，状态码应该是500，所以这里要对状态码进行自定义。

### 自定义状态码

spring-boot提供了两种可以自定义状态码的方式：

##### 注解

直接在异常处理函数上标记一个叫做

`@ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)`并制定状态码的枚举值

![image-20200510152620279](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589095580.png)

重启程序，访问路由：

![image-20200510121218008](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589083938.png)

可以看到状态码已经变成500了。

上面使用注解的的形式虽然能够实现我们的目的，但是这种方式不太灵活，,这里很多的response的设置都是spring-boot帮我们做了，如果需要做一些自定义就不是太方便，接下来的这种方式确可以让我们通过代码灵活的进行控制。

##### ResponseEntity

ResponseEntity是一个泛型类，是可以直接return回去的，可以设置很多属性，包括status、headers、body等。

用ResponseEntity来自定义已知异常处理方法的返回信息：

![image-20200510152555316](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589095555.png)

然后再Controller里抛出一个NotFoundException，重新运行程序，访问路由：

![image-20200510152800514](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589095680.png)

可以看到也能够返回正确的状态码。

### 总结

本篇文章我们介绍了，定义错误响应以及如何返回自定义的错误信息，多种方式进行定制状态码，但是我们在文章的错误信息都是硬编码在代码里的，这样很不好管理，所以下篇文章我们将介绍如何对错误信息管理，敬请关注！！！



