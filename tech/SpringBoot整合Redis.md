## 添加依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <!--基于注解使用缓存-->
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

## 配置 Bean
```java
@Configuration // SpringBoot 配置注解
@EnableCaching // 开启 Spring Cache
@AutoConfigureAfter(RedisAutoConfiguration.class)
public class AppConfig {

    /**
     * 配置操作 Redis 的工具 RedisTemplate
     */
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);
        // 使用 StringRedisSerializer 来序列化和反序列化 Redis 的 key 值
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(jackson2JsonRedisSerializer());
        template.setHashKeySerializer(new StringRedisSerializer());
        template.setHashValueSerializer(jackson2JsonRedisSerializer());
        template.afterPropertiesSet();
        return template;
    }

    /**
     * 配置 Redis 序列化
     */
    @Bean
    public RedisSerializer<Object> jackson2JsonRedisSerializer() {
        // 使用 Jackson2JsonRedisSerializer 来序列化和反序列化 Redis 的 value 值
        Jackson2JsonRedisSerializer<Object> serializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper mapper = new ObjectMapper();
        mapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        mapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        serializer.setObjectMapper(mapper);
        return serializer;
    }

    /**
     * 配置缓存 key: 包名 + 方法名 + 参数列表
     * 
     */
    @Bean
    public KeyGenerator keyGenerator() {
        return (target, method, objects) -> {
            StringBuilder sb = new StringBuilder();
            sb.append(target.getClass().getName());
            sb.append("::" + method.getName() + ":");
            for (Object obj : objects) {
                sb.append(obj.toString());
            }
            return sb.toString();
        };
    }

    /**
     * 配置 Spring Cache 的缓存管理器，Spring Cache 将使用 Redis 作为缓存数据库
     */
    @Bean
    public CacheManager cacheManager(RedisConnectionFactory redisConnectionFactory) {
        // 生成一个默认配置，通过 config 对象即可对缓存进行自定义配置
        RedisCacheConfiguration config = RedisCacheConfiguration
                .defaultCacheConfig()
                // 设置缓存的默认过期时间，也是使用 Duration 设置
                .entryTtl(Duration.ofMinutes(10))
                // 设置 key 为 String 序列化
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(new StringRedisSerializer()))
                // 设置 value 为 json 序列化
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer()))
                // 不缓存空值
                .disableCachingNullValues();
        // 使用自定义的缓存配置初始化一个 CacheManager
        RedisCacheManager redisCacheManager = RedisCacheManager.builder(redisConnectionFactory)
                .cacheDefaults(config)
                .build();
        return redisCacheManager;
    }

}
```

## 使用 RedisTemplate 操作
SpringBoot 把对 Redis 的操作封装成了一个 RedisTemplate，在 Spring 容器中配置 RedisTemplate 后，我们就可以在其它类注入 RedisTemplate 用来操作 Redis。  
不过我们在自己的业务逻辑中使用 RedisTemplate 实现缓存，这样耦合度高，不易于维护。
```java
public String get(String key) {
    String value = userMapper.selectById(key);
    if (value != null) {
        cache.put(key,value);
    }
    return value;
}
```
还记得我们配置了`spring-boot-starter-cache`吗，有了这个 starter 我们就可以抛弃 RedisTemplate，使用注解的方式操作缓存。  
使用`spring-boot-starter-cache`操作缓存降低的耦合度，还可以随意切换 Cache Client，不需要修改业务代码。

## 注解操作缓存
Spring Cache 提供了三个操作缓存的注解。

> @Cacheable(根据方法的请求参数对其结果进行缓存)
- key： 缓存的 key，可以为空，如果指定要按照 SpEL 表达式编写，如果不指定，则缺省按照方法的所有参数进行组合（如：@Cacheable(value="user",key="#userName")）
- value： 缓存的名称，必须指定至少一个（如：@Cacheable(value="user") 或者 @Cacheable(value={"user1","use2"})）
- condition： 缓存的条件，可以为空，使用 SpEL 编写，返回 true 或者 false，只有为 true 才进行缓存（如：@Cacheable(value = "user", key = "#id",condition = "#id < 10")）

> @CachePut(根据方法的请求参数对其结果进行缓存，和 @Cacheable 不同的是，它每次都会触发真实方法的调用)
- key： 同上
- value： 同上
- condition： 同上

> @CachEvict(根据条件对缓存进行清空)
- key： 同上
- value： 同上
- condition： 同上
- allEntries： 是否清空所有缓存内容，缺省为 false，如果指定为 true，则方法调用后将立即清空所有缓存（如：@CacheEvict(value = "user", key = "#id", allEntries = true)）
- beforeInvocation： 是否在方法执行前就清空，缺省为 false，如果指定为 true，则在方法还没有执行的时候就清空缓存，缺省情况下，如果方法执行抛出异常，则不会清空缓存（如：@CacheEvict(value = "user", key = "#id", beforeInvocation = true)）

## 参考
- https://www.qikegu.com/spring-boot-tutorial/1320
- https://www.ibm.com/developerworks/cn/opensource/os-cn-spring-cache/
- https://docs.spring.io/spring/docs/4.3.15.RELEASE/spring-framework-reference/html/cache.html
- https://blog.battcn.com/2018/05/13/springboot/v2-cache-redis/