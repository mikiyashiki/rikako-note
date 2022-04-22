# Java Annotation
* ## Java注解格式：
  * @annotation_name(element_name1=value_1,element_name2=value_2...)
  * 注解可以没有element，如果一个注解没有element，那么该注解的括号可以被省略，例如@Override
  * 如果注解只有一个叫做value的元素，那么value的名称可以被省略，例如@SuppressWarnings("unchecked")
  * 对于同一个类，可以为其声明多个注解，例如
    ```java
    @RequestMapping("/hello")
    @ResponseBody
    String hello() {return "hello world";}
    ```
* ## 注解类型的定义：
  * 注解类型可以用如下方式来定义：
    ```java
    @interface ClassMessage {
        String author();
        String date();
        // 可以为注解中的element定义默认值
        int currentRevision() default 1;
        String[] reviewers();
    }
    ```
* ## jdk中预定义的java注解
  * @Deprecated：
    * 被该注解标明的类、方法、域会被标记为不推荐的，再使用这些方法时编译器会生成警告
    * 标记为@Deprecated的元素，再java doc中也应该使用@deprecated标记其为被抛弃的
  * @Override：
    * 标明该方法会覆盖父类中的同名方法
  * @SuppressWarnings(warnings)：
    * 抑制警告
    * 如果要抑制多个警告，可使用如下方法
        ```java
        @SuppressWarnings({"unchecked","deprecation"}
        )
        ```
  * @Target:
    * 该注解标注在注解上，指明注解应该应用于哪些位置
    * 在使用时，应该为@Target的value指定如下值：
      * ElementType.ANNOTATION_TYPE
      * ElementType.CONSTRUCTOR
      * ElementType.FILED
      * ElementType.LOCAL_VARIABLE
      * ElementType.METHOD
      * ElementType.PACKAGE
      * ElementType.Parameter
      * ElementType.TYPE (可用于类型、接口（包括注解）、枚举）
  * @Inherited：
    * 该注解标注在注解上，如果一个注解被标明为继承的，那么该注解标明的父类，其子类即使没有被注解标明，在查询该子类的注解类型时也会返回父类的注解
  * @Repeatable：
    * 标明一个注解可以多次重复的应用到同一个类、方法上
* ## 可重复注解
  * 如果想要将一个注解标名为可重复的，需要有如下两个步骤
    * 为注解标明@Repatable，并且在括号中指明容器注解
    * 在指定的容器注解中添加可重复注解的数组返回类型
  * 样例
    ```java
    @Repatable(Timers.class)
    @interface Timer {
        String time() default "0:00";
    }

    @interface Timers {
        Timer[] value();
    }
    ```