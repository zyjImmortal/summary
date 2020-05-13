Lombok



```
<lombok.version>1.18.10</lombok.version>
```

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>${lombok.version}</version>
</dependency>
```

@Data是一系列注解的合集

Getter\setter\equals\hashcode\toString

有选择的使用，不建议使用@Data，根据实际情况去使用特定的注解，除非很明确要使用的hashcode\toString\equals

被final关键字修饰的变量，是不会给生成setter方法的

java会给默认生成一个无参数的构造函数

@AllArgsConstructor全参数构造函数，但是在某些情况下，我们要求不是全参数的，只选定几个特定的参数作为构造函数的入参，这个时候可以使用

@RequiredArgsConstructor ,再用NonNull标记，这个时候就会只生成那些不为空参数的构造函数

@NoArgsConstructor 无参数的构造函数



@Builder，默认只使用builder来构建对象，不能再调用无参数构造函数



默认生成一个私有的无参数构造函数，如果想要使用new的这种方式生成对象，就需要添加一个@NoArgsConstructor 注解



JSR 校验规范

JSR-303 bean validation







