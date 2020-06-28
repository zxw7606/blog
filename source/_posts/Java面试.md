---
thumbnail: /images/java.jpeg
title: Java面试
tags: Java
categories:
  - 面试
  - Java
widgets:
  - type: recent_posts
    position: right
  - type: toc
    position: right
toc: true
date: 2020-06-20 13:13:38
---

<!-- more -->

## 基础篇

### Q1. hashCode 与 equals 的区别

A:会根据当前对象的hashCode()方法生成的Hash码来查找在散列表key中映射的已存在的对象,再利用Equals()方法来判断相等,加快

- 如果两个对象相等，则 hashcode 一定也是相同的
- 两个对象相等,对两个对象分别调用 equals 方法都返回 true
- 两个对象有相同的 hashcode 值，它们也不一定是相等的
- equals 方法被覆盖过，则 hashCode 方法也必须被覆盖
- hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

<!-- more -->

测试
```java

public class Test {


    public static void main(String[] args) {
        T t = new T(1,"a");
        T t1 = new T(1, "a");
        HashSet<T> ts = new HashSet<>();
        ts.add(t);
        ts.add(t1);
        // 如果没有覆盖HashCode()方法,2个对象即便是属性全部相同,HashSet也会同时存在
        System.out.println(ts);

        boolean equals = t.equals(t1); // true
        System.out.println(equals);
    }

    static class T{
        private Integer a;
        private String b;

        public T(Integer a, String b) {
            this.a = a;
            this.b = b;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            T t = (T) o;
            return Objects.equals(a, t.a) &&
                    Objects.equals(b, t.b);
        }
    }

}

```

### Q2. 当 try 语句和 finally 语句中都有 return 语句时，在方法返回之前，finally 语句的内容将被执行，并且 finally 语句的返回值将会覆盖原始的返回值吗
A: √


### Q3. 集合的使用

![集合类型](https://panjier0409.github.io/tuchuang/img/img20200620151424.png)


```java
public class CollectionTest {

    private List<Integer> integerList;


    @Before
    public void setUp(){
        integerList = Arrays.asList(-1, 2, 3 ,9);
    }


    /**
     * 反转
     */
    @Test
    public void reverseTest(){
        Collections.reverse(integerList);
        System.out.println(integerList);
    }


    /**
     * 随即
     */
    @Test
    public void shuffleTest(){
        Collections.shuffle(integerList);
        System.out.println(integerList);
    }

    /**
     * 排序,默认升序
     */
    @Test
    public void sortTest(){
        Collections.sort(integerList);
        System.out.println(integerList);
    }

    /**
     * 排序,默认升序
     */
    @Test
    public void swapTest(){
        System.out.println(integerList);
        Collections.swap(integerList, 0, 2);
        System.out.println(integerList);
    }

    /**
     * 排序,默认升序
     */
    @Test
    public void rotateTest(){
        System.out.println("before = " + integerList);
        Collections.rotate(integerList, -1);
        System.out.println("after = " + integerList);
    }


    /**
     * 排序,默认升序
     */
    @Test
    public void binarySearchTest(){
        int i = Collections.binarySearch(integerList, 3);
        System.out.println(i);
    }

    /**
     * 排序,默认升序
     */
    @Test
    public void maxTest(){
        Integer max = Collections.max(integerList);
        System.out.println(max);
    }



    /**
     * 排序,默认升序
     */
    @Test
    public void fillTest(){
        Collections.fill(integerList, 0);
        System.out.println(integerList);
    }

    /**
     * 排序,默认升序
     */
    @Test
    public void synchronizedTest(){
        List<Integer> synchronizedList = Collections.synchronizedList(integerList);
        synchronizedList.get(0);
    }
}
```


## 线程篇


### Q1. ThreadPoolExecutor 类的重要参数 

A: `    public ThreadPoolExecutor(int corePoolSize, // 保留在池中的线程数，即使它们处于空闲状态
                               int maximumPoolSize, // 池中允许的最大线程数
                               long keepAliveTime, // 当线程数大于内核数时，这是多余的空闲线程将在终止之前等待新任务的最长时间。
                               TimeUnit unit, // 参数的时间单位
                               BlockingQueue<Runnable> workQueue // 在执行任务之前用于保留任务的队列。 此队列将仅保存execute方法提交的Runnable任务。
                               ) {
         this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
              Executors.defaultThreadFactory(), defaultHandler);`

### Q2. 为什么不能使用Executors.newFixedThreadPool和newCachedThreadPool

A: 

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
     return new ThreadPoolExecutor(nThreads, nThreads,
                                   0L, TimeUnit.MILLISECONDS,
                                   new LinkedBlockingQueue<Runnable>()); // 该队列没有数量的限制
 }

