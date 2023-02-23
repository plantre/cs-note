## 手写消费者生产者模式

```java
//仓库
public class Storage {
    private static int MAX_VALUE = 100;
    private List<Object> list = new ArrayList<>();

    public void produce(int num) {
        synchronized (list) {
            //一定是while，因为wait被唤醒后需要判断是不是满足生产条件；仓库剩余的容量不足以存放即将要生产的数量，暂停生产；要注意，notify唤醒沉睡的线程后，线程会接着上次的执行继续往下执行。所以在进行条件判断时候，可以先把 wait 语句忽略不计来进行考虑，显然，要确保程序一定要执行，并且要保证程序直到满足一定的条件再执行，要使用while来执行，以确保条件满足和一定执行
            while (list.size() + num > MAX_VALUE) {
                System.out.println("暂时不能执行生产任务");
                try {
                    //条件不满足，生产阻塞，会释放当前的锁，然后让出CPU，进入等待状态
                    list.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            //满足条件后开始生产
            for (int i = 0; i < num; i++) {
                list.add(new Object());
            }
            System.out.println("已生产产品数"+num+" 仓库容量"+list.size());
            //只有当 notify/notifyAll() 被执行时候，才会唤醒一个或多个正处于等待状态的线程，然后继续往下执行，直到执行完synchronized 代码块的代码或是中途遇到wait() ，再次释放锁。
            list.notifyAll();
        }
    }
    
    public void consume(int num) {
        synchronized (list) {
            while (list.size() < num) {
                System.out.println("暂时不能执行消费任务");

                try {
                    list.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            //满足条件后开始消费
            for (int i = 0; i < num; i++) {
                list.remove(0);
            }
            System.out.println("已消费产品数"+num+" 仓库容量" + list.size());
            list.notifyAll();
        }
    }
}
```

```java
public class Producer extends Thread {
    private int num;
    private Storage storage;

    public Producer(Storage storage) {
        this.storage = storage;
    }

    public int getNum() {
        return num;
    }

    public void setNum(int num) {
        this.num = num;
    }

    public void run() {
        storage.produce(getNum());
    }
}
```

```java
public class Customer extends Thread {
    private int num;
    private Storage storage;

    public Customer(Storage storage) {
        this.storage = storage;
    }

    public int getNum() {
        return num;
    }

    public void setNum(int num) {
        this.num = num;
    }

    public void run() {
        storage.consume(getNum());
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Storage storage = new Storage();
        Producer p1 = new Producer(storage);
        Producer p2 = new Producer(storage);
        Producer p3 = new Producer(storage);
        Producer p4 = new Producer(storage);
        
        Customer c1 = new Customer(storage);
        Customer c2 = new Customer(storage);
        Customer c3 = new Customer(storage);

        p1.setNum(10);
        p2.setNum(20);
        p3.setNum(10);
        p4.setNum(80);

        c1.setNum(50);
        c2.setNum(20);
        c3.setNum(20);

        c1.start();
        c2.start();
        c3.start();

        p1.start();
        p2.start();
        p3.start();
        p4.start();

    }
}
```

## 多线程交替打印ABC的多种实现方法

```java
public class ABC_Synch {
    public static class ThreadPrinter implements Runnable {
        private String name;
        private Object prev;
        private Object self;

        private ThreadPrinter(String name, Object prev, Object self) {
            this.name = name;
            this.prev = prev;
            this.self = self;
        }

        @Override
        public void run() {
            int count = 10;
            while (count > 0) {// 多线程并发，不能用if，必须使用whil循环
                synchronized (prev) { // 先获取 prev 锁
                    synchronized (self) {// 再获取 self 锁
                        System.out.print(name);// 打印
                        count--;
                        self.notifyAll();// 唤醒其他线程竞争self锁，注意此时self锁并未立即释放。
                    }
                    // 此时执行完self的同步块，这时self锁才释放。
                    try {
                        if (count == 0) {// 如果count==0,表示这是最后一次打印操作，通过notifyAll操作释放对象锁。
                            prev.notifyAll();
                        } else {
                            prev.wait(); // 立即释放 prev锁，当前线程休眠，等待唤醒
                        }
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }

    public static void main(String[] args) throws Exception {
        Object a = new Object();
        Object b = new Object();
        Object c = new Object();
        ThreadPrinter pa = new ThreadPrinter("A", c, a);
        ThreadPrinter pb = new ThreadPrinter("B", a, b);
        ThreadPrinter pc = new ThreadPrinter("C", b, c);

        new Thread(pa).start();
        Thread.sleep(10);// 保证初始ABC的启动顺序
        new Thread(pb).start();
        Thread.sleep(10);
        new Thread(pc).start();
        Thread.sleep(10);
    }
}
```

