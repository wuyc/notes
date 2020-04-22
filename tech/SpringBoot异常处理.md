在 SpringBoot 开发中，如果服务端产生 HTTP 错误，如 403、404、500 等等，SpringBoot 就会把错误信息映射到 `/error` 路由上。`/error`这个路由会被一个 SpringBoot 自带的 Controller 处理，这个 Controller 叫 `BasicErrorController`。
`BasicErrorController`可以根据不同的请求客户端产生不同的响应数据。
如果是浏览器的请求产生错误，SpringBoot 默认会返回 HTML；
如果是非浏览器的请求产生错误，比如 Postman、AJAX，SpringBoot 默认会返回 JSON；
## 如何自定义 HTTP 错误响应
### 浏览器
浏览器发出请求产生错误后，SpringBoot 默认返回一个 Whitelabel Error Page，如果觉得这个页面不好看，我们可以自定义成自己的页面。
在项目目录`resources/static`或`resources/templates`下面新建`error.html`文件后，请求出错就会自动把这个页面返回给客户端。
如果想让不同的错误响应不同的页面，比如 404 错误响应`404.html`、500 错误响应`500.html`，很简单，只需要新建一个`error`文件夹（`resources/static/error`或`resources/templates/error`），然后将以错误代码命名的 HTML 文件放入`error`文件夹即可。
如果想让以4开头的错误响应一个页面，以5开头的错误响应一个页面，那就创建文件名为`4xx.html`和`5xx.html`。
### 非浏览器（Postman、AJAX...）
创建一个 Controller 继承`BasicErrorController`，重写`error`方法。
```java
@Controller
public class MyBasicErrorController extends BasicErrorController {

    public MyBasicErrorController(ErrorAttributes errorAttributes, List<ErrorViewResolver> errorViewResolvers) {
        super(errorAttributes, new ErrorProperties(), errorViewResolvers);
    }

    /**
     * 处理浏览器访问出错
     * 返回自定义的错误页面
     */
    @Override
    public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
        return super.errorHtml(request, response);
    }

    /**
     * 处理非浏览器访问出错
     * 返回带有 message 字段的 JSON 数据
     */
    @Override
    public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
        HttpStatus status = this.getStatus(request);
        Map<String, Object> body = new HashMap<>();
        body.put("message", status.getReasonPhrase());
        return new ResponseEntity(body, status);
    }

}
```
## 统一异常处理
在业务代码中如果抛出异常，默认情况下会响应 HTTP 500 错误。我们可以用 SpringMVC 捕获这些错误，然后对这些错误做出不同的响应。
创建全局异常处理类：通过使用`@ControllerAdvice`定义统一的全局异常处理类，在类中用`@ExceptionHandler(value = Exception.class)`标注方法，`value`表示处理抛出的哪个异常。