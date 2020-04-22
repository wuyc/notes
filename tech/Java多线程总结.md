## 什么是线程
线程是程序的执行单元，它包含在进程之中。

## 什么是进程
进程是线程的集合，一个线程内至少包含一个进程。

## 创建线程
**1. 继承 Thread 类**
```java
public class MyThread extends Thread {

    public void run() {
        // 线程代码块
    }

    public static void main(String[] args) {
        MyThread myThread = new MyThread();
        myThread.start(); // 开启线程并等待 CPU 分配时间片
    }
}
```
**2. 实现 Runnable 接口**
```java
public class MyThread implements Runnable {

    @Override
    public void run() {
        // 线程代码块
    }

    public static void main(String[] args) {
        Thread thread = new Thread(new MyThread());
        thread.start(); // 开启线程并等待 CPU 分配时间片
    }
}
```
**tips:** 在实际开发中建议使用 Runnable 接口创建线程，因为符合面向接口编程的思想，实现 Runnable 接口后可以实现其它接口，还可以继承其它类。

## sleep() 和 wait() 的区别
1. sleep() 是 Thread 类中的方法， wait() 是 Object 类中的方法。
2. Thread.sleep() 不会改变当前锁的行为，如果当前线程有锁，Thread.sleep() 不会释放当前的锁。
3. wait() 方法会释放当前线程持有的锁，执行 wait() 后需要执行 notify() 或 notifyAll() 进行唤醒，唤醒后线程进入就绪状态，等待 CPU 调度。如果不唤醒，此线程会一直处于等待状态。

## 什么是守护线程
守护线程的作用是为其它线程的运行提供服务。简单的理解为：任何一个守护线程都是整个 JVM 中所有非守护线程的保姆。当程序只剩下守护线程的时候（也就是主线程运行结束），程序就会退出。例如 GC线程 就是守护线程。

## synchronized 关键字
synchronized 可以修饰方法和代码块。
修饰实例方法表示锁定当前方法所属的对象；修饰静态方法表示锁定当前方法所属的 class 类。两者不会出现互斥现象。
修饰代码块时指定锁定的数据，可以锁定类，也可以锁定实例对象。锁定类只对类有效，对实例对象无效。

## volatile 关键字
> 推荐阅读：
> https://www.cnblogs.com/dolphin0520/p/3920373.html

要了解 volatile 关键字，首先要了解主内存和工作内存，在每个 Java 线程中，线程会把要操作的数据从主内存拷贝一份到线程内部的工作内存中，然后线程操作的是工作内存中的数据，数据处理完成后再刷新到主内存中。这就可能造成一个线程在工作内存中修改了一个变量的值，而另外一个线程还继续使用它在主内存中的变量值的拷贝，造成数据的不一致。
要解决这种问题，就需要引入 volatile 关键字，这就指示 JVM 这个变量是不稳定的，每次使用它都到主存中进行读取。数据不经过线程内的工作内存。volatile 关键字的主要作用就是保证变量的可见性然后还有一个作用是防止指令重排序。

## synchronized 和 Lock 的区别
Lock 是 Java 5 引入的新的 API，它使用 Java API 的方式进行线程加锁，Lock 能完成 synchronized 所实现的所有功能。Lock 有比 synchronized 更精确的线程语义和更好的性能，而且不强制性的要求一定要获得锁。synchronized 会自动释放锁，而 Lock 一定要求程序员手工释放，并且最好在 finally 块中释放（这是释放外部资源的最好的地方）。