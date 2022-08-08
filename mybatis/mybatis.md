# mybatis框架
## Mybatis中主要的类及其生命周期
### SqlSessionFactoryBuilder
对于SqlSessionFactoryBuilder，其推荐的生命周期是创建之后立即销毁，不必被保留；其理想的作用域是方法的作用域。
> 可以将SqlSessionFactoryBuilder进行保留，并用其创建多个SqlSessionFactory实例。但是，为了保证XML解析后资源被正常的释放，应该在将其创建之后立刻丢弃
### SqlSessionFactory
对于SqlSessionFactory实例，其理想的作用域和生命周期都应该是整个应用的执行期间。在应用运行期间，都会通过该工厂实例来获取SqlSession对象。
> SqlSessionFactory实例在创建完成之后，都不应该被修改或者是被重新创建。
### SqlSession
对于SqlSession对象来说，其生命周期和作用域应该是方法级别或者是请求级别。SqlSession对象并不是线程安全的，无法在多线程环境之下被共享。因而，无法将其作为Servlet或是HttpSession的属性或是静态属性，否则会产生非预期的并发安全问题。  
推荐情况下，SqlSession应该在接受到http请求时被创建，并且在请求返回httpResponse时被销毁。  
并且，SqlSession必须要被保证正确的关闭。
```java
try(SqlSession sqlSession=sqlSessionFactory.openSession()) {
    // operations on sqlSession
}
```
### Mapper实例
Mapper实例是通过SqlSession实例来获取的，故而，Mapper实例最大的生命周期和作用域和SqlSession。Mapper最推荐的生命周期和作用域是方法级别的，在方法中，获取Mapper并使用后即可丢弃，无需在方法外保存Mapper对象。
虽然Mapper也可以和SqlSession一样在request范围内有效，但是会发现保留太多Mapper级别的资源而不销毁会很快的产生失控。
Mapper实例和SqlSession实例不同，并不需要被显式的销毁。
## Mybatis中的配置文件
Mybatis中配置文件主要有properties和setting两部分。
### properties
对于那些可外部化、可替换的属性，可以通过java的.properties文件进行配置，或者通过&lt;properties&gt;标签中配置的子属性来进行传入，具体如下：
```xml
<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
</properties>
```
此时，在整个xml配置文件中，都可以使用${username}和${password}变量或者从config.properties文件中导入的变量。  
在向SqlSessionFactoryBuilder.build方法中传入propertis属性时，优先级如下：
```java
/*
*   首先，会读入xml配置文件中properties标签中的变量内容
*   然后，会读入properties标签resource或者url属性指定的properties属性
*   最后，会读入作为参数传入的props属性
*   按上述顺序读入属性，后读入的属性会覆盖前面读入的同名属性
*/

SqlSessionFactory factory =
  sqlSessionFactoryBuilder.build(reader, props);

// ... or ...

SqlSessionFactory factory =
  new SqlSessionFactoryBuilder.build(reader, environment, props);
```
### settings
配置settings属性可以修改mybatis运行时的行为。
### typeAliases
typeAliases标签中可以定义全限定类名的别名，在定义typeAliases之后，xml文件中可以使用别名来代替全限定类名
```xml
<typeAliases>
    <typeAlias alias="author" type="domain.blog.Author">
</typeAliases>
```
如下是java常用类型的别名：
<table>
<tr>
    <th>Aias</th>
    <th>Mapped Type</th>
</tr>
<tr>
    <td>_byte</td>
    <td>byte</td>
</tr>
<tr>
    <td>_int</td>
    <td>int</td>
</tr>
<tr>
    <td>byte</td>
    <td>Byte</td>
</tr>
<tr>
    <td>string</td>
    <td>String</td>
</tr>
<tr>
    <td>long</td>
    <td>Long</td>
</table>

