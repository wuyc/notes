> TCP 是一种面向连接的、可靠的、基于字节流的传输层通信协议。TCP 建立连接需要经过三次握手的过程，断开连接需要经过四次挥手。

## TCP建立连接和断开连接流程
![](https://user-images.githubusercontent.com/40560751/77065199-d0ebd880-6a1b-11ea-9cb9-604228b019e9.png)
## 三次握手
- ### 第一次握手（SYN=1，seq=x）
客户端发送连接请求报文段，将 SYN 位置为 1（SYN=1），Sequence Number为x（seq=x，x是ISN，它是随机值），发送完毕后，客户端进入 SYN_SEND 状态，等待服务器的确认。
- ### 第二次握手（SYN=1，ACK=1，seq=y，ACKnum=x+1）
服务器收到数据后发回确认包（ACK）应答，即 SYN 标志位和 ACK 标志位均为1（SYN=1，ACK=1）；  
服务器端选择自己 ISN 序列号，放到 seq 域里（seq=y）；  
同时将确认序号（Acknowledgement Number）设置为客户的 ISN 加 1（ACKnum=x+1）。发送完毕后，服务器进入 SYN_RCVD 状态。
- ### 第三次握手（ACK=1，ACKnum=y+1）
客户端再次发送确认包（ACK），SYN 标志位为0，ACK 标志位为1，并且把服务器发来 ACK 的序号字段 +1，放在确定字段中发送给对方。发送完毕后，客户端和服务器端都进入 ESTABLISHED 状态，TCP 握手结束。

## 四次挥手
- ### 第一次挥手（FIN=1，seq=x）
客户端发送一个 FIN 标志位置为 1，seq=x 的包，表示自己已经没有数据可以发送了，但是仍然可以接受数据。发送完毕后，客户端进入 FIN_WAIT_1 状态。
- ### 第二次挥手（ACK=1，ACKnum=x+1）
向客户端回一个 ACK 报文段（ACK=1），确认序号 ACKnum=x+1，表明自己接受到了客户端关闭连接的请求，但是还没准备好关闭连接（理论上：有可能还有数据向客户端传送）。  
发送完毕后，服务器端进入 CLOSE_WAIT 状态，客户端接收到这个确认包之后，进入 FIN_WAIT_2 状态，等待服务器端关闭连接。
- ### 第三次挥手（FIN=1， seq=y）
服务器端准备好关闭连接时，向客户端发送结束连接请求，FIN 置为1，seq=y。  
发送完毕后，服务器端进入 LAST_ACK 状态，等待来自客户端的最后一个 ACK。
- ### 第四次挥手（ACK=1，ACKnum=y+1）
客户端接收到来自服务器端的关闭请求，发送一个确认包 ACK=1 和 ACKnum=y+1，并进入 TIME_WAIT 状态，等待可能出现的要求重传的 ACK 包。  
服务器端接收到这个确认包之后，关闭连接，进入 CLOSED 状态。  
客户端等待了某个固定时间（两个最大段生命周期，2MSL，2 Maximum Segment Lifetime）之后，没有收到服务器端的 ACK ，认为服务器端已经正常关闭连接，于是自己也关闭连接，进入 CLOSED 状态。

## 为什么建立连接是三次握手，而关闭连接却是四次挥手呢？
这是因为服务端在 LISTEN 状态下，收到建立连接请求的 SYN 报文后，把 ACK 和 SYN 放在一个报文里发送给客户端。  
而关闭连接时，当收到对方的 FIN 报文时，仅仅表示对方不再发送数据了但是还能接收数据，服务端是否现在关闭发送数据通道，需要上层应用来决定，因此，己方 ACK 和FIN一般都会分开发送。

## ISN
三次握手的一个重要功能是客户端和服务端交换 ISN(Initial Sequence Number), 以便让对方知道接下来接收数据的时候如何按序列号组装数据。简单的可以理解为一个随机数。  
如果 ISN 是固定的，攻击者很容易猜出后续的确认号。  
ISN = M + F(localhost, localport, remotehost, remoteport)  
M是一个计时器，每隔4毫秒加1。  
F是一个 Hash 算法，根据源IP、目的IP、源端口、目的端口生成一个随机数值。要保证 hash 算法不能被外部轻易推算得出。

## 推荐阅读
- https://github.com/jawil/blog/issues/14
- https://www.zhoulujun.cn/html/theory/network/2015_0708_65.html
- https://hit-alibaba.github.io/interview/basic/network/TCP.html