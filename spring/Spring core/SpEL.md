# SpEL(Spring Expression Language)
- ## SpEL的用法
  - SpEL如何将表达式从字符串转化为计算后的值
  - 在转化过程中，在parseExpression方法执行时可能会抛出ParseException异常，在执行getValue方法时可能会抛出EvaluationException
  ```java
  ExpressionParser parser = new SpelExpressionParser();
  Expression exp = parser.parseExpression("'Hello World'"); 
  String message = (String) exp.getValue();
  ```
  - 在SpEL中获取String的字节数组
  ```java
  ExpressionParser parser=new SpelExpressionParser();
  Expression exp=parser.parseExpression("'Hello World'.bytes");
  byte[] bytes=(byte[])exp.getValue();
  ```
  - 在调用Expression类型的getValue方法时，可以不用进行强制类型转换，而是在getValue方法中传入一个Class参数，返回值将会被自动转换成Class对应的目标类型，当转换失败时会抛出EvaluationException
  ```java
  ExpressionParser parser=new SpelExpressionParser();
  Expression exp=parser.parseExpression("'Hello World'.bytes.length");
  Integer bytes=exp.getValue(Integer.class);
  ```
  - SpEL可以针对特定的对象，给出一个表达式并且在getValue方法中传入一个对象，那么表达式中的变量将会针对该对象中的特定属性
  ```java
  // 如下步骤会比较waifu对象的name属性是否为"touma"字符串
  ExpressionParser parser=new SpelExpressionParser();
  Expression exp=parser.parseExpression("name=='touma'");
  Boolean equals=exp.getValue(waifu,Boolean.class);
  ```
  - 可以为parser设置一个parserconfiguration，用于处理当列表或集合元素的index操作超过集合长度时的默认行为
  ```java
  class Demo {
    public List<String> list;
  }

  // Turn on:
  // - auto null reference initialization
  // - auto collection growing
  SpelParserConfiguration config = new SpelParserConfiguration(true, true);

  ExpressionParser parser = new SpelExpressionParser(config);

  Expression expression = parser.parseExpression("list[3]");

  Demo demo = new Demo();

  Object o = expression.getValue(demo);

  // demo.list will now be a real collection of 4 entries
  // Each entry is a new empty String
  ```
- ## SpEL在bean对象定义时的使用
  - 在使用@Value注解时，可以结合SpEL表达式进行使用，@Value注解可以运用在域变量、方法、方法和构造器的参数上。@Value会指定默认值
- ## SpEL对List、Map的支持
  - 可以通过{}来直接表示list
    ```java
    List numbers = (List) parser.parseExpression("{1,2,3,4}").getValue(context);

    List listOfLists = (List) parser.parseExpression("{{'a','b'},{'x','y'}}").getValue(context);
    ```
  - 可以通过{key:value}形式来直接表示map，空map用{:}来进行表示
    ```java
    // evaluates to a Java map containing the two entries
    Map inventorInfo = (Map) parser.parseExpression("{name:'Nikola',dob:'10-July-1856'}").getValue(context);

    Map mapOfMaps = (Map) parser.parseExpression("{name:{first:'Nikola',last:'Tesla'},dob:{day:10,month:'July',year:1856}}").getValue(context);
    ```
  - 可以通过new int[]{}的形式为SpEL指定数组
    ```java
    int[] numbers1 = (int[]) parser.parseExpression("new int[4]").getValue(context);

    // Array with initializer
    int[] numbers2 = (int[]) parser.parseExpression("new int[]{1,2,3}").getValue(context);

    // Multi dimensional array
    int[][] numbers3 = (int[][]) parser.parseExpression("new int[4][5]").getValue(context);
    ```
- ## SpEL支持的特殊操作符
  - instanceof
    ```java
    boolean falseValue = parser.parseExpression(
            "'xyz' instanceof T(Integer)").getValue(Boolean.class);
    ```
  - 正则表达式
    ```java
    boolean trueValue = parser.parseExpression(
        "'5.00' matches '^-?\\d+(\\.\\d{2})?$'").getValue(Boolean.class);
    ```
  - 类型操作符，获取类型的Class对象、调用静态方法
    ```java
    Class dateClass = parser.parseExpression("T(java.util.Date)").getValue(Class.class);

    Class stringClass = parser.parseExpression("T(String)").getValue(Class.class);

    boolean trueValue = parser.parseExpression(
            "T(java.math.RoundingMode).CEILING < T(java.math.RoundingMode).FLOOR")
            .getValue(Boolean.class);
    ```
  - new操作符：
    - 可以在SpEL表达式中通过new操作符来调用构造器，但是除了位于java.lang包中的类，对其他的类调用构造器时都必须指定类的全类名
    ```java
    Inventor einstein = p.parseExpression(
        "new org.spring.samples.spel.inventor.Inventor('Albert Einstein', 'German')")
        .getValue(Inventor.class);

    // create new Inventor instance within the add() method of List
    p.parseExpression(
            "Members.add(new org.spring.samples.spel.inventor.Inventor(
                'Albert Einstein', 'German'))").getValue(societyContext);
    ```