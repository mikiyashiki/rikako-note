# File Upload and Download in Spring Boot
## SpringBoot上传文件配置
在application.properties中，可以进行如下配置
```properties
spring.servlet.multipart.enabled=true
spring.servlet.multipart.max-file-size=10MB
spring.servlet.multipart.max-request-size=15MB
```