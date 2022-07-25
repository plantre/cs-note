## 理论

主键（主码）

外键（外码）外键是另一表的主键

### 范式

###### 1NF(第一范式)

###### 2NF(第二范式)

###### 3NF(第三范式)

### 语句分类

###### DDL - Data Definition Language-数据库定义语言

不需要commit,CREAT,ALTER,DROP

###### DML - Data Manipulation Language-数据库操作语言

SELECT,UPDATE,INSERT,DELETE

###### DCL - Data Control Language-数据库控制语言

授权，角色控制等 GRANT,REVOKE

###### TCL - Transaction Control Language-事务控制语言

COMMIT,ROLLBACK

### 删除

###### drop(丢弃数据)-DDL

drop table 表名，表结构会被删除

###### truncate (清空数据)-DDL

truncate table 表名

###### delete（删除数据）-DML

delete from 表名 where 列名=值



## 存储引擎

### MyISAM（5.5以前默认存储引擎）

表级锁(table-level locking)，不提供事务支持，不支持外键，不支持数据库异常崩溃后的安全恢复

### InnoDB（事务性数据库引擎）

行级锁(row-level locking)（默认）和表级锁，提供事务支持，支持外键

#### 二进制日志

binlog（归档日志）-Server层

###### statement

###### row

###### mix

#### 事务日志（InnoDB 引擎）

redo log(重做日志)-保证事务的持久性

undo log(回滚日志) -保证事务的原子性



锁机制、MVCC-保证事务的隔离性

MVCC 可以看作是行级锁的一个升级，可以有效减少加锁操作，提高性能

#### 锁的算法

###### Record lock-记录锁

###### Gap lock-间隙锁

###### Next-key lock-临键锁

#### 内存结构详解

##### Buffer Pool（LRU算法）

MySQL 会先改内存，然后记录 redo log，等有空了再刷磁盘，如果内存里没有数据，就去磁盘 load，以「页」（page）为单位从磁盘读取数据的，Buffer Pool 里的数据也是如此，实际上，Buffer Pool 是a linked list of pages，一个以页为元素的链表

##### 写缓冲 Change Buffer

如果 MySQL 发现你要修改的页，不在内存里，就把你要对页的修改，先记到一个叫 Change Buffer 的地方，同时记录 redo log，然后再慢慢把数据 load 到内存，load 过来后，再把 Change Buffer 里记录的修改，应用到内存（Buffer Pool）中

merge：Change Buffer -> Buffer Pool

purge：Buffer Pool -> Disk

##### 自适应hash索引（Adaptive Hash Index）

子会监控对表上各索引页的查询，如果观察到建立hash索引可以提高查询速度，则自动建立hash索引1

##### Log Buffer

使大型事务可以运行，而无需在事务提交之前将redo日志数据写入磁盘，节省了磁盘I/O

#### 磁盘结构

##### Tables

##### Indexes

##### Tablespaces

##### Doublewrite Buffer

Change Buffer 是提升性能，那么 Doublewrite Buffer 就是保证数据页的可靠性

MySQL 在刷数据到磁盘之前，要先把数据写到另外一个地方，也就是 Doublewrite Buffer，写完后，再开始写磁盘，Doublewrite Buffer 可以理解为是一个备份（recovery），万一真的发生 crash，就可以利用 Doublewrite Buffer 来修复磁盘里的数据

##### Redo Log

##### Undo Logs

##### 写缓冲 Change Buffer

## 事务

### 数据库事务

###### 原子性（Atomicity）

事务是最小的执行单位，不允许分割

###### 一致性（Consistency）

转账者和收款人的总额应该是不变的

###### 隔离性（Isolation）

各并发事务之间数据库是独立的

###### 持久性（Durability）

它对数据库中数据的改变是持久的

### 并发事务带来问题

###### 脏读（Dirty read）

###### 丢失修改（Lost to modify）

###### 不可重复读（Unrepeatable read）

###### 幻读（Phantom read）

### 事务隔离级别

#### READ-UNCOMMITTED(读取未提交)

可能会导致脏读、幻读或不可重复读

#### READ-COMMITTED(读取已提交）——大部分数据库系统

可以阻止脏读，但是幻读或不可重复读仍有可能发生

#### REPEATABLE-READ(可重复读)—（MySQL默认）

可以阻止脏读和不可重复读，但幻读仍有可能发生，加锁使用 Next-Key Locks避免幻读

#### SERIALIZABLE(可串行化)——分布式事务下用到

可以防止脏读、不可重复读以及幻读



## 锁

### 基于属性

#### 共享锁（Share Lock）(S锁)

`SELECT ... LOCK IN SHARE MODE`

其他事务只能对该数据加读锁，而不能加写锁

读数据的时候不支持修改，避免重复读

#### 排它锁（eXclusive Lock）(X锁)

`SELECT ... FOR UPDATE`

其他事务不能为数据加任何锁

不允许其他人同时修改，不允许其他人读取，避免了出现出现脏数据和脏读

### 基于粒度

#### 表锁

`LOCK TABLES ... READ`

`LOCK TABLES ... WRITE`

#### 行锁

#### 记录锁（Record Lock）

唯一索引命中

避免重复读：数据在查询的时候被修改

避免脏读：在修改的事务未提交前被其他事务读取

#### 间隙锁（Gap Lock）

左开右闭  (]

范围查询并且未命中记录，查询条件必须命中索引

只会出现在REPEATABLE_READ事务级别中

防止幻读问题

#### 临键锁（Next-Key Lock）

InnoDB行锁默认算法

范围查询并命中，查询命中了索引

避免了脏读，重复读，幻读

### 基于状态

#### 意向共享锁

#### 意向排它锁





## 索引

### 索引结构

#### B-Tree

#### B+Tree（InnoDB）

#### Hash

### 按功能划分

#### 主键索引

#### 唯一索引

#### 普通索引

#### 全文索引

### 物理分类

#### 聚簇索引（只有主键索引是）

#### 非聚簇索引（辅助索引，二级索引）



## 日志

### 二进制日志

### 事务日志（InnoDB 引擎）



## 多机