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
```java
/**
  * 该方法会覆盖默认的针对String类型和varchar类型的typeHandler
**/
// ExampleTypeHandler.java
@MappedJdbcTypes(JdbcType.VARCHAR)
public class ExampleTypeHandler extends BaseTypeHandler<String> {

  @Override
  public void setNonNullParameter(PreparedStatement ps, int i,
    String parameter, JdbcType jdbcType) throws SQLException {
    ps.setString(i, parameter);
  }

  @Override
  public String getNullableResult(ResultSet rs, String columnName)
    throws SQLException {
    return rs.getString(columnName);
  }

  @Override
  public String getNullableResult(ResultSet rs, int columnIndex)
    throws SQLException {
    return rs.getString(columnIndex);
  }

  @Override
  public String getNullableResult(CallableStatement cs, int columnIndex)
    throws SQLException {
    return cs.getString(columnIndex);
  }
}
```
```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <typeHandler handler="org.mybatis.example.ExampleTypeHandler"/>
</typeHandlers>
```
mybatis会通过泛型参数来获知其typeHandler想要处理的javaType，但是可以通过两种方法来覆盖该种行为：
- 在typeHandler元素中添加javaType属性（例如，javaType="String")
- 通过@MappedTypes注解指定该typeHandler想要对应的javaType列表（如果javaType属性在typeHandler元素中被指定，那么该注解内容将会被忽略）

同样的，jdbcType也可以通过两种方式来指定：
- 在typeHandler元素中添加jdbcType属性（例如，jdbcType="VARCHAR")
- 为typeHandler类添加@MappedJdbcTypes注解，并且在jdbcType属性被指定时该注解被忽略
      
