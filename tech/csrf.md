## 什么是CSRF
如果某个 WebApp 存在 CSRF 漏洞，当受害者登录该页面后，黑客可以用受害者的身份执行恶意代码。  
比如一个转账接口如下：
```
// GET 请求，向 bob 转账 100 元
GET http://www.mybank.com/transfer?username=bob&amount=100
```
另外一个黑客制作的恶意页面中包含以下代码：
```html
<img src="http://www.mybank.com/transfer?username=hack&amount=999" width="500" height="300" />
```
当受害者之前登录过 `www.mybank.com`，然后访问黑客制作的恶意页面，浏览器就会发送 GET 请求到 `http://www.mybank.com/transfer?username=hack&amount=999`，这时就会向用户 hack 转账 999 元。  
黑客伪造受害者的身份进行操作，服务端没有识别是不是这个用户触发的。

## 如何防范
### 1. 尽量使用 POST
GET 请求太容易被利用，POST 可以降低被攻击的风险，但不是万无一失。
### 2. 加入验证码
加入验证码后服务端可以识别是不是真正的用户，不过有些验证码也可以被破解，这也不是万无一失的解决方案。
### 3. 验证 Referer
Referer 是 HTTP 请求头中的一个字段，它记录了当前请求来源的地址。通过 Referer 可以识别当前请求是不是合法的。这种方法操作方便，但也不是万无一失，因为 Referer 可能会被篡改。
### 4. Anti CSRF Token
服务端每次都生成一个一次性随机 Token 存到 Session/Redis 中，然后传给浏览器，浏览器请求 API 时带上 Token，服务端收到请求后验证 Token 是不是之前生成的。这个 Token 在浏览器可以存在 head 的 meta 标签中，也可以存在表单中，还可以存在 HTTP 响应头中传递给浏览器。

## CSRF 和 CORS 有什么区别
CORS 机制的目的是为了解决脚本的跨域资源请求问题，不是为了防止 CSRF。  
CORS 在同源策略的限制下，响应会被拦截，请求还是发送到了服务端。  
CSRF 只是向服务器发送请求，并不需要获取数据。  

## 参考
- https://zh.wikipedia.org/zh-cn/跨站请求伪造
- https://www.bilibili.com/video/av33502871