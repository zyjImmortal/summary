# Junit和testng的比较

注解支持

表达式测试

忽略测试

超时测试

测试集

测试组

参数化

依赖测试



## 注解支持

在JUnit 4中，我们必须声明@BeforeClass和@AfterClass方法作为静态方法，testng没有这个约束，而且testng比Junit多了三个额外的注解，before/after suit,before/after test,before/after group

## 异常测试

是都支持的

## 忽略测试

都支持

## 设置超时测试

“时间测试”表示如果单元测试所花费的时间超过指定的毫秒数，则测试将会终止，并将其标记为失败，此功能在JUnit 4和TestNG中均可实现。

```text
@Test(timeOut = 1000)
```

## 测试套件

“套件测试”是指捆绑几个单元测试并一起运行

```text
@RunWith(Suite.class)
@Suite.SuiteClasses({
        JunitTest1.class,
        JunitTest2.class
})
public class JunitTest5 {
}
```

```
<suite name="My test suite">
  <test name="testing">
    <classes>
       <class name="com.fsecure.demo.testng.TestNGTest1" />
       <class name="com.fsecure.demo.testng.TestNGTest2" />
    </classes>
  </test>
</suite>
```

## 参数化测试

“参数化测试”是指单位测试参数值的变化。 此功能在JUnit 4和TestNG中都实现。 然而，两者都使用非常不同的方法来实现它。

### Junit

这里有很多限制，我们必须遵循“JUnit”的方式来声明参数，并且必须将参数传递给构造函数才能初始化类成员作为测试的参数值。参数类的返回类型为“List []”，数据已被限制为String或用于测试的原始类型值

```
@RunWith(value = Parameterized.class)
public class JunitTest6 {

     private int number;

     public JunitTest6(int number) {
        this.number = number;
     }

     @Parameters
     public static Collection<Object[]> data() {
       Object[][] data = new Object[][] { { 1 }, { 2 }, { 3 }, { 4 } };
       return Arrays.asList(data);
     }

     @Test
     public void pushTest() {
       System.out.println("Parameterized Number is : " + number);
     }
}
```

### TestNG

有两种方式xml和注解@DataProvider,TestNG的参数化测试非常用户友好和灵活(在XML文件或类内)。 它可以支持许多复杂的数据类型作为参数值，可能性是无限的

```
@Test(dataProvider = "Data-Provider-Function")
public void parameterIntTest(TestNGTest6_3_0 clzz) {
   System.out.println("Parameterized Number is : " + clzz.getMsg());
   System.out.println("Parameterized Number is : " + clzz.getNumber());
}

//This function will provide the patameter data
@DataProvider(name = "Data-Provider-Function")
public Object[][] parameterIntTestProvider() {

    TestNGTest6_3_0 obj = new TestNGTest6_3_0();
    obj.setMsg("Hello");
    obj.setNumber(123);

    return new Object[][]{
               {obj}
    };
}
```

```
<suite name="My test suite">
  <test name="testing">

    <parameter name="number" value="2"/>

    <classes>
       <class name="com.fsecure.demo.testng.TestNGTest6_0" />
    </classes>
  </test>
</suite>
```

