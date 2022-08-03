# POJO
## POJO定义
POJO（Plain Old Java Object）是一种直接的类型，POJO并不包含对任何框架的引用。
> 对于POJO类型，该类属性和方法的定义并没有特定的约束和限制
## Java Bean命名约束
由于对POJO本身并没有对POJO类属性和方法的定义强制指定命名约束，因而许多框默认支持Java Bean命名约束。
> ### Java Bean命名约束  
> 在Java Bean命名约束中，为POJO类属性和方法的命名指定了如下规则：
> 1. 属性的访问权限都被设置为private，属性通过getter和setter向外暴露
> 2. 对于方法的命名，getter和setter遵循getXXX/setXXX的命名规范（对于boolean属性的getter，可以使用isXXX形式
> 3. Java Bean命名规范要求Java Bean对象需要提供无参构造方法
> 4. 实现Serializable接口，能够将对象以二进制的格式进行存储
## 其他命名规范
由于Java Bean命名规范中有些规则强制对Java Bean的命名进行限制可能会带来弊端，故而如今许多框架在接受Java Bean命名规范之余，仍然支持其他的POJO命名规范
> 如在Spring中，通过@Component注解注册Bean对象时，被@Component注解的类并不一定要实现Serializable接口，也不一定要拥有无参构造方法。
