## 集合

### Collection

#### List

##### ArrayList（数组）

##### LinkedList（链表）

##### Vector（数组实现、线程同步）

#### Set

##### HashSet（Hash 表）(底层由HashMap实现)

##### TreeSet（二叉树）

底层红黑树，支持自定义排序规则

##### LinkedHashSet（HashSet+LinkedHashMap）

#### Queue（单端队列，满足FIFO）

##### Deque

###### LinkedList(基于链表)

基于链表，支持存储Null，1.2存在，每次申请堆空间

###### ArrayDeque(基于数组)

基于可变长数组和双指针，不支持存储NULL，1.6引入，可能扩容，实现队列更好好

##### PriorityQueue（基于堆）

优先级最高的元素先出队，利用了二叉堆，时间复杂度logn，不支持存储 NULL 和 non-comparable 的对象，默认是小顶堆，可接收 Comparator 定义元素优先级的先后

### Map

##### HashMap（数组+链表+红黑树)

（可以存储 null 的 k和 v，容量初始16，2n，指定大小将其扩充为 2 的幂次方大小）使用 Collections.synchronizedMap() 方法来包装我们的 HashMap，底层数据结构是数组+链表/红黑树，负载因子0.75，超过size*0.75就扩容，只有大小为2次幂时，才能合理用位运算替代取模（高效），数组>64&&链表>8,链表变红黑树，红黑树<6，红黑树变链表，取余(%)操作中如果除数是 2 的幂次则等价于与其除数减一的与(&)操作（也就是说 hash%length==hash&(length-1)的前提是 length 是 2 的 n 次方；）。” 并且 采用二进制位操作 &，相对于%能够提高运算效率，

##### TreeMap（可排序）

红黑树，可排序，通过Comparator排序，对集合中的元素根据键排序的能力以及对集合内元素的搜索的能力

##### LinkedHashMap（记录插入顺序）

底层数据结构是数组+链表/红黑树+双向链表，插入有序

##### HashTable（安全）

方法基本都经过synchronized 修饰，被淘汰，不允许有 null 键和 null 值，容量初始11，2n+1，使用 synchronized 来保证线程安全，效率非常低下

### Collections

#### 排序

reverse(List list)//反转

sort(List list)//按自然排序的升序排序

sort(List list, Comparator c)//定制排序

#### 查找替换

fill(List list, Object obj)//用指定的元素代替指定list中的所有元素

replaceAll(List list, Object oldVal, Object newVal)//用新元素替换旧元素

#### 同步控制

synchronizedXxx()



## 并发编程

### JUC(java.util.concurrent)

#### ConcurrentHashMap（高效）

##### 1.7

jdk1.7采用分段的Segment 数组 + HashEntry 数组 + 链表，对整个桶数组进行了分割分段(Segment)，每一把锁只锁容器其中一部分数据，Segment 实现了 ReentrantLock是一种可重入锁，Segment 的结构和 HashMap 类似，是一种数组和链表结构，一个 Segment 包含一个 HashEntry 数组，每个 HashEntry 是一个链表结构的元素

##### 1.8

jdk1.8采用数组+链表/红黑树，用 Node 数组 + 链表 / 红黑树的数据结构来实现，并发控制使用 synchronized 和 CAS 来操作，synchronized 只锁定当前链表或红黑二叉树的首节点

#### CopyOnWriteArrayList

#### HashSet（底层哈希表，基于HashMap）

#### LinkedHashSet(底层链表和哈希表，插入取出顺序满足FIFO)

#### ConcurrentLinkedQueue

#### BlockingQueue

##### ArrayBlockingQueue

##### LinkedBlockingQueue

##### PriorityBlockingQueue

#### ConcurrentSkipListMap







### synchronized

解决的是多个线程之间访问资源的同步性，可以保证被它修饰的方法或者代码块在任意时刻只能有一个线程执行，重量级锁，效率低下

监视器锁（monitor）是依赖于底层的操作系统的 Mutex Lock 来实现的，JDK1.6 对锁的实现引入了大量的优化，如自旋锁、适应性自旋锁、锁消除、锁粗化、偏向锁、轻量级锁等技术来减少锁操作的开销

