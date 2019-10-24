#### RestAssured在接口自动化测试中应用

##### 接口请求

在接口自动化测试项目中通常把关于http请求的方法单独封装到```HttpClientUtil```工具类中，

```java
// 导入相关的一些静态方法
import static io.restassured.RestAssured.config;
import static io.restassured.RestAssured.given;
import static io.restassured.config.JsonConfig.jsonConfig;
import static io.restassured.path.json.config.JsonPathConfig.NumberReturnType.DOUBLE;

public class HttpClientUtil {
		
  	// 使用slf4j的logger，当前类的类名ClassNname通过MethodHandles.lookup().lookupClass()发射动态获取
    private static Logger LOGGER = LoggerFactory.getLogger(MethodHandles.lookup().lookupClass());
    private static RestAssuredConfig assuredConfig;
    public static HashMap<String, Object> headers = new HashMap<>();
    public static HashMap<String, Object> cookies = new HashMap<>();
		// 通过静态代码块加载配置，静态代码块和类加载的时候一起被加载，保证在各处引用restassured的配置都是一样的
    static {
        assuredConfig = config().jsonConfig(jsonConfig().numberReturnType(DOUBLE));
    }

    private static RequestSpecification baseRequestSpecification(String baseURL) {
        RestAssured.baseURI = baseURL;
        return given()
                .relaxedHTTPSValidation()
                .headers(headers)
                .cookies(cookies);
    }

    private static RequestSpecification requestBusiness() {
        return baseRequestSpecification(GlobalVar.ADVERT_ROOT_SERVER);
    }

    public static Response requestBusiness(String url, Object obj) {
        Response response = requestBusiness()
                .headers(GlobalVar.HEADERS)
                .contentType(ContentType.JSON)
                .accept(ContentType.JSON)
                .body(obj)
                .when()
                .post(url);
        LOGGER.info("Request Url=>" + url + ", Response=>" + response.prettyPrint());
        return response;
    }
}
```

##### 序列化与反序列化

##### 断言



