---
thumbnail: /images/java.jpeg
title: java并发编程实战阅读1
date: 2019-11-20 11:13:34
categories:
  -  [学习]
  -  [阅读笔记]

tags:
	- java
	- 多线程
---

## 可重入锁（synchronized）

```java
class Widget {
    public synchronized void doSomething() {
    }
}

class LoggingWidget extends Widget {
  	// 方法锁 Widget是对象锁
    public synchronized void doSomething() {
        System.out.println(toString() + ": calling doSomething");
      	// 再次获取Widget对象锁
        super.doSomething();
    }
}
```



<!--more-->

## 发布与逸出

### 逸出问题

```java
public class ThisEscape {
    public ThisEscape(EventSource source) {
       	// 该监听器是不安全的
        source.registerListener(new EventListener() {
            public void onEvent(Event e) {
              	// 该监听器不属于ThisEscape，却能修改ThisEscape 。this逸出
                doSomething(e);
            }
        });
    }

    void doSomething(Event e) {
        // 当 多个线程实例化ThisEscape 时， 这个方法会被多个线程调用
        // 如果当前方法会修改this域中的属性，那么讲无法保证该对象是线程安全的
    }


    interface EventSource {
        void registerListener(EventListener e);
    }

    interface EventListener {
        void onEvent(Event e);
    }

    interface Event {
    }
}
```





```java
public class SafeListener {
    private final EventListener listener;

    private SafeListener() {
      	// 该监听器只属于当前对象 ，并且只作用域当前对象
        listener = new EventListener() {
            public void onEvent(Event e) {
                doSomething(e);
            }
        };
    }

    public static SafeListener newInstance(EventSource source) {
        SafeListener safe = new SafeListener();
      	// 该监听器是安全的
        source.registerListener(safe.listener);
        return safe;
    }

    void doSomething(Event e) {
    }


    interface EventSource {
        void registerListener(EventListener e);
    }

    interface EventListener {
        void onEvent(Event e);
    }

    interface Event {
    }
}
```







### 发布问题

```java
public class Holder {
    private int n;

    public Holder(int n) {
        this.n = n;
    }

    public void assertSanity() {
      	// 左值是旧的
        if (n < n)
            throw new AssertionError("This statement is false.");
    }

    public static void main(String[] args) {
        Holder holder = new Holder(5);
      
      	// 一个线程模拟不断调用一个对象。（预期是想正确运行的）
        new Thread(new Runnable() {
            @Override
            public void run() {
                while (true){
                    holder.assertSanity();
                }
            }
        }).start();
				// 主线程 模拟 修改对象的属性。
      	// 导致Holder 不安全。使assertSanity异常
        while (true){
            holder.n += 1;
        }
    }
}
```





### 监视器模式（基础）

```java
public class PrivateLock {
  	// 等同this🔐
    private final Object myLock = new Object();

    void someMethod() {
        synchronized (myLock) {
             // 访问被监视的变量
        }
    }
}
```





```java
@ThreadSafe
public class MonitorVehicleTracker {
    @GuardedBy("this")
    private final Map<String, MutablePoint> locations;

    public MonitorVehicleTracker(Map<String, MutablePoint> locations) {
        this.locations = deepCopy(locations);
    }
// 监视 locations
    public synchronized Map<String, MutablePoint> getLocations() {
        return deepCopy(locations);
    }
// 监视 locations
    public synchronized MutablePoint getLocation(String id) {
        MutablePoint loc = locations.get(id);
        return loc == null ? null : new MutablePoint(loc);
    }
// 监视 locations
    public synchronized void setLocation(String id, int x, int y) {
        MutablePoint loc = locations.get(id);
        if (loc == null)
            throw new IllegalArgumentException("No such ID: " + id);
        loc.x = x;
        loc.y = y;
    }

    private static Map<String, MutablePoint> deepCopy(Map<String, MutablePoint> locations) {
        Map<String, MutablePoint> result = new HashMap<>();

        for (String id : locations.keySet())
            result.put(id, new MutablePoint(locations.get(id)));

        return Collections.unmodifiableMap(result);
    }
}


@NotThreadSafe
public class MutablePoint {
    public int x, y;

    public MutablePoint() {
        x = 0;
        y = 0;
    }

    public MutablePoint(MutablePoint p) {
        this.x = p.x;
        this.y = p.y;
    }
}

```





