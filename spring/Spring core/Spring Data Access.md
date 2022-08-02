# Spring Data Access
- ## Spring事务
  - 本地事务和全局事务：
    - 全局事务：全局事务允许使用多个事务资源，应用服务器来对全局事务进行管理
    - 本地事务：本地事务无法管理多个事务资源
  - 本地事务和全局事务的优缺点
    - 全局事务的使用需要和服务器环境相绑定，降低了代码的重用性
    - 本地事务无法使用多个事务资源，无法通过JTA等框架来对多个事务资源进行管理，无法使用分布式事务
- ## 声明式事务
  - Spring中声明式事务是通过AOP来实现的
  - 在声明式事务中，可以为方法级的粒度指定事务行为
  - 声明式事务的回滚规则：
    - 在Spring声明式事务中，可以为事务指定回滚规则，即指定针对哪些异常，事务会自动执行回滚操作
    - 在默认情况下，只有抛出unchecked异常（通常为RuntimeException）时，声明式事务才会进行回滚
  - 声明式事务的实现细节：
    - 声明式事务通过aop代理实现，并且事务的advice是通过xml元数据配置来驱动的
    - aop和事务元数据联合产生了一个aop代理对象，并且该代理对象通过使用TransactionInterceptor和TransactionManager来实现事务
    - @Transactional通常和线程绑定的事务一起工作，线程绑定的事务由PlatformTransactionManager管理。@Transactional会将事务暴露给当前执行线程中所有的dao操作
  - 声明式事务的回滚：
    - 在Spring事务中推荐让事务回滚的方式是在事务执行的方法中抛出一个异常
    - Spring事务在默认情况下只会针对unchecked异常（RuntimeException）进行回滚，对于Error，Spring事务也会执行回滚操作
    - checked异常并不会导致事务的回滚操作，可以注册rollback rule来指定对特定的异常（包括checked异常）进行回滚操作
  - rollback rule：
    - 回滚规则（rollback rule）通常用来指定当一个异常被抛出时，是否为该异常执行事务的回滚操作
    - 在@Transactional注解中，可以指定rollbackFor/noRollbackFor、rollbackForClassName/noRollbackForClassName来指定为那些异常类执行回滚操作
    ```java
    @Transactional(rollbackFor={MyException.class})
    public void myOperation() {
      // ...
    }
    ```
- ## 基于注解的声明式事务
  - @Transactional既可以作用于类上，也可以作用于方法上。当作用于类上时，该声明类中所有的方法都会被声明是事务的，该类子类中所有的方法也都是事务的
  - @Transactional是可以继承的，被@Inherited元注解修饰。
  >@Inherited类是元注解，用来修饰注解类。如果一个注解类被@Inherited注解标识，那么在对class查询该注解类时，如果当前class没有声明该注解，将会在当前class的父类中查找该注解，依次递归。直到在父类中找到该注解或是到达继承结构的顶部（Object类）。@Inherited标注的注解仅能在类继承中有效，如注解被标注在接口上，那么将@Inherited标注的注解将不会被递归查询到。
  - 并且，class级别的@Transactional并不应用在从父类继承的方法上，即若一个类被@Transactional注解标注，并且该类从父类继承方法，那么该类从父类继承的方法并不会被看作是事务的，除非在该类中重新声明继承的方法。
  - 通过在Configuration类上注解@EnableTransactionManagement，配合@Transactional可以将一个bean对象声明是事务的
  - 当基于标准spring配置时，应该仅将@Transactional注解标注于public方法，当将@Transactional注解标注于非public方法时无效
  - 在Spring中，仅推荐将@Transactional注解应用于类上，不推荐将其应用在接口（接口方法）上。如果将其应用在接口上，那么该事务配置仅仅对基于接口的动态代理有效，对基于class的代理无效。
  - 当类级别和方法级别都设置了@Transactional注解时，方法级别的设置会优先被使用
