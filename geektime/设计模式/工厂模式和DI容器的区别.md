### 工厂模式介绍

### DI容器

DI 容器底层最基本的设计思路就是基于工厂模式的。DI 容器相当于一个大的工厂类，负责在程序启动的时候，根据配置（要创建哪些类对象，每个类对象的创建需要依赖哪些其他类对象）事先创建好对象。当应用程序需要使用某个类对象的时候，直接从容器中获取即可。正是因为它持有一堆对象，所以这个框架才被称为“容器”

##### DI容器的核心功能

* 配置解析

将类对象和创建类对象所需要的必要信息，放到配置文件中，容器读取配置文件，根据配置文件提供的信息来创建对象

* 对象创建

对象的创建是通过一个统一的工厂类来实现的BeansFactory，它能在程序运行的过程中，动态地加载类、创建对象，不需要事先在代码中写死要创建哪些对象。

* 对象生命周期管理

简单工厂模式有两种实现方式，一种是每次都返回新创建的对象，另一种是每次都返回同一个事先创建好的对象，也就是所谓的单例对象。在 Spring 框架中，我们可以通过配置 scope 属性，来区分这两种不同类型的对象。scope=prototype 表示返回新创建的对象，scope=singleton 表示返回单例对象。

还可以配置对象是否支持懒加载，懒加载就是说在获取对象的时候才创建，不设置懒加载的话，在应用程序启动的时候就会创建好对象。

DI 容器在创建好对象之后，会主动调用 init-method 属性指定的方法来初始化对象。在对象被最终销毁之前，DI 容器会主动调用 destroy-method 属性指定的方法来做一些清理工作，比如释放数据库连接池、关闭文件。

##### DI容器设计实现

DI容器最核心的两部分就是配置文件解析和根据配置文件通过java的反射语法来创建对象

* Bean对象的描述

在容器中通过BeanDefinition这个数据结构来描述bean对象，负责保存 bean 对象的所有必要信息，包括 bean 对象的id，name，以及 class 类型、是否是抽象类、构造方法和参数、其他属性等等

```java
public class BeanDefinition {
    private String id;
    private String className;
    private List<ConstructorArg> constructorArgs = new ArrayList<>();
    private Scope scope = Scope.SINGLETON;
    private boolean lazyInit = false;

    public boolean isSingleton(){
        return scope.equals(Scope.SINGLETON);
    }
  // 其他getter和setting方法
}
```

* 配置文件解析

配置文件解析类会把xml定义的bean解析成BeanDefinition结构，

* sadf
* 