当决定在ResultMap中使用typeHandler时，java类型已知但是jdbc类型未知。因此ResultMap采用javaType=[javaType],jdbcType=null的组合来选择typeHandler。为了使typeHandler在ResultMap中能够被使用，需要在@MappedJdbcTypes加入includeNullJdbcType=true。
> 如果对于某一个java类型，只有一个typeHandler被注册，那么该typeHandler会自动被使用，即使includeNullJdbcType=true并没有被设置。
### EnumTypeHandler
对于枚举类型的处理，可以使用EnumTypeHandler和EnumOridinalTypeHandler。
> 默认情况下，mybatis会使用EnumTypeHandler来处理枚举类型，会将枚举类型的转化成其名字的字符串。      
> 可以强制将EnumOridinalTypeHandler指定给sql语句的typeHandler属性，此时会将枚举类型转换成其对应的数字值。
```xml
<!-- 将枚举类型通过数字存储 -->
<!-- mybatis-config.xml -->
<typeHandlers>
  <typeHandler handler="org.apache.ibatis.type.EnumOrdinalTypeHandler"
    javaType="java.math.RoundingMode"/>
</typeHandlers>
```
可以在不同的地方使用不同的枚举类型处理器，将其映射为整数类型或字符串类型。只需在mapper使用非默认类型时显示指定即可。
```xml
<mapper namespace="org.apache.ibatis.submitted.rounding.Mapper">
    <resultMap type="org.apache.ibatis.submitted.rounding.User" id="usermap">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <result column="funkyNumber" property="funkyNumber"/>
        <!-- 默认情况下，在指定完typeHandler为EnumOrdinalTypeHandler后，其roundingMode被映射为数字 -->
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

    <!-- 为resultMap显示指定column的typeHandler为EnumTypeHandler -->
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
    <!-- insert语句中在#{}中显式指定typeHandler -->
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
<!-- 设置默认环境为development -->
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
> 对于resultType和resultMap，应该只使用其中的一个，同一条select语句中不应该既包含resultType又包含resultMap
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
```xml
<!-- 使用mysql数据库自动生成的key（如auto_increment) -->
<insert id="insertAuthor" useGeneratedKeys="true"
    keyProperty="id">
  insert into Author (username,password,email,bio)
  values (#{username},#{password},#{email},#{bio})
</insert>


<!-- 通过自定义方式生成的主键来填充id -->
<insert id="insertAuthor">
  <selectKey keyProperty="id" resultType="int" order="BEFORE">
    select CAST(RANDOM()*1000000 as INTEGER) a from SYSIBM.SYSDUMMY1
  </selectKey>
  insert into Author
    (id, username, password, email,bio, favourite_section)
  values
    (#{id}, #{username}, #{password}, #{email}, #{bio}, #{favouriteSection,jdbcType=VARCHAR})
</insert>
```
### selectKey
对于selectKey子元素，其通常具有如下属性：
- keyProperty：该属性指定selectKey生成的结果应该被设置到insert语句的哪个地方
- resultType：selectKey语句执行结果的返回类型
- order：可以是“BEFORE"或者”AFTER"，如果被设置为“BEFORE"，那么会先执行selectKey，然后将产生结果设置到keyProperty，最后才会执行insert语句

## sql
sql标签通常被用来定义一些可以复用的sql片段，sql片段可以接受一个参数。  
sql标签定义的sql片段可以被include标签进行引用，并且include标签的refid属性对应sql标签的id属性。
> 如果一个sql片段嵌套了另一个sql片段，那么在sql语句include外层sql片段时，可以同时为内层和外层sql片段的变量进行赋值操作
```xml
<sql id="sometable">
  ${prefix}Table
</sql>

<sql id="someinclude">
  from
    <include refid="${include_target}"/>
</sql>

<select id="select" resultType="map">
  select
    field1, field2, field3
  <include refid="someinclude">
    <property name="prefix" value="Some"/>
    <property name="include_target" value="sometable"/>
  </include>
</select>
```
## parameter
- 简单的参数映射
```xml
<!-- 在该简单映射中，对于基本数据类型或者如String、Integer等没有属性的简单数据类型，会用该类数据的值完全替换参数的值 -->
<select id="selectUsers" resultType="User">
  select id, username, password
  from users
  where id = #{id}
</select>
```
- 向参数传递复杂对象，例如User类型对象，那么将会从对象中获取id、username、password等属性并传递给参数
```xml
<insert id="insertUser" parameterType="User">
  insert into users (id, username, password)
  values (#{id}, #{username}, #{password})
</insert>
```
## mybatis ${}
对于mybatis，可以通过#{}来设置PreparedStatement的占位参数，但是，当想要动态设置sql statement中的元数据（如表名称、字段名称）时，可以通过${}来插入一个未修改的字符串进行拼串。
```java
// 未使用${}
@Select("select * from user where id = #{id}")
User findById(@Param("id") long id);

@Select("select * from user where name = #{name}")
User findByName(@Param("name") String name);

@Select("select * from user where email = #{email}")
User findByEmail(@Param("email") String email);

// 使用${}之后
@Select("select * from user where ${column} = #{value}")
User findByColumn(@Param("column") String column, @Param("value") String value);
```
## resultMap
- 对于简单的statement，其映射不需要用到resultMap
```xml
<!-- 返回集中所有的column会自动映射到HashMap中的key -->
<select id="selectUsers" resultType="map">
  select id, username, hashedPassword
  from some_table
  where id = #{id}
</select>
```
- 对于POJO，当select的返回列名和POJO类的属性名相同时，会自动生成resultMap来将column和POJO对象属性关联到一起
```xml
<select id="selectUsers" resultType="com.someapp.model.User">
  select
    user_id             as "id",
    user_name           as "userName",
    hashed_password     as "hashedPassword"
  from some_table
  where id = #{id}
</select>
```
- 自定义外部的resultMap来映射到User类
```xml
<resultMap id="userResultMap" type="com.someapp.model.User">
  <id property="id" column="user_id" />
  <result property="username" column="user_name"/>
  <result property="password" column="hashed_password"/>
</resultMap>

<select id="selectUsers" resultMap="userResultMap">
  select user_id, user_name, hashed_password
  from some_table
  where id = #{id}
</select>
```
- 高级结果集映射
```xml
<!-- Very Complex Statement -->
<select id="selectBlogDetails" resultMap="detailedBlogResultMap">
  select
       B.id as blog_id,
       B.title as blog_title,
       B.author_id as blog_author_id,
       A.id as author_id,
       A.username as author_username,
       A.password as author_password,
       A.email as author_email,
       A.bio as author_bio,
       A.favourite_section as author_favourite_section,
       P.id as post_id,
       P.blog_id as post_blog_id,
       P.author_id as post_author_id,
       P.created_on as post_created_on,
       P.section as post_section,
       P.subject as post_subject,
       P.draft as draft,
       P.body as post_body,
       C.id as comment_id,
       C.post_id as comment_post_id,
       C.name as comment_name,
       C.comment as comment_text,
       T.id as tag_id,
       T.name as tag_name
  from Blog B
       left outer join Author A on B.author_id = A.id
       left outer join Post P on B.id = P.blog_id
       left outer join Comment C on P.id = C.post_id
       left outer join Post_Tag PT on PT.post_id = P.id
       left outer join Tag T on PT.tag_id = T.id
  where B.id = #{id}
</select>

<!-- Very Complex Result Map -->
<resultMap id="detailedBlogResultMap" type="Blog">
  <constructor>
    <idArg column="blog_id" javaType="int"/>
  </constructor>
  <result property="title" column="blog_title"/>
  <association property="author" javaType="Author">
    <id property="id" column="author_id"/>
    <result property="username" column="author_username"/>
    <result property="password" column="author_password"/>
    <result property="email" column="author_email"/>
    <result property="bio" column="author_bio"/>
    <result property="favouriteSection" column="author_favourite_section"/>
  </association>
  <collection property="posts" ofType="Post">
    <id property="id" column="post_id"/>
    <result property="subject" column="post_subject"/>
    <association property="author" javaType="Author"/>
    <collection property="comments" ofType="Comment">
      <id property="id" column="comment_id"/>
    </collection>
    <collection property="tags" ofType="Tag" >
      <id property="id" column="tag_id"/>
    </collection>
    <discriminator javaType="int" column="draft">
      <case value="1" resultType="DraftPost"/>
    </discriminator>
  </collection>
</resultMap>
```
### resultMap的元素和属性
- resultMap中可嵌套如下元素：
  - constructor：在通过构造方法实例化类时，将select语句返回的result注入到构造方法中
    - idArg：标识为id的参数，将result标识为id可以提高整体的性能
    - arg：将result注入到constructor中
  - id：标识为id的result，将result标识为id可以提高整体性能
  - result：select语句返回的result，将被注入到返回POJO类型的field
  - association：复杂关联，返回results中的部分results将会被包装到这种关联中
    - 关联被嵌套在resultMap中，关联本身可以是resultMap，或者可以引用一个外部resultMap
  - collection：复杂类型的集合
    - 集合被嵌套在resultMap中，其本身可以是一个resultMap，或者其可以引用要给外部的resultMap
  - discrimination：鉴别器，可以通过结果值来判断使用哪个resultMap
    - case：条件选项，当值为xx时使用特定的resultMap
    - case同样是一个resultMap，case可以是resultMap本身，也可以引用其他的resultMap
- resultMap元素可包含如下属性：
  - id：在命名空间内，可以通过该id引用该resultMap
  - type：java类的全限定类名或typeAlias
  - autoMapping：为resultMap开启或关闭自动映射
### id和result元素
id和result元素是resultMap的基础，id和result都将一个column字段值映射到java类型的field域。
> id和result标签的区别是，当result被标识为id时，该result将会被看作对象的标识符，在比较对象时id result将会被使用。这样可以提高整体性能，尤其是在缓存或是嵌套resultMap时。  

- id和result元素的属性：
  - property：column映射到的java对象的field
  - column：数据库表的列名
  - javaType：java类型的全限定类名或者typeAlias，通常映射到java bean时mybatis可以推断java类型，但是当映射到HashMap时，需要显式指定javaType
  - jdbcType
  - typeHandler
### constructor元素
当想要构建不可变对象时，可以通过constructor属性来向构造方法中注入result构建对象。
```java
public class User {
   //...
   public User(Integer id, String username, int age) {
     //...
  }
//...
}
```
```xml
<constructor>
   <!-- 该constructor会自动匹配构造方法参数为(Integer,String,int)的构造函数 -->
   <idArg column="id" javaType="int"/>
   <arg column="username" javaType="String"/>
   <arg column="age" javaType="_int"/>
</constructor>
```
- constructor元素的属性：
  - column：数据库表列名
  - javaType
  - jdbcType
  - typeHandler
  - resultMap
  - select
### association元素
association元素通常用来标识“has-a”的关系，通过关联，有两种方式将数据加载到关联中去
- 嵌套select：执行另一个sql statement并且返回预期的复杂对象
- 嵌套result：通过嵌套的result mapping来处理连接结果的重复子集
#### 嵌套select
嵌套select,其会先返回查询的主表数据,对关联的子表数据会通过association标签指定的select属性值来再次执行sql查找预期数据并组装城复杂对象   
当使用嵌套select来处理association时,association标签具有如下属性：
- column：数组库表的列名，该列保存的值将会被传递给嵌套的statement作为输入参数。
> 当想要传递复合key作为输入参数时，可以通过column="{prop1=col1,prop2=col2}"的形式来传递参数，这会导致传递给嵌套statement的参数对象的prop1和prop2属性被设置
- select：会返回预期复杂类型的其他select statement标签的id
- fetchType：可选参数，可以为“lazy”或者“eager”，会覆盖全局配置中的lazyLoadingEnable
```xml
<!-- association内层嵌套样例 -->
<resultMap id="blogResult" type="Blog">
  <association property="author" column="author_id" javaType="Author" select="selectAuthor"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
  SELECT * FROM BLOG WHERE ID = #{id}
</select>

<select id="selectAuthor" resultType="Author">
  SELECT * FROM AUTHOR WHERE ID = #{id}
</select>
```
> 对于嵌套select方法来加载association,其会导致***N+1***的问题  
> 例如,当一条select语句返回一个数据集时,通过嵌套select语句来加载association,其会:
> #### 对数据集中的每一条数据,都会再次执行一个select语句来为该条数据加载association信息,当数据集中数据条数很多时,如果未设置延迟加载,那么成百上千的sql语句被执行,会严重影响性能

#### 嵌套result
不同于嵌套select先查主表信息后再次执行sql语句查询关联子表数据,嵌套result会通过A JOIN B关联来通过查询需要的主表信息和子表信息  
嵌套result不需要像嵌套select一样分次执行sql语句
> #### 使用嵌套result时association可以使用的属性
> - resultMap:指定用于将results加载到association的resultMap id
> - columnPrefix:可以将具有某一个前缀的列映射到外部的resultMap中,从而即使在select语句中重新为列名定义了alias(添加了前缀,如"co_"等),还是能复用外部的同一个resultMap
> - notNullColumn:该属性可以指定多个列,只有当指定的多个列中任何一个不为空时,mybatis
> 才会创建该子对象
> - autoMapping:开启或关闭自动映射,该属性对外部映射无效,故而不能搭配select或者resultMap使用
```xml
<!-- 通过left join连接两个表 -->
<select id="selectBlog" resultMap="blogResult">
  select
    B.id            as blog_id,
    B.title         as blog_title,
    B.author_id     as blog_author_id,
    A.id            as author_id,
    A.username      as author_username,
    A.password      as author_password,
    A.email         as author_email,
    A.bio           as author_bio
  from Blog B left outer join Author A on B.author_id = A.id
  where B.id = #{id}
</select>
<!-- resultMap,映射主表信息 -->
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <!-- association的resultMap属性指定为其他resultMap的id,嵌套resultMap -->
  <association property="author" resultMap="authorResult" />
</resultMap>
<!-- resultMap,映射子表信息 -->
<resultMap id="authorResult" type="Author">
  <id property="id" column="author_id"/>
  <result property="username" column="author_username"/>
  <result property="password" column="author_password"/>
  <result property="email" column="author_email"/>
  <result property="bio" column="author_bio"/>
</resultMap>
```
> 在嵌套result中,id元素(被标识为id的result)非常重要,应该指定一个或多个属性标识为id来唯一标识该对象.  
> 当没有指定id result时,mybatis也会正常运行,但是会产生严重的性能问题  
> 应尽可能的将能唯一标识该结果的最小字段集标识为id(可以选择主键或符合主键)

对于嵌套result,除了上述的办法,还可以将association对应的resultMap直接嵌套写入association的子标签中,样例如下:
```xml
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <association property="author" javaType="Author">
    <id property="id" column="author_id"/>
    <result property="username" column="author_username"/>
    <result property="password" column="author_password"/>
    <result property="email" column="author_email"/>
    <result property="bio" column="author_bio"/>
  </association>
</resultMap>
```
共同作者:通过columnPrefix属性复用外部resultMap的情况
```xml
<!-- 连接author表两次,为author表字段添加不同前缀 -->
<select id="selectBlog" resultMap="blogResult">
  select
    B.id            as blog_id,
    B.title         as blog_title,
    A.id            as author_id,
    A.username      as author_username,
    A.password      as author_password,
    A.email         as author_email,
    A.bio           as author_bio,
    CA.id           as co_author_id,
    CA.username     as co_author_username,
    CA.password     as co_author_password,
    CA.email        as co_author_email,
    CA.bio          as co_author_bio
  from Blog B
  left outer join Author A on B.author_id = A.id
  left outer join Author CA on B.co_author_id = CA.id
  where B.id = #{id}
</select>
<!-- resultMap,将results映射为Author类,可被复用 -->
<resultMap id="authorResult" type="Author">
  <id property="id" column="author_id"/>
  <result property="username" column="author_username"/>
  <result property="password" column="author_password"/>
  <result property="email" column="author_email"/>
  <result property="bio" column="author_bio"/>
</resultMap>
<!-- 复用外部的authorResult resultMap -->
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <association property="author"
    resultMap="authorResult" />
  <!-- 通过columnPrefix指定前缀,将添加前缀的results仍然映射到resultMap -->
  <association property="coAuthor"
    resultMap="authorResult"
    columnPrefix="co_" />
</resultMap>
```
### collection元素
collection元素用来处理A类中含有List&lt;B&gt;属性的情况,其用法基本和association元素相同
```xml
<collection property="posts" ofType="domain.blog.Post">
  <id property="id" column="post_id"/>
  <result property="subject" column="post_subject"/>
  <result property="body" column="post_body"/>
</collection>
```
和association元素相同,coolection元素也可以通过嵌套select或者嵌套result来映射复杂类型
#### collection元素的嵌套select
```xml
<resultMap id="blogResult" type="Blog">
  <collection property="posts" javaType="ArrayList" column="id" ofType="Post" select="selectPostsForBlog"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
  SELECT * FROM BLOG WHERE ID = #{id}
</select>

<select id="selectPostsForBlog" resultType="Post">
  SELECT * FROM POST WHERE BLOG_ID = #{id}
</select>
```
#### collection元素的嵌套result
```xml
<!-- 关联查询 -->
<select id="selectBlog" resultMap="blogResult">
  select
  B.id as blog_id,
  B.title as blog_title,
  B.author_id as blog_author_id,
  P.id as post_id,
  P.subject as post_subject,
  P.body as post_body,
  from Blog B
  left outer join Post P on B.id = P.blog_id
  where B.id = #{id}
</select>
<!-- 嵌套result -->
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <collection property="posts" ofType="Post">
    <id property="id" column="post_id"/>
    <result property="subject" column="post_subject"/>
    <result property="body" column="post_body"/>
  </collection>
</resultMap>
<!-- 嵌套result并复用外部resultMap -->
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <collection property="posts" ofType="Post" resultMap="blogPostResult" columnPrefix="post_"/>
</resultMap>

<resultMap id="blogPostResult" type="Post">
  <id property="id" column="id"/>
  <result property="subject" column="subject"/>
  <result property="body" column="body"/>
</resultMap>
```
## discriminator
某些情况下,单条sql查询语句会返回具有不同类型的结果集(如不同的Car数据,但是有卡车和轿车的区别).  
discriminator可以通过某些字段的值来选择性采用某些resultMap,并且支持继承层次.  
```xml
<!-- 
  通过vehicle_type字段的值来选择采用那些resultMap 
  当某行数据vihicle_type字段值为1时,只有carResult的resultMap会被使用,外面如vin等属性都不会被映射
  如果想要
-->
<resultMap id="vehicleResult" type="Vehicle">
  <id property="id" column="id" />
  <result property="vin" column="vin"/>
  <result property="year" column="year"/>
  <result property="make" column="make"/>
  <result property="model" column="model"/>
  <result property="color" column="color"/>
  <discriminator javaType="int" column="vehicle_type">
    <case value="1" resultMap="carResult"/>
    <case value="2" resultMap="truckResult"/>
    <case value="3" resultMap="vanResult"/>
    <case value="4" resultMap="suvResult"/>
  </discriminator>
</resultMap>

<resultMap id="carResult" type="Car">
  <result property="doorCount" column="door_count" />
</resultMap>
```
如果想要在使用carResult时也执行vehicleResult中的映射,可以在carResult中指定extends属性
```xml
<resultMap id="carResult" type="Car" extends="vehicleResult">
  <result property="doorCount" column="door_count" />
</resultMap>
```
## automapping
对于automapping,其会获取resultset中的字段名并且在java对象中查找同名的property(忽略大小写,如果想将数据表字段名的underscore形式映射到java类的camelCase属性,可以在mybatis config文件中添加mapUnderscoreToCamelCase配置).  
即使手动指定了resultMap,automapping一样会发挥作用.对于每个指定的resultMap,所有在resultSet中出现过的字段如果没有在resultMap中手动为其配置一个映射,那么会对该字段执行automapping.
```xml
<!-- 如下列中,password会被手动映射,而id和userName都会被自动映射 -->
<select id="selectUsers" resultMap="userResultMap">
  select
    user_id             as "id",
    user_name           as "userName",
    hashed_password
  from some_table
  where id = #{id}
</select>

<resultMap id="userResultMap" type="User">
  <result property="password" column="hashed_password"/>
</resultMap>
```
> 自动映射有三种等级,可以在mybatis config文件中进行配置
> - NONE:禁用自动配置
> - PARTIAL:会对除嵌套result映射以外的属性进行自动行摄
> - FULL:会对所有属性执行自动映射
```xml
<!-- 
  对于该映射,当automapping level为full时,由于Blog和Author类中都具有id属性,故而在automapping时会将Blog
  对象中的id和Author对象中的id都填充为B.id,该行为是非预期的
  而对于automapping level为partial时,该automapping不会填充嵌套result映射中的属性,故而只会填充Blog类中
  的id属性,而不会填充Author类中的id属性
 -->
<select id="selectBlog" resultMap="blogResult">
  select
    B.id,
    B.title,
    A.username,
  from Blog B left outer join Author A on B.author_id = A.id
  where B.id = #{id}
</select>

<resultMap id="blogResult" type="Blog">
  <association property="author" resultMap="authorResult"/>
</resultMap>

<resultMap id="authorResult" type="Author">
  <result property="username" column="author_username"/>
</resultMap>
```
默认情况下,automapping级别为partial,并且可以为resultMap元素指定autoMapping属性为false来为该resultMap关闭autoMapping
```xml
<resultMap id="userResultMap" type="User" autoMapping="false">
  <result property="password" column="hashed_password"/>
</resultMap>
```
## mybatis缓存
默认情况下,mybatis只启用了会话级别的缓存,其缓存数据只在sqlsession期间有效.  
如果想要开启二级缓存,只需要在接口对应的mapper文件中添加一行
```xml
<cache/>
```
该行语句的效果如下:
- 所有select语句的执行结果都会被缓存
- 所有insert/update/delete语句都会清除缓存
- 缓存会通过LRU算法来作为淘汰策略
- 缓存不定期的刷新
- 缓存会存储1024个引用指向列表或者对象(不管查询方法会返回哪种类型)
- 缓存被设计为可读写缓存,通过缓存获取到的对象并不会和其他线程共享,故而可以安全的对获取到的对象进行共享
> 缓存只会作用于位于mapper文件中的语句,如果使用java api和xml混合的方式,那么在共用接口声明的sql语句并不会被缓存.

可以自定义二级缓存
```xml
<!-- 
  该二级缓存通过FIFO策略进行淘汰
  刷新缓存周期为60000ms
  缓存大小可以保存512个结果(类型为对象或列表:queryForOne或queryForList)
  该缓存是只读的,对该缓存的写操作可能会产生线程安全问题
 -->
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```
对于二级缓存,可以使用的淘汰策略有:
- LRU:默认策略
- FIFO
- SOFT:基于垃圾回收器状态和软引用规则移除对象
- WEAK:弱引用,基于垃圾回收器状态和弱引用规则移出对象

flushInterval:
- 定义cache的刷新间隔,单位为ms,默认没有刷新间隔

size:
- 定义缓存大小,为引用数目,默认为1024

readOnly:
- true:只读,会给所有调用者返回相同的缓存对象实例,调用者如果进行修改可能会产生线程安全问题
- false:可读写,会给所有调用者返回缓存对象的拷贝,调用者可以安全的对返回对象进行修改,默认情况下缓存类型是可读写的

> ### 二级缓存的事务性
> 当sqlsession完成并且提交,或完成并回滚时,即使没有执行flushCache=true的insert/delete/update,缓存也会进行刷新

> select/update/insert/delete
> sql语句都会有flushCache属性,指定是否在执行完成后刷新cache,默认情况下,select标签的flushCache属性为false,insert/update/delete语句的flushCache属性为true

### cache-ref
默认情况下,某一命名空间中的语句只会对当前命名空间中的cache进行刷新.但是,如果想在多个命名空间(Mapper)之间共享缓存,可以通过cache-ref来引用其他命名空间中的缓存.
```xml
<cache-ref namespace="com.someone.application.data.SomeMapper"/>
```

## 动态sql
### if
可以通过&lt;if&gt;标签根据条件向where子句中添加查询条件
```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```
### choose、when、otherwise
不同于if会检测所有的条件，choose、when、otherwise只会从多个条件中选择一个使用，类似于java中的switch。
```xml
<!-- 
  如果传入了title，那么会只按照title查找
  如果title没有传入，且author传入，那么会按照author查找
  如果title和author都没有提供，那么会根据featured=1查找
 -->
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```
### where、trim、set
#### where
对于where标签，只有在子元素返回任何内容不为空的前提下才会插入where子句，并且，如果子句开头返回的内容为”or“或者”and“，则where标签会删除子句开头的”and“或者”or“
```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```
#### trim
如果where标签并不能满足场景需求，那么可以通过trim来自定义想要实现的场景需求。  
和where标签等价的trim标签为
```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```
对于trim标签，其会匹配prefixOverrides属性中指定的内容，文本内容通过'|'符号分隔，并将开头prefixOverrides中的内容删除,并在首部加入prefix属性指定的内容
#### set
set标签用于update语句中动态设置属性，并且删除额外的逗号
```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```
与set标签等效的trim标签是
```xml
<!-- 
  其会在首部加入set，并且删除位于末尾的','符号
 -->
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```
### foreach
foreach标签允许在动态sql中遍历集合，通常用于构建in条件。
```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  <where>
    <foreach item="item" index="index" collection="list"
        open="ID in (" separator="," close=")" nullable="true">
          #{item}
    </foreach>
  </where>
</select>
```
> 可以通过foreach执行便利操作，如果foreach指定的collection为array，那么index对应的时数组下标  
> 若collection为map类型，那么index对应的是entry中的key，而value对应的是value