##### 无锁状态

当一个线程访问同步块时升级成偏向锁，会在 Mark Word 里存储锁偏向的线程 ID

##### 偏向锁状态

有锁竞争时升级为轻量级锁，偏向锁只需要在置换 ThreadID 的时候依赖一次 CAS 原子指令

##### 轻量级锁状态

自旋十次失败升级为重量级锁，轻量级锁的获取及释放依赖多次 CAS 原子指令

##### 重量级锁状态

后续线程尝试获取锁时，发现被占用的锁是重量级锁，则直接将自己挂起（而不是忙等），等待将来被唤醒

**锁可以升级不可降级，这种策略是为了提高获得锁和释放锁的效率**

##### 修饰实例方法

当前对象实例加锁

并没有 monitorenter 指令和 monitorexit 指令，取得代之的确实是 ACC_SYNCHRONIZED 标识，该标识指明了该方法是一个同步方法

##### 修饰静态方法

给当前类加锁

##### 修饰代码块

synchronized(this|object) 表示进入同步代码库前要获得给定对象的锁。synchronized(类.class) 表示进入同步代码前要获得 当前 class 的锁

使用的是 monitorenter 和 monitorexit 指令，Monitor 是基于 C++实现的，由ObjectMonitor实现的，每个对象中都内置了一个 ObjectMonitor对象，wait/notify等方法也依赖于monitor对象，只有在同步的块或者方法中才能调用wait/notify等方法

**构造方法本身就属于线程安全的，不能使用 synchronized 关键字修饰**

##### 原理

JVM层面，本质都是对对象监视器 monitor 的获取

##### 双重校验锁实现对象单例（线程安全）


```java
public class Singleton {
    private volatile static Singleton uniqueInstance;
    private Singleton() {
    
    }

    public  static Singleton getUniqueInstance() {
       //先判断对象是否已经实例过，没有实例化过才进入加锁代码
        if (uniqueInstance == null) {
            //类对象加锁
            synchronized (Singleton.class) {
                if (uniqueInstance == null) {
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
```

##### 和 ReentrantLock 的区别

两者都是可重入锁,自己可以再次获取自己的内部锁，同一个线程每次获取锁，锁的计数器都自增 1

synchronized 依赖于 JVM 而 ReentrantLock 依赖于 API,ReentrantLock 是 JDK 层面实现的，需要 lock() 和 unlock() 方法配合 try/finally 语句块来完成

###### ReentrantLock 比 synchronized 增加了一些高级功能

等待可中断,lock.lockInterruptibly()，正在等待的线程可以选择放弃等待

可实现公平锁,synchronized只能是非公平锁，ReentrantLock默认情况是非公平的

**可实现选择性通知**（锁可以绑定多个条件）,

synchronized关键字与wait()和notify()/notifyAll()方法相结合可以实现等待/通知机制，而synchronized关键字就相当于整个 Lock 对象中只有一个Condition实例，如果执行notifyAll()方法的话就会通知所有处于等待状态的线程这样会造成很大的效率问题

ReentrantLock类当然也可以实现，但是需要借助于Condition接口与newCondition()方法，而Condition实例的signalAll()方法 只会唤醒注册在该Condition实例中的所有等待线程。

### ReentrantLock



### volatile

#### JMM(Java 内存模型)

##### 主内存

##### 本地内存

每个线程都有一个私有的本地内存来存储共享变量的副本，并且，每个线程只能访问自己的本地内存，无法访问其他线程的本地内存

#### 原理

对于用volatile修饰的变量，对其写操作时会额外执行一个Lock指令

1.将该处理器的工作内存写回到主内存

2.使其他处理器的工作内存失效

#### 作用

##### 防止 JVM 的指令重排，保证变量的可见性

在单线程环境下，编译器和处理器为了提高运行速度，会在不影响运行结果的前提下对指令进行重排序

#### synchronized 和 volatile 是互补非对立