public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE, // 该队列没有线程数量的限制
                                 60L, TimeUnit.SECONDS,
                                 new SynchronousQueue<Runnable>()); 
}
```

### Q3. 线程池的Worker




![Worker](https://panjier0409.github.io/tuchuang/img/img20200620192047.png)

可见继承了AQS同步锁类

<pre> 
  <code>
     public void execute(Runnable command) {
            if (command == null)
                throw new NullPointerException();
            /*
             * 进行3个步骤：
             *
             * 1.如果正在运行的线程少于corePoolSize线程，请尝试执行以下操作：
             * 使用给定的命令作为第一个启动新线程
             * 任务。 对addWorker的调用自动检查runState和
             * workerCount，这样可以防止假警报增加
             * 通过返回false返回不应该执行的线程。
             *
             * 2.如果任务可以成功排队，那么我们仍然需要
             * 仔细检查我们是否应该添加线程
             * （因为现有的自上次检查后死亡）或
             * 自从进入此方法以来，该池已关闭。 所以我们
             * 重新检查状态，并在必要时回退排队
             * 停止，如果没有，则启动一个新线程。
             *
             * 3.如果我们无法将任务排队，那么我们尝试添加一个新的
             * 线。 如果失败，我们知道我们已经关闭或饱和
             * 因此拒绝任务。
             */
            int c = ctl.get();
            if (workerCountOf(c) < corePoolSize) {
                // worker添加后会直接开始
                if (<b style="color: red">addWorker(command, true)</b>)
                    return;
                c = ctl.get();
            }
            // 如果核心线程满了的话 添加到工作队列
            if (isRunning(c) && workQueue.offer(command)) {
                int recheck = ctl.get();
                if (! isRunning(recheck) && remove(command))
                    reject(command);
                // 如果worker数量为0的?
                else if (workerCountOf(recheck) == 0)
                    addWorker(null, false);
            }
            else if (!addWorker(command, false))
                reject(command);
        }
  </code>

</pre>




<pre>
  <code>
     private boolean addWorker(Runnable firstTask, boolean core) {
            retry:
            for (;;) {
                int c = ctl.get();
                //  运行状态 c & ~CAPACITY 
                int rs = runStateOf(c);
    
                // Check if queue empty only if necessary.
                if (rs >= SHUTDOWN &&
                    ! (rs == SHUTDOWN &&
                       firstTask == null &&
                       ! workQueue.isEmpty()))
                    return false;
    
                for (;;) {
                    // c & CAPACITY
                    int wc = workerCountOf(c);
                    if (wc >= CAPACITY ||
                        wc >= (core ? corePoolSize : maximumPoolSize))
                        return false;
                    // 原子操作:如果能增加Worker数量,停止
                    if (compareAndIncrementWorkerCount(c))
                        break retry;
                    // 如果Worker数量
                    c = ctl.get();  // Re-read ctl
                    if (runStateOf(c) != rs)
                        continue retry;
                    // else CAS failed due to workerCount change; retry inner loop
                }
            }
    
            boolean workerStarted = false;
            boolean workerAdded = false;
            Worker w = null;
            try {
                // 创建一个Worker(线程)
                w = new Worker(firstTask);
                final Thread t = w.thread;
                if (t != null) {
                    // 线程池的锁
                    final ReentrantLock mainLock = this.mainLock;
                    mainLock.lock();
                    try {
                        // Recheck while holding lock.
                        // Back out on ThreadFactory failure or if
                        // shut down before lock acquired.
                        // 重新检查状态
                        int rs = runStateOf(ctl.get());
                        // 如果是运行状态
                        if (rs < SHUTDOWN ||
                            (rs == SHUTDOWN && firstTask == null)) {
                            // 如果线程已经非法开始
                            if (t.isAlive()) // precheck that t is startable
                                throw new IllegalThreadStateException();
                            // worker添加到HashSet中
                            workers.add(w);
                            int s = workers.size();
                            if (s > largestPoolSize)
                                largestPoolSize = s;
                            workerAdded = true;
                        }
                    } finally {
                        mainLock.unlock();
                    }
                    if (workerAdded) {
                        t.start();
                        workerStarted = true;
                    }
                }
            } finally {
                if (! workerStarted)
                    addWorkerFailed(w);
            }
            return workerStarted;
        }
  </code>
</pre>

```java

    // 1110 0000 00000000 00000000 00000000
    private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
    // 29
    private static final int COUNT_BITS = Integer.SIZE - 3;
    // 0001 1111 11111111 111111111 11111111
    private static final int CAPACITY   = (1 << COUNT_BITS) - 1;

    // runState is stored in the high-order bits
    // 1110 0000 00000000 00000000 00000000
    private static final int RUNNING    = -1 << COUNT_BITS;
    // 0000 0000 00000000 00000000 00000000
    private static final int SHUTDOWN   =  0 << COUNT_BITS;
    // 0010 0000 00000000 00000000 00000000
    private static final int STOP       =  1 << COUNT_BITS;
    // 0100 0000 00000000 00000000 00000000
    private static final int TIDYING    =  2 << COUNT_BITS;
    // 0110 0000 00000000 00000000 00000000
    private static final int TERMINATED =  3 << COUNT_BITS;

    // Packing and unpacking ctl
    private static int runStateOf(int c)     { return c & ~CAPACITY; }
    private static int workerCountOf(int c)  { return c & CAPACITY; }
    private static int ctlOf(int rs, int wc) { return rs | wc; }

