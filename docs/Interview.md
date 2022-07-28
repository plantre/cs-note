# 基础

排序算法了解哪些，快排，快排复杂度，优化，堆排序，建堆过程

nio讲讲，实现原理，优缺点

了解netty么，讲讲netty的设计模型，架构，使用场景

io模型了解么，多路复用，selete，poll，epoll，epoll的结构，怎么注册事件，et和lt模式

tcp和udp的报文结构了解么

tcp和udp的区别，tcp怎么保证可靠连接的，出现网络拥塞怎么解决

socket了解么，tcp和udp的实现区别，不了解，用的不多

讲讲你最熟悉的技术，jvm，mysql，redis，具体哪方面

怎么理解用户态，内核态，为什么要分级别，有几种转换的方式，怎么转换的，转换失败怎

http请求头，expire，cache-control字段，状态码，301，302，401，403

https原理，数字签名，数字证书，非对称加密算法过程，有什么问题

tcp连接client和server有哪些状态，time_wait状态

虚拟内存，虚拟地址和物理地址怎么转换，内存分段，内存分页，优缺点

linux最多可以建立多少个tcp连接，client端，server端，超过了怎么办

函数a调用函数b的过程，是怎么传参的

.java里面的函数调用有哪些，io流里面有函数调用么

fork函数，父子进程的区别，孤儿进程，僵尸进程会有什么问题，进程有哪些状态，进程间怎么同步，通信，消息队列，管道怎么实现的，进程调度算法，各有什么优缺点

dos攻击，ddos攻击，drdos攻击，怎么解决，syn flood

自旋锁，线程上下文切换的开销具体是什么，中断，有哪些中断，用户态和内核态切换过程

# MySQL

mysql日志文件有哪些，分别介绍下作用

Innodb的结构了解么，磁盘页和缓存区是怎么配合，以及查找的，缓冲区和磁盘数据不一致怎么办，mysql突然宕机了会出现数据丢失么

mysql事务，acid，实现原理，脏读，脏写，隔离级别，实现原理，mvcc，幻读，间隙锁原理，什么情况下会使用间隙锁，锁失效怎么办，其他锁了解么，行锁，表锁

mysql索引左前缀原理，怎么优化，哪些字段适合建索引，索引有什么优缺点

线上遇到过慢查询么，怎么定位，优化的，explain，using filesort表示什么意思，产生原因，怎么解决

mysql中有一个索引(a,b,c)，有一条sql，where a = 1 and b > 1 and c =1;可以用到索引么，为什么没用到，B+树的结构，为什么不用红黑树，B树，一千万的数据大概多少次io

mysql聚簇索引，覆盖索引，底层结构，主键索引，没有主键怎么办，会自己生成主键为什么还要自定义主键，自动生成的主键有什么问题

mysql多事务执行会产生哪些问题，怎么解决这些问题

分库分表做过么，怎么做到不停机扩容，双写数据丢失怎么办，跨库事务怎么解决

mysql主从复制，主从延时怎么解决

线上有遇到大流量的情况么，产生了什么问题，为什么数据库2000qps就撑不住了，有想过原因么，你们当时怎么处理的

一张大表怎么更改表的数据结构，字段，用alter会有什么问题，怎么解决呢，有什么好的方案，双写的话会有什么问题，还有其他方案么

做过分库分表么，为什么要分库分表，会有什么问题，多少数据适合分库分表，跨库，聚合操作怎么做

你们建表会定义自增id么，为什么，自增id用完了怎么办

一般你们怎么建mysql索引，基于什么原则，遇到过索引失效的情况么，怎么优化的

使用过事务么，遇到过事务失效的情况么，原因是什么

# Redis

你们项目为什么用redis，快在哪，怎么保证高性能，高并发的

redis字典结构，hash冲突怎么办，rehash，负载因子

redis集群，为什么是16384，哨兵模式，选举过程，会有脑裂问题么，raft算法，优缺点

redis字符串实现，sds和c区别，空间预分配

