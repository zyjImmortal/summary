在持续集成中如何写出干净整洁、解耦以及可维护的case

使用Rest-Assured

[Rest-Assured](https://github.com/jayway/rest-assured)提供了很好用的流式api帮助我们去创建可读性很好的case

```java
@Test
void testBlog(){
    given()
            .param("limit",20)
            .when()
            .get("/articles")
            .then()
            .statusCode(200);
}
```

创建可重用的RequestSpecifications对象

创建一个RequestSpecifications对象去重用请求配置，例如base url，header，日志打印配置等

```java
private RequestSpecification spec;

@BeforeEach
void before(){
    spec = new RequestSpecBuilder()
            .setContentType(ContentType.JSON)
            .setBaseUri("http://127.0.0.1:8000")
            .addFilter(new ResponseLoggingFilter())
            .addFilter(new RequestLoggingFilter())
            .build();
}
```

使用POJO和对象映射

在构造请求的时候，不要使用字符连接或者创建笨重的json对象，在进行断言的时候也尽量不要直接对json进行处理，这样会造成case冗长，也不是类型安全的，并且容易出错。比如在断言中不得不进行跟多的类型转换或者时间格式化操作等

我们通过创建单独POJO对象，然后通过对象映射，例如Jackson直接对请求或者响应进行序列化和反序列化，取值就可以直接通过对象获取，序列化框架会帮助我们完成字段的类型检查。

```java
ArticleDto retrievedArticleDto = given()
        .spec(spec)
        .when()
        .get("/articles")
        .then()
        .statusCode(200)
        .extract()
        .as(ArticleDto.class);
```

POJO使用流式setters方法

参数较多的时候不应该使用构造方法或者一般的setter方法，这样会难以阅读并且容易出错。也可以使用Builder模式来构建。

```java
public class ArticleDto {
    private int id;
    private String content;
    private String digest;

    public ArticleDto setId(int id) {
        this.id = id;
        return this;
    }

    public ArticleDto setContent(String content) {
        this.content = content;
        return this;
    }

    public ArticleDto setDigest(String digest) {
        this.digest = digest;
        return this;
    }
    // getter
}
```

然后就可以利用流式api创建测试数据了

```java
ArticleDto dto = new ArticleDto()
        .setId(1)
        .setContent("example")
        .setDigest("example");
```

Rest-Assured提供了对象映射功能，可以序列化对象为json作为请求的body。

```java
given()
        .spec(spec)
        .body(dto)
        .when()
        .get("/path")
        .then()
        .statusCode(200)
```

使用AssertJ检查返回的POJO

AssertJ是一个提供各种流式断言的第三方库，可读性更高，可以写出类型安全的测试断言。

``````java
ArticleDto expectedEntity = new ArticleDto()
                .setId(0)
                .setContent("example")
                .setDigest("example");

        ArticleDto retrievedArticleDto = given()
                .spec(spec)
                .body(expectedEntity)
                .when()
                .get("/path")
                .then()
                .statusCode(200)
                .extract()
                .as(ArticleDto.class);
        assertThat(retrievedArticleDto.getContent())
                .as("content wrong") // 如果不相等，可以自定义错误信息
                .isEqualTo(dto.getContent());
``````

有时我们需要服务端创一个Article对象，并返回这个对象。此时需要传递一个ArticleDto，服务端创建后会返回一个ArticleDto，不过这个时候对多一个id参数。我们需要检查两个dto的字段是否一致，但是要排除掉id字段，此时可以使用AssertJ的`isEqualToIgnoringGivenFields()`方法，

``````
assertThat(retrievedArticleDto).isEqualToIgnoringGivenFields(expectedEntity, "id");
``````

这样就不必一个字段一个字段的检查。

下篇文章我们讨论如何使用泛型处理通用方法，如何更简洁的处理返回数据带有列表的情况，以及其他更多使测试代码更健壮的方法。