### 代理模式

```java
// 代理 车辆追踪，存储的是车辆的坐标点
@ThreadSafe
public class DelegatingVehicleTracker {
  	// 代理的同步对象
  	// ConcurrentMap 是同步的，外部就不需要同步
    private final ConcurrentMap<String, Point> locations;
    private final Map<String, Point> unmodifiableMap;

    //对locations 设置不可改变
    public DelegatingVehicleTracker(Map<String, Point> points) {
        locations = new ConcurrentHashMap<String, Point>(points);
        unmodifiableMap = Collections.unmodifiableMap(locations);
    }

    // 取消了监视器（同步锁）
    public Map<String, Point> getLocations() {
        return unmodifiableMap;
    }

    // 取消了监视器（同步锁）
    public Point getLocation(String id) {
        return locations.get(id);
    }
		// 取消了监视器（同步锁）
    public void setLocation(String id, int x, int y) {
        if (locations.replace(id, new Point(x, y)) == null)
            throw new IllegalArgumentException("invalid vehicle name: " + id);
    }


    // Alternate version of getLocations (Listing 4.8)
    public Map<String, Point> getLocationsAsStatic() {
        // 返回不可改变的引用
        return Collections.unmodifiableMap(
                new HashMap<String, Point>(locations));
    }
}
```



**代理失效情况**

```java
public class NumberRange {
    // INVARIANT: lower <= upper
    private final AtomicInteger lower = new AtomicInteger(0);
    private final AtomicInteger upper = new AtomicInteger(0);

  	// 设置lower
    public void setLower(int i) {
        // Warning -- unsafe check-then-act
      
      	// 如果要设置的值大于 最大值 抛出异常
        if (i > upper.get())
            throw new IllegalArgumentException("can't set lower to " + i + " > upper");
        lower.set(i);
    }
		// 设置upper
  	// 如果要设置的值小于最小值 抛出异常
    public void setUpper(int i) {
        // Warning -- unsafe check-then-act
        if (i < lower.get())
            throw new IllegalArgumentException("can't set upper to " + i + " < lower");
        upper.set(i);
    }

    public boolean isInRange(int i) {
        return (i >= lower.get() && i <= upper.get());
    }
}
```

<u>每个方法都是合理的
但是两个线程同时分别设置（5，4）也可成立</u>





## ConcurrentMap

```java
/**
     * {@inheritDoc}
     *
     * @return the previous value associated with the specified key,
     *         or {@code null} if there was no mapping for the key
     * @throws NullPointerException if the specified key or value is null
     */
// 返回之前的对应key值 或者 如果没有key对应的值，返回null
public V putIfAbsent(K key, V value) {
  return putVal(key, value, true);
}
```



之前自己有个小需求对大量的文件进行处理，为了性能采取多线程，因此要维护一个已完成修理的表，当时采用HashMap和同步锁，每个线程处理文件时都要访问Map，此时会造成严重的性能瓶颈。因为每个线程处理完文件后会继续访问Map表







另一个例子-1

```java
public class Memoizer1<A, V> implements Computable<A, V> {
    @GuardedBy("this")
    private final Map<A, V> cache = new HashMap<A, V>();
    private final Computable<A, V> c;

    public Memoizer1(Computable<A, V> c) {
        this.c = c;
    }

    public synchronized V compute(A arg) throws InterruptedException {
        V result = cache.get(arg);
        if (result == null) {
            // 这里会花费大量的时间占据锁
            result = c.compute(arg);
            cache.put(arg, result);
        }
        return result;
    }
}

/**
 * 消耗时间的方法
 */
interface Computable<A, V> {
    V compute(A arg) throws InterruptedException;
}

class ExpensiveFunction
        implements Computable<String, BigInteger> {
    public BigInteger compute(String arg) {
        // after deep thought...
        return new BigInteger(arg);
    }
}
```



另一个例子-2

```java
public class Memoizer2<A, V> implements Computable<A, V> {
    private final Map<A, V> cache = new ConcurrentHashMap<A, V>();
    private final Computable<A, V> c;

    public Memoizer2(Computable<A, V> c) {
        this.c = c;
    }

    public V compute(A arg) throws InterruptedException {
        // 可能会有多个个线程同时获取到null，额外的计算
        V result = cache.get(arg);
        if (result == null) {
            result = c.compute(arg);
            cache.put(arg, result);
        }
        return result;
    }
}
```