redis有序集合怎么实现的，跳表是什么，往跳表添加一个元素的过程，添加和获取元素，获取分数的时间复杂度，为什么不用红黑树，红黑树有什么特点，左旋右旋操作

redis缓存穿透，布隆过滤器，怎么使用，有什么问题，怎么解决这个问题

redis分布式锁，过期时间怎么定的，如果一个业务执行时间比较长，锁过期了怎么办，怎么保证释放锁的一个原子性，你们redis是集群的么，讲讲redlock算法

redis线程模型，单线程有什么优缺点，为什么单线程能保证高性能，什么情况下会出现阻塞，怎么解决

你们用的redis集群么，扩容的过程，各个节点间怎么通信的

怎么保证redis和mysql的一致性，redis网络原因执行超时了会执行成功么，那不成功怎么保证数据一致性

.redis持久化过程，aof持久化会出现阻塞么，一般什么情况下使用rdb，aof

你们用redis么，用来做什么，什么场景使用的，遇到过什么问题，怎么解决的

redis管道用过么，用来做什么，它的原理是，保证原子性么，和事务的区别，redis事务保证原子性么

redis强一致性么，怎么保证强一致性，有什么方案

怎么统计一亿用户的日活，hyperloglog有什么缺点，bitmap不行么

redis的几种数据类型，你们用过哪些，zset有用来做什么

redis实现分布式锁，还有其他方式么，zookeeper怎么实现，各有什么有缺点，你们为什么用redis实现

# Java

hashmap原理，put和get，为什么是8转红黑树，红黑树节点添加过程，什么时候扩容，为什么是0.75，扩容步骤，为什么分高低位，1.7到1.8有什么优化，hash算法做了哪些优化，头插法有什么问题，为什么线程不安全

arraylist原理，为什么数组加transient，add和get时间复杂度，扩容原理，和linkedlist区别，原理，分别在什么场景下使用，为什么

new Object[100]对象大小，它的一个对象引用大小，对象头结构

怎么理解异常，它的作用是什么，你们工作中是怎么使用的

反射了解么，原理是什么

treemap和linkdedhashmap区别，实现原理

# 并发

线程安全的类有哪些，平时有使用么，用来解决什么问题

锁升级过程，轻量锁可以变成偏向锁么，偏向锁可以变成无锁么，自旋锁，对象头结构，锁状态变化过程

synchronized原理，怎么保证可重入性，可见性，抛异常怎么办，和lock锁的区别，2个线程同时访问synchronized的静态方法，2个线程同时访问一个synchronized静态方法和非静态方法，分别怎么进行

volatile作用，原理，怎么保证可见性的，内存屏障

你了解那些锁，乐观锁和悲观锁，为什么读要加锁，乐观锁为什么适合读场景，写场景不行么，会有什么问题，cas原理

什么情况下产生死锁，怎么排查，怎么解决

了解哪些并发工具类

reentrantlock的实现原理，加锁和释放锁的一个过程，aqs，公平和非公平，可重入，可中断怎么实现的

concurrenthashmap原理，put，get，size，扩容，怎么保证线程安全的，1.7和1.8的区别，为什么用synchronized，分段锁有什么问题，hash算法做了哪些优化

threadlocal用过么，什么场景下使用的，原理，hash冲突怎么办，扩容实现，会有线程安全问题么，内存泄漏产生原因，怎么解决

线程有哪些状态，等待状态怎么产生，死锁状态的变化过程，中止状态，interrupt()方法

你怎么理解线程安全，哪些场景会产生线程安全问题，有什么解决办法

while(true)里面一直new thread().start()会有什么问题

线程池原理，核心参数，线程数设置，参数动态调整后变化过程，Tomcat线程池原理，常用的线程池，你们一般使用哪种，为什么，会有什么问题，线程抛异常怎么办，阻塞队列原理

# JVM

jvm了解哪些参数，用过哪些指令

jvm类加载器，自定义类加载器，双亲委派机制，优缺点，tomcat类加载机制

tomcat热部署，热加载了解么，怎么做到的

cms收集器过程，g1收集器原理，怎么实现可预测停顿的，region的大小，结构

