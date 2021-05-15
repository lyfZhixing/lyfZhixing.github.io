---
title: Java多线程基础
date: 2020-5-10 13:43:21
tags: [java, 并发, 多线程]
categories: [java基础]
---

# Java多线程基础    

线程是一个轻量级的“进程”， 是程序最小的执行单位。了解多线程，我们从线程的生命周期、基本操作、线程安全等几个方面来入手。   


## 生命周期
在Java中一个线程的生命周期状态有以下六种（都在Thread.State枚举中）：  

```
public enum State {
        // 刚创建线程，尚未执行
        NEW,
        // 线程执行中
        RUNNABLE,
        // 线程阻塞（遇到了synchronized同步块）
        BLOCKED,
        // 线程等待（wait()）,无限期直到被notify()唤醒
        WAITING,
        // 线程等待，有时限等待
        TIMED_WAITING,
        // 线程结束
        TERMINATED;
}

```  



可以简单画一张图来描述Java线程的生命周期：     

![线程状态图](https://gitee.com/lyfZhixing/draw/raw/master/multi-Thread/png/线程状态图.png)  


## 基本操作  

其实在上面的线程状态图中已经包含了大部分线程的基本操作了：   

### 1. `new `一个线程对象，用`Thread#start()`启动线程。   

`Thread#start()`会让这个线程执行`Thread#run()`,但`Thread#run` 默认的实现为空，故在`new`一个线程对象时需要重写（`@Override`）`run()`方法，实现自己的任务逻辑。

> tips: start()会调用native方法start0(),继而由 JVM 来实现多线程的控制,因为需要系统调用来控制时间分片


我们可以通过继承Thread类来重写run方法，实现自定义任务。
Java是单继承（只能继承一个类）多实现（可以实现多个接口），由于单继承的局限性，更推荐通过实现`Runnable`接口的`run()`方法来实现自定义任务（实际上该接口也只有这一个方法）.   

Thread类有一个重要的构造方法： `public Thread(Runnable target)` ,看到target我们自然而然就回想到代理模式。实际上Thread和Runnable就是一种静态代理的实现，参考以下代码（JDK8部分源码）   

```

    // 接口
    @FunctionalInterface
    public interface Runnable {
        public abstract void run();
    }

    // Thread 实现了Runnable接口 作为代理
    public class Thread implements Runnable {

        // 构造方法之一
        public Thread(Runnable target) {
            init(null, target, "Thread-" + nextThreadNum(), 0);
        }

        @Override
        public void run() {
            if (target != null) {
                target.run();
            }
        }
    }

    // 自己实现Runnable 接口
    public class MyThread implements Runnable {
        public int count = 10;
        @Override
        public synchronized void run(){
            while(count>0){
                count--;
            }
        }
    }

    // 创建线程实例
    public class Test {
        public static void main(String[] args) {
            Thread thread = new Thread(new MyThread());
            // start()会调用native方法start0(),继而由 JVM 来实现多线程的控制,因为需要系统调用来控制时间分片
            thread.start();
        }
    }


```    


## 2. 线程终止与中断  

线程终止的情况：  
1. 正常执行结束   
2. 调用Thread#stop()方法暴力终止。由于其会把执行一半的线程强行终止，可能还会引起数据不一致问题，目前已被废弃（`@Deprecated`）     

![stop](https://gitee.com/lyfZhixing/draw/raw/master/multi-Thread/png/stop.png)   

stop已被废弃，JDK提供了一个更为强大的支持，线程中断:
```   


  public class Thread implements Runnable {
      // 中断线程
      pubulic void interrupt();     
      // 判断是否被中断  
      public boolean isInterrupted();
      // 判断是否被中断，并清除当前中断状态
      poublic static boolean interrupted();
  }


```    

线程中断并不会使线程立马终止，而是给线程发送一个通知，告知线程目标需要终止，线程具体在什么时候退出，由目标线程自行决定（通过isInterrupted判断状态，决定怎么退出以及退出逻辑）。  

关于中断还有中断响应这个概念，所有抛出InterruptedException的方法都可以响应中断，例如：  

```   

  // Object#wait()
  public final void wait() throws InterruptedException {
    wait(0);
  }

  // Thread#sleep(long)
  public static native void sleep(long millis) throws InterruptedException;   


```    


> tips: 对抛出的InterruptedException进行捕获会清除中断标记，如果后续还需要判断中断状态，需要再次设置中断标记Thread.currentThread.interrupt()   


## 3. wait()和notify()。    

wait() 和 notify()是Object类中的两个方法。用于对多线程协作进行支持。    

obj.wait()并不是随便可以调用的，它 **必须包含在对应synchronized(obj) 语句中** ，因为无论是wait()还是notify()都需要先获得一个目标对象的监视器 `（源码中wait()的注释This method should only be called by a thread that is the owner of this object's monitor.）` 。notify() 也一样。  

obj.wait()使持有obj对象锁的线程进入等待，**并释放资源** 让其他等待obj对象的线程可以正常执行，obj.notify()会 **随机** 唤醒一个等待中的线程， **但不会立即释放资源** 需要等待当前线程的synchronized语句执行完毕才会释放，还有一个obj.notifyAll()会唤醒所有等待的线程.

> tips: 实际上, hotspot JVM 中notify（）是顺序唤醒的。[参考文章](https://www.jianshu.com/p/99f73827c616)   

>tips: sleep()方法也会让线程等待若干时间，但并不释放锁资源，也不能被通知唤醒。  但wait() 和 sleep() 都可响应中断。sleep()会使线程进入TIMED_WAITING状态  

>tips: 使用jstack命令可以查看当前系统的线程信息。配合jps使用   

>tips: 推荐wait()和notify()配合使用来替换挂起suspend()和继续执行resume()这两个已被废弃的方法。线程挂起suspend()并不会释放资源，若使用不当，resume()方法在suspend()之前执行了，还会导致死锁。被挂起的线程状态还是Runnable
