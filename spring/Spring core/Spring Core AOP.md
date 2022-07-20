# Spring AOP
- ## Spring AOP的核心概念
  - Aspect：切面，一个模块化的考虑
  - Joint Point：连接点，程序执行时的一个时间点，通常是方法的执行
  - Advice：当切面在一个切入点执行多做时，执行的动作被称之为Advice，Advice有不同的类型：before、after、around
  - Pointcut：切入点，advice通常运行在满足pointcut的join point上，pointcut表达式与join point相关联，Spring中默认使用AspectJ切入点表达式
  - Introduction：在类中声明新的方法、域变量甚至是接口实现
  - linking：将应用类型或对象和切面链接起来
- ## Spring AOP的类型
  - before：在连接点之前运行，但是无法阻止后续连接点的执行
  - after returning：在连接点正常返回之后进行
  - after throwing：在链接点抛出异常正常退出之后进行
  - after finally：上两种的结合，不管连接点是正常退出还是抛出异常退出，都会在其之后执行
  - around：around可以自定义连接点之前和之后的执行内容，其也能够选择时候执行连接点的方法
- ## Spring AOP的特点
  - 区别于AspectJ AOP框架，Spring AOP框架是基于代理来实现的
  - 对于实现了接口的类，Spring AOP通常是通过JDK动态代理来实现的，对于没有实现接口的类，Spring AOP是通过cglib来实现的
  - 可以强制Spring AOP使用cglib，在如下场景：
    - 如果想要advise类中方法，而该方法没有在接口中定义
    - 如果想要将代理对象传递给一个具有特定类型的方法作为参数
- ## Spring AOP的AspectJ注解支持
  - Spring AOP支持AspectJ注解，Spring AOP可以解释和AspectJ 5相同的注解，通过使用AspectJ提供的包来进行切入点解析和匹配
  - 但是，即使使用了AspectJ注解，AOP在运行时仍然是纯粹的Spring AOP，项目不需要引入AspectJ的编译器和weaver
  - Spring AOP对AspectJ注解支持的开启
    - 通过@EnableAspectJAutoProxy注解，会自动的为满足切入点匹配的连接点bean对象创建移动代理对象
    ```java
    @Configuration
    @EnableAspectJAutoProxy
    class AspectJConfiguration {
        // ...
    }
    ```
- ## 声明Spring AOP切面
  - 在容器中，任何bean对象，如其类型具有@AspectJ注解，将会被自动探知到并且用来配置spring aop
  - 在Spring AOP中，aspect其自身是无法作为其他aspect的目标对象的。被标记为@Aspect的类，不仅标明其为aspect，并且将其从自动代理中排除
  - 如果为某个bean对象配置了切面，那么在后续创建该bean对象时，实际上是创建该bean对象的代理对象
  ```java
  @Component // 将该类型声明为bean对象
  @Aspect // 声明切面
  public class ProxyAspect {

  }
  ```
- ## 声明Spring AOP切入点
  - 由于Spring AOP仅仅支持方法的连接点，故而可以将切入点看做对bean对象方法的匹配
  - Join Point expression的种类：
    - execution：匹配目标方法的执行，可以在括号中接收一个函数签名，包含返回类型、函数名和函数参数类型
    ```java
    // 被@JointPoint注解标注的方法必须具有void的返回类型
    @JoinPoint("execution(* Point.*(..))")
    void methodInjected() {

    }
    ```
    - within:匹配声明在某一特定类中的方法
    ```java
    @JoinPoint("within(Point)")
    ```
    - this：匹配生成的代理对象为该类型的一个实例
    - target：匹配目标对象为该类型的一个实例
    - args：匹配特定参数
    - @args：传递参数的类型具有指定的注解
    - @target：运行时该对象的类具有指定的注解
    - @within：运行时执行的方法，其方法定义在具有指定注解的类中（可以是继承父类的方法，父类指定了注解
    - @annotation：执行的方法具有指定注解
  - Spring AOP同样支持将JoinPoint匹配为具有特定name的Spring bean对象
    ```java
    @JoinPoint("bean(nameA) || bean(nameB))")
    ```