- ## @Transactional注解的配置
  - 事务的传播： 默认情况下，@Transactional的propagation属性是PROPAGATION_REQUIRED
  - 事务的隔离级别： 默认情况下，@Transactional的isolation属性是ISOLATION_DEFAULT，使用数据库默认的隔离级别
  - readOnly： 默认情况下，@Transactional的readOnly属性是false，默认事务是可读写的
  - timeout： 默认况下下，@Transactional的超时属性取决于底层的事务系统，如果底层事务系统不支持timeout，则timeout属性为none
  - rollbackFor： 默认情况下，@Transactional会针对unchecked异常和Error进行回滚操作
  - transactionManager： 默认情况下，@Transactional注解会使用项目中默认的事务管理器（即bean name为transactionManager的事务管理器）。可以为@Transactional注解指定value属性或是transactionManager属性来指定想要采用的事务管理器的bean name或是qualifier
- ## Transaction Propagation
  - ### PROPAGATION_REQUIRED
    - 在Spring中，事务的传播行为默认是PROPAGATION_REQUIRED，默认情况下该选项会强制的要求一个物理事务
      - 如果当前作用域中不存在事务，那么会创建一个新的事务
      - 如果当前作用域的外层作用域已经存在事务，那么会加入到当前作用域的事务中去
    - 在Spring中，默认情况下，当嵌套事务加入到外层的事务中时，会忽略内层事务定义的隔离级别、timeout设置和读写标志等。
    > 如果想要对外层事务进行验证，可以手动将事务管理器的validateExistingTransaction属性设置为true。这样，当加入到一个隔离级别与内层事务完全不同的外层事务中时，该加入操作会被拒绝。在该设置下，如果read-write内层事务想要加入到外层的read-only事务中时，该加入操作也会被拒绝。
    - 在事务传播行为被设置为PROPAGATION_REQUIRED的情况下，会为每个被设置事务的方法创建一个逻辑的事务作用域。各个逻辑事务作用域之间都是相互独立的，在不同逻辑事务作用域之间都可以独立设置事务的rollback-only属性。但是，在PROPAGATION_REQUIRED的情况下，内层事务和外层事务都映射到同一个物理事务，内层事务加入到外层事务中，故而在内层逻辑事务中为物理事务设置rollback-only会切实影响到外层事务的提交。
    - 当事务传播行为被设置为PROPAGATION_REQUIRED时，如果内层事务设置了rollback-only标记，那么会导致外层物理事务的回滚。当外层事务尝试提交并失败回滚后，会抛出一个UnexceptedRollbackException异常，外层事务commit方法的调用者会接受到该UnexceptedRollbackException，代表内层发生了非预期的回滚操作
  - ### PROPAGATION_NEW
    - 相对于PROPAGATION_REQUIRED，PROPAGATION_NEW传播行为会一直使用独立的物理事务，而不会尝试区加入外部已经存在的物理事务。
    - 对于PROPAGATION_NEW,其内层事务和外层事务都可以独立的提交或回滚，内层事务的回滚并不会导致外层事务的回滚。
    - 将事务传播行为设置为PROPAGATION_NEW时，内层事务可以独立定义自己的隔离级别、timeout值、read-only属性，而不必继承外部事务的这些属性。在PROPAGATION_REQUIRED中，内部事务自定义这些属性将会被忽略，内部事务加入外部事务后会采用外部事务的设置。
  - ### PROPAGATION_NESTED
    - 和PROPAGATION_REQUIRED类似，PROPAGATION_NESTED同样也只有一个物理事务。但是其支持多个savepoint（存档点），该物理事务可以回滚到特定的存档点而非必须回滚整个事务。
    - 由于PROPAGATION_NESTED对存档点的支持，故而在PROPAGATION_NESTED条件下，可以进行部分回滚。内层事务的回滚操作并不会造成外部事务的回滚，内层事务回滚后外层事务仍然能够继续执行和提交。
    > 由于PROPAGATION_NESTED需要JDBC savepoint（存档点）的支持，故而该设置仅仅对JDBC事务资源有效。
