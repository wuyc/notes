**XPress 后台使用 Spring Boot，持久层框架是 MyBatis-Plus，后台管理界面使用 Vue.js，后台管理界面的 API 全部采用 REST API 的风格，前台展示界面没有前后端分离，用的模板引擎 FreeMarker 渲染。**

## 数据库表设计
数据库表的设计参考了两个开源的 PHP 博客系统，分别是 Z-BlogPHP 和 Typecho。  
- comment 表：保存评论内容，其中字段`post_id`表示评论对应的文章 ID。
- meta 表：保存分类和标签。
- option 表：保存站点全局配置，如站点标题，关键词，站点描述等。
- post 表：保存文章和页面，其中字段`category_id`表示文章对应的分类。如果保存的是页面，该字段为`NULL`。
- relationship 表：保存文章和标签之间的关系。`relationship`表总共有两个字段`post_id`和`meta_id`，分别保存`post`表和`meta`表的 ID。一篇文章可以有多个标签。
- upload 表：保存上传文件的文件信息，如上传者、文件名、文件类型、文件大小、上传时间。
- user 表：保存管理员信息，密码存储 MD5 后的密文。

> 数据库表在项目初期就要设计合理，不然后期开发起来会很吃力。这个项目在开发的时候数据库表的结构也是改了很多地方。当初把文章对应的分类 ID 存在了`relationship`关系表，这样关系表中就维护文章分类和文章标签的关系，开发时代码就要写很多。最后把文章对应的分类 ID 放在了文章表，一篇文章对应一个分类，这样的设计就合理了很多。

---

## 接口设计
接口的设计采用当前很流行的 REST API 风格。
对资源的操作类型使用 HTTP 方法表示，常见有以下类型。
- GET：从服务器获取资源。
- POST：从服务器创建资源。
- PUT：更新服务器资源（客户端提供完整资源）。
- PATCH：更新服务器资源（客户端提供部分数据）。
- DELETE：删除服务器资源。

例子：
- 获取所有文章：GET http://localhost/posts  
  指定获取第几页，以及每页的记录数：http://localhost/posts?page=2&size=10
- 创建一篇文章：POST http://localhost/posts
- 更新一篇文章：PUT http://localhost/posts/{id}
- 删除一篇文章：DELETE http://localhost/posts/{id}
> 资源名字使用复数形式`posts`，不要使用`post`。

HTTP 状态码：
- 200 OK 资源操作成功，如使用 GET、DELETE
- 201 CREATED 资源创建成功，如使用 POST
- 204 NO CONTENT 资源删除成功，如使用 DELETE
- 400 Bad Request 请求出错
- 401 Unauthorized 未认证
- 403 Forbidden 无权限，访问被禁止
- 404 NOT FOUND 资源不存在
- 500 INTERNAL SERVER ERROR 服务器内部出错
- ............

---

## 项目目录结构
```
├─src
│  ├─main
│  │  ├─java
│  │  │  └─com
│  │  │      └─wuyuncheng
│  │  │          └─xpress
│  │  │              ├─config            // 全局配置
│  │  │              ├─controller        // 控制器
│  │  │              │  └─admin          // 后台接口控制器
│  │  │              ├─exception         // 自定义全局异常
│  │  │              │  └─handler        // 全局异常处理
│  │  │              ├─filter            // 过滤器
│  │  │              ├─interceptor       // 拦截器
│  │  │              ├─model             // Model 层
│  │  │              │  ├─dao            // DAO 层
│  │  │              │  ├─dto            // DTO 对象
│  │  │              │  ├─entity         // 数据库对应的实体类
│  │  │              │  ├─enums          // 枚举类，定义数据库相关字段的值
│  │  │              │  │  └─base        // 枚举基类
│  │  │              │  ├─param          // 前端数据接收实体类
│  │  │              │  └─vo             // 视图对象
│  │  │              ├─service           // service 层，业务逻辑
│  │  │              │  └─impl           // service 实现类
│  │  │              └─util              // 工具类
│  │  │                  └─validator     // 前端数据接收效验器
│  │  └─resources
│  │      ├─admin                        // Vue.js
│  │      │  └─static
│  │      │      ├─css
│  │      │      ├─fonts
│  │      │      ├─img
│  │      │      └─js
│  │      ├─mapper
│  │      ├─static
│  │      │  ├─images
│  │      │  └─styles
│  │      └─templates
│  │          └─error
```