- ## Spring AOP中的Advice
  - Advice和Pointcut Expresion相关联，主要可以分为before、after、around等种类
  - Before：
  ```java
  @Before("execution(* Point.*(..))")
  public void doSomething() {

  }
  ```
  - AfterReturning:
  ```java
  // AfterReturning支持获取切入点执行后返回的值
  @AfterReturning(
    pointcut="execution(* Point.*(..))",
    returning="retVal")
  public void doSomething(int retVal) {

  }
  ```
  - AfterThrowing:
  ```java
  @AfterThrowing(
    pointcut="execution(* Point.*())",
    throwing="ex"
    )
  public void doSomething(Throwable ex) {

  }
  ```
  - After:After不管是切入点正常返回还是抛出异常，都会执行，类似于finally
  ```java
  @After("execution(* Point.*())")
  public void doSomething() {

  }
  ```
  - Around:其方法必须会一个Oject类型的返回值，并且方法的第一个参数类型是ProceedingJoinPoint
  ```java
  @Around("execution(* Point.*())")
  public Object doSomething(ProceedingJoinPoint pjp) {
    return isCacheExisted()?returnFromCache():pjp.proceed();
  }
  ```
- ## Spring AOP中Advice方法对JoinPoint的访问
  - 任何advice方法，都可以声明声明其第一个参数为JoinPoint类型。@Around标注的adivce方法其第一个参数的类型必须为ProceedingJoinPoint类型，该类型为JoinPoint的子类型
  - JoinPoint接口具有如下方法：
    - getArgs：返回方法参数
    - getThis：返回代理对象
    - getTarget：返回目标对象
    - getSignature：返回函数的签名
    - toString：返回该advice方法的描述信息
- ## Advice方法通过参数来获取传递给底层方法的参数
  - 在pointcut表达式的args中，如果用advice方法中的参数名来代替参数类型，那么该类型的参数值会被传递给该参数
  ```java
  @Before("execution(* Point.*(..) && args(position,..))")
  public void adviceMethod(Position position) {

  }
  ```
  - 或者，可以通过如下方式，先通过一个Pointcut获取参数，在在另一个方法中获取named pointcut已获取的参数
  ```java
  // 此时，adviceMethodTwo同样能够获取Position参数
  @Pointcut("execution(* Point.*(..)) && args(position,..)")
  public void adviceMethodOne(Position position) {

  }

  @Before("adviceMethodOne(position)")
  public void adviceMethodTwo(Position position) {

  }
  ```
  - Spring AOP可以通过如下方式来约束泛型的参数
  ```java
  @Before("execution(* GenericsInterface+.method(*) && args(param))")
  public void adviceMethod(DesiredType param) {

  }
  ```
- ## 通过Spring AOP对参数进行预处理
  ```java
  @Around("execution(* Point.area(*) && args(width,height))")
  public double caculateInCM(ProceedingJoinPoint jp,double width,double height) {
    width*=100;
    height*=100;
    return jp.proceed(width,height);
  }
  ```
- ## Spring AOP中多个advice对应到同一个Pointcut
  - 如果多个advice都具有相同的pointcut，那么多个advice之间的执行顺序是未定义的。可以为Aspect类实现Ordered接口，或者添加@Order标记来定义该advice的执行优先级，那么具有具有较小order值的方法将会优先被执行
- ## Spring AOP Introduction
  - 在Spring AOP中，可以通过Introduction来声明一个对象继承了某接口，并且为被代理的对象提供被继承接口的实现
  - 可以通过@DeclareParent注解为指定对象添加接口并且指明该接口默认的实现类，完成后可以直接将生成的代理对象复制给接口变量
  ```java
  @Aspect
  public class MyAspect {
    @DeclareParent(value="cc.rikakonatsumi.interfaces.*+",defaultImpl=DefaultImpl.class)
    private static MyInterface myInterface;

    // 之后，可以直接通过this(ref)在pointcut表达式中获取服务对象，也可以通过getBean方法获取容器中的对象
  }
  ```
- ## @RestControllerAdvice的使用
  - @RestControllerAdvice是@Componnent注解的一个特例，@RestControllerAdivce注解的组成包含@Component
  - @RestControllerAdivce组合了@ControllerAdvice和@ResponseBody两个注解
  - 通常，@RestControllerAdvice用作为spring mvc的所有方法做ExceptionHandler


