# gson
## gson简介
gson是一个java库，通常用来将java对象转化为其json表示的字符串，或者将json格式的字符串转化为其等价的java对象。
## gson使用
在gson中，使用最频繁的类是Gson。可以通过new Gson()构造函数来创建Gson对象，也可以通过GsonBuilder来创建Gson对象，GsonBuidler在创建Gson对象时能够指定一些设置，如版本控制等。  
由于Gson对象在执行json操作时并不会保存任何状态，故而Gson对象是线程安全的，单一的Gson对象可以在多线程环境下被重复使用。
### Gson库通过Maven引入
```xml
<dependencies>
    <!--  Gson: Java to JSON conversion -->
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.9.1</version>
      <scope>compile</scope>
    </dependency>
</dependencies>
```
### 基本类型的序列化和反序列化
```java
// Serialization
Gson gson = new Gson();
gson.toJson(1);            // ==> 1
gson.toJson("abcd");       // ==> "abcd"
gson.toJson(new Long(10)); // ==> 10
int[] values = { 1 };
gson.toJson(values);       // ==> [1]

// Deserialization
int one = gson.fromJson("1", int.class);
Integer one = gson.fromJson("1", Integer.class);
Long one = gson.fromJson("1", Long.class);
Boolean false = gson.fromJson("false", Boolean.class);
String str = gson.fromJson("\"abc\"", String.class);
String[] anotherStr = gson.fromJson("[\"abc\"]", String[].class);
```
### 对象的序列化和反序列化
类似于java自带的序列化机制，当成员字段被transient修饰时，并不会序列化该字段
```java
class BagOfPrimitives {
  private int value1 = 1;
  private String value2 = "abc";
  private transient int value3 = 3;
  BagOfPrimitives() {
    // no-args constructor
  }
}

// Serialization
BagOfPrimitives obj = new BagOfPrimitives();
Gson gson = new Gson();
String json = gson.toJson(obj);

// ==> json is {"value1":1,"value2":"abc"}

// Deserialization
BagOfPrimitives obj2 = gson.fromJson(json, BagOfPrimitives.class);
// ==> obj2 is just like obj
```