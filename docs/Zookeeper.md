## ZooKeeper = 文件系统 + 监听通知机制

ZooKeeper 的过半机制导致不可能产生 2 个 leader，因为少于等于一半是不可能产生 leader 的

Zookeeper是一个分布式协调系统，满足CP性，跟SpringCloud中的Eureka满足AP不一样

## 文件系统

持久化目录节点 PERSISTENT

持久化顺序编号目录节点 PERSISTENT_SEQUENTIAL

临时目录节点 EPHEMERAL

临时顺序编号目录节点 EPHEMERAL_SEQUENTIAL

### 监听通知机制

Watcher 监听机制

当数据发生变化的时候，  Zookeeper  会产生一个 Watcher 事件，并且会发送到客户端。但是客户端只会收到一次通知。

### 特点

集群

高可用

全局数据一致

更新请求顺序进行

数据更新原子性

实时性

基于观察者设计模式

### 功能

数据发布/订阅

分布式锁

负载均衡

命名服务

分布式协调/通知

集群管理

### Leader选举

启动时选举

运行时选举