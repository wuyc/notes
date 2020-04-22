## HTTP 无状态协议
HTTP 是一种无状态协议，打个比方，用户发两次 HTTP 请求给服务端，服务端不会知道这两次请求来自同一个用户。如果服务端想记录用户的状态，可以使用 Cookie + Session。

## Cookie
Cookie 保存在浏览器，可以存储大概 4KB 的纯文本数据。Cookie 可以通过服务端 HTTP Response Headers 中 Set-Cookie 字段给浏览器设置 Cookie，不过客户端也可以自己使用 JavaScript 给浏览器设置 Cookie，设置 Cookie 后，浏览器每次请求都会带上这个 Cookie。

## Session
Session 保存在服务端，服务端通过生成一个 Session，把用户信息存储在 Session 中，然后服务端把这个 Session 的 ID 设置给客户端，客户端拿到这个 Session ID 后存在 Cookie 中，每次请求都带上这个 Session ID，服务端就知道了是谁的请求。

**通过 Cookie + Session  的组合使用，实现了 HTTP 的状态保留。**