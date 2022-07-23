## 集合

### Collection

#### List

##### ArrayList（数组）

##### LinkedList（链表）

##### Vector（数组实现、线程同步）

#### Set

##### HashSet（Hash 表）(底层由HashMap实现)

##### TreeSet（二叉树）

##### LinkHashSet（HashSet+LinkedHashMap）

#### Queue（单端队列，满足FIFO）

##### Deque

###### LinkedList(基于链表)

###### ArrayDeque(基于数组)

##### PriorityQueue（基于堆）

### Map

##### HashMap（数组+链表+红黑树)

##### TreeMap（可排序）

##### LinkHashMap（记录插入顺序）

##### HashTable

### Collections

#### 排序

reverse(List list)//反转

sort(List list)//按自然排序的升序排序

sort(List list, Comparator c)//定制排序

#### 查找替换

fill(List list, Object obj)//用指定的元素代替指定list中的所有元素

replaceAll(List list, Object oldVal, Object newVal)//用新元素替换旧元素



## 并发编程

### JUC(java.util.concurrent)

### synchronized

### ReentrantLock

### volatile

### 三个重要特性

### ThreadLocal

### 线程池

### Atomic原子类

### AQS（AbstractQueuedSynchronizer）

java.util.concurrent.locks