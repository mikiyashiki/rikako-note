# Spring Core IOC
- ## IOC容器和bean简介
  - IOC简介：
    - IOC（控制反转）也被称之为依赖注入（DI），对象通过构造函数参数、工厂方法参数、或者在构造后通过setter来设置属性来定义依赖。在对象被创建时，IOC容器会将依赖注入到bean对象中，
  - IOC容器：
    - IOC容器接口：
      - BeanFactory：BeanFactory是一个接口，提供了高级配置功能来管理任何类型的对象
      - ApplicationContext：ApplicationContext是BeanFactory的一个子接口，在BeanFactory的基础上，其添加了一些更为特殊的特性。
    - IOC容器职责
      - IOC容器负责来初始化、配置、组装bean对象
- ## 基于注解的Spring容器配置
  - @Required
    - @Required应用于bean对象属性的setter方法，表示该属性在配置时必须被填充，通过依赖注入或是用xml定义bean时显式指定值
    - 该注解当前已经被弃用
  - @Autowired
    - 通过在构造函数上标明@Autowired来对方法参数进行注入
    - 当在构造函数上标记@Autowired时，如果当前类中只有一个构造函数，那么@Autowired注解可以被省略；如果当前类有多个构造函数，那么应该在某个构造函数上指明@Autowired注解
    ```java
    @Component
    class Shiromiya {
        private JdbcTemplate jdbcTemplate;

        @Autowired
        public Shiromiya(JdbcTemplate jdbcTemplate) {
            this.jdbcTemplate=jdbcTemplate;
        }
    }
    ```
    - 同样，可以在setter上应用@Component
    ```java
    @Component
    class Shiromiya {
        private JdbcTemplate jdbcTemplate;

        @Autowired
        public setJdbcTemplate(JdbcTemplate jdbcTemplate) {
            this.jdbcTemplate=jdbcTemplate;
        }
    }
    ```
    - 将@Autowired应用于字段
    ```java
    @Component
    class Shiromiya {
        @Autowired
        private JdbcTemplate jdbcTemplate;
    }
    ```
    - 通过@Autowired获取相同类型的所有bean对象
    ```java
    @Component
    class Shiromiya {
        private String[] waifus;
        /*
        * 这里，同样也支持Collections类型
        * 例如 List<String>
        */ 

        @Autowired
        public Shiromiya(String[] waifus) {
            this.waifus=waifus;
        }
    }

    // 同样，可以通过Map类型来获取所有相同类型bean对象的name和value
    // key：对应bean对象的name
    // value：对应该bean对象的值
    @Component
    class Shiromiya {
        private Map<String,String> waifus;

        @Autowired
        public void setWaifus(Map<String,String> waifus) {
            this.waifus=waifus;
        }
    }
    ```
    - 在@Autowired标记在构造函数上时，即使required为true，在参数为多bean类型时，即使没有匹配的bean，该属性会赋值为{}（空集合）而不是抛出异常
    - @Autowired作用与构造函数的规则
      - 当required属性为其默认值true时，在bean类型中只有一个构造函数可以用@Autowired标注
      - 如果bean类型中有多个构造函数标注了@Autowired注解，那么那么他们都必须将required属性设置为false，并且所有标注了@Autowired属性的构造函数都会被视为依赖注入的候选构造函数
        - 如果有多个候选的构造函数，那么在IOC容器中可以满足的匹配bean最多的构造函数将会被选中
        - 如果没有候选函数被选中，那么其会采用默认构造函数，如无默认构造函数，则抛出异常
      - 如果bean有多个构造函数，并且所有构造函数都没有标明@Autowired注解，那么会采用默认构造函数，如果默认构造函数不存在，抛出异常
      - 如果bean类型只有一个构造函数，那么该构造函数会被用来进行依赖注入，即使该构造函数没有标注@Autowired注解
    - 除了使用@Autowired的required属性，还可以使用@Nullable注解来标注可为空
    ```java
    @Component
    public class Shiromiya {
        @Autowired
        @Nullable
        private int num;
    }
    ```
  - @Primary
    - @Autowired注解是通过类型注入，如果相同类型存在多个bean时，可以通过@Primary注解来表明一个primary bean
    ```java
    @Configuration
    public class BeanConfiguration {
        @Bean
        @Primary
        public String name_1() {
            return "kazusa";
        }

        @Bean
        public String name_2() {
            return "ogiso";
        }
    }
    /*
     * 此时，若通过@Autowired注入String类型，“kazusa”将会是默认值
     */ 
    ```
  - @Qualifier
    - 可以通过@Qualifier来指定bean的name导入特定bean，并且可以为bean指定默认的qualifier
    ```java
    @Component
    public class Shiromiya {
        @Autowired
        @Qualifier("name_2")
        private String n_2;

        private String n_1;

        @Autowired
        public Shiromiya(@Qualifier("name_1") String n) {
            this.n_1=n;
        }
    }
    ```
    - bean对象的qualifier并不需要唯一，可以为不同的bean对象赋值相同的qualifier，并且在注入bean集合的时候根据qualifier过滤
    ```java
    @Configuration
    @Qualifier("config")
    class BeanConfiguration {
        @Bean
        @Qualifier("name")
        public String name_1() {
            return "kazusa";
        }

        @Bean
        @Qualifier("name")
        public String name_2() {
            return "ogiso";
        }

        @Bean
        @Qualifier("not-name")
        public String not_name_1() {
            return "fuck";
        }
    }

    @Component
    public class Person {
        /* 此nameList属性会注入qualifier为name的所有bean
         * 在此处为"kazusa"和"ogiso" 
         */ 
        @Autowired
        @Qualifier("name")
        Map<String,String> nameList;

        @Autowired
        @Qualifier("config")
        BeanConfiguration conf;

        @Override
        public String toString() {
            return "Person{" +
                    "nameList=" + nameList +
                    ", conf=" + conf +
                    '}';
        }
    }
    ```
    - 作为一种回退机制，当bean的qualifier未被定义时，bean的name属性将会被作为其qualifier，autowired时会根据@Qualifier注解中指定的值匹配具有相同name的bean对象
    - 若想根据bean的name进行匹配，无需@Qualifier注解，只需要将注入点的name(filed的变量名，标注为@Autowired函数的形参名)和bean的name进行比较，如果相同则匹配成功，否则匹配失败
    - @Autowired同样支持自身引用的注入，但是自身引用的注入只能作为一种fallback机制。如果当前IOC容器中存在其他的同类型对象，那么其他对象会被优先注入，对象自己并不会参与候选的对象注入。但是，如果IOC中并不存在其他同类型对象，那么自身对象将会被作为引用注入。
  - @Resource
    - @Resource标签类似于@Autowired标签，但是@Resource具有一个name属性用来匹配bean对象的name属性
    - @Resource标签首先会对具有相同name的bean对象，如果没有匹配到具有相同name的bean对象，才会fallback到类型匹配