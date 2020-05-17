上文我们讨论了如何使用@Min、@Max等注解进行参数校验，主要是针对基本数据类型和级联对象进行参数校验的演示，但是在实际中我们往往需要更为复杂的校验规则，比如注册用户的密码和确认密码进行校验，这个时候基本的注解就无法满足我们的要求了，需要去按照业务需求去自定义注解进行校验



### 元注解

在自定义注解之前我们有必要了解一些元注解，元注解就是在注解上的注解，可以对一个注解进行配置，元注解包括@Retention、@Target、@Document、@Inherited四种

* @Retention，表示注解保留到什么时候，有以下三种模式
  * @Retention(RetentionPolicy.SOURCE) 表示注解仅存在于源码中，在class字节码文件中不包含
  * @Retention(RetentionPolicy.CLASS) 表示 默认的保留策略，注解会在class字节码文件中存在，但运行时无法获得
  * @Retention(RetentionPolicy.RUNTIME) 表示注解会在class字节码文件中存在，在运行时可以通过反射获取到　

* @Target表示注解的作用目标是什么，只列出下面几个，剩余的大家自行谷歌把
  * @Target(ElementType.TYPE)  表示注解可以应用于接口、类、枚举、注解
  * @Target(ElementType.FIELD) 表示可以应用于字段、成员变量、枚举的常量等
  * @Target(ElementType.METHOD)表示可以作用于方法
* @Document表示注解包含在javadoc中
* @Inherited表示注解可以被继承

### 自定义校验注解

就以用户注册为例，我们需要校验密码和确认密码是否一致以及是否符合密码的规则，先新建一个PasswordEqual注解类

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Constraint(validatedBy = PasswordValidator.class)
public @interface PasswordEqual {

    String message() default "密码不一样";

    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

下面我们来解释一下上面的注解，在PasswordEqual注解上，又标记了四个注解，前三个我们上面已经说过了。

这里说一下@Constraint注解，它表示这个注解是一个验证注解，并且通过validatedBy指定自定义校验注解的关联类，PasswordValidator类就是我们自定义的注解关联的类。

注解里面的groups和payload方法是模板方法，实现自定义注解必须写这么两个方法。

### 定义验证类

验证类里面包含具体的验证逻辑了，下面是一个简版的：

```java
public class PasswordValidator implements ConstraintValidator<PasswordEqual, BannerCreateDto> {

    @Override
    public boolean isValid(BannerCreateDto dto, ConstraintValidatorContext constraintValidatorContext) {
        
        return false;
    }
}
```

这里需要对上面的代码进行一下说明，实现自定义校验类必须实现

ConstraintValidator接口，它是一个泛型接口，需要指定两个类型参数，第一个是自定义注解类型，第二个类型指定自定义注解修饰目标的类型，就是准备把自定义注解标记到什么类型上面。

必须重写isValid方法，所有的校验逻辑都在这个方法里面，下面我们简单写一下:

```java
@Override
public boolean isValid(UserDto dto, ConstraintValidatorContext constraintValidatorContext) {
    if (dto.getPassword().equals(dto.getConfirmPassword())){
        return true;
    }
    return false;
}
```

然后我们将自定义的注解类标记到UserDto类上：

```java
@Builder
@Getter
@Setter
@PasswordEqual
public class UserDto {

    @Length(min = 4, max = 10, message = "用户名长度必须在4-10个字符之间")
    private String name;

    private String password;

    private String confirmPassword;
}
```

接下来我们在写一个简单的创建用户的接口：

```java
@RestController
public class UserController {


    @PostMapping("/v2/user/create")
    @ResponseBody
    public UserDto createUser(@RequestBody @Validated UserDto dto){
        return dto;
    }
}
```

注意这里有要使用@ResponseBody能够返回自动序列化自定义对象，并且要写上 @Validated开启校验机制。

我们先输入正确的密码和确认密码一下：

![image-20200514233442583](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589470483.png)

可以看到能够正常的返回数据，这时再把两个密码改的不一样，试试：

![image-20200514233522345](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589470522.png)

这个时候就抛出了异常，这里的异常信息是因为进了全局异常处理器，不清楚的童鞋可以看下之前的文章。我们再来看一下控制台的输出：

![image-20200514233648244](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589470608.png)

控制台已经输出了校验的错误信息。



### 总结

我们今天介绍了自定义参数校验，并编写了校验注解和校验类，但是最后返回给用户的信息非常不友好，需要针对参数校验错误，能够返回定义的message，能够让有用户明白是哪里错了，下篇文章我们将介绍这块的内容，敬请关注！！