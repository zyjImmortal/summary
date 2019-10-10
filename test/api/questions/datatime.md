### Rest Assured如何序列化和反序列化Joda-Time中的DateTime对象

Rest Assured使用jackson日期进行序列化和反序列，项目中使用Joda-Time作为时间、日期的处理工具，刚好jackson中有对Joda-Time的扩展支持，在项目中引入包，

``````xml
<dependency>
     <groupId>com.fasterxml.jackson.datatype</groupId>
     <artifactId>jackson-datatype-joda</artifactId>
     <version>2.2.3</version>
</dependency>
``````

在序列化DateTime对象的时候，就会序列化成时间戳的形式，此时序列化的值是以毫秒为单位表示的时间戳，但是服务端是使用秒为单位，所以在序列化和反序列化的时候需要修改为秒为单位，这就需要重写jackson的序列化和反序列化的方式，通过自定义序列化和反序列化器，在DateTime变量上使用@JsonSerialize(use='')来指定以自定义的序列化器

先看一下，jackson是如何序列化一个DateTime对象的,jackson的时间默认序列方式是以时间戳的形式序列化，当然还有其他的方式都在```SerializationFeature```枚举了类中，```WRITE_DATES_AS_TIMESTAMPS(true),```默认为true，看源码，

``````java

public final class DateTimeSerializer
    extends JodaSerializerBase<DateTime>
{
    public DateTimeSerializer() { super(DateTime.class); }

    @Override
    public void serialize(DateTime value, JsonGenerator jgen, SerializerProvider provider)
        throws IOException, JsonGenerationException
    {	  // WRITE_DATES_AS_TIMESTAMPS默认true是启用状态
        if (provider.isEnabled(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS)) {
        		// getMillis()返回的是DateTime对象的毫秒表示，那我们只要处理这里就可以了，让其除以1000就是以秒为单位了
            jgen.writeNumber(value.getMillis());
        } else {
            jgen.writeString(value.toString());
        }
    }
``````

但是我们自定义序列化器的时候，却不能使用DateTimeSerializer的父类JodaSerializerBase抽象类,因为它的构造方法定义protected的，不能再包外访问到，但是在JodaSerializerBase并没有发现serialize方法,说明DateTimeSerializer并不是实现的这个父类的，那继续向上找，看到JodaSerializerBase继承的StdSerializer抽象类，在这个类里发现了serialize方法,但是这个抽象类的所有构造方法全都是protected的，陷入懵逼。。。。。。再看，发现serialize方法也是继承而来的，发现曙光，继续往上找，JsonSerializer这个抽象类中了，StdSerializer的父类，而且构造方法是public的，那就开工了喽

``````java
import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.JsonSerializer;
import com.fasterxml.jackson.databind.SerializerProvider;
import org.joda.time.DateTime;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.IOException;

// 自定义序列化器
public class CustomTimestampSerializer extends JsonSerializer<DateTime> {
    private Logger logger = LoggerFactory.getLogger(CustomTimestampSerializer.class);

    @Override
    public void serialize(DateTime value, JsonGenerator jgen, SerializerProvider provider) throws IOException, JsonProcessingException {
        jgen.writeNumber(value.getMillis() / 1000);
    }
}
``````

同样我们再把反序列化器再实现一下

``````java
public class CustomTimestampDeserializer extends JsonDeserializer<DateTime> {
    @Override
    public DateTime deserialize(JsonParser jp, DeserializationContext ctxt) throws IOException, JsonProcessingException {
        Logger logger = LoggerFactory.getLogger(CustomTimestampDeserializer.class);
        String timestamp = jp.getText().trim();
        try {
          // DateTime不支持直接传入秒级的时间戳,所以需要处理一下
            return new DateTime(Long.valueOf(timestamp + "000"));
        } catch (NumberFormatException e) {
            logger.warn("Unable to deserialize timestamp: " + timestamp, e);
            return null;
        }
    }
}
``````