另一个例子-3

```java
public class Memoizer3 <A, V> implements Computable<A, V> {
    private final Map<A, Future<V>> cache
            = new ConcurrentHashMap<A, Future<V>>();
    private final Computable<A, V> c;

    public Memoizer3(Computable<A, V> c) {
        this.c = c;
    }

    public V compute(final A arg) throws InterruptedException {

      	// 依旧可能多个线程同时获取到null，但是概率小很多
        Future<V> f = cache.get(arg); 

        if (f == null) {
            Callable<V> eval = new Callable<V>() {
                public V call() throws InterruptedException {
                    return c.compute(arg);
                }
            };
            FutureTask<V> ft = new FutureTask<V>(eval);
            f = ft;
            cache.put(arg, ft);
            ft.run(); // call to c.compute happens here
        }
        try {
            return f.get();
        } catch (ExecutionException e) {
            throw LaunderThrowable.launderThrowable(e.getCause());
        }
    }
}
```



另一个例子-Final

```java
public class Memoizer<A, V> implements Computable<A, V> {
    private final ConcurrentMap<A, Future<V>> cache
            = new ConcurrentHashMap<A, Future<V>>();
    private final Computable<A, V> c;

    public Memoizer(Computable<A, V> c) {
        this.c = c;
    }

    public V compute(final A arg) throws InterruptedException {
        while (true) {
            Future<V> f = cache.get(arg);
            if (f == null) {
                Callable<V> eval = new Callable<V>() {
                    public V call() throws InterruptedException {
                        return c.compute(arg);
                    }
                };
                FutureTask<V> ft = new FutureTask<V>(eval);
                //原子操作
                f = cache.putIfAbsent(arg, ft);
                if (f == null) {
                    f = ft;
                  	// 仅仅调用了run方法 并没有启动一个线程
                    ft.run();
                }
            }
            try {
                return f.get();
            } catch (CancellationException e) {
              	// 取消移除f
                cache.remove(arg, f);
            } catch (ExecutionException e) {
                throw LaunderThrowable.launderThrowable(e.getCause());
            }
        }
    }
}
```



## 中断线程

### Thread.interrupted();

```java
/**
 * Tests whether the current thread has been interrupted(测试当前线程是否被中断).  The
 * <i>interrupted status</i> of the thread is cleared by this method（然后当前线程的中断状态会被重置）.  In
 * other words, if this method were to be called twice in succession, the
 * second call would return false(unless the current thread were
 * interrupted again（然后当前线程的中断状态被重置后再次调用，会返回true,除非这个线程又被中断一次) , after the first call had cleared its interrupted
 * status and before the second call had examined it).
 *
 * <p>A thread interruption ignored because a thread was not alive
 * at the time of the interrupt will be reflected by this method
 * returning false.
 *
 * @return  <code>true</code> if the current thread has been interrupted;
 *          <code>false</code> otherwise.
 * @see #isInterrupted()
 * @revised 6.0
 */
public static boolean interrupted() {
    return currentThread().isInterrupted(true);
}




```

### Thread.currentThread().isInterrupted()

```java

/**
* Tests if some Thread has been interrupted.  The interrupted state
* is reset or not based on the value of ClearInterrupted that is
* passed.
* 测试线程是否被中断。 
* 这个中断状态是否会被重置 取决于 ClearInterrupted
*/
private native boolean isInterrupted(boolean ClearInterrupted);
```



### Thread.currentThread().interrupt();

```java
public void interrupt() {
    if (this != Thread.currentThread())
        checkAccess();

    synchronized (blockerLock) {
        Interruptible b = blocker;
        if (b != null) {
            interrupt0();           // Just to set the interrupt flag
            b.interrupt(this);
            return;
        }
    }
    interrupt0();
}
```



举个例子

```java
Thread currentThread = Thread.currentThread();
currentThread.interrupt(); // 设置中断状态
System.out.println(currentThread.isInterrupted()); // true 这设置了中断状态
boolean b = Thread.interrupted();// true 重置当前线程中状态成功(当前线程中断状态重置为false) 
System.out.println(b);
boolean b2 = Thread.interrupted();// false 重置失败 因为重置过了
System.out.println(b2);
```



