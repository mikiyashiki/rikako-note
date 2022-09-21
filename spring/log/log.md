# Spring Logging
## Log Format
默认Spring Boot输出日志的格式如下
```console
2022-08-18 05:33:51.660  INFO 16378 --- [           main] o.s.b.d.f.s.MyApplication                : Starting MyApplication using Java 1.8.0_345 on myhost with PID 16378 (/opt/apps/myapp.jar started by myuser in /opt/apps/)
2022-08-18 05:33:51.664  INFO 16378 --- [           main] o.s.b.d.f.s.MyApplication                : No active profile set, falling back to 1 default profile: "default"
2022-08-18 05:33:53.907  INFO 16378 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2022-08-18 05:33:53.939  INFO 16378 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2022-08-18 05:33:53.939  INFO 16378 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.65]
2022-08-18 05:33:54.217  INFO 16378 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2022-08-18 05:33:54.217  INFO 16378 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 2343 ms
2022-08-18 05:33:55.396  INFO 16378 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2022-08-18 05:33:55.640  INFO 16378 --- [           main] o.s.b.d.f.s.MyApplication                : Started MyApplication in 5.456 seconds (JVM running for 6.299)
```
- 日期与时间 ： 精度为ms
- log level ： ERROR, WARN, INFO, DEBUG, TRACE
- 进程ID
- 线程名称 ： [main]
- logger name : 输出日志类的类名（通常为缩写）
- log信息
## 控制台输出
默认情况下，log日志信息会回显输出到console中，默认ERROR, WARN, DEBUG三个级别的信息将会被日志输出。  
可以通过--debug选项来启用“debug”模式
```shell
java -jar xxx.jar --debug
```
通过在application.properties中指定debug=true也可以开启“debug”模式
```properties
debug=true
```
当“debug”模式被开启后，一部分核心的logger（内嵌容器、Hibernate、SpringBoot）将会被配置输出更多的信息。
> ***开启debug模式并不意味着输出所有日志级别为Debug的信息***

> ***同样，也可以通过--trace或者在properties中指定trace=true来开启trace模式***

## 文件输出
默认情况下，Spring Boot只会将日志输出到console中，如果想要额外定义将日志输出到文件中，需要在application.properties中定义logging.file.name或者logging.file.path
| logging.file.name | logging.file.path | example | description |
| :-: | :-: | :-: | :-: |
| (none) | (none) |  | 只在控制台输出 |
| 特定文件 | (none) | my.log | 特定log文件路径，可以是绝对路径或相对路径） |
| (none) | 特定目录 | /var/log | 将日志输出到该路径下的spring.log文件，可以是绝对路径或相对路径|
> 当log文件大小到达10MB时将会旋转重写，和console log一样，log文件也会输出ERROR, WARN和INFO

> ***logging properties和实际的logging机制是相互独立的，因而，特定的配置属性（如logback.configurationFile)并不由SpringBoot管理***

## File Rotation
如果使用的是Logback，则可以在application.properties中定义file rotation行为。
| Name | Description |
| :-: | :-: |
| logging.logback.rollingpolicy.file-name-pattern | 定义创建log归档的命名模式 | 
| logging.logback.rollingpolicy.clean-history-on-start | 定义是否应该在项目启动时清理历史日志 | 
| logging.logback.rollingpolicy.max-file-size | 定义日志在归档前的最大大小 |
| logging.logback.rollingpolicy.total-size-cap | 日志归档在被删除前可以占用的最大大小 |
| logging.logback.rollingpolicy.max-history | 要保留归档日志文件的最大数量 | 

## Log Level
所有的日志系统都可以通过Application.properties定义logging.level.&lt;logger-name&gt;=&lt;level&gt;来定义事务级别，事务级别可以是TRACE, DEBUG, INFO, WARN, ERROR, FATAL, OFF。  
可以通过logging.level.root来定义root logger的隔离级别。
```properties
logging.level.root=warn
logging.level.org.springframework.web=debug
logging.level.org.hibernate=error
```
## Log Group
可以通过log group将关联的logger组合在一起，并且对log group统一指定日志级别。
```properties
# 定义一个名为“tomcat”的log group
logging.group.tomcat=org.apache.catalina,org.apache.coyote,org.apache.tomcat

# 为名为“tomcat”的log group统一指定log level
logging.level.tomcat=trace
```
> Spring Boot具有如下先定义好的log group，可以开箱即用
> - web ： org.springframework.core.codec, org.springframework.http, org.springframework.web, org.springframework.boot.actuate.endpoint.web, org.springframework.boot.web.servlet.ServletContextInitializerBeans
> - org.springframework.jdbc.core, org.hibernate.SQL, org.jooq.tools.LoggerListener

