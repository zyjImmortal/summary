

上文我们提高一个类被@Component标记后，就可以被容器扫描到，并加载到容器中。但是会有一个问题，如果一类的构造方法时有参构造方法，那在实例化的时候这些参数该怎么传呢？那还有没有其他方式将一个类加载到容器中呢，那就引出里我们今天要谈论的@Configuration注解



# @Configuration注解的使用

用@Configuration注解标注的类，表明是一个配置类，在这个配置类里面可以自定义bean的加载。比如文章开篇提到的问题，在初始化bean的时候传入参数：

新增一个带参数的英雄实现类：

```java
public class Camille implements Skill{
    private String skillName = "Camille R";
    private String name;
    private Integer age;
    public Camille(String name, Integer age){
        this.name = name;
        this.age = age;
    }
    public Camille() {
        System.out.println("Hello,Camille");
    }
    public void setSkillName(String skillName) {
        this.skillName = skillName;
    }
    public void setName(String name) {
        this.name = name;
    }
    public void setAge(Integer age) {
        this.age = age;
    }
    public void q(){
        System.out.println("Camille Q");
    }
    public void w(){
        System.out.println("Camille W");
    }
    public void e(){
        System.out.println("Camille E");
    }
    public void r(){
        System.out.println(this.skillName);
    }
}
```

再新建一个配置类：

```java
@Configuration
public class HeroConfiguration {
    @Bean
    public Skill camille(){
        return new Camille(“camille”， 34);
    }
}
```

@Configuration标记HeroConfiguration类表明它是一个配置类，@Bean表明这个方法返回的是一个bean对象，需要加载到容器中，然后手动new一个自定义的对象，并返回。当然在一个配置类中，可以存在多个被@bean标记到的方法。

# @Configuration注解的作用

配置文件具有集中性

清晰，没有业务逻辑

配置类更加的灵活，能够自定义一些bean的初始化逻辑，而且对于某一个业务方向到的bean都可以放到一起进行管理。

