# Spring Data Redis
- ## Spring Data Redis
  - 在Spring框架中，与Redis进行通信，既提供了低层次的api与字节数据进行交互，也提供了高度抽象的api供用户使用
  - Redis Connection支持通过连接器和Redis服务端进行低层次的交互
  - RedisTemplate则是向用户提供了高层次api与Rdis服务端进行交互
- ## RedisTemplate
  - RedisTemplate是线程安全的，可以在多个线程中被并发安全的使用
  - RedisTemplate使用java底层的序列化机制，在通过RedisTemplate读取或者写入数据时，会通过Java的序列化机制将对象序列化/反序列化。
  - RedisTemplate要求key是非空的，但是可以接受空的value值
  - 当想要使用RedisTemplate的某个视图时，可以将RedisTemplate对象注入给该名为xxxOperations的视图。
    ```java
    @Service
    public class RedisTemplateOperations {
        // 直接注入
        @Autowired
        private RedisTemplate<String,String> redisTemplate;

        // 将RedisTemplate转换成其某一个视图之后再注入
        @Resource(name="redisTemplate")
        private ListOperations<String,String> opsForList;
    }
    ```
  - ## StringRedisTemplate
    - 在Redis操作中，key和Value通常都是String类型。故而，Spring Data Redis提供了操作StringRedisTemplate类
    - StringRedisTemplate是RedisTemplate的子类，并且在应用启动时IOC容器中会注入redisTemplate和stringRedisTemplate两个对象
    - 相比较于RedisTemplate，StringRedisTemplate底层使用StringRedisSerializer来进行序列化，其序列化的key和value都是可读的
  - ## 序列化Serializer选择
    - 除了先前提到的jdk默认的序列化机制和StringRedisSerializer，Spring Data Redis还提供了其他Serializer
    - 例如，可以选择将key和value化为json字符串格式，可以选用Jackson2JsonSerializer或者GenericJackson2JsonSerializer