内存溢出，内存泄漏遇到过么，什么场景产生的，怎么解决的

垃圾收集算法，各有什么优缺点，gc roots有哪些，什么情况下会发生full gc

对象一定分配在堆上么，JIT，分层编译，逃逸分析

jvm元空间内存结构，永久代有什么问题

jvm类加载的过程讲讲，符号引用是什么，哪些情况会发生初始化

垃圾收集器，cms垃圾收集过程，为什么停顿时间短，有什么缺点，concurrent mode failure怎么办，内存碎片怎么解决，为什么不用标记整理法

jvm内存结构，堆结构，栈结构，a+b操作数栈过程，方法返回地址什么时候回收，程序计数器什么时候为空

什么对象会进入老年代，eden和survivor比例可以调整么，参数是什么，调整后会有什么问题

# Spring

对spring aop的理解，解决什么问题，实现原理，jdk动态代理，cglib区别，优缺点，怎么实现方法的调用的

spring的循环依赖，怎么解决的，为什么需要加个三级缓存，二级不行么

springboot有什么特点，相比与spring，了解springboot的自动装配的一个原理么

spring你比较了解哪方面，讲讲，生命周期，bean创建过程

springboot是怎么加载类的，通过什么方式



# 分布式微服务

eureka原理，强一致性么，为什么，怎么保证强一致性，多级缓存怎么保证一致性，eureka集群，宕机了服务还能调用么

hystrix原理，半开状态知道么，具体的一个转换过程，它的隔离是怎么实现的

怎么理解分布式和微服务，为什么要拆分服务，会产生什么问题，怎么解决这些问题

怎么理解高可用，如何保证高可用，有什么弊端，熔断机制，怎么实现

dubbo和spring cloud区别，具体区别，分别什么场景使用

给了几个场景解决分布式事务问题

对于高并发怎么看，怎么算高并发，你们项目有么，如果有会产生什么问题，怎么解决

一致性hash原理，解决什么问题，数据倾斜，为什么是2的32次方，20次方可以么

怎么理解幂等性，有遇到过实际场景么，怎么解决的，为什么用redis，redis过期了或者数据没了怎么办

容器化技术了解么，主要解决什么问题，原理是什么

# Zookeeper

zookeeper的基本原理，数据模型，znode类型，应用场景有哪些

zookeeper一致性保证，zab协议原理，半数原则如果查询到另外一半呢，那zookeeper属于哪种一致性，强一致性么，还是最终一致性

zookeeper选举机制，选举过程有什么问题

zookeeper读写数据过程

# Kafka

kafka重平衡，重启服务怎么保证kafka不发生重平衡，有什么方案

你们用的什么消息中间件，kafka，为什么用kafka，高吞吐量，怎么保证高吞吐量的，设计模型，零拷贝

kafka是怎么保证高可用性的，讲讲它的设计架构，为什么读写都在主分区，这样有什么优缺点

你们为什么要用mq，遇到过什么问题么，怎么就解决的

kafka怎么保证消息不丢失的

kafka支持事务么，你们项目中有使用么，它的原理是什么

# ES

es的写入，查询过程，底层实现，为什么这么设计

es集群，脑裂问题，怎么产生的，如何解决

es倒排索引，原理，lucene，分词，分片，副本

es写数据原理，数据实时么，为什么不实时，会丢数据么，segment，cache，buffer，translog关系

es深度分页，优化

# 线上问题&思想&设计题

你平时开发中怎么解决问题，假如现在线上有一个告警，你的解决思路，过程

了解哪些设计模式，工厂，策略，装饰者，桥接模式讲讲，单例模式会有什么问题

了解DDD么，不是很了解

限流怎么做，如果让你设计一个限流系统，怎么实现

微信朋友圈设计，点赞，评论功能实现，拉黑呢，redis数据没了怎么办

一个热榜功能怎么设计，怎么设计缓存，如何保证缓存和数据库的一致性

设计一个秒杀系统能承受千万级并发，如果redis也扛不住了怎么办