### typeHandler
每当mybatis向PreparedStatement设置值或者从ResultSet中获取值时，typeHandler都会使用合适的方法来将ResultSet中的属性传递给java类对象或者从Java类对象中获取属性并将其传递给PreparedStatement的sql语句。     
mybatis内置了很多typeHandler，用户可以实现自己的TypeHandler，通过实现TypeHandler接口或者继承BaseTypeHandler类。     
可以通过为typeHandler设置javaType和jdbcType属性来指定typeHandler对应的java类型和jdbc类型。      
当决定在ResultMap中使用typeHandler时，java类型已知但是jdbc类型未知。因此ResultMap采用javaType=[javaType],jdbcType=null的组合来选择typeHandler。为了使typeHandler在ResultMap中能够被使用，需要在@MappedJdbcTypes加入includeNullJdbcType=true。
> 如果对于某一个java类型，只有一个typeHandler被注册，那么该typeHandler会自动被使用，即使includeNullJdbcType=true并没有被设置。
### EnumTypeHandler
对于枚举类型的处理，可以使用EnumTypeHandler和EnumOridinalTypeHandler。
> 默认情况下，mybatis会使用EnumTypeHandler来处理枚举类型，会将枚举类型的转化成其名字的字符串。      
> 可以强制将EnumOridinalTypeHandler指定给typeHandler属性，此时会将枚举类型转换成其对应的数字值。
可以在不同的地方使用不同的枚举类型处理器，将其映射为整数类型或字符串类型。只需在mapper使用非默认类型时显示指定即可。
```xml
<mapper namespace="org.apache.ibatis.submitted.rounding.Mapper">
    <resultMap type="org.apache.ibatis.submitted.rounding.User" id="usermap">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <result column="funkyNumber" property="funkyNumber"/>
        <result column="roundingMode" property="roundingMode"/>
    </resultMap>

    <select id="getUser" resultMap="usermap">
        select * from users
    </select>
    <insert id="insert">
        insert into users (id, name, funkyNumber, roundingMode) values (
            #{id}, #{name}, #{funkyNumber}, #{roundingMode}
        )
    </insert>

    <resultMap type="org.apache.ibatis.submitted.rounding.User" id="usermap2">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <result column="funkyNumber" property="funkyNumber"/>
        <result column="roundingMode" property="roundingMode"
         typeHandler="org.apache.ibatis.type.EnumTypeHandler"/>
    </resultMap>
    <select id="getUser2" resultMap="usermap2">
        select * from users2
    </select>
    <insert id="insert2">
        insert into users2 (id, name, funkyNumber, roundingMode) values (
            #{id}, #{name}, #{funkyNumber}, #{roundingMode, typeHandler=org.apache.ibatis.type.EnumTypeHandler}
        )
    </insert>

</mapper>
```
### ObjectFactory
mybatis会使用ObjectFactory来创建返回对象。ObjectFactory仅仅会调用默认构造函数或者参数化的构造函数来创建返回对象。
### environment
Mybatis可以设置复数个环境，但是对于每一个SqlSessionFactory对象，只能够选择一个环境。为了指定构建的环境，可以将其以参数的方式传递给SqlSessionFactoryBuilder.build。如果environment参数被省略，那么默认的环境将会被采用。     
环境的配置格式如下：
```xml
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC">
      <property name="..." value="..."/>
    </transactionManager>
    <dataSource type="POOLED">
      <property name="driver" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
    </dataSource>
  </environment>
</environments>
```
#### transactionManager
当在spring中使用mybatis框架时，无需在environment中指定transactionManager，Spring会用自己的transactionManager来覆盖environment中的定义。
#### datasource
在datasource标签中，通过标准jdbc接口来定义。datasource可以分为如下三个类型：POOLED|UNPOOLED|JNDI
- UNPOOLED：对于每次请求，都会都会打开一个新的连接，并且在请求结束之后关闭该链接。
- POOLED：会在连接池中池化链接，可以避免每次请求都会创建链接和身份认证的开销。在高并发场景下，池化数据源的响应时间要由于未池化的数据源。
- JNDI：JNDI数据源通常和特定容器一起使用，例如EJB服务器，该类服务器会集中或者在外部配置该数据源。
#### mappers
通过mappers属性可以向mybatis注册mapper.xml文件。
```xml
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```
# Mybatis Mapper
## select
通常来说，数据库操作中select的频率远远大于update、insert、delete的频率。    
select元素有如下属性：
- id： 命名空间中唯一的标识符，用来引用该sql声明
- parameterType： 传递参数的全限定类名或者alias，该属性是可选的，mybatis会根据根据实际传递的参数来计算应该使用的typeHandler
- resultType：方法预期返回类型的全类名
> 如果方法的返回类型是集合类型，那么resultType为集合中包含元素的类型，而不是集合本身的类型。
- resultMap：对于外部resultMap的命名引用
- flushCache：如果该属性设置为true，在该Statement被调用时，会导致本地缓存和二级缓存都被刷新。默认情况下，flushCache被设置为false。
- useCache：如果该属性设置为true，会导致该Statement的查询结果被缓存在二级缓存中。默认情况下，useCache属性为true。
> mybatis缓存结构：
> - 一级缓存： mybatis一级缓存是针对SqlSession的缓存，如果SqlSession查询数据会将第一次查询到的数据存放在Map缓冲区中。如果后续SqlSession没有对数据进行添加、修改、删除等操作，那么缓存将会失效。  
>   默认情况下，若未开启事务，那么每条语句都可以看作一个事务，多条select语句之间都会刷新缓  存，一级缓存不会起作用。
> - 二级缓存：mybatis中二级缓存是针对Mapper级别的。二级缓存针对mapper.xml中所有Statement的作用域。二级缓存可以在多个SqlSession之间进行共享。
>   在执行sql查询之前，如果执行了插入或者删除操作，那么二级缓存会失效。
>   二级缓存需要手动配置，settings标签中cacheEnabled默认是true，只需要在需开启缓存的mapper.xml中加入cache标签即可开启二级缓存
- timeout：drvier会等待数据库返回查询结果的超时时间，如果超过该timeout上线，那么会抛出异常。默认情况下，该timeout是未设置的，取决于底层的driver
## update、insert、delete
修改语句通常有如下属性：
- id
- parameterType
- flushCache
- timeout
- statementType: STATEMENT/PREPARED/CALLABLE其中之一，默认值为PREPARED
> statementType通常可以设置为如下值之一：
> - STATEMENT:mybatis会使用Statement
> - PREPARED:mybatis会使用PreparedStatement
> - CALLABLE:mybatis会使用CallableStatement
- useGeneratedKeys：当useGeneratedKeys被设置为true时，会调用JDBC中的getGeneratedKeys方法从数据库内部获取自动生成的key。默认情况下该值为false
> 该属性仅针对insert和update语句
- keyProperty：后跟随字段或者字段列表，对于想要数据库内部自动生成的字段，mybatis会通过getGeneratedKeys方法返回的值来设置该字段的内容；对于想要自定义字段自动生成（如随机生成）的字段，mybatis会通过insert元素selectKey子元素中的值来设置
- 