Spring AOP 使用动态代理的设计模式在执行方法的前后或者出现异常的时候加入相关逻辑，它可以抽离出重复性的代码，使代码变得更简洁。常见使用到 AOP 的有日志处理、事务处理等等。
Spring Boot 项目中添加 Spring AOP starter：
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
    <version>2.1.9.RELEASE</version>
</dependency>
```
使用`@EnableAspectJAutoProxy`注解配置类，表示开启 AOP。
新建一个类使用`@Aspect`注解、表示这是一个切面，再使用`@Component`把这个切面加入到 Spring 容器中。
```java
/**
 * class TestAspect 是一个切面
 * 切点、连接点、通知 所在的类称之为切面
 */
@Component
@Aspect
public class TestAspect {

    /**
     * @Pointcut 表示一个切点
     * execution 表示连接点
     * 切点是连接点的集合
     */
    @Pointcut("execution(* com.example.aopdemo.controller..*(..))")
    private void controllerMethod() {
    }

    /**
     * @Before 表示一个通知
     * 在切入点干什么，指定在 Pointcut 地方做什么事情（增强），打日志、执行缓存、处理异常等等。
     */
    @Before("controllerMethod()")
    public void before() {
        System.out.println("========== BEFORE ==========");
    }

    /**
     * @After 表示一个通知
     */
    @After("controllerMethod()")
    public void after() {
        System.out.println("========== AFTER ==========");
    }

}
```