> CORS 全称是 Cross-origin resource sharing （跨域资源共享），它允许浏览器跨源请求数据，克服了 AJAX 只能同源使用的限制。

## 同源策略
对于协议相同，域名相同，端口相同的被视为同一个源，在同一个源内可以获取本域中的 Cookie，可以 AJAX 请求本域中的资源。同源策略下无法获取其它域的资源。  
同源策略是一个很重要的安全机制用来隔离那些有潜在安全隐患。保证用户信息的安全，防止恶意的网站窃取数据。

## CORS
我们在 `www.a.com` 内通过 AJAX 请求 `www.b.com` 中的资源，浏览器在 HTTP 请求头中会自动加上下面的字段↓
```http
Origin: https://www.a.com // 当前请求所在的域
Referer: https://www.a.com // 当前请求所在页面的 URL
```
上面的两条请求头信息是浏览器自动加上的，我们无法控制。  
请求发出后，此时浏览器会报错↓
```javascript
Access to XMLHttpRequest at 'https://www.b.com/' from origin 'https://www.a.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```
`www.a.com` 接收到请求后，发现请求头中有 `Origin` 和 `Referer` 两个字段，它就知道了这是一个跨域请求，是其它域发来的。默认情况下会拒绝跨域请求。所以我们无法跨域获取 `www.b.com` 中的数据。这是浏览器的安全策略，保证了用户信息的安全。  

## 实现跨域获取数据
要想在其它域获取 `www.b.com` 中的数据，我们需要在 `www.b.com` 响应头中设置一些字段
```http
Access-Control-Allow-Origin: https://www.a.com
Access-Control-Allow-Credentials: true
```
- Access-Control-Allow-Origin: https://www.a.com  
该字段必选。它的值要么是请求时 Origin 字段的值，要么是一个 *，表示接受任意域名的请求。
- Access-Control-Allow-Credentials: true  
该字段可选。它的值是一个布尔值，默认 false，表示是否允许发送 Cookie 或 HTTP 认证信息，比如 cookies, authorization headers 或 TLS client certificates。

**除了以上字段，还有一些跨域相关的字段，它们都以 Access-Control- 开头。**

## 其它跨域方式
- Nginx 反向代理。
- JSONP（在当前域声明跨域访问的函数，然后用`<scrip>`标签引入目标域中调用当前域的 JS 脚本，利用`<scrip>`标签没有跨域限制来达到与第三方进行通讯。）

## 推荐阅读
- http://www.ruanyifeng.com/blog/2016/04/cors.html
- https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS