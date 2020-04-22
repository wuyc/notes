## 添加依赖
```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

## 配置 Bean
```java
@Configuration
@EnableSwagger2 // 启用 Swagger2
public class AppConfig {
    @Bean
    public Docket createRestAPI() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.example.springbootswagger2"))
                .paths(PathSelectors.any())
                .build();
    }
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Swagger2 API 文档")
//                .description("Github: https://github.com/")
                .termsOfServiceUrl("https://github.com/")
                .contact(new Contact("Your name", "https://github.com/", "xxx@gmail.com"))
                .version("1.0")
                .build();
    }
}
```

## 开始使用
上面的配置完成后就可以在 SpringBoot 中使用 Swagger2 了。  
使用很简单，我们只需要在 Controller 的类和方法上写上相关注解，Swagger2 就会自动帮我们生成 API 文档。

### 常用注解

- @Api 用于类
```java
@Api(value="用户Controller",tags={"用户操作接口"})
public class UserController {}
```

- @ApiOperation 用于方法
1. value 用于方法描述
2. notes用于提示内容
3. tags可以重新分组（视情况而用）
```java
@ApiOperation("获取用户列表")
public List<User> getUserList() {}
```

- @ApiParam 用于方法的参数，字段说明
```java
@ApiOperation(value="获取用户信息", tags={"获取用户信息copy"}, notes="注意问题点")
public User getUserInfo(@ApiParam(name="id",value="用户id",required=true) Long id){}
```

- @ApiIgnore 用于类或者方法上，表示不被 Swagger2 显示在页面上

- @ApiModel 表示对类进行说明，用于参数用实体类接收

- @ApiModelProperty 表示对 model 属性的说明或者数据操作更改

启动 SpringBoot 程序，访问`http://localhost:8080/swagger-ui.html`查看 API

## 参考
- http://blog.didispace.com/springbootswagger2/
- https://github.com/swagger-api/swagger-core/wiki
- https://www.ibm.com/developerworks/cn/java/j-using-swagger-in-a-spring-boot-project/index.html