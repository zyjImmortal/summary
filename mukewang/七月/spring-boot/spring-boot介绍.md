# 自动装配

1、原理

2、为什么要有自动装配

有些属性要在配置文件中配置，部分的属性是要写在配置文件当中的，允许从配置文件中更改bean的属性

把已经配置好的bean加入到容器中

```
EnableAutoConfiguration
```

EnableAutoConfiguration本身也是一个配置类，也是在做配置类的事情，只不过它主要是加载pom文件引入的第三方依赖的bean，加载到IOC容器中

自动装配就是提供的一种加载机制



@Enable

模块装配：



先用@ComponentScan再用@Import

SPI机制：Service Provider Interface

系统中可能会依赖很多模块，但是各个模块的实现机制可能不一样，有时候需要灵活的替换不同的实现方案，

调用方可以通过调用标准的服务接口，从而能够调用到模块提供到的功能，而不用关心背后的具体实现方案，这里替换不同的实现方案就可以通过配置文件的形式实现。

spring-boot的自动加载机制就是这种SPI思想的一种衍生



@Primary 条件注解关注的点，是类 bean



SPI机制关注的是整体解方案的变化





多出来一个IOC容器，自动配置其实就是解决了把配置的bean加载到IOC容器了，自己配置类的factories写上要加入IOC容器到的配置类，springboot会自动加载到IOC容器中，以前呢是需要通过手动在xml文件自己配置加载到容器中，

