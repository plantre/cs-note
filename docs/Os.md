OS是管理计算机硬件与软件资源的程序，是计算机的基石

## 进程管理

### 系统调用

#### 用户态(user mode)

#### 核心态(kernel mode)

### 状态

#### 创建状态(new)

#### 就绪状态(ready)

一旦得到处理器资源(处理器分配的时间片)即可运行

#### 运行状态(running)

#### 阻塞状态(waiting)

进程正在等待某一事件而暂停运行，即使处理器空闲，该进程也不能运行。

#### 结束状态(terminated)

### 通信方式

#### 管道/匿名管道(Pipes)

具有亲缘关系的父子进程间或者兄弟进程之间，无名管道：只存在于内存中的文件；

#### 有名管道(Names Pipes)

有名管道以磁盘文件的方式存在，可以实现本机任意两个进程通信，命名管道：存在于实际的磁盘介质或者文件系统

#### 信号(Signal)

用于通知接收进程某个事件已经发生

#### 消息队列(Message Queuing)

消息队列是消息的链表,具有特定的格式,存放在内存中并由消息队列标识符标识。存放在内核中，只有在内核重启(即，操作系统重启)或者显式地删除一个消息队列，克服了信号承载信息量少，管道只能承载无格式字 节流以及缓冲区大小受限等缺

#### 信号量(Semaphores)

信号量是一个计数器，用于多进程对共享数据的访问

#### 共享内存(Shared memory) 

最有用的进程间通信方式

#### 套接字(Sockets)

不同主机之间的进程进行双向通信

### 同步方式

#### 互斥量(Mutex)

采用互斥对象机制，只有拥有互斥对象的线程才有访问公共资源的权限，如 Java 中的 synchronized 关键词和各种 Lock 

#### 信号量(Semphares)

允许同一时刻多个线程访问同一资源

#### 事件(Event)

通知操作的方式来保持多线程同步

### 调度算法

#### 先到先服务(FCFS)调度算法

#### 短作业优先(SJF)的调度算法

#### 时间片轮转调度算法

最古老，最简单，最公平且使用最广的算法

#### 多级反馈队列调度算法

既能使高优先级的作业得到响应又能使短作业（进程）迅速完成，被公认的一种较好的进程调度算法，UNIX 操作系统采取的便是这种调度算法

#### 优先级调度

具有相同优先级的进程以 FCFS 方式执行

### 死锁

#### 互斥

资源一次只有一个进程可以使用

#### 占有并等待

一个进程至少应该占有一个资源，并等待另一被其他进程所占有的资源

#### 非抢占

资源不能被抢占，

#### 循环等待

Pn 等待的资源被 P0 占有



四大条件仅仅是产生死锁的必要条件

### 解决死锁

#### 预防，考虑破坏第二个条件和第四个条件

##### 静态分配策略

一个进程必须在执行前就申请到它所需要的全部资源，并且知道它所要的资源都得到满足之后才开始执行

##### 层次分配策略

一个进程得到某一次的一个资源后，它只能再申请较高一层的资源；当一个进程要释放某层的一个资源时，必须先释放所占用的较高层的资源

### 避免（ Dijkstra 的银行家算法）

将系统的状态分为 安全状态 和 不安全状态，若把系统资源分配给申请者会产生死锁，则拒绝分配，否则接受申请

### 检测，

进程-资源分配图中无环路，则此时系统没有发生死锁

进程-资源分配图中有环路，且每个资源类仅有一个资源，则系统中已经发生了死锁

进程-资源分配图中有环路，且涉及到的资源类有多个资源，此时系统未必会发生死锁

### 解除

立即结束所有进程的执行，重新启动操作系统

撤销涉及死锁的所有进程，解除死锁后继续运行

逐个撤销涉及死锁的进程，回收其资源直至死锁解除

抢占资源 

## 内存管理

### 连续分配

#### 块式管理

### 非连续分配

页是物理单位，段是逻辑单位，分页机制和分段机制都是为了提高内存利用率，减少内存碎片，页和段都是离散存储的，页的大小是固定的，由操作系统决定；而段的大小不固定，取决于我们当前运行的程序

#### 页式管理

##### 主存分为大小相等且固定的一页一页的形式

##### 快表（TLB是MMU的一部分）

根据虚拟地址中的页号查快表；

如果该页在快表中，直接从快表中读取相应的物理地址；

如果该页不在快表中，就访问内存中的页表，再从页表中得到物理地址，同时将页表中的该映射表项添加到快表中；

当快表填满后，又要登记新页时，就按照一定的淘汰策略淘汰掉快表中的一个页

##### 多级页表

#### 段式管理

把主存分为一段段的，段是有实际意义的，每个段定义了一组逻辑信息

#### 段页式管理

把主存先分成若干段，每个段又分成若干页

### 虚拟内存

定义了一个连续的虚拟地址空间，并且把内存扩展到硬盘空间

#### 页面置换算法

##### FIFO（First In First Out） 页面置换算法（先进先出页面置换算法）

##### OPT 页面置换算法（最佳页面置换算法）

##### LRU （Least Recently Used）页面置换算法（最近最久未使用页面置换算法）

##### LFU （Least Frequently Used）页面置换算法（最少使用页面置换算法）

## 文件管理

## IO管理