## 应用层（应用层，表示层，会话层）

### 基于（TCP）

#### HTTP

###### 请求头、响应头（Header）=起始行（start line）+头部字段（header）

##### 起始行（start line）

###### 请求方法

GET(获取资源)(1.0)

POST（传输实体主体）(1.0)

PUT（传输文件）(1.0)

HEAD（获得报文首部）(1.0)

DELETE（删除文件）(1.0)

OPTIONS（询问支持的方法）

TRACE（追踪路径）

CONNECT（要求用隧道协议连接代理）

###### URL

方案或协议+主机+端口+路径+查询+片段

###### HTTP版本

HTTP1.0

HTTP1.1

HTTP2.0

##### 头部字段（header）

###### 通用标头

Cache-Control

Connection

###### 实体标头

Content-Encoding

Content-Type

Expires

###### 请求标头

Authorization

User-Agent

###### 响应标头

##### 空行（CRLF）

##### 消息正文（entity）



##### 状态码

###### 1XX:提示信息，表示目前是协议处理的中间状态，还需要后续操作

###### 2XX:成功，报文已经收到并被正确处理

200 OK:最常见的成功状态码，表示一切正常

204 No Content:常见的成功状态码，但响应头没有 body 数据

206 Partial Content:应用于 HTTP 分块下载或断点续传，表示响应返回的 body 数据并不是资源
的全部，而是其中的一部分

###### 3XX:重定向，资源位置发生变动，需要客户端重新发送请求

301 Moved Permanently:表示永久重定向，说明请求的资源已经不存在了，需改用新的 URL

302 Found:表示临时重定向，说明请求的资源还在，但暂时需要用另一个 URL 来访问

304 Not Modified:不具有跳转的含义，表示资源未修改，重定向已存在的缓冲文件，也称缓存重定
向，用于缓存控制

###### 4XX:客户端错误，请求报文有误，服务器无法处理

400 Bad Request:表示客户端请求的报文有错误，但只是个笼统的错误

403 Forbidden:表示服务器禁止访问资源，并不是客户端的请求出错

404 Not Found:表示请求的资源在服务器上不存在或未找到，所以无法提供给客户端

###### 5XX:服务器错误，服务器在处理请求时内部发生了错误

500 Internal Server Error:与 400 类型，是个笼统通用的错误码

501 Not Implemented:表示客户端请求的功能还不支持，类似“即将开业，敬请期待”的意思

502 Bad Gateway:服务器作为网关或代理时返回的错误码，表示服务器自身工作正常，访问
后端服务器发生了错误

503 Service Unavailable:表示服务器当前很忙，暂时无法响应服务器，类似“网络服务正忙，请稍
后重试”的意思

#### HTTPS

### 基于（UDP）

## 传输层(报文段(segment))

负责向两台终端设备进程之间的通信提供通用的数据传输服务

### 传输控制协议 TCP（Transmisson Control Protocol）

**网络包（MTU=1500）=IP头部+TCP头部+数据（MSS）**

当 TCP 层发现数据超过 MSS 时,就先会进行分片，如果一个 TCP 分片丢失后，进行重发时也是以 MSS 为单位，而不用重传所有的分片，大大增加了重传的效率

#### 三次握手

SYN,Seq Num=client_isn

SYN+ACK,Ack Num=client_isn+1,Seq Num=server_isn

ACK,Ack Num=server_isn+1

第三次握手是可以携带数据的，前两次握手是不可以携带数据的

##### 三次原因

避免历史连接

同步双方初始序列号

避免资源浪费

#### SYN攻击

攻击者短时间伪造不同 IP 地址的 SYN 报文，服务端发送出去的 ACK + SYN 报文，无法得到未知 IP 主机的 ACK 应答，久而久之就会占满服务端的 SYN 接收队列（未连接队列），使得服务器不能为正常用户服务

##### 对策

###### 修改 Linux 内核参数，控制队列大小和当队列满时应做什么处理

net.core.netdev_max_backlog	当网卡接收数据包的速度大于内核处理的速度时，会有一个队列保存这些数据包

net.ipv4.tcp_max_syn_backlog	SYN_RCVD 状态连接的最大个数

net.ipv4.tcp_abort_on_overflow	超出处理能时，对新的 SYN 直接回报 RST

###### tcp_syncookies

net.ipv4.tcp_syncookies = 1

SYN队列满以后，后续服务器收到的包不进入，计算一个cookie值再以SYN+ACK返回给客户端，服务器收到应答报文时合法直接放入Accept队列

