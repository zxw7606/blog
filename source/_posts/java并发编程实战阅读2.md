---
thumbnail: /images/java.jpeg
title: java并发编程实战阅读2
date: 2019-11-21 15:39:25
categories:
  -  [学习]
  -  [阅读笔记]
tags:
	- java
	- 多线程
---



## 线程池



#### 线程池状态

```java
/*
*   RUNNING:  Accept new tasks and process queued tasks
							能接收并且处理队列中的任务
*   SHUTDOWN: Don't accept new tasks, but process queued tasks
							不接受但是处理队列中的任务 
*   STOP:     Don't accept new tasks, don't process queued tasks,
*             and interrupt in-progress tasks
							不接受，不处理任务 并且正在中断正在进行的任务
*   TIDYING:  All tasks have terminated, workerCount is zero,
*             the thread transitioning to state TIDYING
*             will run the terminated() hook method
							所有的任务都被中断，并且工作的线程为0， 将会调用terminated() 方法
*   TERMINATED: terminated() has completed
*/
```



<!-- more -->

#### 延时任务

Timer的缺点

```java
public class OutOfTime {
    public static void main(String[] args) throws Exception {
        Timer timer = new Timer();
        // 创建线程, 抛出 RuntimeException
        timer.schedule(new ThrowTask(), 1);
        SECONDS.sleep(1);
        // 捕获不了异常无法继续分配任务
        timer.schedule(new ThrowTask(), 1);
        SECONDS.sleep(5);
    }

    static class ThrowTask extends TimerTask {
        public void run() {
            System.out.println("start...");
            throw new RuntimeException();
        }
    }
}
```





#### 获取提交的线程的结果

```java
public abstract class Renderer {
    private final ExecutorService executor;

    Renderer(ExecutorService executor) {
        this.executor = executor;
    }

    void renderPage(CharSequence source) {
        final List<ImageInfo> info = scanForImageInfo(source);
      
      	// CompletionService 支持 take and poll
        CompletionService<ImageData> completionService =
                new ExecutorCompletionService<ImageData>(executor);
        for (final ImageInfo imageInfo : info)
          	// 提交单个任务
          	// 好处: 不用等到全部下载好
            completionService.submit(new Callable<ImageData>() {
                public ImageData call() {
                    return imageInfo.downloadImage();
                }
            });
   
        for (int t = 0, n = info.size(); t < n; t++) {
          Future<ImageData> f = completionService.take();
          ImageData imageData = f.get();
          renderImage(imageData);

    }
}
```





#### 批量获取结果

```java
public class TimeBudget {
    private static ExecutorService exec = Executors.newCachedThreadPool();

    public List<TravelQuote> getRankedTravelQuotes(TravelInfo travelInfo, Set<TravelCompany> companies,time, TimeUnit unit)
            throws InterruptedException {
        List<QuoteTask> tasks = new ArrayList<QuoteTask>();
        for (TravelCompany company : companies)
            tasks.add(new QuoteTask(company, travelInfo));

        // 执行所有的Callable
        List<Future<TravelQuote>> futures = exec.invokeAll(tasks, time, unit);

        List<TravelQuote> quotes =
                new ArrayList<TravelQuote>(tasks.size());
        Iterator<QuoteTask> taskIter = tasks.iterator();
        for (Future<TravelQuote> f : futures) {
            QuoteTask task = taskIter.next();
                quotes.add(f.get());
        }
        return quotes;
    }

}
```





## 任务的取消



```java
public static void main(String[] args) {
        Thread thread = new Thread(new Runnable() {
            boolean interrupted = false;

            @Override
            public void run() {
                try {
                    Thread.sleep(60000);
                } catch (InterruptedException e) {
                  	// 捕获中断异常
               			// 这里依旧是false
                  	// 可见外部调用只有一个中断信号，正确的状态要手动中断自己
                    displayStatus(); // false
                    System.out.println("interrupted...");
                    interrupted = true;
                } finally {
                    if (interrupted) {
                        displayStatus(); // false
                      	// 手动中断自己
                        Thread.currentThread().interrupt();
                        displayStatus(); // true
                    }
                }
            }

            private void displayStatus() {
                System.out.println(Thread.currentThread().isInterrupted());
            }
        });
        thread.start();
      	// 让线程中断
        thread.interrupt();
    }
```



这段代码有点意思。 可以无视取消信号

并且可以恢复线程的信号

```java
public class NoncancelableTask {
    public Task getNextTask(BlockingQueue<Task> queue) {
        boolean interrupted = false;
        try {
            while (true) {
                try {
                    return queue.take();
                } catch (InterruptedException e) {
                    interrupted = true;
                    // fall through and retry
                }
            }
        } finally {
            if (interrupted)
                Thread.currentThread().interrupt();
        }
    }
}
```