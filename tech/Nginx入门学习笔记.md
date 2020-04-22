Nginx 是一个 HTTP 反向代理软件，它可以将客户端的请求转发到不同的服务器上，再把从服务器获取的资源返回给客户端，服务器对客户端来说是隐藏的，客户端不知道 Nginx 把请求分发到哪台服务器。通过 Nginx 可以配置多台服务器，大量请求到达 Nginx 服务器时，可以均匀的把这些请求分发到不同的服务器，达到负载均衡的效果。


## 常用命令
- 启动 Nginx：
`nginx`
- 关闭 Nginx：
`nginx -s stop`
- 重新加载 Nginx：
`nginx -s reload`


## 配置说明
Nginx 配置文件有三部分组成。

### 1. 全局块  
全局块设置一些影响 Nginx 服务器整体运行的配置指令，主要包括配置运行 Nginx 服务器的用户（组）、允许生成的`worker_processes`数，进程 PID 存放路径、日志存放路径和类型以及配置文件的引入等。  
```
worker_processes  1;
```
这是 Nginx 服务器并发处理服务的关键配置，`worker_processes`值越大，可以支持的并发数量也就越多，但是会受到硬件、软件等设配的制约。

### 2. events 块
`events`块涉及的指令主要影响 Nginx 与用户的网络链接，常用的设置包括是否开启对多`worker_processes`下的网络连接进行序列化，是否允许同时接受多个网络连接，选取哪种事务驱动模型来处理连接请求，每个`worker_processes`可以同时支持的最大连接数等。  
这部分的配置对 Nginx 的性能影响较大，在实际中应该灵活配置。

### 3. http 块
`http`块是 Nginx 服务器配置中最频繁的一部分，代理、缓存和日志定义等绝大多数功能和第三方模块的配置都在这里。需要注意的是：`http`块可以包括`http`全局块、`server`块。


## 配置反向代理
```
server {
    listen       80;
    server_name  localhost;

    location / {
        root        html;
        proxy_pass  http://127.0.0.1:8888;
        index       index.html index.htm;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```
配置反向代理主要在`server`块中进行，以下是几个比较常用的配置字段。
- `listen`: Nginx 监听端口
- `server_name`: 服务器名称（IP 或 域名）
- `proxy_pass`: 需要代理的服务器地址

配置完成后，访问`localhost`的80端口，请求会被 Nginx 转发到`http://127.0.0.1:8888`。


## 配置负载均衡
> 负载均衡通过增加服务器数量，然后将请求分发到不同服务器，提升网站性能。

在`http`块添加以下内容：
```
upstream myserver {
    server  127.0.0.1:8080;
    server  127.0.0.1:8081;
}

server {
    listen       80;
    server_name  localhost;

    location / {
        root        html;
        proxy_pass  http://myserver;
        index       index.html index.htm;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```
`upstream myserver`块表示进行负载均衡的服务器列表。  
负载均衡常见的的几种策略：
- 轮询（默认）：每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。
- weight：weight 代表权重，默认为1，权重越高分配的客户端越多。
- ip_hash：每个请求按访问 IP 的 Hash 分配，这样每个访客固定访问一台后端服务器，可以解决 Session 共享问题。
- fair：按后端服务器的响应时间来分配请求，响应时间短的优先分配。


## 参考
- https://www.zhihu.com/question/24723688
- https://www.bilibili.com/video/av68136734