#### 四次挥手

FIN

ACK

FIN

ACK，主动关闭连接的，才有 TIME_WAIT 状态

##### 四次原因

关闭连接时，客户端向服务端发送 FIN 时，仅仅表示客户端不再发送数据了但是还能接收数据

服务器收到客户端的 FIN 报文时，先回一个 ACK 应答报文，等服务端不再发送数据时，才发送 FIN 报文给客户端

服务端通常需要等待完成数据的发送和处理，ACK 和 FIN 一般都会分开发送

#### TIME_WAIT

##### TIME_WAIT(60秒)=2MSL?

MSL 是 Maximum Segment Lifetime，报文最大生存时间，它是任何报文在网络上存在的最长时间

TTL 字段，是 IP 数据报可以经过的最大路由数,MSL>=TTL

网络中可能存在来自发送方的数据包，当这些发送方的数据包被接收方处理后又会向对方发送响应，所以一来一回需要等待 2 倍的时间

2MSL 的时间是从客户端接收到 FIN 后发送 ACK 开始计时的,如果在 TIME-WAIT 时间内，因为客户端的 ACK 没有传输到服务端，客户端又接收到了服务端重发的 FIN 报文，那么 2MSL 时间将重新计时

##### 为什么需要 TIME-WAIT

防止旧连接的数据包

保证「被动关闭连接」的一方能被正确的关闭，即保证最后的 ACK 能让被动关闭方接收，从而帮助其正常关闭；

##### TIME_WAIT 过多危害

内存资源占用

对端口资源的占用，一个 TCP 连接至少消耗一个本地端口，占满了所有端口资源，则会导致无法创建新连接

#### 保活机制

在这个时间段内，如果没有任何连接相关的活动，TCP 保活机制会开始作用，每隔一个时间间隔，发送一个探测报文，连续几个探测报文都没有得到响应，则认为当前的 TCP 连接已经死亡，系统内核将错误信息通知给上层应用程序

net.ipv4.tcp_keepalive_time=7200	保活时间是 7200 秒（2小时），也就 2 小时内如果没有任何连接
相关的活动，则会启动保活机制

net.ipv4.tcp_keepalive_intvl=75	每次检测间隔 75 秒；

net.ipv4.tcp_keepalive_probes=9	检测 9 次无响应，认为对方是不可达的，从而中断本次的连接

在 Linux 系统中，最少需要经过 2 小时 11 分 15 秒才可以发现一个「死亡」连接

#### TCP报文格式



![](https://img-blog.csdnimg.cn/5b5328ac323747b9a826db3f631211b7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiA5py16Iqx6Iqx,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

###### 序号：

在建立连接时由计算机生成的随机数作为其初始值，通过 SYN 包传给接收端主机，每发送一
次数据就「累加」一次该「数据字节数」的大小,用来解决网络包乱序问题

###### 确认号：

下一次「期望」收到的数据的序列号,在这个序号以前的数据都已经被正常接收,用来解决不丢包的问题

###### 六个控制位：

URG

ACK	TCP 规定除了最初建立连接时的 SYN包之外该位必须设置为 1

PSH	窗口大小（16位）

PST	表示 TCP 连接中出现异常必须强制断开连接

SYN	表示希望建立连接，并在其「序列号」的字段进行序列号初始值的设定

FIN	表示今后不会再有数据发送，希望断开连接

#### 可靠性传输

序列号

确认应答

重发控制

连接管理

窗口控制

#### 重传机制

超时重传

快速重传

SACK

D-SACK

#### 流量控制

操作系统缓冲区与滑动窗口的关系

窗口关闭

糊涂窗口综合症

#### 拥塞控制

慢启动

拥塞避免

拥塞发生

快速恢复

#### TCP参数

### 用户数据协议 UDP（User Datagram Protocol）

#### UDP报文格式

![](https://img-blog.csdnimg.cn/c87a239a39fe4df1bd1595a335947058.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiA5py16Iqx6Iqx,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)



## 网络层(数据报(datagram))

为分组交换网上的不同主机提供通信服务

### IP（Internet Protocol）

### ARP

### NAT

### ICMP

### RIP

### OSPF

### BGP

## 网络接口层

### 数据链路层(帧(frame))

将网络层交下来的 IP 数据报组装成帧，在两个相邻节点间的链路上传送帧

### 物理层

实现相邻计算机节点之间比特流的透明传送，尽可能屏蔽掉具体传输介质和物理设备的差异

## IO

## Reactor 和 Proactor

## Netty