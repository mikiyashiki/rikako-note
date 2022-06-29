# SpringBoot 核心特性
* ## 外部配置
  * springboot允许通过如下方式来完成外部配置：
    * properties文件配置
    * yaml文件配置
    * 环境变量配置
    * 命令行参数配置
  * springboot项目的外部配置顺序如下，位于后面的配置会覆盖前面的配置项
    * 各项默认值
    * @PropertiesSource
      * @PropertiesSource注解用于将properties文件中的键值对加入到Environment中
    * config data（在application.properties中定义的键值对）
      * 如果一个键值对同时在properties文件和yaml文件中定义，那么properties文件中的定义将会覆盖yaml文件中的定义
      * config data的覆盖顺序
        * application properties(properties和yaml文件中的配置项)
        * profile文件中的配置项
        * 位于jar包之外的properties（当前目录下的properties文件或yaml文件）
        * 位于jar包之外的profile（位于当前目录之下的profile文件）
    * RandomPropertySource：以random开头的随机值，可以是int、long、uuid、byte[]
    * 操作系统的环境变量
    * Java System Properties（在命令行中通过-Dpropertyname=value指定的键值对）
    * java:comp/env中的JND属性
    * ServletContext中的初始化参数
    * ServletConfig中的初始化参数
    * Properties from SPRING_APPLICATION_JSON (inline JSON embedded in an environment variable or system property).
    * Command line arguments.
    * properties attribute on your tests. Available on @SpringBootTest and the test annotations for testing a particular slice of your application.
    * @TestPropertySource annotations on your tests.
    * Devtools global settings properties in the $HOME/.config/spring-boot directory when devtools is active.
  * 外部配置可以通过如下方式来指定
    * --propertyname=value形式来指定
    ```shell
    # 通过命令行指定server.port属性的值
    $ java -jar --server.port=8080
    ```
    * -Dpropertyname=value形式指定
    ```shell
    # 通过-D选项来指定属性的值
    $ java -Dserver.port=8080
    ```
  * 外部配置时配置文件可位于的位置
    * 在启动应用程序时，通过会按顺序在如下位置查找properties文件和yaml文件
      * classpath：
        * 在classpath下的root路径
        * classpath下的/config路径
      * 当前目录：
        * 当前目录下的root路径
        * 当前目录下的/config路径
        * 当前目录下/config目录的直接子目录
      * 在上述顺序中，位于后面位置的配置文件会覆盖位于前面位置的配置
    * spring.config.location设置
      * 在spring.config.location属性下，可以为其设置多个配置文件位置，多个位置之间通过‘，’来隔开
      * 如果将多个位置视为同一层次，可以使用‘；’来隔开位于同一位置组的所有位置，位于同一位置组的所有位置都被是为同一层次被同时加载
    * spring.config.additional-location设置
      * 如果只是想添加配置文件路径，而不是重新定义。可以设置spring.config.additional-location，其设置的路径将会被添加到配置文件路径
      * 添加路径下的配置将会覆盖之前默认路径下的配置
    * 可选路径
      * 可以将location设置为optional，如果该路径不存在，应用并不会抛出异常而是继续启动
      * 通配符路径无法用在classpath下
      * 通配符路径只能包含一个*，并且
        * 对于目录，以*/结尾
        * 对于文件，以*/filename结尾
  * spring.config.import配置
    * 可以通过import来导入外部配置文件，相当于将外部文件的配置项插入到当前配置文件的末尾
    * 被导入的配置文件将由于当前的配置文件
    * 在spring.config.import中可以指定多个外部配置文件，配置文件将会按指定的顺序导入，后导入的配置将会覆盖前面导入的配置
    * 可以通过指定[.yaml/.properties]来导入没有后缀名称导入配置
    ```shell
    spring.config.import=file:./config[.yaml]
    ```
  * 属性占位符
    * 通过${palceholder}可以用占位符来引用先前已经被定义的变量值
  * yaml格式
    * yaml无法被@PropertySource和@TestPropertySource导入
    * 可以通过YamlPropertiesFactoryBean导入yaml文件为Properties
    * 可以通过YamlMapFactoryBean来导入yaml文件
* ## 注入配置属性到spring bean中
  * 可以通过@ConfigurationProperties注解来为bean对象中的域填充配置信息，该注解的value值为对象在配置文件中的前缀
    * @ConfigurationProperties默认通过setter绑定，故而需要为绑定的域添加setter
  ```
  @ConfigurationProperties("waifu.kazusa")
  @Component
  class WaifuKazusa {
      private String name;
      private String gender;
      private String relationship;
  }
  ```
  * @ConfigruationProperties添加绑定后为绑定了环境变量的类注册bean对象到容器内中的方法
    * 通过@ConfigurationPropertiesScan注解来定义扫描路径
    * 通过@EnableConfigurationProperties来显式制定注册bean对象的属性类
  ```java
  // 指定想要注册的属性类
  @Configruation
  @EnableConfigurationProperties({PropertyBean1.class,...})
  class ConfigurationRegisterBean {
    // ...
  }

  // 通过@ConfigurationPropertiesScan来自动扫描
  @SpringBootApplication
  @ConfigurationPropertiesScan({"package-path-1",...})
  class SpringBootApplicationDemo {
    // ...
  }
  ```
  * 如果为@ConfigurationProperties指定@ConstructorBinding注解，可以指定构造器绑定
    * 如果为一个类指定了构造器绑定，那么该类中的嵌套成员（对象域）也会通过构造器绑定
    * 可以通过@DefaultValue来为配置文件中没有指定的域指定默认值
    * 通过@ConstructorBinding指定的类通常是不可变的，不向外提供setter，域赋值通过构造函数完成
    * @ConstructorBinding指定的类无法通过@Component等注解来标记为bean，而是要显示通过指定@ConfigurationPropertiesScan或是@EnableConfigurationProperties来实现
  * @ConfigurationProperties可以注解在@bean标签的方法上
  * Relaxed Bidning：
    * 通常，配置文件中的属性和bean类中的域名并不需要严格匹配，在bean类中成员变量名称通过‘firstSecond’形式定义，可以与配置文件中如下格式都匹配
      * first-second
      * first_second
      * FIRST_SECOND
      * firstSecond
    * 如果要向properties中添加map类型的值，可以用[]来包围key值
    ```shell
    my.map.[key1]=value1
    my.map.[key2]=value2
    ```
    * 向properties中添加list类型的值，同样可以用[]来包围index
    ```properties
    my.list[0]=value0
    my.list[1]=value1
    ```
  * Duration绑定
    * 默认情况下，不指定时间单位时单位是ms
    * 通过@DurationUnit指定时间单位
  * DataSize绑定
    * 默认情况下,DataSize的单位是byte
    * 可以通过@DataSizeUnit来显示指定单位
  * @Value和@ConfigurationProperties区别：
    * @Value只有当其value值是"first-second"形式时，才支持relaxed binding
    * @Value支持spel