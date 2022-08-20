# Spring Data Redis
- ## Spring Data Redis
  - 在Spring框架中，与Redis进行通信，既提供了低层次的api与字节数据进行交互，也提供了高度抽象的api供用户使用
  - Redis Connection支持通过连接器和Redis服务端进行低层次的交互
  - RedisTemplate则是向用户提供了高层次api与Rdis服务端进行交互
- ## 连接Redis
  - 为了通过IOC容器连接Redis，需要使用统一的Spring Redis API。对于任何库提供的Redis Connector，都会统一向外提供一致的Spring Redis API。
  - Spring Redis API通过RedisConnection和RedisConnectionFactory接口进行工作，并且从Redis处获取连接
- ## RedisConnection和RedisConnectionFactory
  - RdisConnection用于提供和Redis后端的交互。RedisConnection接口会自动将底层Connector库异常转化为统一的Spring DAO异常，因而在使用ReidsConnection的过程中，如果切换底层链接库，并不需要代码的改动。
  - RedisConnection时通过RedisConnectionFactory来获取的
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
    > 相对于Jackson2JsonSerializer，GenericJackson2JsonSerializer在序列化对象为json串时添加了对象的java类型信息，故而在将json串反序列化并且转换为原有类型时不会抛出异常
    ```java
    /**
    * 如果想要自定义redisTemplate的序列化方式，可以添加如下配置类
    * RedisTemplate支持分别自定义key/value/hashkey/hashvalue的序列化方式
    * RedisTemplate也支持设置defaultSerializer，当key/value/hashkey/hashvalue的Serializer没有显式指定时，会应用defaultSerializer
    **/
      @Configuration
      public class SerializerConfig {
          @Bean(name="redisTemplate")
          RedisTemplate<Object,Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
              RedisTemplate<Object, Object> redisTemplate = new RedisTemplate<>();
              redisTemplate.setConnectionFactory(redisConnectionFactory);
              redisTemplate.setDefaultSerializer(new GenericJackson2JsonRedisSerializer());
              return redisTemplate;
          }
      }
    ```