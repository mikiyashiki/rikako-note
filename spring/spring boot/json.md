# Spring Boot JSON
## Spring Boot JSON简介
在Spring Boot中为JSON提供了三个集成的内置库：Gson、Jackson、JSON-B。
> 其中，Jackson是Spring Boot推荐并且默认的JSON库。

Spring Boot项目为Jackson提供了自动装配，并且Jackson是spring-boot-starter-json启动器的一部分。当Jackson依赖在classpath下时，ObjectMapper的bean对象会自动的被配置。
## 自定义序列化器和反序列化器
如果使用Jackson进行json数据的序列化和反序列化，你可能需要实现自己的序列化类和反序列化类。可以通过@JsonComponent注解来定义自己的JsonSerializer, JsonDeserializer,JsonObjectSerializer,JsonObjectDeserializer
```java
@JsonComponent
public class MyJsonComponent {

    public static class Serializer extends JsonSerializer<MyObject> {

        @Override
        public void serialize(MyObject value, JsonGenerator jgen, SerializerProvider serializers) throws IOException {
            jgen.writeStartObject();
            jgen.writeStringField("name", value.getName());
            jgen.writeNumberField("age", value.getAge());
            jgen.writeEndObject();
        }

    }

    public static class Deserializer extends JsonDeserializer<MyObject> {

        @Override
        public MyObject deserialize(JsonParser jsonParser, DeserializationContext ctxt) throws IOException {
            ObjectCodec codec = jsonParser.getCodec();
            JsonNode tree = codec.readTree(jsonParser);
            String name = tree.get("name").textValue();
            int age = tree.get("age").intValue();
            return new MyObject(name, age);
        }

    }

}

@JsonComponent
public class MyJsonComponent {

    public static class Serializer extends JsonObjectSerializer<MyObject> {

        @Override
        protected void serializeObject(MyObject value, JsonGenerator jgen, SerializerProvider provider)
                throws IOException {
            jgen.writeStringField("name", value.getName());
            jgen.writeNumberField("age", value.getAge());
        }

    }

    public static class Deserializer extends JsonObjectDeserializer<MyObject> {

        @Override
        protected MyObject deserializeObject(JsonParser jsonParser, DeserializationContext context, ObjectCodec codec,
                JsonNode tree) throws IOException {
            String name = nullSafeValue(tree.get("name"), String.class);
            int age = nullSafeValue(tree.get("age"), Integer.class);
            return new MyObject(name, age);
        }

    }

}
```
## Jackson使用
Jackson使用ObjectMapper来将json转化为java对象，或者将java对象转化为json。
### ObjectMapper处理json域字段和java类域字段的映射关系

