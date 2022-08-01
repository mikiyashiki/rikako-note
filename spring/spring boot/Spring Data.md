# Spring Data
- ## Spring Boot中选择连接池的算法：
  - HikariCP的表现和并发性都很好，如果HikariCP可以被获取，选择HikariCP
  - 如果HikariCP不能获取，选用Tomcat Datasource
  - 如果Tomcat Datasource也不能获取，选用DBCP2
  - 如果上述都无法获取，选用Oracle UCP
- Spring Boot使用自定义连接池：
  - 可以通过显式指定自定义的连接池种类来绕过该算法，通过指定spring.datasource.type来自定义连接池
  - 可以通过DatasourceBuilder来定义额外的datasource。如果定义了自己的datasouce bean，那么自动装配将不会发生。