## 为什么那么快

### 基于内存实现

### 高效的数据结构

### 丰富而合理的编码

### 写时拷贝（CopyOnWrite）

内核只为新生成的子进程创建虚拟空间结构，它们来复制于父进程的虚拟空间结构，但是不为这些段分配物理内存

### 零拷贝技术

直接从 PageCache 中把数据复制到 Socket 缓冲区中，不用把数据复制到用户内存空间，DMA 控制器可以直接完成数据复制，不需要 CPU 参与，速度更快

### 客户端管道批量命令

### 单线程

### 合适的线程模型

I/O 多路复用

### Redis6.0后引入多线程提速

## 数据结构

### string

适用于简单key-value存储、setnx key value实现分布式锁、计数器(原子性)、分布式全局唯一ID

底层：用char[]数组表示，源码中用SDS(simple dynamic string)封装char[]，这是是Redis存储的最小单元，一个SDS最大可以存储512M信息，

Redis对SDS再次封装生成了RedisObject，type = REDIS_STRING,Redis会创建键的RedisObject 和 值的RedisOjbect 

自动存储int类型，非int类型用raw编码

###### OBJ_ENCODING_EMBSTR

Redis针对短字符串的优化

内存申请和释放都只需要调用一次内存操作函数

redisObject、sdshdr结构保存在一块连续的内存中，减少了内存碎片

###### OBJ_ENCODING_RAW

长度大于OBJ_ENCODING_EMBSTR_SIZE_LIMIT的字符串，在该编码中，redisObject、sds结构存放在两个不连续的内存块中

###### OBJ_ENCODING_INT

将数值型字符串转换为整型，可以大幅降低数据占用的内存空间

#### sds

获取字符串长度复杂度为O(1),因为前期已经将字符串的长度写在len这个变量里面了

SDS在修改的时候，Redis的API会主动将SDS的大小扩展到执行修改所需要的大小，然后才执行实际的修改操作

C 字符串 只能保存文本数据,比如“\0”

通过空间预分配和惰性空间释放两种方法，搭配len和free两个变量，来实现对内存分配策略的优化,减少修改字符串时带来的内存重分配次数

1.C字符串中，更改字符串长度，内存就会重新分配，而SDS不需要每次都进行重新分配

2.空间预分配,当SDS的API对一个SDS进行修改，并且需要对SDS进行空间扩展的时候，程序不仅会为SDS分配修改所必须要的空间，还会为SDS分配额外的未使用空间

小于1MB，*2

于等于1MB,=1m

### list

lpush + lpop = stack 先进后出的栈 

lpush + rpop = queue 先进先出的队列

lpush + ltrim = capped collection 有限集合

lpush + brpop = message queue 消息队列

字符串长度且元素个数小于一定范围使用 ziplist 编码，否则转化为 linkedlist 编码

### hash

#### 渐进式rehash

rehashidx != -1则表示扩容到数组中的第几个了

#### load_factor = ht[0].used / ht[0].size

###### <0.1收缩

###### >=1

###### >=5,服务器目前正在执行BGSAVE命令或者BGREWRITEAOF命令

K是字符串，V是多种

### set

保存元素为整数及元素个数小于一定范围使用 intset 编码，任意条件不满足，则使用 hashtable 编码

### zset

底层：跳表，查找数据时，是从上往下，从左往右进行查找。

保存的元素个数小于定值且成员长度小于定值使用 ziplist 编码，任意条件不满足，则使用 skiplist 编码

###### 应用

积分排行榜

时间排序新闻

延时队列



### Redis Geo

GEO利用 GeoHash 将二维的经纬度转换成字符串，来实现位置的划分跟指定距离的查询

### HyperLogLog

### bitmap

###### 应用

用户签到

统计活跃用户

### Bloom Filter

不存在的一定不存在，存在的不一定存在

## 持久化

### 快照（snapshotting，RDB）

### 只追加文件（append-only file, AOF）



## 高可用

### 单机

架构简单，部署方便

机器故障、容量瓶颈、QPS瓶颈

### 主从复制（6379）

高可靠性，读写分离

故障恢复复杂，主库的写跟存受单机限制

#### 一主一从

slaveof/slaveof no one

从节点只读	slave-read-only = yes

网络延迟	repl-disable-tcp-nodelay no	当开启时，主节点会合并较小的 TCP 数据包从而节省带宽

#### 一主多从

replicaof/info replication

#### 树状主从结构

### 哨兵模式-Redis Sentinel （26379）

###### 优点：

集群部署简单，HA

###### 缺点：

原理繁琐，slave存在资源浪费，不能解决读写分离问题

当主节点出现故障时，Redis Sentinel 能自动完成故障发现和故障转移，并通知应用方，从而实现真正的高可用

redis-server sentinel.conf --sentinel 

监控

通知

主节点故障转移

配置提供者

#### 脑裂问题

因为网络原因，导致master节点、slave节点 和 sentinel集群处于不用的网络分区，因为sentinel集群无法感知到master的存在，所以将slave节点提升为master节点

## 高并发

### 集群

数据动态存储solt，可扩展，高可用

客户端动态感知后端变更，批量操作支持查

`redis-cli --cluster create 192.168.153.128:6379 192.168.153.131:6379 192.168.153.132:6379`

`redis-cli --cluster add-node 192.168.153.129:6379 192.168.153.128:6379 --cluster-slave --cluster-master-id a7e948208badf171d19dbfe2d444ea7295bdbf60`

`redis-cli --cluster check 192.168.153.128:6379 --cluster-search-multiple-owners`

`redis-cli --cluster info 192.168.153.128:6379`

#### 为什么RedisCluster设计成16384个槽

如果槽位为65536，发送心跳信息的消息头达8k，发送的心跳包过于庞大。
如上所述，在消息头中，最占空间的是myslots[CLUSTER_SLOTS/8]。
当槽位为65536时，这块的大小是:
65536÷8÷1024=8kb

redis的集群主节点数量基本不可能超过1000个

槽位越小，节点少的情况下，压缩率高

## 常见问题

### 缓存雪崩

Redis中大批量key在同一时间同时失效导致所有请求都打到了MySQL

##### 解决

缓存数据的过期时间加上个随机值

如果缓存数据库是分布式部署，将热点数据均匀分布在不同得缓存数据库中

设置热点数据永远不过期

### 缓存穿透

指缓存和数据库中都没有的数据

##### 解决

后端接口层增加 用户鉴权校验，参数做校验等

单个IP每秒访问次数超过阈值直接拉黑IP

从缓存取不到的数据，在数据库中也没有取到，这时也可以将key-value对写为key-null 失效时间可以为15秒防止恶意攻击

用Redis提供的  Bloom Filter 特性也OK

### 缓存击穿

大并发集中对这一个热点key进行访问，当这个Key在失效的瞬间，持续的大并发就穿破缓存，直接请求数据库

##### 解决

设置热点数据永远不过期

加上互斥锁

### 双写一致性

先更新数据库，再更新缓存

先删缓存，再更新数据库

######  Cache Aside Pattern

更新 DB，然后直接删除 cache 

### 事务

### 过期策略和内存淘汰策略

#### 过期策略

定时过期

惰性过期

定期过期

#### 内存淘汰策略

在Redis的用于缓存的内存不足时，怎么处理需要新写入且需要申请额外空间的数据

volatile-lru

volatile-ttl

volatile-random

allkeys-lru

allkeys-random

no-enviction

### 消息通知

### 管道



