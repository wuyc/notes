## RabbitMQ 是什么
RabbitMQ 即一个消息队列，主要是用来实现应用程序的异步和解耦，同时也能起到消息缓冲，消息分发的作用。  
比如有这样一个场景：  
用户下单购买后，通过短信发送订单通知到用户的手机，然后还要将订单信息记录到日志数据库。  
那么下单模块内就需要调用短信接口和日志接口，三者互相依赖，出现耦合。  
如果使用消息队列，下单后把订单信息放到消息队列中，短信模块和日志模块自己到消息队列中取得订单信息，模块之间就解耦了。

## 简单使用
1. 新建SpringBoot项目，在`pom.xml`中添加RabbitMQ依赖。
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```
2. 配置`application.properties`，添加RabbitMQ地址、端口和用户信息。
```properties
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
```
3. 新建RabbitMQ队列，并添加到Spring容器中。
```java
@Configuration
public class RabbitConfig {

    @Bean
    public Queue Queue() {
        return new Queue("hello");
    }

}
```
4. 新建消息发送者
```java
@component
public class HelloSender {

    @Autowired
    private AmqpTemplate rabbitTemplate;

    public void send() {
        rabbitTemplate.convertAndSend("hello", "this is my msg...");
    }

}
```
5. 新建消息接收者
```java
@Component
@RabbitListener(queues = "hello")
public class HelloReceiver {

    @RabbitHandler
    public void process(String hello) {
        System.out.println("Receiver: " + hello);
    }

}
```
6. 测试
```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class RabbitMqHelloTest {

    @Autowired
    private HelloSender helloSender;

    @Test
    public void hello() throws Exception {
        helloSender.send();
    }

}
```
```
output:
"Receiver: this is my msg...
```

## 高级使用
> 交换机(Exchange)主要的功能是就收发送者的消息，并转发到交换机绑定的队列，交换机不保存消息。在启用ack模式后，交换机找不到队列会返回错误。  
交换机有四种类型：Direct, topic, Headers and Fanout

- Direct：direct 类型的行为是“先匹配，再投送”，即在绑定时设定一个 routing_key，消息的 routing_key 匹配时，才会被交换器投送到绑定的队列中去。
- Topic：按规则转发消息（最灵活），根据 routing_key 的规则绑定到不同的队列中。
- Headers：设置 header attribute 参数类型的交换机。
- Fanout：转发消息到所有绑定队列，Fanout 就是我们熟悉的广播模式或者订阅模式，给 Fanout 交换机发送消息，绑定了这个交换机的所有队列都收到这个消息。

## 推荐阅读
- http://blog.didispace.com/spring-boot-rabbitmq/
- http://www.ityouknow.com/springboot/2016/11/30/spring-boot-rabbitMQ.html