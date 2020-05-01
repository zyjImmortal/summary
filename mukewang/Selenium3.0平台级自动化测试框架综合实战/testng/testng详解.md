testng是一个开源的java的自动化测试框架，其灵感来自于junit和Nunit，但是映入了一些新的功能，使其更加强大，使用也更加的方便

 

不能够支持足够的注解，参数注解，junit是为开发设计，进行单元测试的工具，不能支持命令行，不能支持多线程的方式，更兼容测试范畴。



特点：

注解：更完善到的测试体系提出的注解

使用java和面向对象的功能

支持测试类的组合

灵活的运行时配置，更多的配置参数

支持依赖测试，支持测试，定义多个测试类的时候可以进行相互依赖

支持多线程测试



注解;



```
@Tes t执行测试
@BeforeTest 指定是在并行的第一个test测试前运行
@BeforeSuite 里边的方法是在所有测试前运行
@BeforeClass 在当前类的第一个测试方法前运行
@BeforeMethod 在每一个方法前运行

@AfterTest
@AfterClass
@AfterMethod
@AfterSuite
```

testng的运行模式suit-test-class-method