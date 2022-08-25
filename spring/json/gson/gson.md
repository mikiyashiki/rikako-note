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
### gson和对象联用的使用规范
- gson使用过程中，待序列化或反序列化的成员字段可以是private的，同时也推荐将待序列化或反序列化的成员字段声明为private
- 没有必要对成员字段使用注解来特定标明在序列化或者反序列化中包含该字段，默认情况下该对象所有成员字段和该对象父类对象所包含的所有字段都会被序列化
- 类似于jdk自带的序列化和反序列化机制，如果一个字段被标明为transient，该字段将不会被包含到序列化和反序列化过程中
- gson实现能够正确处理字段为空的情况
  - 当序列化过程中，为null的字段将会被省略
  - 当反序列化过程中，如果一个字段在json串中并没有被设置，反序列化得到的对象中该字段将会被设置为默认值：引用类型默认值为null、数字类型默认值为0，boolean类型其默认值为false
- 在内部类、匿名类、本地类中关联外部类的字段将会被忽略，并不会包含在序列化和反序列化过程中
### gson和嵌套类的关联使用
gson可以单独的对静态内部类进行序列化和反序列化，因为静态内部类并不包含对外部类的引用；但是gson无法单独的反序列化内部类，因为在反序列化内部类的过程中，其无参构造器需要一个指向其外部类对象的引用，但是该外部类对象在对内部类进行反序列化时是不可访问的。  
可以通过将内部类改为静态的内部类，此时对内部类的反序列化将不会需要指向外部类对象的引用。
### gson和数组的关联使用
gson支持多维数组，并且支持任意的复杂元素类型
```java
Gson gson = new Gson();
int[] ints = {1, 2, 3, 4, 5};
String[] strings = {"abc", "def", "ghi"};

// Serialization
gson.toJson(ints);     // ==> [1,2,3,4,5]
gson.toJson(strings);  // ==> ["abc", "def", "ghi"]

// Deserialization
int[] ints2 = gson.fromJson("[1,2,3,4,5]", int[].class);
// ==> ints2 will be same as ints
```
### gson对java中的集合进行序列化和反序列化
gson可以序列化任意对象的集合，但是无法对其进行反序列化，因为在反序列化时用户没有任何方法去指定其生成的集合中元素的类型。因而，需要通过typeToken来告知Gson需要反序列化的类型。
```java
Gson gson = new Gson();
Collection<Integer> ints = Arrays.asList(1,2,3,4,5);

// Serialization
String json = gson.toJson(ints);  // ==> json is [1,2,3,4,5]

// Deserialization
Type collectionType = new TypeToken<Collection<Integer>>(){}.getType();
Collection<Integer> ints2 = gson.fromJson(json, collectionType);
// ==> ints2 is same as ints
```
### gson对Map类型的序列化和反序列化
默认情况下，gson会将java中任意的Map实现类型序列化为JSON对象。由于JSON对象其key只支持字符串类型，gson会将待序列化的Map key调用toString转化为字符串。如果map中的key为null，则序列化后的key为"null"
```java
/**
  * gson对map进行序列化
  **/
Gson gson = new Gson();
Map<String, String> stringMap = new LinkedHashMap<>();
stringMap.put("key", "value");
stringMap.put(null, "null-entry");

// Serialization
String json = gson.toJson(stringMap); // ==> json is {"key":"value","null":"null-entry"}

Map<Integer, Integer> intMap = new LinkedHashMap<>();
intMap.put(2, 4);
intMap.put(3, 6);

// Serialization
String json = gson.toJson(intMap); // ==> json is {"2":4,"3":6}
```
在反序列化的过程中，gson会使用为Map key类型注册的TypeAdapter的read方法来进行反序列化。为了让gson知道反序列化得到的Map对象的key和value类型，需要使用TypeToken。
```java
Gson gson = new Gson();
Type mapType = new TypeToken<Map<String, String>>(){}.getType();
String json = "{\"key\": \"value\"}";

// Deserialization
Map<String, String> stringMap = gson.fromJson(json, mapType);
// ==> stringMap is {key=value}
```
默认情况下，gson序列化map时，复杂类型的key会调用toString方法来将其转化成字符串。但是，gson同样支持开启复杂类型key的序列化操作。通过Gson.enableComplexMapKeySerialization()方法来开启，Gson会调用为Map的key类型注册的TypeAdapter的write方法来序列化key，而不是通过toString方法将key转化为字符串。  
> 当Map中任意一条Entry的key通过Adapter被序列化为了JSON数组或者对象，那么Gson会将整个Map序列化为Json数组，数组元素为map中entry的键值对。如果map中所有的entry key都不会被序列化为json object或json array，那么该map将会被序列化为json对象

> 在对枚举型key进行反序列化的过程中，如果enum找不到一个具有匹配name()值的常量时，其会采用一个回退机制，根据枚举常量的toString()值来进行反序列化的匹配。

### 序列化和反序列化泛型对象
当gson对object对象调用toJson方法时，gson会调用object.getClass()来获取需要序列化的字段信息。类似的，在gson调用fromJson时，会向fromJson方法传递一个MyClass.class对象。该方法在序列化和反序列化类型是非泛型类型时能够正常运行。  
但是，当待序列化和反序列化的类型是泛型类型时，在序列化和反序列化对象时泛型类型信息会丢失，因为泛型采用的是类型擦除。
```java
class Foo<T> {
  T value;
}
Gson gson = new Gson();
Foo<Bar> foo = new Foo<Bar>();
gson.toJson(foo); // May not serialize foo.value correctly

gson.fromJson(json, foo.getClass()); // Fails to deserialize foo.value as Bar
```
> 上述代码中，foo.getClass()方法返回的只是Foo.class对象，并不会包含泛型类型Bar的信息，故而在反序列化时gson并不知道应该将value反序列化为Bar类型

可以通过向fromJson中传入Type参数来详细指定想要将json串转化成的泛型类型信息
```java
Type fooType = new TypeToken<Foo<Bar>>() {}.getType();
gson.toJson(foo, fooType);

gson.fromJson(json, fooType);
```
### 序列化和反序列化集合，集合中保存任意类型的对象
当JSON串中数组包含各种类型元素时，将其转化为包含任意类型的java集合，可以有如下方法：
- 使用Gson Parser API（JsonParser，底层parser api）将json串中数组转化为JsonArray，并且为每个元素调用Gson.fromJson。该方法是推荐的方法
> gson.fromJson可以针对String、Reader、JsonElement来调用


