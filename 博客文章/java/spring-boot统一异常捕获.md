# java异常介绍

异常时相对于return的一种退出机制，可以由系统触发，也可由程序通过throw语句触发，异常可以通过try/catch语句进行捕获并处理，如果没有捕获，则会导致程序退出并输出异常栈信息，异常有不同的类型，所有异常类都有一个共同的父类Throwable，下面我们先从Throwable开始介绍。

## Throwable

`Throwable`是所有异常类的父类，有四个构造方法

```
public Throwable(Throwable cause)
public Throwable(String message, Throwable cause) 
public Throwable(String message)
public Throwable()
```
主要有两个类，一个是message，表示异常的消息，一个是cause，表示触发该异常的其他异常，异常可以形成一个异常链，上层的异常由底层到的异常触发，cause表示底层异常。

## 异常体系

java定义了非常多的异常类，来表示各种类型的异常，下面的图示是部分的异常类：

![image-20200508232051535](https://zyjblog.oss-cn-beijing.aliyuncs.com/1588951251.png)

Throwable是所有异常类的基类，它有两个子类：Error和Exception。

- Error错误

操作系统或者虚拟机发生的错误，这个时候程序是跑不起来的，代码是无法处理的，一般表示系统错误或者资源耗尽，由java系统自己处理，比如图示中给出的：虚拟机错误、栈溢出、内存溢出等错误

- Exception

表示应用程序错误，是可以通过代码处理的，有两大类：一类是受检异常(checked exception)，一类是非受检异常(uncheck exception)，也就是runtime异常，他两的区别就在于java是如何对待他们的，受检异常，java会要求强制进行处理，不然编译时不通过的，对于非受检异常则没有这个强制性的要求。

**解释**

该怎么理解受检异常和非受检异常呢，我的理解就是做某件事的时候我们能够顺利的按照我们预期的做完，但是实际上呢可能会出现各种各样的情况，这种可能出现的情况就把他称之为异常，这种异常有的我们能处理，有的我们不能处理，能处理的就把它称作为受检异常，不能处理的称作为非受检异常。

比如说，客户端发送一个请求，要查询数据库，那有可能找到有可能没找到，没找到的话，就应该抛出runtime异常，再比如要去读取文件，文件可能是不存在的，那就应该抛出checked exception，这其实就是bug，我们应该去处理的。

怎么理解呢，比如读取文件，当文件不存在，发生异常，能处理么，当然可以处理啊，怎么处理，把文件路径改成正确的不就行了。从某种意义上来说，checked异常是真正的bug

没有办法处理的情况，比如说用户输入ID为2，查询记录能找到，但是如果输入2000就找不到了，这里的找不到就是一种异常情况，就需要产生一个运行时异常也就是非受检异常。

# 已知异常和未知异常

上面我们提到的受检异常`unchecked exception`和运行时异常`RuntimeException`都是从java语法层面来说的，那从程序开发者的角度来说，分为两类：已知异常和未知异常。

已知异常和未知异常，其实就在于我们程序员是否主动的去处理异常。

- 未知异常：当代码中未对一些情况做出处理而引发的异常，这就是未知异常，一般这种异常都是因为服务端的代码写的有问题的，对于前段开发或者用户，都是无意义的，记录日志供自己查看就行了。

- 已知异常：更多的时候表示的是一种消息，用来提示用户的输入是否有问题，只不过这里我们用异常的形式来处理，当然也可以通过其他方式处理，比如当做结果去返回，也是可以的，但是这种方式不如全部都看做异常，通过全局的异常处理器直接处理来的简单。

# Spring-Boot全局异常处理器

在使用spring-boot进行开发到的时候，有时我们需要对程序中抛出的异常进行统一的处理，spring-boot预留了响应的扩展点，我们只需要按照要求的方式去自定义自己的实现即可。

```java
@ControllerAdvice
public class GlobalExceptionAdvice {

    @ExceptionHandler(value = Exception.class)
    public void handleHttpException(HttpServletRequest req, Exception ex){
        
    }
}
```

`@ControllerAdvice`表明GlobalExceptionAdvice是一个异常的处理类，具体的处理方法通过`@ExceptionHandler`进行标记，通过value来指定能够处理的异常类型。异常的处理方法需要传入两个参数，一个是HttpServletRequest请求对象，可以从这里获取请求相关的一些信息，比如请求的url或者参数等，另一个是Exception就是抛出的异常。

## 模拟异常发生

我们在异常的处理方法中打印一条语句

```java
@ControllerAdvice
public class GlobalExceptionAdvice {

    @ExceptionHandler(value = Exception.class)
    public void handleHttpException(HttpServletRequest req, Exception ex){
        System.out.println("发生异常了");
    }
}
```

然后再Controller里抛出一个异常

```java
@RestController
public class BannerController {

    @RequestMapping(value = "/v2/banner", method = {RequestMethod.GET})
    public String test() throws Exception {
        throw new Exception("我抛出来的");
    }
}
```

启动程序后在浏览器中访问路由，可以看到在控制台中打印出了预期的信息

![image-20200509001215452](https://zyjblog.oss-cn-beijing.aliyuncs.com/1588954335.png)

当然在实际开发中，我们可能要多异常进行区分，该抛出什么类型的异常，异常处理函数可也不只一个，还要对返回的异常信息进行自定义。

# 自定义异常类

当我们需要自定义异常类的时候，是该继承自exception呢，还是继承RuntimeException?下面我们就来讨论一下。

当用户访问一个不存在的资源的时候，很明显这种情况我们是处理不了的，而且这种情况我们是可以判断出来的，所以这里应该使用Runtime异常。

![image-20200509100041018](https://zyjblog.oss-cn-beijing.aliyuncs.com/1588989641.png)

`HttpException`是所有自定义运行时异常类的基类，这里定义两个状态码，一个code是我们业务层面的定义，一个httpStatusCode是http请求的，资源不存在的异常定义为`NotFoundException`

![image-20200509100342221](https://zyjblog.oss-cn-beijing.aliyuncs.com/1588989822.png)

在controller里面抛出这个异常，

![image-20200509103228206](https://zyjblog.oss-cn-beijing.aliyuncs.com/1588991548.png)

那之前定义的`GlobalExceptionAdvice`能够监听到这个异常呢？

当然能，`NotFoundException`是`HttpException`的子类，`HttpException`是`RuntimeException`的子类，``RuntimeException``又是`Exception`的子类，`@ExceptionHandler(value = Exception.class)`全局异常处理器里指定的能够处理的异常类是`Exception`自然也能处理`NotFoundException`。

但是这里也有不同的地方，我们自定义的`HttpException`是多了两个扩展字段的，`code`和`httpStatusCode`，所以在全局异常处理器里需要对异常的类型进行判断，如果是自定义的就需要添加这两个字段。

这里呢有两种方式进行处理：一种是在之前的异常处理方法里面，进行类型判断，另一种呢是可以再添加一个异常处理方法，专门处理自定义异常类

![image-20200509104418301](https://zyjblog.oss-cn-beijing.aliyuncs.com/1588992258.png)

这里我们可以考虑一下，当我们抛出`NotFoundException`异常的时候，是会进入哪一个异常处理方法里？还是说两个都进呢？

我们可以来试验一下，启动程序后，看看在控制台打印的是什么

![image-20200509165843525](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589014724.png)

在浏览器里访问路由以后，控制台显示了handleHttpException异常处理方法里到的打印语句输出的内容。

可以看到多个异常处理方法时可以同时存在的，各自处理自己所能处理的异常类。



下篇文章，我们来探讨一下，如何自定义异常的返回信息，敬请关注，[博客原文]()



























