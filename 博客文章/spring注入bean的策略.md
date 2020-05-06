上篇文章我们提到在有多个实现类的情况下，spring是如何选择特定的bean将其注入到代码片段中，我们讨论了按照名称注入和使用@Qualifier 注解输入的两种方式，本篇文章将结合之前提到的和spring的其他注入方式一起进行讨论。

# 本文主题

我们将讨论在一个接口或者抽象类在具有多个实现类的情况下，有多少种策略能够让我们在特定的代码片段中注入想要的bean。

# 按照名称

定义一个Skill借口，他描述了英雄具备哪些技能，现在有两个英雄的实现类Diana和Irelia，通过component注解将其标注为bean，在容器启动的时候会将他们加载到容器中。

现在有一个BannerController要使用Diana这个bean

```java
@RestController
public class BannerController {

    @Autowired
    private Skill diana;

    @RequestMapping(value = "/v2/banner", method = {RequestMethod.GET})
    public String test() {
        diana.r();
        return "Hello SpringBoot";
    }
}
```

`private Skill diana;`这里将成员变量的名字写成Diana这个bean的名字，容器就会选择Diana这个注入，这就叫做按照名称注入。spring会给每个bean设置默认到的名字也可以自定义，大家自行查找资料了解即可。

# @Qualifier 注解

还是上文的例子，如果成员变量名不写成bean的名称，是其他的名字，比如按照常规的写法会写成

`private Skill skill;`这个时候就可以使用@Qualifier 注解，

```java 
@RestController
public class BannerController {

    @Autowired
    @Qualifier("diana")
    private Skill skill;

    @RequestMapping(value = "/v2/banner", method = {RequestMethod.GET})
    public String test() {
        skill.r();
        return "Hello SpringBoot";
    }
```

# 有选择的注入一个bean，注释掉某个bean上的@Component

如果我们确定要使用哪个bean，那可以把其他的注释掉

```java
//@Component
public class Irelia implements Skill {

    public Irelia() {
        System.out.println("Hello, Irelia");
    }
    public void q(){
        System.out.println("Irelia Q");
    }

    public void w(){
        System.out.println("Irelia W");
    }

    public void e(){
        System.out.println("Irelia E");
    }

    public void r(){
        System.out.println("Irelia R");
    }
}
```

让Skill的实现类只有一个，自然就不要再操心注入哪个了，就只会注入存在于容器当中的惟一的那一个了。

# 使用@Primary提高bean的优先级

还可以使用@Primary注解提高我们想让注入bean的优先级，那spring在扫描到相同类型的bean的时候，就会看谁的优先级高，谁高谁注入

```java
@Component
@Primary
public class Diana implements Skill {
    private String skillName = "Diana R";

    public Diana() {
        System.out.println("I am Diana");
    }
    public void q(){
        System.out.println("Diana Q");
    }

    public void w(){
        System.out.println("Diana W");
    }

    public void e(){
        System.out.println("Diana E");
    }

    public void r(){
        System.out.println(this.skillName);
    }
}
```

然后启动启动程序，访问路由，就会看到下面的输出：

![image-20200505154939833](https://zyjblog.oss-cn-beijing.aliyuncs.com/1588664983.png)

说明确实是注入了Diana这个bean。

**阶段总结**

上面的实现方式看起来都能实现按照所需注入特定的bean，但是有个问题，当发生变化的时候，这里的变化可能是英雄名变了，新增英雄或者要替换其他英雄，都需要去手动改代码，有些情况下可能还不只改一处，还是比较繁琐，并不方便。那有没有一种方式可以通过配置文件的形式来指定要注入的bean，当变化发生，只需要改配置，那岂不是很方便很灵活很开心。

# 条件注解

条件注解顾名思义，就是按照条件决定注入哪一个bean，所以要想使用条件注解，就得先写一个条件。

spring为我们提供了特定的方式来实现自己的条件：@Conditional注解+Condition接口,spring的这种方式的原理是把符合条件的bean加加载到容器中，不合的不加载，那这样在注入的时候就不会有多个相同类型的bean存在了，自然也就注入成功了。

## 定义一个类实现Condition接口

```java
import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.type.AnnotatedTypeMetadata;

public class HeroCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        
        return false;
    }
}
```

实现Condition接口必须实现它的matches方法，我们先来解释一下这个方法的入参和返回值，

ConditionContext是一个接口，定义了他支持那些操作

```java 
public interface ConditionContext {
	BeanDefinitionRegistry getRegistry();
	ConfigurableListableBeanFactory getBeanFactory();
	Environment getEnvironment();
	ResourceLoader getResourceLoader();
	ClassLoader getClassLoader();
}
```

其中的getEnvironment方法会返回Environment对象，他里面会记录当前应用所有的环境信息，可以通过这个对象获取到我们的配置信息。

返回值是Boolean类型的，哪个bean的条件匹配成功，就会把这个bean注入到代码片段中去。我们来具体实现一下。

```java
public class HeroCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        String name = context.getEnvironment().getProperty("hero.condition");
        System.out.println(name);
        if ("diana".equalsIgnoreCase(name)){
            return true;
        }else if ("irelia".equalsIgnoreCase(name)){
            return true;
        }
        return false;
    }
}
```

```java
@Configuration
public class HeroConfiguration {
    @Bean
    @Conditional(HeroCondition.class)
    public Skill diana(){
        return new Diana();
    }

    @Bean
    @Conditional(HeroCondition.class)
    public Skill irelia(){
        return new Irelia();
    }
    
}
```

通过以上的方式，就可以完成一个简单的条件注解，但是这种需求其实spring boot早已经帮我们实现了，提供了一些注解可以使用，下篇文章我们看看使用spring boot的内置注解，如何来解决我们的问题。