volatile 关键字是线程同步的轻量级实现，性能肯定比synchronized关键字要好，volatile 关键字只能用于变量，synchronized 关键字可以修饰方法以及代码块

volatile 关键字能保证数据的可见性，但不能保证数据的原子性，synchronized 关键字两者都能保证

volatile关键字主要用于解决变量在多个线程之间的可见性，而 synchronized 关键字解决的是多个线程之间访问资源的同步性



### 三个重要特性

#### 原子性

synchronized 可以保证代码片段的原子性

#### 可见性

volatile 关键字可以保证共享变量的可见性

#### 有序性

volatile 关键字可以禁止指令进行重排序优化

### ThreadLocal

#### 原理

最终的变量是放在了当前线程的 ThreadLocalMap 中，并不是存在 ThreadLocal 上，ThreadLocal 可以理解为只是ThreadLocalMap的封装，传递了变量值

#### 内存泄漏

ThreadLocalMap 中使用的 key 为 ThreadLocal 的弱引用,而 value 是强引用，在调用 set()、get()、remove() 方法的时候，会清理掉 key 为 null 的记录，使用完 ThreadLocal方法后 最好手动调用remove()方法

### 线程池

#### 为什么用

降低资源消耗

提高响应速度

提高线程的可管理性

#### Runnable 和 Callable

Runnable 接口 不会返回结果或抛出检查异常，Callable 接口 可以

工具类 Executors 可以实现将 Runnable 对象转换成 Callable 对象

####  execute()方法和 submit()方法

execute()方法用于提交不需要返回值的任务，submit()方法用于提交需要返回值的任务，线程池会返回一个 Future 类型的对象

#### 创建（不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor ）

##### 构造方法ThreadPoolExecutor（）

###### corePoolSize

核心线程数

###### maximumPoolSize

最大线程数

###### workQueue

当前运行的线程数量达到核心线程数，新任务就会被存放在队列中

###### keepAliveTime

线程池中的线程数量大于 corePoolSize 的时候，核心线程外的线程等待时间超过了 keepAliveTime才会被回收销毁

###### unit

keepAliveTime 参数的时间单位

###### threadFactory

executor 创建新线程的时候会用到

###### handler

饱和策略，当前同时运行的线程数量达到最大线程数量并且队列也已经被放满了任务时

ThreadPoolExecutor.

AbortPolicy（默认）,抛出 RejectedExecutionException来拒绝新任务的处理

CallerRunsPolicy,调用执行自己的线程运行任务

DiscardPolicy,不处理新任务，直接丢弃掉

DiscardOldestPolicy,丢弃最早的未处理的任务请求

##### 通过 Executor 框架的工具类 Executors 

###### FixedThreadPool

返回一个固定线程数量的线程池

###### SingleThreadExecutor

返回一个只有一个线程的线程池

###### CachedThreadPool

返回一个可根据实际情况调整线程数量的线程池

### Atomic原子类

#### 基本类型

##### AtomicInteger

主要利用 CAS (compare and swap) + volatile 和 native 方法来保证原子操作

##### AtomicLong

##### AtomicBoolean

#### 数组类型

##### AtomicIntegerArray

##### AtomicLongArray

##### AtomicReferenceArray

#### 引用类型

##### AtomicReference

##### AtomicStampedReference

原子更新带有版本号的引用类型。该类将整数值与引用关联起来，可用于解决原子的更新数据和数据的版本号，可以解决使用 CAS 进行原子更新时可能出现的 ABA 问题。

##### AtomicMarkableReference

原子更新带有标记位的引用类型

### AQS（AbstractQueuedSynchronizer）

java.util.concurrent.locks,一个用来构建锁和同步器的框架

#### Semaphore(信号量)

synchronized 和 ReentrantLock 都是一次只允许一个线程访问某个资源，Semaphore(信号量)可以指定多个线程同时访问某个资源

#### CountDownLatch （倒计时器）

可以让某一个线程等待直到倒计时结束，再开始执行

#### CyclicBarrier(循环栅栏)

让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，每个线程调用 await() 方法告诉 CyclicBarrier 我已经到达了屏障