```

Worker运行状态
<pre>
  <code>
  final void runWorker(Worker w) {
          // Worker线程
          Thread wt = Thread.currentThread();
          // Worker线程的第一个Runable
          Runnable task = w.firstTask;
          w.firstTask = null;
          w.unlock(); // allow interrupts
          boolean completedAbruptly = true;
          try {
              while (task != null || (task = getTask()) != null) {
                  // task执行的时候会锁住当前线程
                  w.lock();
                  // If pool is stopping, ensure thread is interrupted;
                  // if not, ensure thread is not interrupted.  This
                  // requires a recheck in second case to deal with
                  // shutdownNow race while clearing interrupt
                  // 尝试停止Worker
                  if ((runStateAtLeast(ctl.get(), STOP) ||
                       (Thread.interrupted() &&  // 如果线程未被中断一直是false,并且清空中断状态为false
                        runStateAtLeast(ctl.get(), STOP))) &&
                      !wt.isInterrupted())
                      wt.interrupt();
                  try {
                      // 执行自定义的Task之前的函数
                      beforeExecute(wt, task);
                      Throwable thrown = null;
                      try {
                          task.run();
                      } catch (RuntimeException x) {
                          thrown = x; throw x;
                      } catch (Error x) {
                          thrown = x; throw x;
                      } catch (Throwable x) {
                          thrown = x; throw new Error(x);
                      } finally {
                          // 自定义函数
                          afterExecute(task, thrown);
                      }
                  } finally {
                      task = null;
                      w.completedTasks++;
                      w.unlock();
                  }
              }
              completedAbruptly = false;
          } finally {
              <b style="color: red">processWorkerExit(w, completedAbruptly);</b>
          }
      }
  </code>
</pre>


<pre>
  <code>
      private void processWorkerExit(Worker w, boolean completedAbruptly) {
          if (completedAbruptly) // If abrupt, then workerCount wasn't adjusted
              decrementWorkerCount();
  
          final ReentrantLock mainLock = this.mainLock;
          mainLock.lock();
          try {
              completedTaskCount += w.completedTasks;
              // 移除Worker线程
              workers.remove(w);
          } finally {
              mainLock.unlock();
          }
          // 尝试终止
          <b style="color: red">tryTerminate();</b>
  
          int c = ctl.get();
          // 如果还在运行?
          if (runStateLessThan(c, STOP)) {
              if (!completedAbruptly) {
                  // 如果允许核心线程闲置超时
                  int min = allowCoreThreadTimeOut ? 0 : corePoolSize;
                  if (min == 0 && ! workQueue.isEmpty())
                      min = 1;
                  if (workerCountOf(c) >= min)
                      return; // replacement not needed
              }
              addWorker(null, false);
          }
      }
  </code>
</pre>


<pre>
  <code>
    final void tryTerminate() {
            for (;;) {
                int c = ctl.get();
                // 如果还在跑的话
                // 否则如果已经中断的话
                // 否则运行状态为终止但是工作队列不为空的话
                if (isRunning(c) ||
                    runStateAtLeast(c, TIDYING) ||
                    (runStateOf(c) == SHUTDOWN && ! workQueue.isEmpty()))
                    return;
                // 如果工作的worker数量不为0的话
                if (workerCountOf(c) != 0) { // Eligible to terminate
                    // 之中断一个线程
                    interruptIdleWorkers(ONLY_ONE);
                    return;
                }
                // 如果为0的话,全部终止
                final ReentrantLock mainLock = this.mainLock;
                mainLock.lock();
                try {
                    // 标记死亡状态
                    if (ctl.compareAndSet(c, ctlOf(TIDYING, 0))) {
                        try {
                            // 自定义的终止回调方法
                            terminated();
                        } finally {
                            // 设置终止状态
                            ctl.set(ctlOf(TERMINATED, 0));
                            // 唤醒终止的线程队列
                            termination.signalAll();
                        }
                        return;
                    }
                } finally {
                    mainLock.unlock();
                }
                // else retry on failed CAS
            }
        }
  </code>
</pre>

可见Worker不仅继承了AQS同步锁类, 还是一个不停循环的线程, 当Worker执行完firstTask之后会从workQueue读取未工作完的task


### Q4. ReentrantLock

<pre>
  <code>
    final void lock() {
        <b style="color: red"> acquire(1);</b>
    }
    
    // 尝试获取锁或者阻塞
    public final void acquire(int arg) {
        if (<b style="color: red">!tryAcquire(arg) &&
            // 如果获取锁失败那么就加入到队列中等待获取
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg)</b>)
            selfInterrupt();
    }
    
    // 尝试获取锁
    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            // 如果队列不为null 并且 head.next 不为当前线程
            if (!hasQueuedPredecessors() &&
                // 原子操作
                compareAndSetState(0, acquires)) {
                // 设置独有线程
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        // 如果状态不为0但是独占线程为当前线程
        else if (current == getExclusiveOwnerThread()) {
            // 设置重入
            int nextc = c + acquires;
            if (nextc < 0)
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
    
    // 添加阻塞Node到队列尾部
    private Node addWaiter(Node mode) {
        // 添加一个独战锁的线程node
        Node node = new Node(Thread.currentThread(), mode);
        // Try the fast path of enq; backup to full enq on failure
        
        
        Node pred = tail;
        if (pred != null) {
            // 将node插入到队列尾部
            node.prev = pred;
            if (compareAndSetTail(pred, node)) {
                pred.next = node;
                return node;
            }
        }
        // 如果为null直接插入
        enq(node);
        return node;
    }
    
    // CAS 直到阻塞
    final boolean acquireQueued(final Node node, int arg) {
        boolean failed = true;
        try {
            boolean interrupted = false;
            for (;;) {
                // 获取node的前驱
                final Node p = node.predecessor();
                // 如果前驱节点是head 那么就再次获取锁
                if (p == head && tryAcquire(arg)) {
                    // 获取锁成功以后将节点设置为当前节点
                    // 之前的head节点让他变为悬空指针,让他消失
                    setHead(node);
                    p.next = null; // help GC
                    failed = false;
                    return interrupted;
                }
                // 如果前驱节点是SIGNAL状态的话, 需要阻塞自己
                if (<b style="color: red">shouldParkAfterFailedAcquire(p, node)</b> &&
                    // 阻塞自己
                    parkAndCheckInterrupt())
                    interrupted = true;
            }
        } finally {
            if (failed)
                cancelAcquire(node);
        }
    }
    
    private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
        int ws = pred.waitStatus;
        // 如果前驱节点是唤醒状态
        if (ws == Node.SIGNAL)
            /*
             * This node has already set status asking a release
             * to signal it, so it can safely park.
             */
            return true;
        // static final int CANCELLED =  1; 将node的前驱所有的CANCELLED节点删除
        if (ws > 0) {
            /*
             * Predecessor was cancelled. Skip over predecessors and
             * indicate retry.
             */
            do {
                node.prev = pred = pred.prev;
            } while (pred.waitStatus > 0);
            pred.next = node;
        } else {
        // ws < 0
            /*
             * waitStatus must be 0 or PROPAGATE.  Indicate that we
             * need a signal, but don't park yet.  Caller will need to
             * retry to make sure it cannot acquire before parking.
             */
            // 设置前驱为激活信号并且返回false, 在循环中再次调用该方法体,然后返回true,再之后会被阻塞
            compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
        }
        return false;
    } 
    
    
    // 释放锁的核心代码
    private void unparkSuccessor(Node node) {
        /*
         * If status is negative (i.e., possibly needing signal) try
         * to clear in anticipation of signalling.  It is OK if this
         * fails or if status is changed by waiting thread.
         */
        // 如果头指针的状态 < 0
        int ws = node.waitStatus;
        if (ws < 0)
            compareAndSetWaitStatus(node, ws, 0);

        /*
         * Thread to unpark is held in successor, which is normally
         * just the next node.  But if cancelled or apparently null,
         * traverse backwards from tail to find the actual
         * non-cancelled successor.
         */
        Node s = node.next;
        if (s == null || s.waitStatus > 0) {
            s = null;
            // 获取第一个NODE状态为SIGNAL / PROPAGATE的
            for (Node t = tail; t != null && t != node; t = t.prev)
                if (t.waitStatus <= 0)
                    s = t;
        }
        if (s != null)
            LockSupport.unpark(s.thread);
    }
  </code>
</pre>

#### acquireInterruptibly
<pre>
  <code>
    public final void acquireInterruptibly(int arg)
            throws InterruptedException {
        if (Thread.interrupted())
            // 如果当前线程被中断,终止获取锁
            throw new InterruptedException();
            // 如果获取锁失败
        if (!tryAcquire(arg))
           <b style="color: red">doAcquireInterruptibly(arg);</b>
    }
    
    private void doAcquireInterruptibly(int arg)
        throws InterruptedException {
        // 添加一个Node到链表中
        final Node node = addWaiter(Node.EXCLUSIVE);
        boolean failed = true;
        try {
            for (;;) {
                // 获取头节点
                final Node p = node.predecessor();
                if (p == head && tryAcquire(arg)) {
                    setHead(node);
                    p.next = null; // help GC
                    failed = false;
                    return;
                }
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                    throw new InterruptedException();
            }
        } finally {
            if (failed)
                cancelAcquire(node);
        }
    }
  </code>
</pre>

#### ConditionObject#addConditionWaiter

![ConditionObject的属性](https://panjier0409.github.io/tuchuang/img/img20200621103836.png)

即每一个条件有2个指针, 一个指向头部, 一个指向尾部

<pre>
  <code>
    // 阻塞策略
    public final void await() throws InterruptedException {
        if (Thread.interrupted())
            throw new InterruptedException();
        // 增加一个Node
        <b style="color: red">Node node = addConditionWaiter();</b> 
        int savedState = fullyRelease(node);
        int interruptMode = 0;
        while (!isOnSyncQueue(node)) {
            LockSupport.park(this);
            if ((interruptMode = checkInterruptWhileWaiting(node)) != 0)
                break;
        }
        if (acquireQueued(node, savedState) && interruptMode != THROW_IE)
            interruptMode = REINTERRUPT;
        if (node.nextWaiter != null) // clean up if cancelled
            unlinkCancelledWaiters();
        if (interruptMode != 0)
            reportInterruptAfterWait(interruptMode);
    }
    
    // 添加一个Node包含当前线程的到这个条件的阻塞队列中
    private Node addConditionWaiter() {
        Node t = lastWaiter;
        // If lastWaiter is cancelled, clean out.
        // 如果队尾的节点等待条件被修改成cancelled的时候
        // 可能有几种情况, 1.await(XXX时间) 
                        2.在同一时间,这个节点被唤醒了
        if (t != null && t.waitStatus != Node.CONDITION) {
            // 清空取消的waiters
            unlinkCancelledWaiters();
            t = lastWaiter;
        }
        Node node = new Node(Thread.currentThread(), Node.CONDITION);
        // 如果尾指针指向null
        if (t == null)
            // 设置为头指针
            firstWaiter = node;
        else
        // 有尾指针的话,放到最后一个
            t.nextWaiter = node;
        // 并让尾指针指向node
        lastWaiter = node;
        return node;
    }


    // 清空取消的waiters
    private void unlinkCancelledWaiters() {
        
        Node t = firstWaiter;
        Node trail = null;
        
        // 1. 从第一个waiter开始
        while (t != null) {
            Node next = t.nextWaiter;
            // 2. 如果waiter不是CONDITION条件的话
            if (t.waitStatus != Node.CONDITION) {
                // 他的之后的后继设置为null
                t.nextWaiter = null;
                // 3. 如果队列中一开始就不是CONDITION的节点,就把该节点的下一个节点设置为firstWaiter
                if (trail == null)
                    firstWaiter = next;
                else
                    trail.nextWaiter = next;
                
                // 4. 重新定义尾结点
                if (next == null)
                    lastWaiter = trail;
            }
            else
                trail = t;
            t = next;
        }
    }
  </code>
</pre>


### Q5. ConcurrentHashMap

#### Q1. ConcurrentHashMap 和 Hashtable 的区别

A: 

底层数据结构： JDK1.7 的 ConcurrentHashMap 底层采用 分段的数组+链表 实现，JDK1.8 采用的数据结构跟 HashMap1.8 的结构一样，数组+链表/红黑二叉树。Hashtable 和 JDK1.8 之前的 HashMap 的底层数据结构类似都是采用 数组+链表 的形式，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的；
实现线程安全的方式（重要）： ① 在 JDK1.7 的时候，ConcurrentHashMap（分段锁） 对整个桶数组进行了分割分段(Segment)，每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。 到了 JDK1.8 的时候已经摒弃了 Segment 的概念，而是直接用 Node 数组+链表+红黑树的数据结构来实现，并发控制使用 synchronized 和 CAS 来操作。（JDK1.6 以后 对 synchronized 锁做了很多优化） 整个看起来就像是优化过且线程安全的 HashMap，虽然在 JDK1.8 中还能看到 Segment 的数据结构，但是已经简化了属性，只是为了兼容旧版本；② Hashtable(同一把锁) :使用 synchronized 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程不能使用 put 添加元素，也不能使用 get，竞争会越来越激烈效率越低。


### Q6. ConcurrentLinkedQueue

#### offer(e) 和 poll()

<pre>
  <code>
    // 插入元素到队尾
    public boolean offer(E e) {
        checkNotNull(e);
        final Node<E> newNode = new Node<E>(e);
        // 开始指向 t = p = tail
        for (Node<E> t = tail, p = t;;) {
            // 如果插入p.next失败的话第二次会不为null
            Node<E> q = p.next;
            if (q == null) {
                // p is last node
                // 对当前节点的CAS锁
                // 只有在同一时间有两个线程同时在这个p节点插入元素,总有一个线程无法插入
                if (p.casNext(null, newNode)) {
                    // Successful CAS is the linearization point
                    // for e to become an element of this queue,
                    // and for newNode to become "live".
                    // 针对第二次插入元素
                    if (p != t) // hop two nodes at a time
                        // 对全局tail节点的CAS锁
                        // 将t赋值为newNode
                        // 失败的话意味着其他线程更新了tail, 其实也没问题, 再下一次插入的时候p再往后跳一格
                        casTail(t, newNode);  // Failure is OK.
                    return true;
                }
                // Lost CAS race to another thread; re-read next
                // 插入失败代表p.next 不为null(两种情况, 1.另一个线程插入, 2.另一个线程把队尾的节点删除掉), 下一个循环开始
            }
            else if (p == q)
                // We have fallen off list.  If tail is unchanged, it
                // will also be off-list, in which case we need to
                // jump to head, from which all live nodes are always
                // reachable.  Else the new tail is a better bet.
                p = (t != (t = tail)) ? t : head;
            // p != q
            else
                // Check for tail updates after two hops.
                // 如果t,p,tail都没有改变,p往后跳一格
                p = (p != t && t != (t = tail)) ? t : q;
        }
    }
    // 获取对头元素
    public E poll() {
        restartFromHead:
        for (;;) {
            // 初始设置h = head = p 
            for (Node<E> h = head, p = h, q;;) {
                // head 元素
                E item = p.item;
                
                // 设置为item的next为null, 第二个线程设置为null会失败
                if (item != null && p.casItem(item, null)) {
                    // Successful CAS is the linearization point
                    // for item to be removed from this queue.
                    if (p != h) // hop two nodes at a time
                        // 更新下一个节点为q, 如果p.next为null的话 就指向自己
                        // 并且更新旧的head.next 为 head
                        updateHead(h, ((q = p.next) != null) ? q : p);
                    return item;
                }
                // 如果p的下一个节点为null的话, head设置为null
                else if ((q = p.next) == null) {
                    updateHead(h, p);
                    return null;
                }
                else if (p == q)
                    continue restartFromHead;
                // p != q
                else
                    p = q;
            }
        }
    }
  </code>
</pre>


## 数据库篇

### Q1.MySQL的索引
A1. MySQL索引使用的数据结构主要有BTree索引 和 哈希索引，Hash索引（该记录重复的值不能过多）适合为单条记录查询的时候，可以选择哈希索引，查询性能最快，范围查询适合走Btree索引。
MyISAM: B+Tree叶节点的data域存放的是数据记录的地址。在索引检索的时候，首先按照B+Tree搜索算法搜索索引，如果指定的Key存在，则取出其 data 域的值，然后以 data 域的值为地址读取相应的数据记录。这被称为“非聚簇索引”。
InnoDB: 其数据文件本身就是索引文件。相比MyISAM，索引文件和数据文件是分离的，其表数据文件本身就是按B+Tree组织的一个索引结构，树的叶节点data域保存了完整的数据记录。这个索引的key是数据表的主键，因此InnoDB表数据文件本身就是主索引。这被称为“聚簇索引（或聚集索引）”。而其余的索引都作为辅助索引，辅助索引的data域存储相应记录主键的值而不是地址，这也是和MyISAM不同的地方。在根据主索引搜索时，直接找到key所在的节点即可取出数据；在根据辅助索引查找时，则需要先取出主键的值，再走一遍主索引。 因此，在设计表的时候，不建议使用过长的字段作为主键，也不建议使用非单调的字段作为主键，这样会造成主索引频繁分裂。 PS：整理自《Java工程师修炼之道》

### Q2. 事务的特性

原子性（Atomicity）： 事务是最小的执行单位，不允许分割。事务的原子性确保动作要么全部完成，要么完全不起作用；
一致性（Consistency）： 执行事务前后，数据保持一致，多个事务对同一个数据读取的结果是相同的；
隔离性（Isolation）： 并发访问数据库时，一个用户的事务不被其他事务所干扰，各并发事务之间数据库是独立的；
持久性（Durability）： 一个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发生故障也不应该对其有任何影响。


在典型的应用程序中，多个事务并发运行，经常会操作相同的数据来完成各自的任务（多个用户对同一数据进行操作）。并发虽然是必须的，但可能会导致以下的问题。

脏读（Dirty read）: 当一个事务正在访问数据并且对数据进行了修改，而这种修改还没有提交到数据库中，这时另外一个事务也访问了这个数据，然后使用了这个数据。因为这个数据是还没有提交的数据，那么另外一个事务读到的这个数据是“脏数据”，依据“脏数据”所做的操作可能是不正确的。
丢失修改（Lost to modify）: 指在一个事务读取一个数据时，另外一个事务也访问了该数据，那么在第一个事务中修改了这个数据后，第二个事务也修改了这个数据。这样第一个事务内的修改结果就被丢失，因此称为丢失修改。 例如：事务1读取某表中的数据A=20，事务2也读取A=20，事务1修改A=A-1，事务2也修改A=A-1，最终结果A=19，事务1的修改被丢失。
不可重复读（Unrepeatableread）: 指在一个事务内多次读同一数据。在这个事务还没有结束时，另一个事务也访问该数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改导致第一个事务两次读取的数据可能不太一样。这就发生了在一个事务内两次读到的数据是不一样的情况，因此称为不可重复读。
幻读（Phantom read）: 幻读与不可重复读类似。它发生在一个事务（T1）读取了几行数据，接着另一个并发事务（T2）插入了一些数据时。在随后的查询中，第一个事务（T1）就会发现多了一些原本不存在的记录，就好像发生了幻觉一样，所以称为幻读。~~~~~~~~

参考:[可重复读的实现原理](https://zhuanlan.zhihu.com/p/146010424)

大体的意思是每个字段都会记录当前的事物id(trx_id)，假设A事务创建后,B事务再创建，如果B修改了(id=1)的数据并提交，该行数据的事务字段trx_id会被记录为B的,若A事务再次查询时，只会查询小于等于当前事务trx_id的数据，
但是该行的trx_id为B的,大于A,于是通过undo log查找上一次的版本，直到trx_id<当前A的trx_id，此时A查出来的记录为当前记录，也就保证了可重复读。
假设A事务创建后,B事务再创建，注意此时的顺序，A修改了(id=1)的数据未提交，B此时也想修改该行数据，该行数据会被加锁，B事务会被阻塞，知道A回滚或者提交