---

## 项目执行逻辑
- 用户在客户端发起请求，首先会跟服务器进行 HTTP 三次握手，握手完成后表示客户端和服务端都可以正常收发数据。
- 第一个请求到达 Tomcat 时，先执行`Filter`，然后请求首先会落到`DispatchServlet`，`DispatchServlet`会根据不同的请求类型分发到不同的`Controller`进行处理。如果有配置 SpringMVC 拦截器先执行拦截器，再进入`Controller`。
- `Controller`把前端传来的数据进行效验，效验通过后调用`Service`业务逻辑层。
- `Service`调用`DAO`层，CRUD 操作。
> 请求执行顺序：Request -> Filter -> Servlet(DispatchServlet) -> HandlerInterceptor -> Controller -> Service -> DAO -> Database

---

## 项目逻辑
这个项目的逻辑不是很复杂的。创建一篇文章需要文章别名是否存在，标签是否存在，创建文章后还要让分类（`meta`表）的`count`字段+1，文章所添加的标签在关系表（`relationship`表）中创建数据，还要让标签`count`字段+1。更新操作，删除操作都相似，这里不多说。

---

## 身份认证
> 身份认证的方式大概分为两种：
### 1. Session+Cookie：
在服务端创建一个 Session，再把 Session ID 给客户端，客户端可以把它放到 Cookie 中，也可以放在 HTTP Headers 中，每次请求的时候带上 Session ID，服务端通过 Session ID 取出之前创建的 Session，从而完成身份认证。
### 2. JWT：
JWT 不在服务端存储任何数据，它会把数据对称加密后给客户端，客户端每次请求的时候带上这份加密后的数据，服务器收到后对数据进行解密，从而完成身份认证。
<details>
<summary>简单解释对称加密和非对称加密</summary>
对称加密：使用同一个密钥进行加密和解密。
<br />
非对称加密：使用公钥对数据进行加密，使用私钥对数据解密。
<br />
顺便说一下 MD5 属于不可逆加密，MD5 加密后的数据理论上是不能被解密的，但可以通过枚举等方法破解，所以不是绝对的安全。我们存在数据库的用户密码一般都是经过 MD5 加密后存储的，这样可以避免明文密码泄漏。
</details>

---

## 异常处理
在项目中自定义一些异常类，当业务逻辑出错的时候抛出自定义的异常类，然后抛出的这些异常统一由 SpringMVC 的全局异常处理器处理。比如登录的时候身份认证失败，我们可以抛出自定义的`AuthException`。  
首先定义一个异常基类`BaseException`，之后的所以自定义异常类都继承这个基类，这样在全局异常处理器中只需要捕获这一个异常就可以了，因为它的继承类都可以被捕获到。我们还要在这个异常基类中添加一个抽象方法`getStatus()`，方便全局异常捕获的时候获取 HTTP 错误码。

---

## 跨域处理
浏览器有同源策略，当前域只能 AJAX 请求当前域下的资源，默认是无法跨域请求资源。  
在这个开发的时候，后台 API 接口的地址是`http://localhost:8080/api/`，而前端项目地址是`http://localhost:9528/`，虽然都是 localhost，但是端口不同，也属于不同域。这时候就要做跨域处理，我是通过添加 HTTP 响应头的方式实现跨域请求。前端项目完成后，把打包后的前端文件放到 Spring Boot 的静态资源目录，这样前端和后端都是在同一域下，所以项目发布后可以不用跨域。