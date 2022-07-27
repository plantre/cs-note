## 特点

高吞吐率

消息持久化

完全分布式

在线流处理和离线批处理

## zk作用

Broker 注册

Topic注册

生产者负载均衡生产者负载均衡

消费者负载均衡

分区 与 消费者 的关系

消息消费进度 Offset 记录

消费者注册

## kafka架构

### Broker

#### Topic

### 生成过程

#### 写入方式

每条消息都被 append 到 patition 中，属于 顺序写磁盘

#### 分区 Partition

每个 Partition 中的消息都是有序的，生产的消息被不断追加到 Partition log 上

##### 分区原则

指明 partition 的情况下，直接将指明的值直接作为 partiton 值

没有指明 partition 值但有 key 的情况下，将 key 的 hash 值与 topic 的 partition 数进行取余得到 partition 值

既没有 partition 值又没有 key 值的情况下，第一次调用时随机生成一个整数（后面每次调用在这个整数上自增），将这个值与 topic 可用的 partition 总数取余得到 partition 值

#### Kafka 文件存储机制

生产者跟消费者都是面向 topic 的，topic 只是逻辑上的概念，而 Partition 是物理上的概念，每个 Partition 对应个 log 文件

Topic，Partition（=1log），log（=多个seg），segment

.log文件

.index文件

### 如何保证消息顺序执行

#### 顺序错乱

 Consumer 内部进行多线程消费

不同的消费者去消费，但是每个 Consumer 的执行时间是不固定的，无法保证先读到消息的 Consumer 一定先完成操作

#### 解决办法

确保同一个消息发送到同一个 partition，一个topic，一个partition，一个consumer，内部单线程消费

给信息指定一个key，key相同则一定写入同一个partition

在1的基础上，在一个 Consumer 上根据信息ID映射到不同队列，以此加速消费

## 数据可靠性

### 消息传递语义

at most once：最多一次

at least once：至少一次

exactly once：精确传递一次

### 信息从生产者到 Broker

#### 生产者信息发送至Broker

producer 从 ZK 找到目标 Partition 的 Leader 元数据

producer 发送消息给 Leader

Leader 接受消息持久化，然后根据acks配置选择如何同步Follower

Followder 按照前面说的同步数据后给Leader回复ack

Leader 跟 Follower 同步完毕后 Leader 给 producer 回复 ack

###### request.required.acks

= 0，producer不等待 broker 的ack

 = 1（默认值），producer 等待 broker 的 ack

= -1 / all，producer 等待 broker 的 ack，partition 的 leader 和 follower (ISR中的)全部落盘成功后才返回 ack

#### 如何保证幂等性

At Least Once + 幂等性 = Exactly Once

启动幂等性，在生产者参数中 enable.idompotence= true

幂等性无法保证跨分区会话的 Exactly Once

### Kafka Broker 信息落磁盘

producer.type

###### sync，默认模式

数据必须最终落盘才算OK

###### async，异步模式

数据刷新到OS的 Page Cache就返回，此时如果机器突然出问题，信息就丢失了



### 消费者从 Kafka Broker 消费数据

由一个或者多个消费者组成一个组

先提交 offset 再处理数据可能在处理数据时出现异常导致数据丢失

先处理数据再提交 offset， 如果提交 offset 失败可能导致信息重复消费

## Kafka 分区分配策略

### RangeAssignor 范围分区策略（默认）

Partitions数 / Consumer数，除不尽，那么前面几个消费者将会多消费 1 个分区

### RoundRobinAssignor 轮询分区策略

## Kafka 高效读写

### 顺序读写数据

### Memory Mapped Files 内存映射文件

### Zero Copy

#### 直接内存存取 DMA

外部设备不通过CPU而直接与系统内存交换数据的接口技术

数据传输的时候只用到了 DMA 传输而没经过 CPU 复制数据，为零拷贝 Zero Copy

### Batch Deal

消费者拉取数据的时候，Kafka 不是一个一个的来送数据的，而是批量发送来处理的，这样可以节省网络传输，增大系统的TPS

