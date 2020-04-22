## Cookie
Cookie 用来存储服务端发来的 Session ID，实现 HTTP 的鉴权，它也可以存储一些本地数据，比如购物车中的商品信息等等。Cookie 在每次请求中都会带上发给服务端，如果存储了服务端并不需要的数据，比如购物车中的信息等等，这回带来性能上的消耗。Cookie 的存储大小只有 4KB 的文本数据，存储空间太小，所以后来出现了 localStorage 和 sessionStorage。
## localStorage
localStorage 是 HTML5 标准中新加入的技术。存在 localStorage 中的数据除非手动删除，否则永久保留。localStorage 的存储容量比 Cookie 大很多，有 5MB 容量。localStorage 的数据可以在同源不同窗口中访问。打个比方，在`https://www.google.com/`中设置一个 localStorage 数据，然后新建一个浏览器窗口，也打开`https://www.google.com/`，在第二个窗口中同样可以得到第一个窗口设置的 localStorage 数据。
## sessionStorage
sessionStorage 也是 HTML5 标准中新加入的技术。它和 localStorage 类似，sessionStorage 也有 5MB 的容量。不过 sessionStorage 中的数据不会永久存储，当用户关闭浏览器窗口后，数据会被删除。另外，sessionStorage 中的数据不可以在另外一个同源的窗口中访问。
## 推荐阅读
- https://jerryzou.com/posts/cookie-and-web-storage/