###### Lock锁方法

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class ABC_Lock {
    private static Lock lock = new ReentrantLock();// 通过JDK5中的Lock锁来保证线程的访问的互斥
    private static int state = 0;//通过state的值来确定是否打印

    static class ThreadA extends Thread {
        @Override
        public void run() {
            for (int i = 0; i < 10;) {
                try {
                    lock.lock();
                    while (state % 3 == 0) {// 多线程并发，不能用if，必须用循环测试等待条件，避免虚假唤醒
                        System.out.print("A");
                        state++;
                        i++;
                    }
                } finally {
                    lock.unlock();// unlock()操作必须放在finally块中
                }
            }
        }
    }

    static class ThreadB extends Thread {
        @Override
        public void run() {
            for (int i = 0; i < 10;) {
                try {
                    lock.lock();
                    while (state % 3 == 1) {// 多线程并发，不能用if，必须用循环测试等待条件，避免虚假唤醒
                        System.out.print("B");
                        state++;
                        i++;
                    }
                } finally {
                    lock.unlock();// unlock()操作必须放在finally块中
                }
            }
        }
    }

    static class ThreadC extends Thread {
        @Override
        public void run() {
            for (int i = 0; i < 10;) {
                try {
                    lock.lock();
                    while (state % 3 == 2) {// 多线程并发，不能用if，必须用循环测试等待条件，避免虚假唤醒
                        System.out.print("C");
                        state++;
                        i++;
                    }
                } finally {
                    lock.unlock();// unlock()操作必须放在finally块中
                }
            }
        }
    }

    public static void main(String[] args) {
        new ThreadA().start();
        new ThreadB().start();
        new ThreadC().start();
    }
}
```

## 手写阻塞队列

```java
public class AxinBlockQueue {
    //队列容器
    private List<Integer> container = new ArrayList<>();
    private volatile int size;
    private volatile int capacity;
    private Lock lock = new ReentrantLock();
    //Condition
    private final Condition isNull = lock.newCondition();
    private final Condition isFull = lock.newCondition();

    AxinBlockQueue(int cap) {
        this.capacity = cap;
    }

    public void add(int data) {
        try {
            lock.lock();
            try {
                while (size >= capacity) {
                    System.out.println("阻塞队列满了");
                    isFull.await();
                }
            } catch (InterruptedException e) {
                isFull.signal();
                e.printStackTrace();
            }
            ++size;
            container.add(data);
            isNull.signal();
        } finally {
            lock.unlock();
        }
    }

    public int take() {
        try {
            lock.lock();
            try {
                while (size == 0) {
                    System.out.println("阻塞队列空了");
                    isNull.await();
                }
            } catch (InterruptedException e) {
                isNull.signal();
                e.printStackTrace();
            }
            --size;
            int res = container.get(0);
            container.remove(0);
            isFull.signal();
            return res;
        } finally {
            lock.unlock();
        }
    }
}
```

```java
public static void main(String[] args) {
    AxinBlockQueue queue = new AxinBlockQueue(5);
    Thread t1 = new Thread(() -> {
        for (int i = 0; i < 100; i++) {
            queue.add(i);
            System.out.println("塞入" + i);
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });
    Thread t2 = new Thread(() -> {
        for (; ; ) {
            System.out.println("消费"+queue.take());
            try {
                Thread.sleep(800);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

    });
    t1.start();
    t2.start();
}
```

## 线程池

```java
import java.util.HashSet;
import java.util.Set;
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;
 
public class MySelfThreadPool {
	private static final int WORK_NUM = 5;//默认线程池中的线程的数量
	private static final int TASK_NUM = 100;	//默认处理任务的数量
	private int workNum;//线程数量
	private int taskNum;//任务数量
	private final Set<WorkThread> workThreads;//保存线程的集合
	private final BlockingQueue<Runnable> taskQueue;//阻塞有序队列存放任务
	
	public MySelfThreadPool() {
		this(WORK_NUM, TASK_NUM);
	}
 
	public MySelfThreadPool(int workNum, int taskNum) {
		if (workNum <= 0) workNum = WORK_NUM;
		if (taskNum <= 0) taskNum = TASK_NUM;
		taskQueue = new ArrayBlockingQueue<>(taskNum);
		this.workNum = workNum;
		this.taskNum = taskNum;
		workThreads = new HashSet<>();
		//启动一定数量的线程数，从队列中获取任务处理
		for (int i=0;i<workNum;i++) {
			WorkThread workThread = new WorkThread("thead_"+i);
			workThread.start();
			workThreads.add(workThread);
		}
	}
	
	//线程池执行任务的方法，其实就是往BlockingQueue中添加元素
	public void execute(Runnable task) {
		try {
			taskQueue.put(task);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	public void destroy() {
		System.out.println("ready close thread pool...");
		if (workThreads == null || workThreads.isEmpty()) return ;
		for (WorkThread workThread : workThreads) {
			workThread.stopWork();
			workThread = null;//help gc
		}
		workThreads.clear();
	}
	
	//线程池中的工作线程，直接从BlockingQueue中获取任务然后执行任务而已 blockQueue为阻塞队列
	private class WorkThread extends Thread{
		public WorkThread(String name) {
			super();
			setName(name);
		}
	
		@Override
		public void run() {
			while (!interrupted()) {
				try {
					Runnable runnable = taskQueue.take();//获取任务
					if (runnable !=null) {
						System.out.println(getName()+" ready execute:"+runnable.toString());
						runnable.run();//执行任务
					}
					runnable = null;//help gc
				} catch (Exception e) {
					interrupt();
					e.printStackTrace();
				}
			}
		}
		
		public void stopWork() {
			interrupt();
		}
	}
	
}
```

```java

public class TestMySelfThreadPool {	
	private static final int TASK_NUM = 50;//任务的个数

	public static void main(String[] args) {
		MySelfThreadPool myPool = new MySelfThreadPool(3,50);
		for (int i=0;i<TASK_NUM;i++) {
			myPool.execute(new MyTask("task_"+i));
		}
		
	}
	
	static class MyTask implements Runnable{
		private String name;
		public MyTask(String name) {
			this.name = name;
		}
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
		@Override
		public void run() {
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			System.out.println("task :"+name+" end...");
		}
		@Override
		public String toString() {
			// TODO Auto-generated method stub
			return "name = "+name;
		}
	}
}
```

