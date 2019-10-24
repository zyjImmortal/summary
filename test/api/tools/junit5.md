### Junit5总结

##### Junit5相比Junit4都有那些改进

##### 注解

##### 断言

#####扩展

##### 标签和过滤器

``````xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.20.1</version>
    <configuration>
        <forkMode>once</forkMode>
        <argLine>-Dfile.encoding=UTF-8</argLine>
        <excludes>
            <exclude>**/**/*Test.java</exclude>
        </excludes>
    </configuration>
</plugin>
``````

``````xml
<build>
    <plugins>
        <plugin>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.22.2</version>
            <configuration>
              	<!--表示包含-->
                <groups>acceptance | !feature-a</groups>
              	<!--忽略掉-->
                <excludedGroups>integration, regression</excludedGroups>
            </configuration>
        </plugin>
    </plugins>
</build>
``````



##### TestMethodOrder 自定义case执行顺序

```case```之间应该尽可能的保持解耦，case的执行顺序不能影响case的执行结果，但是在某些场景下，要设置顺序，让错误尽早暴露，这样后面的case如果调用了相同接口的case就不用再执行，比如对一个司机抢单接口，抢单的时候需要先生成订单，如果先执行下单接口相关的case，再执行抢单接口的case，那么如果下单接口报错，抢单接口case就无须再执行。

``````java
// @TestMethodOrder注解标记测试类要启用排序，需要传入MethodOrderer的实现类，表明
// 要使用哪种排序方式
/**
 * 
 *OrderAnnotation 基于数字值进行排序，数值越低优先级越高，使用@Order(1)传入数值
 *Alphanumeric 使用alphanumerically
 * Random 使用随机数
 */
@TestMethodOrder(OrderAnnotation.class)
public class AnnotationTest {
    @Test
    @Order(2)
    void testAnnotation_disables(){

    }

    @Test
    @DisplayName("1 + 1 = 2")
    @Order(1)
    void addsTwoNumbers() {
        Calculator calculator = new Calculator();
        assertEquals(2, calculator.add(1, 1), "1 + 1 should equal 2");
    }
		// 指明是参数测试方法，通过CsvSource提供数据
    @ParameterizedTest(name = "{0} + {1} = {2}") // 格式化字符串，{}占位符，0表示第一位
    @CsvSource({
            "0,    1,   1",
            "1,    2,   3",
            "49,  51, 100",
            "1,  101, 101"
    })
    @Order(3)
    void add(int first, int second, int expectedResult) {
        Calculator calculator = new Calculator();
        assertEquals(expectedResult, calculator.add(first, second),
                () -> first + " + " + second + " should equal " + expectedResult);
    }
``````

##### ```@TestInstance```测试实例生命周期

针对每个被@Test测试方法以及@BeforeEach、@AfterEach标记的方法，都会实例化一个测试类，也就是测试类的所有实例中，以上三个注解标记的方法中的代码每次实例化测试类的时候都会执行一遍，被@AfterAll和@BeforeAll标记的是针对测试类的所有实例共有的，所以只会执行一遍。Junit5默认实例生命周期和Junit4之前版本实例生命周期，都是上面这种模式，@AfterAll和@BeforeAll标记的方法需要用static标记作为静态方法，也只能使用静态成员变量，

```java
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
```

是用这种方式标记测试类以后，@AfterAll和@BeforeAll标记的方法不再需要static修饰，可以使用实例成员变量，但是也还是只执行一次。

```java
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
public class InstanceTest {

    private Calculator calculator;
    @BeforeAll
    void setup(){
        calculator = new Calculator();
        System.out.println("BeforeAll");
    }

    @BeforeEach
    void setupOnce(){
        System.out.println("BeforeEach");
    }

    @ParameterizedTest(name = "{0} + {1} = {2}")
    @CsvSource({
            "0,    1,   1",
            "1,    2,   3",
            "49,  51, 100",
            "1,  101, 101"
    })
    void add(int first, int second, int expectedResult) {
        assertEquals(expectedResult, calculator.add(first, second),
                () -> first + " + " + second + " should equal " + expectedResult);
    }
}
```

输出：

``````markdown
BeforeAll
BeforeEach
BeforeEach
BeforeEach
BeforeEach

org.opentest4j.AssertionFailedError: 1 + 101 should equal 101 ==> 
Expected :101
Actual   :102
``````

