## Java 线程池

线程池用于管理线程，实现线程的复用，相较于 `new Thread()` 大量的创建销毁操作，线程池占用的资源更少。

在 `Java` 中，使用 `ThreadPoolExecutor` 创建线程池，同时也可以使用 `Executors` 创建，实际上 `Executors` 也是调用 `ThreadPoolExecutor` 的构造方法。（在阿里巴巴开发手册中，禁止使用 `Executors` 创建线程池）

## ThreadPoolExecutor 构造方法
```java
ThreadPoolExecutor(int corePoolSize,
                   int maximumPoolSize,
                   long keepAliveTime,
                   TimeUnit unit,
                   BlockingQueue<Runnable> workQueue,
                   ThreadFactory threadFactory,
                   RejectedExecutionHandler handler)
```

- corePoolSize（核心线程数）：核心线程不会被回收，默认创建线程池后不会创建核心线程，当有任务放到线程池中才会创建，也可以通过 `prestartAllCoreThreads()` 初始化全部核心线程。
- maximumPoolSize（最大线程数）：当 `workQueue` 容量用完后，线程池会继续创建线程，线程数到达 `maximumPoolSize` 停止创建，如果继续向线程池添加任务，会调用 `handler` 处理。
- keepAliveTime（线程存活时间）：不会回收核心线程。
- unit（线程存活时间单位）：略。
- workQueue（线程缓存队列）：当核心线程用完后，如果有新任务添加到线程池，会把任务放到 `workQueue` 中。
- threadFactory（线程创建工厂）：略。
- handler（拒绝执行任务回掉）：线程数到达 `maximumPoolSize`，`workQueue` 也用完后，如果有新任务添加到线程池，会调用 `handler`。


## 参考
- https://www.cnblogs.com/dolphin0520/p/3932921.html
- https://crossoverjie.top/2018/07/29/java-senior/ThreadPool/
- https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Multithread/java线程池学习总结.md
- http://www.ideabuffer.cn/2017/04/04/深入理解Java线程池：ThreadPoolExecutor/