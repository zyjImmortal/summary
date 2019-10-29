### Rest Assured使用

##### 静态导入

在导入Rest Assured相关类时，官方推荐使用静态导入的方式

``````java
import static io.restassured.RestAssured.*;
import static io.restassured.matcher.RestAssuredMatchers.*;
import org.hamcrest.Matchers.*;
``````

##### 发送get请求

``````java
public static <T> T requestAdvertForGet(String url, Class<T> responseClass, Object... objects) {
        T response = requestBusiness()
                .headers(GlobalVar.HEADERS)
                .get(url, objects)
                .then()
                .statusCode(200)
                .extract()
                .as(responseClass);
        return response;
    }
``````

##### 配置返回BigDecimal

在返回数据又float或者double类型进行对比的时候，可能会产生精度问题，尽量使用BigDecimal包装后进行比较，

``````java
given().     config(RestAssured.config().jsonConfig(jsonConfig().numberReturnType(BIG_DECIMAL))).
when().
        get("/price").
then().
        body("price", is(new BigDecimal(12.12));
``````

