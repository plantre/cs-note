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

##### 

#### HTTPS

### 基于（UDP）

## 传输层(报文段(segment))

负责向两台终端设备进程之间的通信提供通用的数据传输服务

### 传输控制协议 TCP（Transmisson Control Protocol）

#### 三次握手

#### 四次挥手

### 用户数据协议 UDP（User Datagram Protocol）

## 网络层

### IP（Internet Protocol）

### ICMP

## 网络接口层（数据链路层，物理层）

## IO

## Reactor 和 Proactor

## Netty