```java
Thread currentThread = Thread.currentThread();
currentThread.interrupt();
System.out.println(currentThread.isInterrupted()); 
boolean b = Thread.interrupted();
System.out.println(b);
currentThread.interrupt(); // +1

boolean b2 = Thread.interrupted();// true 重置成功 因为又被中断了
System.out.println(b2);
```





## 闭锁

### CountDownLatch

```java
public class TestHarness {
    public long timeTasks(int nThreads, final Runnable task)
            throws InterruptedException {
        final CountDownLatch startGate = new CountDownLatch(1);
        final CountDownLatch endGate = new CountDownLatch(nThreads);

        for (int i = 0; i < nThreads; i++) {
            Thread t = new Thread() {
                public void run() {
                    try {
                        // 等待为0 确保所有线程停在这里
                        startGate.await();
                        try {
                            task.run();
                        } finally {
                            endGate.countDown();
                        }
                    } catch (InterruptedException ignored) {
                    }
                }
            };
            t.start();
        }

        long start = System.nanoTime();
      	// 主线程让所有线程开始
        startGate.countDown();      
      	// 等待所有线程运行完毕
        endGate.await();
        long end = System.nanoTime();
        return end - start;
    }
}
```



### FutureTask

```java
package net.jcip.examples;

import java.util.concurrent.*;

/**
 * Preloader
 *
 * Using FutureTask to preload data that is needed later
 *
 * @author Brian Goetz and Tim Peierls
 */

public class Preloader {
    ProductInfo loadProductInfo() throws DataLoadException {
        return null;
    }

    private final FutureTask<ProductInfo> future =
        new FutureTask<ProductInfo>(new Callable<ProductInfo>() {
            public ProductInfo call() throws DataLoadException {
                return loadProductInfo();
            }
        });
    private final Thread thread = new Thread(future);
		
    // 1.线程要先初始化
    public void start() { thread.start(); }

  	// 2.用户调用
    public ProductInfo get()
            throws DataLoadException, InterruptedException {
        try {
          	// 等待线程结束
            return future.get();
        } catch (ExecutionException e) {
            Throwable cause = e.getCause();
            if (cause instanceof DataLoadException)
                throw (DataLoadException) cause;
            else
                throw LaunderThrowable.launderThrowable(cause);
        }
    }

    interface ProductInfo {
    }
}

class DataLoadException extends Exception { }
```



### Semaphore

```java
public class BoundedHashSet <T> {
  	
    private final Set<T> set;
    private final Semaphore sem;

    public BoundedHashSet(int bound) {
        // 允许bound数量的线程同时访问set， set还是需要同步
        this.set = Collections.synchronizedSet(new HashSet<T>());
        sem = new Semaphore(bound);
    }

    public boolean add(T o) throws InterruptedException {
      	// 线程获取资源
        sem.acquire();
        boolean wasAdded = false;
        try {
            wasAdded = set.add(o);
            return wasAdded;
        } finally {
            if (!wasAdded)
              	// 线程释放资源
                sem.release();
        }
    }

    public boolean remove(Object o) {
        boolean wasRemoved = set.remove(o);
        if (wasRemoved)
            sem.release();
        return wasRemoved;
    }
}
```



### CyclicBarrier

```java
public class CTest {
    private final CyclicBarrier barrier;
    public CTest() {
      	// 初始化栅栏 设置了parties 为3,即线程必须达到3个调用b.await()
        //parties the number of threads that must invoke {@link #await}
        //before the barrier is tripped
        barrier = new CyclicBarrier(3, new Runnable() {
            @Override
            public void run() {
                System.out.println("所有线程都要准备通过啦");
            }
        });

    }

    void start() {
      	// 此时启动两个线程。无法通过 ，
      	// 改成3个即可通过
        for (int j = 0; j < 2; j++) {
            new Thread() {
                @Override
                public void run() {
                    try {
                        CTest.this.run();
                    } catch (BrokenBarrierException e) {
                    } catch (InterruptedException e) {
                    }
                }
            }.start();
        }
    }

    void run() throws BrokenBarrierException, InterruptedException {
        while (true) {
            barrier.await();
            System.out.println(Thread.currentThread().getName() + "exec...");

        }
    }
    public static void main(String[] args) {
        new CTest().start();
    }
}

```



100页