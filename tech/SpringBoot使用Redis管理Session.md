## 开始使用
使用 Redis 管理 Session 在 SpringBoot 中很简单。首先添加依赖。
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.session</groupId>
        <artifactId>spring-session-data-redis</artifactId>
    </dependency>
</dependencies>
```
然后在标注了`@Configuration`的配置类上再加一个`@EnableRedisHttpSession`的注解，表示我们要使用 Redis 管理 Session，不再使用Web容器提供的 Session。  
上面的配置完成后，就直接使用`javax.servlet.http.HttpSession`操作Session，这个类本来是 Servlet 操作Web容器的 Session，现在变成了操作 Redis 中的 Session，还是使用原来的 API。

## 前后端分离
在服务端设置一个 Session，就会向前端设置一个 Cookie，这个 Cookie 相当于存了一个 Session ID，在前后端分离中，我们不把 Session ID 存在 Cookie 中，而是放到 HTTP Headers 中给服务端验证。  
想要改变 Session ID 存储的位置，我们需要配置一个叫`HttpSessionIdResolver`的 Bean 放到 Spring 容器中。
```java
@Configuration
@EnableRedisHttpSession
public class AppConfiguration {

    @Bean
    public HttpSessionIdResolver httpSessionStrategy() {
        return HeaderHttpSessionIdResolver.xAuthToken();
    }

}
```
加入这个 Bean 后，Session ID 就不会放到 Cookie，而是放到 HTTP Headers 中。

## 参考
- https://www.infoq.cn/article/Next-Generation-Session-Management-with-Spring-Session