# mybatis框架
## 1. Mybatis中主要的类及其生命周期
### 1.1 SqlSessionFactoryBuilder
对于SqlSessionFactoryBuilder，其推荐的生命周期是创建之后立即销毁，不必被保留；其理想的作用域是方法的作用域。
> 可以将SqlSessionFactoryBuilder进行保留，并用其创建多个SqlSessionFactory实例。但是，为了保证XML解析后资源被正常的释放，应该在将其创建之后立刻丢弃
### 1.2 SqlSessionFactory
对于SqlSessionFactory实例，其理想的作用域和生命周期都应该是整个应用的执行期间。在应用运行期间，都会通过该工厂实例来获取SqlSession对象。
> SqlSessionFactory实例在创建完成之后，都不应该被修改或者是被重新创建。
### 1.3 SqlSession
对于SqlSession对象来说，其生命周期和作用域应该是方法级别或者是请求级别。SqlSession对象并不是线程安全的，无法在多线程环境之下被共享。因而，无法将其作为Servlet或是HttpSession的属性或是静态属性，否则会产生非预期的并发安全问题。  
推荐情况下，SqlSession应该在接受到http请求时被创建，并且在请求返回httpResponse时被销毁。  
并且，SqlSession必须要被保证正确的关闭。
```java
try(SqlSession sqlSession=sqlSessionFactory.openSession()) {
    // operations on sqlSession
}
```
### 1.4 Mapper实例
Mapper实例是通过SqlSession实例来获取的，故而，Mapper实例最大的生命周期和作用域和SqlSession。Mapper最推荐的生命周期和作用域是方法级别的，在方法中，获取Mapper并使用后即可丢弃，无需在方法外保存Mapper对象。
虽然Mapper也可以和SqlSession一样在request范围内有效，但是会发现保留太多Mapper级别的资源而不销毁会很快的产生失控。
Mapper实例和SqlSession实例不同，并不需要被显式的销毁。
