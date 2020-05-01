# IoC

IoC,Inversion of Control,控制反转，把传统上由程序代码直接操控对象的调用权交给容器，通过容器来实现对象组件的装配和管理，

## 实现策略

服务定位模式

依赖注入模式：构造注入、参数注入、setter注入、接口注入

上下文依赖查询

模板方式的设计模式- spring中大量使用，

策略模式

## 主要职责

* 通用职责
  * 依赖处理
    * 依赖查找,主动
    * 依赖注入，大都是由容器处理
  * 生命周期管理
    * 容器
    * 托管的资源(Java Beans 或其他资源) 
  * 配置
    * 容器
    * 外部化配置
    * 托管的资源(Java Beans 或其他资源)

### Ioc依赖查找

##### 根据bean名称和ID查找，在一个上下文都是唯一的

* 实时查找
* 延迟查找

##### 根据bean类型查找

* 单个bean对象
* 集合bean对象

```java
Exception in thread "main" org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type 'com.zyj.imooc.domain.User' available: expected single matching bean but found 2: user,superUser
```



##### 根据bean名称和类型查找

##### 根据java注解查找

* 单个bean对象
* 集合bean对象

### ioc依赖注入

##### 根据bean名称注入

##### 根据bean类型注入

	* 单个bean对象
	* 几个bean对象

##### 注入容器内建bean对象

##### 注入非bean对象

##### 注入类型

	* 实时注入
	* 延迟注入