缺点就是不是真正的实时处理的

## Rebalance

### 触发

#### 组成员个数发生变化

新的消费者加入到消费组

消费者主动退出消费组

消费者被动下线。比如消费者长时间的GC, 网络延迟导致消费者长时间未向Group Coordinator发送心跳请求, 均会认为该消费者已经下线并踢出

#### 订阅的Topic的Consumer Group个数发生变化

#### Topic 的分区数发生变化

### 过程

Join Group

Sync Group

## 常见问题

### 为什么会出现重复消费的问题

##### 生产者

可能会重复推送一条数据到 MQ 中，也许是一个 Controller 接口被重复调用了 2 次，没有做接口幂等性导致的

也可能是推送消息到 MQ 时响应比较慢，生产者的重试机制导致再次推送了一次消息

##### MQ

在消费者消费完一条数据响应 ack 信号消费成功时，MQ 突然挂了，导致 MQ 以为消费者还未消费该条数据，MQ 恢复后再次推送了该条消息

##### 消费者

消费者已经消费完了一条消息，正准备但是还未给 MQ 发送 ack 信号时，此时消费者挂了，服务重启后 MQ 以为消费者还没有消费该消息，再次推送了该条消息

### 如何保证消息队列的幂等性

重复消费问题通常是在消费者端解决

###### 状态判断法

消费者消费数据后把消费数据记录在 redis 中，下次消费时先到 redis 中查看是否存在该消息，存在则表示消息已经消费过，直接丢弃消息

###### 业务判断法

使用数据库的唯一性约束防止重复消费。每次消费直接尝试插入数据，如果提示唯一性字段重复，则直接丢失消息

### 如何保证消息的可靠性传输

#### 生产者弄丢了数据

选择用 RabbitMQ 提供的事务功能，就是生产者发送数据之前开启 RabbitMQ 事务 channel.txSelect

消息没有成功被 RabbitMQ 接收到，生产者会收到异常报错，此时就可以回滚事务 channel.txRollback

收到了消息，那么可以提交事务 channel.txCommit



#### RabbitMQ 弄丢了数据

必须开启 RabbitMQ 的持久化，消息写入之后会持久化到磁盘，哪怕是 RabbitMQ 自己挂了，恢复之后会自动读取之前存储的数据，一般数据不会丢

创建 queue 的时候将其设置为持久化

发送消息的时候将消息的 deliveryMode 设置为 2

同时设置这两个持久化才行

#### 消费端弄丢了数据

用 RabbitMQ 提供的 ack 机制，关闭 RabbitMQ 的自动 ack

确保处理完，再ack

### 怎么保证从消息队列里拿到的数据按顺序执行

拆分多个queue，每个queue一个consumer，就是多一些queue而已

就一个queue但是对应一个consumer，然后这个consumer内部使用内存队列做排队，然后分发给底层不同的worker来处理

### 如何解决消息队列的延时以及过期失效问题？消息队列满了以后该怎么处理？有几百万消息持续积压几小时，说说怎么解决？

临时紧急扩容

先修复 consumer 的问题，确保其恢复消费速度，然后将现有 consumer 都停掉

新建一个 topic，partition是原来的 10 倍，临时建立好原先 10 倍的 queue 数量

然后写一个临时的分发数据的 consumer程序，这个程序部署上去消费积压的数据，消费之后不做耗时的处理，直接均匀轮询写入临时建立好的 10 倍数量的 queue

接着临时征用 10 倍的机器来部署 consumer，每一批 consumer 消费一个临时 queue 的数据

等快速消费完积压数据之后，得恢复原先部署的架构，重新用原先的 consumer 机器来消费消息

### 让你来开发一个消息队列中间件

设计个分布式的系统，参照一下kafka的设计理念，broker -> topic -> partition

mq的数据落地磁盘

mq的可用性

多副本 -> leader & follower -> broker挂了重新选举leader即可对外服务。

能不能支持数据0丢失