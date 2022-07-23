## CAP&BASE

## Paxos 算法

## Leader选举

### Raft 算法

### Zab协议（ZooKeeper Atomic Broadcast 原子广播）

### 具体到组件

#### etcd

使用raft算法，比较currentTerm，三个状态Leader、Follower、Candidate

#### Redis 的 Sentinel 

使用 Raft 算法

#### kafka

#### zk

使用ZXID、myid来选举，ZAB协议，Leader、Follower，超过半数，选举（崩溃恢复）和同步数据（消息广播）

## 一致性

### 强一致性

### 弱一致性 

### 最终一致性 

## 分布式事务

## 分布式锁

## 分布式ID

## 高并发

### 消息队列

### 缓存

### 读写分离&分库分表

### 负载均衡

#### 一致性Hash

## 高可用

### 限流

### 降级

### 熔断

### 集群

## 高性能